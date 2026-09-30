# Trace Notes: notes feature (learn-ops-api)

## Request Path

When a user opens the student notes dialog, here's the complete flow:

| Layer | File | Class / Function | What it does |
|-------|------|-----------------|--------------|
| UI dialog | learn-ops-client/src/components/dashboard/StudentNoteDialog.js | StudentNoteDialog component | Displays notes dialog; calls `getStudentNotes(activeStudent.id)` when dialog opens; also calls `createStudentNote()` on Enter key |
| API helper | learn-ops-client/src/components/people/PeopleProvider.js | getStudentNotes() | Calls `fetchIt(${Settings.apiHost}/notes?studentId=${studentId})` with GET method to fetch notes for a student |
| Fetch utility | learn-ops-client/src/components/utils/Fetch.js | fetchIt() | Makes HTTP GET request to API URL with Authorization token header; parses JSON response |
| URL router | learn-ops-api/LearningPlatform/urls.py | router.register() | Registers `r'notes'` route to StudentNoteViewSet; DRF DefaultRouter handles `/notes?studentId=X` GET requests |
| View | learn-ops-api/LearningAPI/views/student_note_view.py | StudentNoteViewSet.list(request) | Validates required `studentId` query param; fetches student by ID; queries notes: `StudentNote.objects.filter(student=student)`; serializes and returns |
| Serializer | learn-ops-api/LearningAPI/views/student_note_view.py | StudentNoteSerializer | Converts StudentNote instances to JSON; includes `note_type` as nested StudentNoteTypeSerializer object; excludes student/coach FK, includes computed `author` property |
| DB Query | learn-ops-api/LearningAPI/models/people/student_note.py | StudentNote.objects.filter(student=student) | Fetches all notes for a given student; ordered by `-created_on` (newest first); also evaluates `author` property which queries coach.user (lazy) |
| Response | learn-ops-api/LearningAPI/views/student_note_view.py | Response class | Returns HTTP 200 with JSON array: [{id, note, author, note_type: {id, label}, created_on}, ...] |

### Database Queries

**List notes:**
- Main query: `StudentNote.objects.filter(student=student)` — fetches all notes for a student
- Lazy query (N+1 risk): `self.coach.user.first_name` + `self.coach.user.last_name` — evaluated for each note's `author` property; evaluates coach relationship then user relationship

**Create note:**
- Query 1: `NssUser.objects.get(pk=studentId)` — fetch target student
- Query 2: `NssUser.objects.get(user=request.auth.user)` — fetch current coach
- Query 3: `StudentNoteType.objects.get(pk=note_type)` — validate note type exists
- Query 4: `note.save()` — INSERT into StudentNote table

---

## Sequence Diagram

```mermaid
sequenceDiagram
    participant UI as StudentNoteDialog (UI)
    participant Provider as PeopleProvider
    participant Fetch as Fetch.js
    participant Router as URL Router
    participant View as StudentNoteViewSet
    participant Serializer as StudentNoteSerializer
    participant DB as StudentNote Model

    UI->>Provider: getStudentNotes(activeStudent.id)
    Provider->>Fetch: fetchIt("/notes?studentId=5", {method: "GET"})
    Fetch->>Fetch: Add Authorization header
    Fetch->>Router: GET /notes?studentId=5
    Router->>View: list(request, studentId=5)
    View->>View: Validate studentId query param
    View->>DB: NssUser.objects.get(pk=5)
    DB-->>View: Student object
    View->>DB: StudentNote.objects.filter(student=student)
    DB-->>View: [Note1, Note2, Note3]
    View->>Serializer: StudentNoteSerializer(notes, many=True)
    Serializer->>Serializer: For each note, get_author()
    Serializer->>DB: coach.user.first_name (lazy)
    DB-->>Serializer: "Jane"
    Serializer->>DB: coach.user.last_name (lazy)
    DB-->>Serializer: "Doe"
    Serializer-->>View: [{id, note, author: "Jane Doe", note_type, created_on}, ...]
    View-->>Router: Response(data, 200)
    Router-->>Fetch: HTTP 200 + JSON
    Fetch-->>Provider: Parsed JSON array
    Provider-->>UI: notes array
    UI->>UI: setState(notes)
    UI->>UI: Render notes list
```

### Alternative: Create Note

```mermaid
sequenceDiagram
    participant UI as StudentNoteDialog (UI)
    participant Fetch as Fetch.js
    participant Router as URL Router
    participant View as StudentNoteViewSet
    participant Serializer as StudentNoteSerializer
    participant DB as StudentNote Model

    UI->>UI: User enters note text & presses Enter
    UI->>Fetch: fetchIt("/notes", {method: "POST", body: {...}})
    Fetch->>Fetch: Add Authorization + Content-Type headers
    Fetch->>Router: POST /notes
    Router->>View: create(request)
    View->>DB: NssUser.objects.get(pk=studentId)
    DB-->>View: Student object
    View->>DB: NssUser.objects.get(user=request.auth.user)
    DB-->>View: Coach (NssUser) object
    View->>DB: StudentNoteType.objects.get(pk=type)
    DB-->>View: StudentNoteType object
    View->>View: Build StudentNote object
    View->>DB: note.save()
    DB-->>View: Saved note with ID
    View->>Serializer: StudentNoteSerializer(note)
    Serializer-->>View: Serialized note JSON
    View-->>Router: Response(serializer.data, 201)
    Router-->>Fetch: HTTP 201 + JSON
    Fetch-->>UI: Created note object
    UI->>UI: Refresh notes list
```

---

## Key Observations

### Authentication & Authorization
- **Token-based:** Authorization header contains `Token {user.token}` from browser localStorage
- **Permission check:** No explicit permission_classes set on StudentNoteViewSet (inherits ModelViewSet defaults; requires authentication via DRF)
- **Implicit authorization:** View validates that `request.auth.user` belongs to a coach, but doesn't check if coach can create notes for this student

### Request Validation
- **GET /notes:** Requires `studentId` query parameter; returns 400 if missing, 404 if student doesn't exist
- **POST /notes:** Requires `studentId`, `note`, `type` in request body; validates note type exists; logs operations with structlog

### N+1 Query Problem
- **List notes:** For each note, evaluates `author` property which triggers TWO database queries (coach FK + user FK)
- **Risk:** If there are 100 notes from 50 unique coaches, this evaluates 100+ queries instead of batching the joins
- **Fix:** Use `select_related('coach__user')` in queryset to pre-fetch coach and user in a single query

### Logging
- Uses structlog for structured logging: `logger.info("student_note_create_start", ...)`, etc.
- Logs are visible in server console and can be integrated with monitoring systems

### Response Fields
**List response:**
```json
[
  {
    "id": 42,
    "note": "Great progress on project X",
    "author": "Jane Doe",
    "note_type": {"id": 2, "label": "General Progress"},
    "created_on": "2025-09-30T14:32:15Z"
  }
]
```

**Create response (201):**
```json
{
  "id": 43,
  "note": "Needs review of algorithm implementation",
  "author": "Jane Doe",
  "note_type": {"id": 1, "label": "Technical"},
  "created_on": "2025-09-30T14:35:22Z"
}
```

### Ordering & Filtering
- Notes are **ordered by `-created_on`** (newest first) via model Meta
- Client-side filtering in StudentNoteDialog by note_type (dropdown filter in UI)

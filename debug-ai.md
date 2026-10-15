## Step 2: Claude's trace of the update method:
The Problem

The update method (lines 77-100) is missing a critical save() call. Here's what's happening:

def update(self, request, pk=None):
    try:
        student = NssUser.objects.get(pk=pk)

        if request.auth.user == student.user or request.auth.user.is_staff:
            if "slack_handle" in request.data:
                student.slack_handle = request.data["slack_handle"]  # Only updates in memory
            if "gitub_handle" in request.data:
                student.gitub_handle = request.data["gitub_handle"]  # Only updates in memory

            return Response(None, status=status.HTTP_204_NO_CONTENT)  # Returns immediately!

The core issue: In Django, setting attributes on a model instance (student.slack_handle = ...) only modifies the object in memory—it doesn't persist to the database. You must call .save() to write those changes to the database.

When the page refreshes, it queries the database again and get never saved.
def update(self, request, pk=None):
    try:
        student = NssUser.objects.get(pk=pk)

        if request.auth.user == student.user or request.auth.u
            if "slack_handle" in request.data:
                student.slack_handle = request.data["slack_handle"]  # Only updates in memory
            if "gitub_handle" in request.data:
                student.gitub_handle = request.data["gitub_hanry

            return Response(None, status=status.HTTP_204_NO_CO!

The core issue: In Django, setting attributes on a model insta..) only modifies the object in memory—it doesn't persist tothe database. You must call .save() to write those changes to the database.

When the page refreshes, it queries the database again and gets the original value that was never saved.

How It Should Work

Look at how the assess method does it correctly (lines 249-250):

latest_assessment.status = assessment_status
latest_assessment.save()  # <-- This is required!

The update method needs the same pattern. After setting the attributes, it should call student.save() before returning the response.

This is a classic Django pattern—attribute assignment alone is temporary; .save() commits it to the database.

## The Missing .save() Call

The .save() call is missing after line 90 and before line 92 (the return statement).

The corrected code should be:

if "slack_handle" in request.data:
    student.slack_handle = request.data["slack_handle"]  # Line 88
if "gitub_handle" in request.data:
    student.gitub_handle = request.data["gitub_handle"]  # Line 89
MISSING (should go here)

return Response(None, status=status.HTTP_204_NO_CONTENT)  # Line 92

The Django Concept: Object-Relational Mapping (ORM) & Explicit Persistence

This pattern relates to Django's ORM design philosophy:

1. Object vs. Database Separation
- When you fetch a model instance (student = NssUser.objects.get(pk=pk)), Django creates a Python object in memory
- This Python object is separate from the database record
- Changes to the Python object don't automatically sync to the database

2. Explicit Persistence (Not Automatic)
- Django requires you to explicitly call .save() to write changes back to the database
- This is intentional—it gives you control over when database writes happen
- You could make multiple changes and decide to save them together, or discard them without hitting the database

3. Why This Design?
- Performance: Avoid unnecessary database writes
- Transactions: Bundle related changes and ensure they all succeed or all fail together
- Control: Decide when to commit changes, potentially after validation
- Predictability: No hidden database operations—you control the database I/O

Compare this to the working assess method that correctly does:
latest_assessment.status = assessment_status
latest_assessment.save()  # Explicitly persists to database

Without the .save() call, the change only exists in the Python object in memory and vanishes when the request ends.

## Step 3: Comparison to debugger findings:
Claude correctly indentified the cause of the .save() issue. The lines of code did not match up between the two but that is just because of a log statement I was using for debugging purposes.
As usual Claude was much more thorough in its explanation than I could be. The changes cannot be made to the datbase without the .save() call, otherwise they will only exist within the object and be lost when the request ends.

## Step 4: The Test
From Claude: 
The test now replaces the stub:
- It sends a PUT to /students/{nss_user.id} with {"slack_handle": "@newhandle"}.
- It asserts the response is 204.
- It fetches the NssUser by pk.
- It asserts that slack_handle equals "@newhandle".

I expect this test to fail. StudentViewSet.update() in LearningAPI/views/student_view.py sets student.slack_handle but never calls student.save(), so the change won't reach the database. I haven't confirmed that by running it. If you want the test to pass, the view needs a student.save() before the 204 return. I haven't changed the view.

The from pytest import fail import is now unused. I left it in place.


## Step 5: Test failure output
=============================================== test session starts ===============================================
platform linux -- Python 3.11.11, pytest-9.1.1, pluggy-1.6.0 -- /usr/local/bin/python3
cachedir: .pytest_cache
django: version: 5.2.17, settings: LearningPlatform.test_settings (from ini)
rootdir: /app
configfile: pytest.ini
plugins: django-4.14.0, cov-7.1.0
collected 1 item

LearningAPI/tests/test_student_update.py::StudentPartialUpdateTests::test_patch_slack_handle_persists_to_database FAILED [100%]

==================================================== FAILURES =====================================================
_____________________ StudentPartialUpdateTests.test_patch_slack_handle_persists_to_database ______________________
LearningAPI/tests/test_student_update.py:33: in test_patch_slack_handle_persists_to_database
    self.assertEqual(updated_user.slack_handle, "@newhandle")
E   AssertionError: '@old_handle' != '@newhandle'
E   - @old_handle
E   + @newhandle
---------------------------------------------- Captured stderr setup ----------------------------------------------
Using existing test database for alias 'default' ('file:memorydb_default?mode=memory&cache=shared')...
============================================= short test summary info =============================================
FAILED LearningAPI/tests/test_student_update.py::StudentPartialUpdateTests::test_patch_slack_handle_persists_to_database - AssertionError: '@old_handle' != '@newhandle'
- @old_handle
+ @newhandle
================================================ 1 failed in 0.68s ================================================

## Step 6: The Fix
Add student.save() inside the if request.auth.user == student.user or request.auth.user.is_staff: block in LearningAPI/views/student_view.py, after the two if checks and before the return Response(None, status=status.HTTP_204_NO_CONTENT) on line 92. Indent it to match the surrounding code (16 spaces). That's the line before the blank line at 91.

The gitub_handle typo on lines 89–90 is a separate bug. Those lines read request.data["gitub_handle"], which doesn't match the github_handle field. I haven't changed anything.

## Step 7: Test passing output
=============================================== test session starts ===============================================
platform linux -- Python 3.11.11, pytest-9.1.1, pluggy-1.6.0 -- /usr/local/bin/python3
cachedir: .pytest_cache
django: version: 5.2.17, settings: LearningPlatform.test_settings (from ini)
rootdir: /app
configfile: pytest.ini
plugins: django-4.14.0, cov-7.1.0
collected 1 item

LearningAPI/tests/test_student_update.py::StudentPartialUpdateTests::test_patch_slack_handle_persists_to_database PASSED [100%]

================================================ 1 passed in 0.53s ================================================

The test passed and the bug with slack_handle not being saved to the database has been fixed.

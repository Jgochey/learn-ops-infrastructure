# learn-ops-api: Service Exploration

## 1. Top-level folders in `learn-ops-api`

| Folder | Why does this folder need to exist? |
|--------|-------------------------------------|
|LearningAPI| Handles Models, Serializers, Views and Tests. |
|LearningPlatform| Database, Settings, Authentication.      |
|LogViewer| Recording user messages/requests.               |

## 2. Folders inside `LearningAPI`

| Folder | What responsibility does it own and why? |
|--------|----------------------------------------------------------|
|Models  | Handling Coursework and Users. Database Interactions.    |
|Serializers | Converts Python <-> JSON for HTTP.                   |
|Tests   | Integration tests for development.                       |
|Views   | Instructions for the app to follow/how the logic is handled. |

## 3. What is the Pipfile?
The pipfile displays all the packages required by the program and the versions it uses.

## 4. Key packages in the Pipfile

| Package | What functionality does it provide and why? |
|---------|----------------------------------|
| django | Web Framework for Python. Handles app logic, models and views.  |
| djangorestframework | Extension for Django. Adds Serializers, ViewSets and Routers. |
| django-allauth | Additional package to handle user registration and authentication. |

## 5. What does `decorators.py` do?
A decorator can be used to more easily export a function for use elsewhere. By wrapping a function with a decorator and importing it elsewhere, the second function will rely on the decorator before running. For instance, an authentication decorator can run before allowing a user to make any sensitive changes. By wrapping the function in a decorator it can be exported out to several places without having to re-write the authentication function again and again.

## 6. What is a serializer, and what serializers are defined here?
A serializer acts as a translator. It can take a Python model and convert it into JSON format to be sent as a response and visa versa. This allows for commmunication between systems like Python and HTTP that would otherwise be incompatible. For this project the serializers relate to the user status, such as student, instructor and staff. As well as serializers for capstone projects, cohorts, and proposals. These ensure that the data types the user enters matches what is required.

## 7. Models and what they represent

| Model | Real-world thing it represents |
|-------|-------------------------------|
|cohort | Tracks each cohort of students including the name of the cohort, the start and end date, whether or not it is active, and the associated slack channel. The API needs to track this data so that students and instructors can manage their class. Presumably their active/inactive portion is for record-keeping purposes later after the class has concluded.  |

## 8. Views vs. viewsets

| Type | Example class | File path |

|------|--------------|-----------|
| View | |Utils/Slack API - This view handles operations for creating and managing student teams, slack channels and student permissions. The functions for these operations are all self-contained within this file. | LearningAPI/views/utils.py

| ViewSet | Book - This viewset handles the CRUD operations related to Books. It handles GET, POST, PUT and DELETE requests in its own functions. These functions operate as part of Rest API and not Django itself. | views/book_view.py |

## 9. Serializers paired with their models

| Serializer | Model | Link |
|---------------|--------------|------|
|Capstone       |Capstone      |Handles all fields related to student capstone projects.|
|Cohort         |Cohort        |Checks if the user is an instructor and allowing editing of student cohort groups.|
|Nssuser_Cohort |NssUserCohort |Allows the assigning of students to specific cohorts.|
|Nssuser        |NssUser       |Handles student information such as assignments, scores, slack id and courses.|
|Proposal_Status|ProposalStatus|Handles student capstone proposals.|
|User           |User          |Handles Authentication and User information such as Name and Email.|

## 10. What replaces the Templates and why?

For this project, the Template is replaced by the Frontend React app. The API sends JSON data to the App which React is able to understand and build into the interface that the user sees. By letting React handle the rendering, all the API needs to do is handle requests and data. It does not need to work with HTML.

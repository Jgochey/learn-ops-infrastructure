# Debug Notes: The Invisible Save - AI

## 1. The Bug

In LearningAPI/views/student_view.py we are trying to fix the update method. When updating a student's slack handle the change appears to be successful but it is not actually saved and disappears after refreshing the page. Explain what the 'update' method is doing and why the change is not being made to the database.

Which specific line is missing the .save() function? What is the Django concept that explains why the save is not made to the database without it?


## 2. The Test

In LearningAPI/tests/test_student_update.py we should have a test that verifies updating a student's slack handle actually persists the change to the database. The test is there already but it is incomplete. The test should:
1. Make a PUT request to students/pk with a new slack_handle value.
2. Assert the response is 204.
3. Fetch the NssUser from the database.
4. Assert that the slack_handle of the fetched NssUser matches the new value.

## 3. The Fix

There is a single line of code that should fix the saving problem. What is it and on what line should it be added?

## 5. What the test verifies

# Debug Notes: The Invisible Save

## 1. Bug report summary

When trying to update their slack handle, the change did not save to the database. Although the form appeared to submit successfully, the slack handle value in the database remained unchanged.

## 2. What the debugger showed

| Question | Answer |
|----------|--------|
| Value of `student.slack_handle` before line 87 | None |
| Value of `student.slack_handle` after line 88 | NewValue |
| Next line executed after line 90 | student.save() |

## 3. Root cause

After making the update request to change the slack handle, the request was never saved to the database because student.save() was not called. With Django, in order to save the change to the database, you must explicitly call the save() method after making changes.

## 4. The fix

I added the line student.save() immediately after updating the student.slack_handle attribute to ensure the change was proplerly saved to the database.

## 5. What the test verifies

The test creates a student object and attempts to update its slack handle. It then verifies that the change was saved properly to the database by re-retrieving the newly created slack handle value back from the database and checking that it matches the value that is expected.

# Bug Fix: Student Assessment Retrieval

## The Bug
[Describe what caused the `'NoneType' object has no attribute 'user'` error.
Which variable was None? Under what condition?]

Instructor Id. When the call was made to add a student assessment, the instructor associated with the assessment was the None value.

## Log Statements Added
[Paste the log statements you added, including the log level and message for each.]

log.info("StudentAssessmentView student_assessment %s", student_assessment )

## What the Logs Revealed
[What did you observe in the logs that pointed you to the root cause?]

The log for the Student Assessment returned an error indicating that the instructor username was the problem:  "^\n File "/app/LearningAPI/views/student_assessment.py", line 188, in get_instructor_username\n return obj.instructor.user.username\n ^^^^^^^^^^^^^^^^^^^\nAttributeError: \'NoneType\' object has no attribute \'user\''"

## The Fix
[Describe the change you made to resolve the bug. One or two sentences is fine.]

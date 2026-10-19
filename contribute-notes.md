# Conventions Notes

## JavaScript Linter (learn-ops-client)

### Configuration
Extends: "react-app"
Plugins active:  'import', 'flowtype', 'jsx-a11y', 'react-hooks', '@typescript-eslint'


### Rules I found (at least 5)
'no-unused-labels': 'warn', -- Checks for labels that are not used anywhere in the code.

'no-whitespace-before-property': 'warn', -- Disallows whitespace before the name of a property.

'no-useless-constructor': 'warn', -- Flags class constructors that can be safely removed.

'no-delete-var': 'warn', -- Disallows deletion of variables.

'import/first': 'error', -- Ensures all imports appear before other statements.

### First warning fixed
File and line: /learn-ops-client/src/components/LearnOps.js Line 8 import { CohortProvider } from "./cohorts/CohortProvider" --- warning  'CohortProvider' is defined but never used
Rule: no-unused-vars 
Fix: Fixed by removing the unused 'CohortProvider' import.

## Python Linter (learn-ops-api)

### Configuration
Disabled rules and what each would flag:
  broad-except -- Disables warnings for exceptions without specific exception types.
  arguments-differ -- Disables warnings when the number of arguments in a method differs from what is expected.
  missing-function-docstring -- Disables warnings for when a function has no docstring.
Good names allowed: i,j,ex,pk are "good names" which means the linter will ignore variables with these names.
What pylint-django likely adds: pylint-django adds checks that are specifcally for Django projects.

### First warning fixed
File and line: LearningAPI/signals.py:1:0:
Rule and category: Missing module docstring (missing-module-docstring)
Fix: Fixed by adding a docstring to the top of the document.

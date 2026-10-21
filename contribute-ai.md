## Step 3: /conventions learn-ops-client.
1. ESLint (via react-scripts / eslint-config-react-app)

- Command: npx eslint ~/workspace/lms/learn-ops-client/src --ext .js,.jsx,.ts,.tsx
  - --ext is needed because ESLint 8 only lints .js by default.
  - Caveat: the node_modules/.bin shims are unreadable from thisoutput error). Run the command from WSL, or use node~/workspace/lms/learn-ops-client/node_modules/eslint/bin/eslint.js <path> directly.
  - I did not execute the linter, so I have no run output.
- Config location: package.json, key eslintConfig, which only contains "extends": "react-app". The actual rules live in node_modules/eslint-config-react-app/ (index.js, base.js).
- Rule categories and examples (all set to warn unless noted):
  - Possible bugs / correctness:
    - no-undef (error): undefined variables.
    - no-unused-vars, with args: 'none'.
    - no-unreachable, no-dupe-keys, use-isnan, valid-typeof, no-
    - no-restricted-globals (error): blocks confusing browser globals such as name or status unless written as window.name.
  - Best practices / style:
    - eqeqeq: ['warn', 'smart']: prefers === but allows x == null.
    - default-case: switch statements need a default, or a // no default comment.
    - no-eval, no-new-func, no-script-url, no-caller.
    - no-mixed-operators: flags a && b || c without parentheses.
    - no-loop-func, array-callback-return, getter-return.
  - React:
    - react-hooks/rules-of-hooks (error): hooks called conditionally.
    - react-hooks/exhaustive-deps (warn): missing useEffect depe
    - react/jsx-no-target-blank: target="_blank" without rel="noreferrer".
    - react/jsx-no-undef (error), react/no-direct-mutation-statere-render-return.
  - Accessibility (jsx-a11y):
    - alt-text: <img> needs alt.
    - anchor-is-valid: flags href="#" or missing href.
    - heading-has-content, iframe-has-title, role-has-required-aria-props.
  - Imports:
    - import/first (error): imports must come before other statements.
    - import/no-amd, import/no-webpack-loader-syntax (error).
    - import/no-anonymous-default-export (warn).
- Explicitly disabled rules:
  - react/no-deprecated is commented out with a note that it cauhave flagged deprecated React APIs and lifecycle methods such ascomponentWillMount, findDOMNode, and similar.
  - no-use-before-define is relaxed: functions, classes, and varg a name before its declaration is allowed.
- Rules explicitly permitted or relaxed:
  - Unused function arguments are ignored (args: 'none').
  - Unused destructured siblings are ignored (ignoreRestSiblings: true), so const { a, ...rest } = obj doesn't flag a.
  - eqeqeq allows == null.
  - no-unused-expressions allows short-circuit and ternary expressions such as cond && fn().
  - default-case accepts // no default as an explicit opt-out.
  - jsx-a11y/aria-role ignores non-DOM components.
  - no-restricted-globals is the only hard error group for globals.
- Overrides:
  - For *.ts/*.tsx, the parser switches to @typescript-eslint/parser. Several base rules are turned off in favour of their TypeScript equivalents (no-unused-vars, no-redeclare, no-use-before-define, and others).
  - The jest.js preset (eslint-config-react-app/jest) is not reft is not active in this project's lint run.
- Plugins and extensions:
  - Plugins: react, import, flowtype, jsx-a11y, react-hooks.
  - Conditional: @typescript-eslint for TypeScript files.
  - Parser: @babel/eslint-parser with babel-preset-react-app/pro
  - @rushstack/eslint-patch/modern-module-resolution is loaded for module resolution.

Related setup that affects linting

- package.json build script: CI=false && react-scripts build. Under CRA, warnings only fail a build when CI=true, so this line is intended to keep warnings non-fatal. Note that CI=false && in the shell does not export a variable, so a CI environment that sets CI=true would still treat warnings as errors. That is worth checking before relying on the build for lint enforcement.                                                                                             .github/workflows/main.yml: runs npm run build:production on peslint as a separate step.
- .vscode/settings.json: sets "javascript.validate.enable": false, which turns off VS Code's built-in JavaScript validation. ESLint warnings may still show in the editor if the ESLint extension is installed.
- Other linters: none found. No Prettier, Stylelint, or Biome config exists.



## Step 4: /conventions learn-ops-api
1. Pylint

- Command: pipenv run pylint --rcfile ~/workspace/lms/learn-ops-api/.pylintrc ~/workspace/lms/learn-ops-api/LearningAPI
  - Pylint auto-discovers .pylintrc from the current directory, so --rcfile is only needed when running from elsewhere.
  - Run it from inside the venv or via pipenv run. The Windows-side python3 is a Store alias, so I could not verify Pylint is installed in an environment I can reach, and I did not run it.
  - Scope it to LearningAPI (the app) rather than the whole repo. LearningPlatform, LogViewer, and config are smaller and may be worth including too.
- Config location: .pylintrc (project root). Its full contents are:
[FORMAT]
good-names=i,j,ex,pk

[MESSAGES CONTROL]
disable=broad-except,arguments-differ,missing-function-docstring
- Rule categories and examples: no enable= list is set, so Pylint's full default set applies. The default categories are:
  - C (convention): e.g. C0114 missing-module-docstring, C0103 invalid-name (short names like x are flagged), C0301 line-too-long.
  - R (refactor): e.g. R0903 too-few-public-methods (common for Django models and small helpers), R0911 too-many-return-statements, R0913 too-many-arguments.
  - W (warning): e.g. W0612 unused-variable, W0613 unused-argument, W0718 broad-exception-caught (older name broad-except).
  - E (error): e.g. E1101 no-member, E0307 invalid-str-returned, E0401 import-error.
  - F (fatal): parse errors that stop analysis.
- Explicitly disabled rules and what they would flag:
  - broad-except: except Exception: and bare except: clauses. Note that current Pylint names this broad-exception-caught (W0718), and the old name still works in disable=, so this line is still effective. Because the disable is global, these clauses are not flagged anywhere in the project.
  - arguments-differ: an overriding method whose signature differs from the parent, such as a DRF get_queryset(self) or a save(self, *args, **kwargs) override with different parameters.
  - missing-function-docstring (C0116): any function or method without a docstring.
- Rules permitted or ignored by the config:
  - good-names=i,j,ex,pk allows those four names to pass invalid-name. ex is typical for except ... as ex, and pk is the Django primary key convention. Other short names are still flagged.
  - The three disabled rules above are global exceptions, not per-file ones.
- Inline suppressions in source (these are rule exceptions in the code itself): there are 9 # pylint: disable= comments, and all target false positives from Django's dynamic attributes or __str__ signatures:
  - E1101 no-member on team.students.all() in LearningAPI/views/team_maker_view.py:118, and on self.book.id / self.book.course.* in LearningAPI/models/people/assessment.py:24-32, and capstone.student.user.* in LearningAPI/models/coursework/capstone_timeline.py:12. Django's reverse relations and FK attributes are invisible to plain Pylint.
  - E0307 invalid-str-returned on __str__ in LearningAPI/models/coursework/foundation_exercise.py:18, course.py:10, and assessment.py:35. These __str__ methods are typed or inferred in a way that trips the check.
- Plugins and extensions:
  - pylint-django is listed in Pipfile [packages] and locked in Pipfile.lock, but it is not active. The .pylintrc has no load-plugins=pylint_django line, and no --load-plugins flag appears anywhere in the repo. This is likely why the inline E1101 disables are needed.
  - To include it for one run, you could try pipenv run pylint --load-plugins=pylint_django --rcfile ... LearningAPI, with DJANGO_SETTINGS_MODULE=LearningPlatform.settings set. This is a suggestion I have not tested. If it works, it may let you remove some of the inline disables.
  - The only editor extension recommendation, in .vscode/extensions.json, is irongeek.vscode-env. It is not a linter.

Related setup that affects linting

- .github/workflows/main.yml, collectstatic.yml, seed.yml: none of them run Pylint or any other lint step. Linting is not enforced in CI.
- pytest.ini: uses --strict-markers and DJANGO_SETTINGS_MODULE = LearningPlatform.test_settings. This is test configuration, not linting.
- .vscode/launch.json: only a remote-debug attach configuration for Docker on port 5678.
- Other linters or formatters: none found. autopep8 is in Pipfile [packages], so it is available as a formatter, but it has no config file in the repo.


## Take Two
1. ESLint (via react-scripts 5.0.1 / eslint-config-react-app 7.0.1)

- Command (WSL):
  npx eslint /home/jgochey/workspace/lms/learn-ops-client/src --ext .js,.jsx,.ts,.tsx
  - --ext is required because ESLint 8 lints only .js by default.
  - Run it from inside WSL. The node_modules/.bin shims are unreadable from the Windows-mounted view.
  - I have not run this, so I have no output to report.
- Command (Docker, untested): the project has no docker-compose.yml or compose file, so docker compose does not apply. The Dockerfile builds from node:22.13.0, copies the project to /app, runs npm install, and starts the dint run in the same image would be:
  docker run --rm -v /home/jgochey/workspace/lms/learn-ops-client:/app -w /app node:22.13.0 npx eslint src --ext .js,.jsx,.ts,.tsx
  - This mounts the WSL project directory. The node_modules there was installed on WSL, which should be compatible with the Linux container.
- Other ways ESLint runs: the dev server (npm start) and production build (npm run build) run ESLint through CRA's webpack plugin. Warnings show in the build or dev-server output. They do not appear in a separate lint st
- Config location:
  - /home/jgochey/workspace/lms/learn-ops-client/package.json, k "extends": "react-app".
  - The actual rules are in /home/jgochey/workspace/lms/learn-ops-client/node_modules/eslint-config-react-app/index.js and base.js.
- Rule categories and examples (all warn unless noted):
  - Possible bugs:
    - no-undef (error): undeclared variables.
    - no-unused-vars, with args: 'none'.
    - no-unreachable, no-dupe-keys, no-dupe-args, use-isnan, valid-typeof.
    - no-fallthrough: missing break in switch cases.
    - no-restricted-globals (error): bare name or status, which
  - Best practices:
    - eqeqeq: smart: flags == except == null.
    - default-case: switch without a default, unless it has a // no default comment.
    - no-eval, no-new-func, no-script-url, no-caller.
    - no-mixed-operators: a && b || c without parentheses.
    - no-loop-func, array-callback-return, getter-return.
  - React:
    - react-hooks/rules-of-hooks (error): hooks called conditionally.
    - react-hooks/exhaustive-deps: missing useEffect dependencies.
    - react/jsx-no-target-blank: target="_blank" without rel="no
    - react/jsx-no-undef (error), react/no-direct-mutation-state, react/no-typos (error), react/require-render-return (error).
  - Accessibility (jsx-a11y):
    - alt-text: <img> without alt.
    - anchor-is-valid: href="#" or a missing href.
    - heading-has-content, iframe-has-title, role-has-required-aria-props.
  - Imports (import):
    - import/first (error): imports after other statements.
    - import/no-amd, import/no-webpack-loader-syntax (error).
    - import/no-anonymous-default-export: export default {...} with no name.
- Explicitly disabled rules. Each one, with what it would flag:
  a. react/no-deprecated: commented out in base.js, with the note "Disabled because of undesirable warnings". If enabled, it would flag deprecated React lifecycle methods such as componentWillMount and componentWillReceiveProps, and deprecated APIs such as findDOMNode, depending on the detected React
     version.
efine functions, classes, variables: set to false in base.js and in the TypeScript override. This relaxes the rule, so the following would NOT be flagged:
     - calling a function declared later in the file (functions),
     - using a class before its declaration (classes),
     - using a var/let/const before its declaration (variables).
  c. TypeScript override (**/*.ts?(x) files), each base rule set
     - default-case: switch without default. TypeScript's noFallthroughCasesInSwitch covers this instead.
     - no-dupe-class-members: duplicate method names in a class.
     - no-undef: undeclared identifiers. tsc covers this.
     - no-array-constructor: new Array(1, 2) or Array(3). Replaced by @typescript-eslint/no-array-constructor (warn).
     - no-redeclare: duplicate declarations such as var x; var xlint/no-redeclare (warn).
     - no-use-before-define: the base rule is off. It is replaced by @typescript-eslint/no-use-before-define (warn), which also relaxes functions, classes, variables, and typedefs.
     - no-unused-expressions: a bare expression like a.b; with nript-eslint/no-unused-expressions (error), which allows
he base rule is off. It is replaced by @typescript-eslint/no-use-before-define (warn), which also relaxes functions, classes, variables, and typedefs.
     - no-unused-expressions: a bare expression like a.b; with no effect. Replaced by @typescript-eslint/no-unused-expressions (error), which allows
       short-circuit, ternary, and tagged-template expressions.
     - no-unused-vars: unused variables. Replaced by @typescript-eslint/no-unused-vars (warn), with args: 'none' and ignoreRestSiblings: true.
     - no-useless-constructor: constructor(a) { super(a); }. Repo-useless-constructor (warn).
  - The jest.js preset is not referenced by eslintConfig, so its rules are not active. Those rules would have been jest/no-conditional-expect,
    jest/valid-expect, and similar test checks.
- Rules and exceptions that are ignored or permitted:
  - no-unused-vars and @typescript-eslint/no-unused-vars use args: 'none', so unused function arguments are allowed.
  - ignoreRestSiblings: true: const { id, ...rest } = obj does n
cond && fn(), cond ? a() : b(), and tagged templates.
  - default-case accepts // no default as an explicit opt-out.
  - no-use-before-define (see above) permits forward references to functions, classes, and variables.
  - jsx-a11y/aria-role ignores non-DOM components (ignoreNonDOM:
  - jsx-a11y/anchor-is-valid is set to check only noHref and invalidHref aspects.
  - react/forbid-foreign-prop-types allows foreign prop types inpes: true).
  - no-restricted-globals is the only group that is a hard error for globals.
  - In package.json, the build script sets CI=false, so warningsote below).
- Plugins and extensions active:
  - Core plugins: react (version 7.32.2), import (2.27.5), flowthooks (4.6.0).
  - TypeScript: @typescript-eslint/parser (5.59.8) and the @typescript-eslint plugin, used only on *.ts/*.tsx files.
  - Parser: @babel/eslint-parser (7.21.8) with babel-preset-react-app/prod.
  - @rushstack/eslint-patch/modern-module-resolution is loaded f
ibrary plugins from jest.js.

Related setup that affects linting

- build script: CI=false && react-scripts build. The CI=false assignment in the shell does not export the variable. A CI environment that sets CI=true, such as GitHub Actions, would therefore turn warnings into build errorparent intent of keeping warnings non-fatal.
- .github/workflows/main.yml: runs npm run build:production on push to main. It has no separate lint step.
- .vscode/settings.json: "javascript.validate.enable": false turaScript validation. The ESLint extension can still show warnings.
- Other linters or formatters: none. No Prettier, Stylelint, or Biome config exists.



1. Pylint

- Command (WSL):
  pipenv run pylint --rcfile /home/jgochey/workspace/lms/learn-ops-api/.pylintrc /home/jgochey/workspace/lms/learn-ops-api/LearningAPI
  - Run it from inside the venv or via pipenv run. The Windows-side python3 is a Store alias, so Pylint may not be installed in an environment I can reach. I have not run it.
  - Scope to LearningAPI, the app package. LearningPlatform, LogViewer, and config are smaller and can be linted too.
- Command (Docker, untested): there is no docker-compose.yml or se does not apply. The Dockerfile installs dev dependencies withpipenv install --system --dev, so Pylint is available in the image. Build and lint like this:
  docker build -t learn-ops-api /home/jgochey/workspace/lms/learn-ops-api
  docker run --rm -v /home/jgochey/workspace/lms/learn-ops-api:/app -w /app learn-ops-api pylint --rcfile .pylintrc LearningAPI
  - The image's ENTRYPOINT is /entrypoint.sh, which runs migratiing a command as above would go through that entrypoint, so checkentrypoint.sh before relying on this. Overriding it with --entrypoint pylint is the cleaner option.
  - The entrypoint.sh file does not call Pylint.
- Config location: /home/jgochey/workspace/lms/learn-ops-api/.pylintrc. Its full contents:
[FORMAT]
good-names=i,j,ex,pk

[MESSAGES CONTROL]
disable=broad-except,arguments-differ,missing-function-docstring
  No pyproject.toml, setup.cfg, or [pylint] section exists elsewhere.
- Rule categories and examples: no enable= list is set, so Pylint's full default set applies except the three disabled rules below.                       (convention): C0114 missing-module-docstring, C0103 invalie flagged), C0301 line-too-long.
  - R (refactor): R0903 too-few-public-methods (common for Django models and small helpers), R0911 too-many-return-statements, R0913 too-many-arguments.  (warning): W0612 unused-variable, W0613 unused-argument, W
  - E (error): E1101 no-member, E0307 invalid-str-returned, E0401 import-error.                                                                           (fatal): parse errors that stop analysis.
- Explicitly disabled rules. Each one, with what it would flag:
  a. broad-except (current name broad-exception-caught, W0718). This would flag except Exception:, except BaseException:, and bare except:. Pylint still accepts the old name, so this line remains effective.
ag an overriding method whose parameters differ from the parent's, such as a DRF get_queryset(self) override that takes extra arguments, or a save(self, *args, **kwargs) override with a different signature.
  c. missing-function-docstring (C0116). This would flag any function or method without a docstring.
- Rules and exceptions explicitly permitted or ignored:
  - good-names=i,j,ex,pk allows those four names to pass invalidfor except ... as ex, and pk is the Django primary-key convention. Other short names are still flagged.
  - The config sets no ignore= or ignore-paths= list, so every .h is linted, including migrations/ if you scan the whole app.
  - Inline suppressions in source are another form of exception. There are 9 # pylint: disable= comments:
    - E1101 no-member at LearningAPI/views/team_maker_view.py:11assessment.py:24, 31, 32, andLearningAPI/models/coursework/capstone_timeline.py:12. These cover Django's reverse relations and foreign-key attributes, which Pylint cannot see without the Django plugin.
    - E0307 invalid-str-returned at LearningAPI/models/courseworLearningAPI/models/coursework/course.py:10, andLearningAPI/models/people/assessment.py:35, on __str__ methods.
- Plugins and extensions:
  - pylint-django 2.8.0 is pinned in Pipfile.lock, and Pipfile lists it under [packages]. It is not active. .pylintrc has no load-plugins=pylint_django line,
    and no --load-plugins flag appears anywhere in the repo. Thiine disables exist.
  - To test the plugin for one run, try pipenv run pylint --load-plugins=pylint_django --rcfile /home/jgochey/workspace/lms/learn-ops-api/.pylintrc
    /home/jgochey/workspace/lms/learn-ops-api/LearningAPI, with ngPlatform.settings set. This is untested. If it works, someinline disables may become unnecessary.
  - The only editor extension recommendation, in .vscode/extensienv. It is not a linter.

Related setup that affects linting

- CI: .github/workflows/main.yml, collectstatic.yml, and seed.yml do not run Pylint or any other lint step.
- pytest.ini: sets DJANGO_SETTINGS_MODULE = LearningPlatform.test_settings and --strict-markers. This is test configuration, not linting.
- .vscode/launch.json: only a remote-debug attach configuration
- Formatters: autopep8 is in Pipfile [packages], but it has no config file in the repo, so it is not configured.


## Step 6: Comparison

The AI was able to match the active Plugins, including the specific typescript plugin conditional. The prompt seemed to struggle with the specific run command for docker. When I specified that it should look for the exact run commands on my revision, I believe it became confused and tried to find docker commands that were not actually present in the repo.

# learn-ops-api: AI Prompts

## 1. Project structure
Inside of the learn-ops-api folder map the directories of this project including the directories at the root of the folder and directories inside of the LearningAPI folder. For each one explain the purpose it serves to this project. Write the answers in a markdown list format like so:
| Folder | Why does this folder need to exist? |
|--------|-------------------------------------|
|        |                                     |

Save this response to the "## 1. Top-level folders in `learn-ops-api`" section of module-exploration-ai.md in the learn-ops-infrastructure directory. @learn-ops-infrastructure/module-exploration-ai.md



## 2. Dependencies
Look at the @learn-ops-api/Pipfile . Explain what it is and what purpose it serves for this project.

For django, djangorestframework and django-allauth; explain what do they each provide and in what ways is the project dependant on them?

Save this response and the previous one for the pipfile to @learn-ops-infrastructure/module-exploration-ai.md


## 3. Decorators, serializers, and models
Open @learn-ops-api/LearningAPI/decorators.py and explain Decorators; what are they and how are they used in this file?  Save your response to @learn-ops-infrastructure/module-exploration-ai.md  under the "## 5. What does `decorators.py` do?" section.

Look at @learn-ops-api/LearningAPI/serializers/ and explain the purpose of Serializers. How do they operate in the request-response cycle? Save your response to the "## 6. What is a serializer, and how does it fit the request/response cycle?" section.

Open @learn-ops-api/LearningAPI/models and look inside the coursework, people and skill folders and read the model files. Explain what a Django model is, then pick one model from this project and explain why the API needs to track it and the real-world thing that model represents. Save your responses to the "## 7. One model and what it represent"  section of @learn-ops-infrastructure/module-exploration-ai.md


## 4. Views, viewsets, and the MTV pattern
Find one example of a view and one of a viewset in this project. What is the difference between a view and a viewset and in what situation would you choose one over the other? Write your response to the ## 8. Views vs. viewsets section of @learn-ops-infrastructure/module-exploration-ai.md

Django uses a Model-Template-View pattern, however this project does not use HTML templates. What is it in this project that takes the role of the template? Why does using this approach make sense for a REST API? Write your response to the "## 9. What replaces templates and why?" section of @learn-ops-infrastructure/module-exploration-ai.md

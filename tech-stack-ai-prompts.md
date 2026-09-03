# Tech Stack AI Prompts

## 1. Run Questions
### 1a. Config Files
 I am looking for the system config files for this project. For each project folder: learn-ops-api, learn-ops-client and learn-ops-infastructure, find the relevant config files for each. Inside the learn-ops-infastructure directory write to tech-stack-ai.md a markdown table with columns containing: the names of the config files, their location, Config Values, What the Config Value is for, and how the value is used. Be sure to replace any literal Config Values with the config variable name and add the missing file [filename].


### 1b. How to Start It
Look at the Makefile in learn-ops-infrastructure and write to tech-stack-ai.md how to start the system. Explain which targets are used for starting the system and how they differ.



### 1c. Where to Access It
Create another markdown column for the service access ports. Find which services are being used and List: the Service name, Port and URL for each.



### 1d. Service Dependencies
What dependencies are there for each service? Create another markdown column and list: the name of each service, what dependencies it has, and why it is dependant on them.

What exactly is Valkey Monitor and Cache? What is it doing for this project? How does it relate to the other service dependencies?

No, to be clear, Valkey is a performance optimizer for the API layer but is not strictly required for the project to run?

Nevermind that, just leave the Valkey Cache and Monitor sections at the end of Service Dependencies.
### 1e. Main Entry Points
For each of the services utilized by the project, find the startup files, routes and URL config files. Add them to a markdown table with columns for Service, Startup File and Routes / URL Config File. Be sure to list the files where the service starts and where the routes are handled, these are two different files.


## 2. Services
Look through the project and find the Tech Stack for each of the project's services. Add another markdown table for the services. The table should included columns for Service Name, Tech Stack (including version) and Purpose (of that service).


## 3. System Overview
Write a system overview to tech-stack-ai.md. There should be 3 paragraphs. The first paragraph should describe what the application is and what problem it solves. The second paragraph should describe what the main features of the application are for a user. The third paragraph should describe who will use the application, if there are different roles and if so, what are the differences in how the roles can interact with the system?

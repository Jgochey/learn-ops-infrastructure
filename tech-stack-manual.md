# Tech Stack Manual

## 1. Run Questions

### 1a. Config Files

| Config File | Location | Config Value | What it's for | How it's used |

|.env | ~lms\learn-ops-api\.env  | LEARN_OPS_DB |The Database info in the .env files contains the connection info for the database | secret values are assigned to varibles to keep them hidden when pushing to repository |
|.env | ~lms\learn-ops-api\.env  | DEVELOPMENT_MODE | Setting the development mode on or off | When set to True or False the development mode will be enabled (as opposed to test or production.) |
|.env | ~lms\learn-ops-api\.env  | LEARNING_GITHUB_CALLBACK |Connection info for authenticating the user with Github | When the user connects and runs the application, their account is verified through github |

|docker-compose.yml|~lms\learn-ops-infrastructure\docker-compose.yml | Database | Docker image for the database, containing values such as the Ports to connect to | Docker will create this image and will run the healthcheck at the interval specified omby the user |
|docker-compose.yml|~lms\learn-ops-infrastructure\docker-compose.yml | Api | Application for the learning platform to use | Handles account creation and authorization for the User
|docker-compose.yml|~lms\learn-ops-infrastructure\docker-compose.yml | Client | Front-end interface component for the application | Display information given for the user, either on a front-end service or locally. |

|Makefile | ~lms\learn-ops-infrastructure\Makefile | setup |Initial creation | Only nessescary for the first initial use, the project files are established in the current directory |
|Makefile | ~lms\learn-ops-infrastructure\Makefile | pull | Gathers the latest project files from the repository | When the repository has recieved an update the user can retrieve the changes and copy them to their local environment |
|Makefile | ~lms\learn-ops-infrastructure\Makefile | up | Starting the Service | Initializes the project locally, can be the client, api, database or all of the above. |

### 1b. How to Start It

up: pull
	docker compose up --build -d
	docker compose logs -f

up-api:
	docker compose up --build -d api
	docker compose logs -f api

up-client-api:
	docker compose up --build -d api client
	docker compose logs -f api client

Runs each component of the project, in part or together.

### 1c. Where to Access It

| Service | Port | URL |

|client | 3000:3000 | localhost:3000 |
|api | 8000:8000  5678:5768 | http://localhost:8000/ http://localhost:5678/ |
|database | 5433:5432 | http://localhost:5433 |

### 1d. Service Dependencies

| Service | Depends On | Why |

|client | api | The client needs the api in order to properly display information |
|api | database | If the database is not working there will be nothing for the api to retrieve |
|database | none | The database can keep running even if nothing ever accesses it |


### 1e. Main Entry Points

| Service | Startup File | Routes / URL Config File |

|Api | entrypoint.sh | postgres, socialaccount.json, superuser.json, manage.py|
|Client | learnops.js | Appears to be handled by a react service function |
|Database | setup.sh | Appears to be managed by the setup file, database information contained in the .env file are handled by github, while docker handles running the database via postgres.  |

---

## 2. Services

| Service Name | Tech Stack (including version) | Purpose |

|Client |
		node:22.13.0	
		"@radix-ui/colors": "1.0.1",
    "@radix-ui/react-dialog": "^1.0.5",
    "@radix-ui/react-dropdown-menu": "2.0.5",
    "@radix-ui/react-icons": "1.3.0",
    "@radix-ui/react-popover": "^1.0.6",
    "@radix-ui/themes": "^1.1.2",
    "@testing-library/jest-dom": "^4.2.4",
    "@testing-library/react": "^9.3.2",
    "@testing-library/user-event": "^7.1.2",
    "buffer": "^6.0.3",
    "chart.js": "^4.4.1",
    "chartjs-plugin-datalabels": "^2.2.0",
    "env-cmd": "^10.1.0",
    "install": "^0.13.0",
    "npm": "^8.19.3",
    "react": "^16.13.1",
    "react-chartjs-2": "^5.2.0",
    "react-dom": "^16.13.1",
    "react-router-dom": "^5.2.0",
    "react-scripts": "^5.0.1",
    "toaster-js": "^2.2.3"
		"autoprefixer": "^10.4.14",
    "postcss-cli": "^10.1.0",
    "tailwindcss": "^3.3.3" | Running and displaying the frontend client UI |
	|API| Python 3.11.11 | Retrieves information from the database and relays it to the client |
	|Database|postgres:16, prometheus, grafana| Stores information and distributes it when prompted by the API |

---

## 3. System Overview

A program for teachers and students, allowing them to more easily manage their work, Organize by cohort, and for teachers: setup student teams, create, manage and assign coursework.
A user takes the role of a Teacher or Student. From the Navbar Teachers can select Students, Teams, Cohorts, Courses and Foundations. In a typical session, they may wish to view the Students in each cohort, assign them to teams and review or create the coursework. For Students; they can see an Overview of their current assignments and upcoming events, view their capstone projects, core skills, and assignment calendar.
The app's functionality changes drastically depending on whether or not the user is a Student or Teacher. The Student view is focused on their own coursework while the Teacher's have substantially more options. From The Students tab on the navbar a Teacher can view each Cohort, the Students and their currently assigned projects and events. The Teams tab allows the User to divide the students into groups and assign them a project and slack channel. The cohorts page displays all the current cohorts and their relavent events and allows the creation of new ones. The courses page displays each learning course and can create, edit or delete current ones, clicking on a specific course will also display additional information about that course. The foundations tab searches a specific student's exercises.

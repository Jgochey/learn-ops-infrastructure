# Learn-Ops Learning Management System

## System Overview

The Learn-Ops platform is a comprehensive learning management and student tracking system designed for coding bootcamps and intensive educational programs. It solves the critical challenge of managing complex, multi-faceted student development across technical skills, soft skills, team dynamics, and project performance in a cohort-based learning environment. The system provides instructors and staff with centralized visibility into student progress, learning records, assessments, and team assignments, while enabling data-driven decisions about student support, curriculum adjustments, and career readiness evaluation.

The primary features of the Learn-Ops platform include: cohort and student management for organizing learners into learning groups; course and curriculum tracking to align content with learning objectives; assessment and learning record systems to measure student comprehension and skill acquisition; team formation and project assignment tools to facilitate collaborative learning; personality assessments and aptitude tracking to understand student learning styles; core skill development tracking to monitor foundational competency growth; GitHub integration for automatic repository and issue management; Slack integration for seamless communication with students; and comprehensive dashboards and reporting for instructors to monitor class-wide and individual student progress. The system also includes specialized features for capstone projects, foundations exercises, and opportunities (networking/career events).

The Learn-Ops platform serves three primary user roles, each with distinct interactions and permissions: **Instructors** are course designers and learning facilitators with full system access—they create curricula, design assessments, manage cohorts and teams, track all student learning records, and access comprehensive analytics dashboards; **Staff members** (program coordinators, mentors, support staff) have limited administrative access—they can view student progress, manage team assignments, submit feedback and notes, and access reporting for their assigned cohorts but cannot modify core curriculum or system settings; **Students** have the most restricted access focused on their own learning journey—they can view their assigned courses and projects, see their assessment results and learning records, view team assignments, access course materials and resources, and may have restricted visibility into their own progress metrics. Role-based access is determined by authentication status and user profile attributes (instructor flag, staff flag), with the system automatically routing users to appropriate views and limiting available actions based on their role.

## Configuration Files by Project

| Config File Name | Location | Config Values | What the Config Value is for | How the Value is Used |
|---|---|---|---|---|
| **learn-ops-api: .env** | `learn-ops-api/.env` | `LEARNING_GITHUB_CALLBACK` | GitHub OAuth callback URL | Redirects user after OAuth authentication |
| | | `LEARN_OPS_CLIENT_ID` | GitHub OAuth application ID | Authenticates with GitHub for OAuth flow |
| | | `LEARN_OPS_SECRET_KEY` | GitHub OAuth secret key | Secures OAuth token exchange |
| | | `LEARN_OPS_HOST` | Database hostname | Connection endpoint for PostgreSQL database |
| | | `LEARN_OPS_PORT` | Database port number | TCP port for database connection |
| | | `LEARN_OPS_DB` | Database name | Schema/database to connect to |
| | | `LEARN_OPS_USER` | Database username | Authentication credential for database |
| | | `LEARN_OPS_PASSWORD` | Database password | Authentication credential for database |
| | | `VALKEY_HOST` | Valkey/Redis hostname | Connection endpoint for cache service |
| | | `VALKEY_PORT` | Valkey port number | TCP port for cache connection |
| | | `VALKEY_DB` | Valkey database number | Database index in Valkey instance |
| | | `LEARN_OPS_DJANGO_SECRET_KEY` | Django secret key | Encrypts sensitive session data and tokens |
| | | `LEARN_OPS_ALLOWED_HOSTS` | Comma-separated hostnames | Restricts HTTP Host header for security |
| | | `LEARN_OPS_SUPERUSER_NAME` | Admin username | Administrative account for Django |
| | | `LEARN_OPS_SUPERUSER_PASSWORD` | Admin password | Authentication for admin account |
| | | `DEBUG` | Boolean debug flag | Enables detailed error pages in development |
| | | `DEVELOPMENT_MODE` | Boolean development flag | Enables development-specific features |
| | | `SLACK_BOT_TOKEN` | Slack bot token | Authenticates bot for Slack API calls |
| | | `GITHUB_TOKEN` | GitHub personal access token | Authenticates with GitHub API |
| | | `INSTRUCTOR_USERNAME` | GitHub username | Identifies instructor for course seeding |
| | | `INSTRUCTOR_COHORT` | Cohort number | Cohort ID for instructor association |
| | | `SLACK_TOKEN` | Slack workspace token | Authenticates with Slack workspace |
| **learn-ops-api: config/learn-ops-api.yaml** | `learn-ops-api/config/learn-ops-api.yaml` | `name` | Application identifier | Labels the deployment in Digital Ocean |
| | | `region` | Deployment region | Specifies geographic location for droplet |
| | | `instance_size_slug` | Instance size | Determines VM resources (CPU, RAM) |
| | | `instance_count` | Number of instances | Horizontal scaling configuration |
| | | `environment_slug` | Runtime environment | Specifies Python as runtime |
| | | `http_port` | Application port | Exposes application on this internal port |
| | | `github.repo` | GitHub repository URL | Source code location for deployment |
| | | `github.branch` | Git branch | Which branch to deploy from |
| | | `github.deploy_on_push` | Auto-deploy flag | Enables CI/CD on git push |
| | | `engines.version` | Database version | PostgreSQL 12 specification |
| | | `databases.size` | Database instance size | VM sizing for database |
| | | `alerts` | Alert rules | Triggers for deployment/domain failures |
| **learn-ops-api: pytest.ini** | `learn-ops-api/pytest.ini` | `DJANGO_SETTINGS_MODULE` | Django settings module | Points to test configuration |
| | | `python_files` | Test file patterns | Identifies which files contain tests |
| | | `python_classes` | Test class pattern | Identifies test class naming convention |
| | | `python_functions` | Test function pattern | Identifies test function naming convention |
| | | `testpaths` | Test directory path | Root directory for test discovery |
| | | `addopts` | pytest command options | Reuse DB, skip migrations, verbose output |
| | | `markers` | Custom test markers | Categorizes tests (unit, integration, slow) |
| **learn-ops-api: config/nginx.api.conf** | `learn-ops-api/config/nginx.api.conf` | `server_name` | Domain for API | Routes incoming requests to this domain |
| | | `proxy_pass` | Backend server address | Forwards requests to Django application |
| | | `access_log` | Log file path | Records HTTP request logs |
| | | `ssl_certificate` | SSL cert path | Encrypts HTTPS traffic |
| | | `ssl_certificate_key` | SSL key path | Private key for SSL certificate |
| | | `root` | Static files directory | Location of compiled frontend assets |
| | | `listen` | Port configuration | Listens on port 80 (HTTP) and 443 (HTTPS) |
| **learn-ops-client: .env** | `learn-ops-client/.env` | `REACT_APP_API_URI` | Backend API endpoint | URL used for API calls from React app |
| | | `REACT_APP_ENV` | Environment type | Determines feature flags and logging |
| | | `CHOKIDAR_USEPOLLING` | File watching method | Enables polling for file changes in Docker |
| | | `GENERATE_SOURCEMAP` | Sourcemap generation | Disabled to reduce build size |
| **learn-ops-client: package.json** | `learn-ops-client/package.json` | `engines.node` | Node.js version | Specifies runtime version for application |
| | | `dependencies` | npm packages | React, routing, UI components, charting libs |
| | | `scripts.start` | Start script | Launches development server |
| | | `scripts.build` | Build script | Creates production bundle |
| | | `scripts.test` | Test script | Runs Jest tests |
| **learn-ops-infrastructure: .env** | `learn-ops-infrastructure/.env` | `POSTGRES_DB` | Database name | PostgreSQL database name for containers |
| | | `POSTGRES_USER` | Database user | PostgreSQL user for container connection |
| | | `POSTGRES_PASSWORD` | Database password | PostgreSQL password for container connection |
| | | `DATA_SOURCE_NAME` | Connection string | Full DSN for database connections |
| **learn-ops-infrastructure: docker-compose.yml** | `learn-ops-infrastructure/docker-compose.yml` | `services.*.image` | Docker image name | Base container image to use |
| | | `services.*.container_name` | Container identifier | Name for running container instance |
| | | `services.*.env_file` | Environment file path | External .env file to load |
| | | `services.*.ports` | Port mappings | Maps host:container ports |
| | | `services.*.volumes` | Volume mounts | Mounts directories for persistence |
| | | `services.*.depends_on` | Service dependencies | Startup order and health checks |
| | | `services.*.networks` | Network configuration | Docker network for service communication |
| | | `volumes` | Named volumes | Persistent storage definitions |
| **learn-ops-infrastructure: prometheus.yml** | `learn-ops-infrastructure/prometheus.yml` | `global.scrape_interval` | Metrics collection interval | How often Prometheus scrapes metrics |
| | | `global.evaluation_interval` | Alert evaluation interval | How often alert rules are checked |
| | | `scrape_configs[].job_name` | Monitoring job name | Labels for metric collection jobs |
| | | `scrape_configs[].metrics_path` | Metrics endpoint | Path where service exposes metrics |
| | | `scrape_configs[].targets` | Scrape targets | Services to collect metrics from |
| **learn-ops-infrastructure: valkey/docker-compose.yml** | `learn-ops-infrastructure/valkey/docker-compose.yml` | `services.valkey.image` | Valkey Docker image | Valkey cache server container |
| | | `services.valkey.ports` | Port mapping | Maps host:container port for cache access |
| | | `services.valkey.volumes` | Data persistence | Volume for persistent cache data |
| | | `services.valkey.command` | Startup command | Configures save interval and logging |
| | | `services.valkey-monitor.entrypoint` | Monitor command | Runs valkey-cli for real-time monitoring |
| | | `networks.learningplatform` | Shared network | Enables service-to-service communication |

## Missing Configuration Files

The following configuration files may be needed but were not found in the project:

- [filename: `.env.production`] - Production environment variables for learn-ops-client
- [filename: `.env.development`] - Development environment variables for learn-ops-client
- [filename: `docker-compose.prod.yml`] - Production docker compose overrides
- [filename: `nginx.conf`] - Primary nginx configuration (found but not fully documented)
- [filename: `settings.py` or `settings/`] - Django settings module configuration
- [filename: `.github/workflows/deploy.yml`] - CI/CD deployment pipeline configuration
- [filename: `Dockerfile`] - Container build specifications for each service
- [filename: `requirements.txt` or `pyproject.toml`] - Python package dependencies
- [filename: `tsconfig.json`] - TypeScript configuration for React app
- [filename: `.eslintrc`] - ESLint rules and configuration
- [filename: `tailwind.config.js`] - Tailwind CSS configuration

## Starting the System

The system is started using Makefile targets in the `learn-ops-infrastructure` directory.

### Main Startup Targets

| Target | Command | What It Does | Use Case |
|---|---|---|---|
| `up` | `make up` | Pulls latest Docker images, builds all services, starts all containers (database, API, client, Prometheus, Grafana, postgres_exporter, Valkey), and displays logs | Full system startup; recommended for initial development setup |
| `up-api` | `make up-api` | Builds and starts only the API service, displays API logs | When you only need backend/API testing without the frontend |
| `up-client-api` | `make up-client-api` | Builds and starts API and client services, displays logs for both | Full-stack development when you need frontend + backend without monitoring |
| `restart` | `make restart` | Pulls latest images, stops all services, removes containers, rebuilds, and restarts all services with logs | When you need a clean restart or pulled images have been updated |

## Service Access Ports

| Service Name | Port | URL/Access |
|---|---|---|
| React Client | 3000 | http://localhost:3000 |
| Django API | 8000 | http://localhost:8000 |
| PostgreSQL Database | 5433 | localhost:5433 (TCP connection) |
| Valkey Cache | 6379 | localhost:6379 (TCP connection) |
| Prometheus | 9090 | http://localhost:9090 |
| Grafana | 3001 | http://localhost:3001 |
| PostgreSQL Exporter | 9187 | http://localhost:9187/metrics |

## Service Dependencies

| Service Name | Dependencies | Why It's Dependent |
|---|---|---|
| PostgreSQL Database | None | Foundation service - provides persistent data storage for the application |
| Django API | PostgreSQL Database (service_healthy) | Requires a running and healthy database to store and retrieve application data; will not start until database is ready |
| React Client | None (soft dependency on API) | Runs independently; makes HTTP calls to API at runtime but doesn't require it to start |
| Prometheus | Django API | Needs API service to be running to scrape metrics from the `/metrics/metrics` endpoint |
| Grafana | Prometheus | Requires Prometheus as its data source to retrieve and visualize metrics |
| PostgreSQL Exporter | PostgreSQL Database | Connects to database to export PostgreSQL metrics for Prometheus to scrape |
| Valkey Cache | None | Standalone cache service; provides caching layer used by API at runtime |
| Valkey Monitor | Valkey Cache | Depends on Valkey service to monitor its real-time operations and keyspace activity |

## Service Startup and Routes / URL Configuration Files

| Service | Startup File | Routes / URL Config File |
|---|---|---|
| Django API | `learn-ops-api/manage.py` | `learn-ops-api/LearningPlatform/urls.py` |
| React Client | `learn-ops-client/src/index.js` | `learn-ops-client/src/components/ApplicationViews.js` (primary routes), `learn-ops-client/src/components/LearnOps.js` (auth & role-based routing) |
| PostgreSQL Database | `learn-ops-infrastructure/docker-compose.yml` | N/A (database service) |
| Valkey Cache | `learn-ops-infrastructure/valkey/docker-compose.yml` | N/A (cache/message broker) |
| Prometheus | `learn-ops-infrastructure/docker-compose.yml` | `learn-ops-infrastructure/prometheus.yml` (metrics config) |
| Grafana | `learn-ops-infrastructure/docker-compose.yml` | N/A (dashboard UI) |
| PostgreSQL Exporter | `learn-ops-infrastructure/docker-compose.yml` | N/A (metrics exporter) |

## Service Technology Stack

| Service | Tech Stack (with Versions) | Purpose |
|---|---|---|
| Django API | Python 3.11.11, Django (latest), Django REST Framework, Gunicorn, psycopg2-binary, Valkey Python client, django-prometheus, django-structlog, OpenSearch 2.3.0 | RESTful API backend for learning platform; handles data management, authentication, course tracking, team assignments, and metrics collection |
| React Client | Node.js 22.13.0, React 16.13.1, React Router DOM 5.2.0, Chart.js 4.4.1, Radix UI, Tailwind CSS 3.3.3 | Frontend web application; provides user interface for instructors, staff, and students to access learning platform features |
| PostgreSQL Database | PostgreSQL 16 | Persistent relational data storage for all application data (users, cohorts, courses, assessments, records, etc.) |
| Valkey Cache | Valkey (latest) | In-memory data structure store for caching query results and pub/sub messaging for asynchronous operations |
| Prometheus | Prometheus (latest) | Time-series metrics collection and monitoring; scrapes metrics from Django API and PostgreSQL Exporter |
| Grafana | Grafana (latest) | Data visualization and dashboard platform; visualizes Prometheus metrics for system monitoring and observability |
| PostgreSQL Exporter | Prometheus PostgreSQL Exporter (latest) | Exports PostgreSQL database metrics in Prometheus format for monitoring database performance and health |

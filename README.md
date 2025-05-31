# ADEM-Connect
ADEM Connect
ADEM Connect is a modernized replacement for ADEM’s outdated “Chiffres-clés de l’ADEM” Tableau dashboard, delivering a secure, automated, and user-friendly platform for Luxembourg’s labour market data. Built with R, Shiny, PostgreSQL 17, and Docker, it automates monthly data updates, provides a mobile-first interface, and offers an optional REST API for external integrations. The project embeds Security by Design through authentication, authorization, code scanning, and dependency management, ensuring a robust and maintainable system.
Project Overview
ADEM Connect sources employment statistics (job seekers, vacancies, unemployment rates) from Open Data Luxembourg (2009–May 2025 or latest available). An R-based ETL pipeline processes data into a PostgreSQL 17 database, which powers a Shiny app with interactive visualizations and an optional Plumber API. The system is containerized with Docker, automated via GitHub Actions, and secured with authentication, role-based access, and continuous scanning.
Key Features:

Automated ETL: Monthly updates with SHA-256 checksum verification.
Shiny App: Mobile-first UI with filters (sector, date, age, gender) and plotly charts, including a 6-month job seeker forecast (ARIMA).
Optional API: Secure REST endpoints with bearer token authentication.
Security: Sodium-hashed passwords, role-based access, CodeQL/Trivy scanning.
Deployment: Dockerized for one-command setup (docker compose up).
CI/CD: GitHub Actions for ETL, code scanning, and dependency updates.

Prerequisites

Docker: For running the app and database.
R: For local development (optional, version 4.4.3+).
PostgreSQL: Version 17, included in Docker Compose.
GitHub Account: For CI/CD and dependency management.
Docker Hub Account: To pull/push the username/adem-connect image.

Setup Instructions

Clone the Repository:
git clone https://github.com/username/adem-connect.git
cd adem-connect


Configure Environment Variables:

Create a .Renviron file in the project root:DB_HOST=db
DB_PORT=5432
DB_NAME=adem_connect
DB_USER=admin
DB_PASSWORD=secure_password
API_TOKEN=your_api_token


Note: Do not commit .Renviron. It’s excluded via .gitignore.


Build and Run with Docker:
docker compose up --build


Access the Shiny app at http://localhost:3838.
PostgreSQL 17 is initialized automatically with the adem_connect schema.


Optional: Local R Development:

Install R and dependencies:Rscript -e "install.packages('renv'); renv::restore()"


Run the Shiny app:Rscript -e "shiny::runApp('R/app.R', port = 3838)"




GitHub Actions Setup:

Add secrets to your GitHub repository (Settings > Secrets and variables > Actions):
DB_HOST, DB_PORT, DB_NAME, DB_USER, DB_PASSWORD, API_TOKEN.


Workflows (push_pr_scan.yml, weekly_scan.yml, monthly_scan.yml, monthly_etl.yml) run automatically.



Usage

Shiny App:

Log in with credentials (default: admin/admin_pass, viewer/viewer_pass).
Admin: Access ETL logs and full dashboard.
Viewer: Access dashboard with filters (sector, date, age, gender, etc.).
Visuals include time series, bar charts, and a predictive job seeker trend (ARIMA).


API (Optional):

Access endpoints like GET /job_seekers?date=YYYY-MM-DD§or=X.
Include header: Authorization: Bearer $API_TOKEN.
Example:curl -H "Authorization: Bearer $API_TOKEN" http://localhost:3838/job_seekers?date=2025-05-01§or=Finance




ETL Pipeline:

Runs monthly via monthly_etl.yml.
Logs stored in etl_run_log table (accessible to admins).



Security Features

Authentication:
Shiny: shinyauthr with sodium-hashed passwords.
API: Bearer token authentication via .Renviron.


Authorization:
Role-based access: Admins view ETL logs; viewers access dashboard only.


Code Scanning:
Push/Pull: CodeQL for R vulnerabilities, TruffleHog for secrets (push_pr_scan.yml).
Weekly: CodeQL, Dependabot for dependencies (weekly_scan.yml).
Monthly: Trivy for Docker image vulnerabilities (monthly_scan.yml).


Data Integrity:
SHA-256 checksums for CSV files (etl_script.R).


Secrets Management:
Credentials and tokens in .Renviron and GitHub Secrets, never hard-coded.


Input Validation:
Sanitized inputs in Shiny app to prevent injection attacks.



Maintenance

Database:
Backup: docker exec -it <db_container> pg_dump -U admin adem_connect > backup.sql.
Restore: docker exec -i <db_container> psql -U admin adem_connect < backup.sql.


ETL:
Monitor etl_run_log for failures (e.g., checksum mismatches).
Update R/etl_script.R if data schema changes on the portal.


Dependencies:
Update renv.lock with renv::snapshot() after adding packages.
Dependabot alerts for outdated dependencies.


Docker:
Rebuild: docker build -t username/adem-connect:latest ..
Push: docker push username/adem-connect:latest.



Troubleshooting

App Fails to Load:
Verify docker-compose.yml environment variables.
Check PostgreSQL 17 container status (docker ps).


ETL Failure:
Review etl_run_log for error details.
Ensure data portal is accessible.


Security Alerts:
Check GitHub Actions logs for CodeQL/Trivy findings.
Update dependencies via renv::restore().



Directory Structure
adem-connect/
├── R/                    # R scripts for ETL, Shiny app, and API
│   ├── etl_script.R      # ETL pipeline
│   ├── app.R             # Shiny app
│   ├── api.R             # Plumber API (optional)
│   └── utils.R           # Shared functions
├── sql/                  # Database scripts
│   ├── schema.sql        # Table creation
│   └── init.sql          # Optional data seeding
├── tests/                # Unit tests
│   ├── test_etl.R        # ETL tests
│   └── test_api.R        # API tests
├── docs/                 # Documentation
│   ├── README.md         # This file
│   ├── data_cleaning.md  # Data transformation details
│   └── er_diagram.png    # Entity-Relationship Diagram
├── .github/workflows/    # GitHub Actions
│   ├── push_pr_scan.yml  # Push/PR code scanning
│   ├── weekly_scan.yml   # Weekly code/dependency scan
│   ├── monthly_scan.yml  # Monthly deep scan (Trivy)
│   └── monthly_etl.yml   # Monthly ETL
├── Dockerfile            # Docker configuration
├── docker-compose.yml    # Docker Compose setup
├── renv.lock             # R dependency lockfile
├── .Renviron             # Environment variables (not committed)
├── .gitignore            # Excludes sensitive files
└── LICENSE               # MIT License

Data Source

Source: Open Data Luxembourg
License: Open Data Luxembourg Licence
Coverage: 2009–May 2025 (or latest available)
Transformations: See docs/data_cleaning.md for cleaning steps (e.g., date parsing, NA handling).

License
This project is licensed under the MIT License (see LICENSE).
Contact
For issues or questions, contact syajiboye@yahoo.co.uk or open a GitHub issue.

ADEM Connect: Empowering Luxembourg’s labour market analysis with secure, automated, and accessible data insights.

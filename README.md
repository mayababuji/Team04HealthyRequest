# Team04 HealthyRequest API Automation

## About the Project

Team04 HealthyRequest is an API automation project for testing Admin, Dietician, and Patient workflows using Postman, Newman, and Jenkins.

The project validates authentication, role-based authorization, patient management, reports, vitals, file uploads, patient retrieval, morbidity operations, cleanup, and negative API scenarios.

## Technology Stack

- Postman
- Newman
- JavaScript
- CSV iteration data
- Swagger/OpenAPI
- Node.js and npm
- Jenkins
- GitHub
- Allure reporting
- Newman HTML Extra reporting

## Project Structure

```text
Team04HealthyRequest/
├── Team04HealthyRequest.postman_collection.json
├── Team04DieticianEnvironment.postman_environment.json
├── testDataDietician copy.csv
├── files/
│   ├── CBC-sample 1.pdf
│   ├── HyperThyroid_Report_final.pdf
│   ├── Hypo Thyroid-Report.pdf
│   └── Diabetic and Hemogram Test_Thyrocare lab.pdf
├── newman/
├── allure-results/
└── reports/
```

The `newman`, `allure-results`, and `reports` folders are created during execution.

## Prerequisites

Install Node.js and Newman:

```bash
npm install -g newman newman-reporter-allure newman-reporter-htmlextra
```

Verify the installation:

```bash
node --version
newman --version
```



## PDF File Uploads

Use relative paths in the Postman collection:

```text
files/CBC-sample 1.pdf
```


All PDF files must be available in the project `files` folder.

## Run Locally on macOS or Linux

Run the following command from the project root:

```bash
newman run "Team04HealthyRequest.postman_collection.json" \
  --environment "Team04DieticianEnvironment.postman_environment.json" \
  --iteration-data "testDataDietician copy.csv" \
  --working-dir "$(pwd)" \
  --reporters cli,allure,htmlextra \
  --reporter-allure-export "allure-results" \
  --reporter-htmlextra-export "newman/newman-report.html"
```

## Jenkins Shell Build Step

Use **Execute shell** in Jenkins and add:

```bash
#!/bin/bash

set -e

export PATH="/opt/homebrew/bin:/usr/local/bin:$PATH"

echo "Workspace: $(pwd)"
echo "Checking Node and Newman..."

node --version
newman --version

rm -rf newman allure-results reports
mkdir -p newman allure-results reports

test -f "Team04HealthyRequest.postman_collection.json"
test -f "Team04DieticianEnvironment.postman_environment.json"
test -f "testDataDietician copy.csv"
test -f "files/CBC-sample 1.pdf"

echo "Running Newman Collection..."

newman run "Team04HealthyRequest.postman_collection.json" \
  --environment "Team04DieticianEnvironment.postman_environment.json" \
  --iteration-data "testDataDietician copy.csv" \
  --working-dir "$(pwd)" \
  --reporters cli,allure,htmlextra \
  --reporter-allure-export "allure-results" \
  --reporter-htmlextra-export "newman/newman-report.html"

test -f "newman/newman-report.html"
cp "newman/newman-report.html" "reports/newman-report.html"

echo "Newman execution completed successfully."
```

The `--working-dir "$(pwd)"` option allows Newman to find files referenced with relative paths such as:

```text
files/CBC-sample 1.pdf
```



## Test Flow

1. Admin logs in.
2. Admin creates a Dietician.
3. Dietician logs in.
4. Dietician creates a Patient.
5. Generated `patientId` and `fileId` are saved.
6. Patient, report, file, morbidity, and retrieval requests are executed.
7. Authorization and validation scenarios are checked.
8. Test data is cleaned up.
9. Newman HTML and Allure reports are generated.





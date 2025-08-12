Run santander-group-shared-assets/gln-alm-sonar-manager-action@v1.16.0
Run santander-group-shared-assets/gln-cm-check-sec-and-qa-onboarding-action@v1
Initializing assurance info retriever: Retrieve inputs 
Loging to Management
Starting token generation process!
Authenticating in the Corporate Login
Getting BKS Token from sts
Getting JWT Token from sts
Token generated successfully!
Retrieving assurance info from Management
Getting application assurance information from Management
Check quality component assurance
Retrieving Quality Assurance response from its API
response.data: {"content":[{"id":174125,"component_id":"chl-dss-lowcodeportal","created_on":"2025-07-22T01:59:48.812Z","template":{"name":"React SPA","href":"/api/v1/catalog/templates/14/versions/2.4.0","version":"2.4.0"},"application":73,"name":"chl-lowcode-portal","short_name":"LOWCODEPORTAL","description":"chl-lowcode-portal","status":"ready","visible":true,"source_code_repository":{"url":"https://github.com/santander-group-chile-gln/chl-dss-lowcodeportal.git","status":"onboarded"},"quality_assurance":{"url":"https://gluon.gs.corp/sonarqube/dashboard?id=chl-dss-lowcodeportal","status":"onboarded"},"security_assurance":{"url":"https://ssc.santander.fortifyhosted.com/html/ssc/version/301613/overview","status":"onboarded"},"catalog":{"url":"https://santander.service-now.com/nav_to.do?uri=/u_cmdb_ci_sw_component.do?sys_id=260f74cb1bf6ea54dc2f6573604bcb3b","status":"onboarded"},"provenance":"greenfield","scope":"component"}],"pageable":{"pageNumber":0,"pageSize":20,"sort":{"empty":true,"unsorted":true,"sorted":false},"offset":0,"unpaged":false,"paged":true},"last":true,"totalPages":1,"totalElements":1,"sort":{"empty":true,"unsorted":true,"sorted":false},"size":20,"number":0,"first":true,"numberOfElements":1,"empty":false}
response.status: 200
Quality Assurance: {"url":"https://gluon.gs.corp/sonarqube/dashboard?id=chl-dss-lowcodeportal","status":"onboarded"}
Quality Assurance with status: onboarded
Quality Assurance URL: "https://gluon.gs.corp/sonarqube/dashboard?id=chl-dss-lowcodeportal"
Run sonarURL="https://gluon.gs.corp/sonarqube/dashboard?id=chl-dss-lowcodeportal"
sonarURL: https://gluon.gs.corp/sonarqube/dashboard?id=chl-dss-lowcodeportal
sonarProjectKey: chl-dss-lowcodeportal
sonarInstanceUrl: https://gluon.gs.corp/sonarqube
Getting SONAR_ID for URL https://gluon.gs.corp/sonarqube
The SONAR_ID is SONAR_GLUON_COMMUNITY
Getting properties of the instance SONAR_GLUON_COMMUNITY
Run santander-group-shared-assets/gln-setup-runner-ohe-action@v2.0.2
initializing createBackupJavaHome method
Show JAVA_HOME backup: 
maven_settings: 
npm_settings: 
pip_settings: 
pypirc_settings: 
initializing checkAndSetJavaHome method
initializing setJavaDefault method
Setting java_home to : /tmp/asdfdata/installs/java/adoptopenjdk-17.0.8+7
initializing javaFixCertificates method
asdf local java adoptopenjdk-17.0.8+7

asdf local maven 3.9.5

asdf local nodejs 18.20.2

asdf local python 3.11.5

cacerts already fixed for this version.
Run santander-group-shared-assets/gln-alm-sonar-action@v1.7.0
Run echo "::group::Set Sonar parameters"
Set Sonar parameters
Run echo "::group::Sonar Scanner For projects No Maven"
Sonar Scanner For projects No Maven
Run $GITHUB_ACTION_PATH/script/check-quality-gate.sh .sonar/report-task.txt

✖ Quality Gate has FAILED.

Detailed information can be found at: https://gluon.gs.corp/sonarqube/dashboard?id=chl-dss-lowcodeportal

Error: Process completed with exit code 1.
Run santander-group-shared-assets/gln-alm-check-waiver-action@1.5.0
Starting waiver process...
Getting application information from Managment
Starting token generation process!
Authenticating in the Corporate Login
Getting BKS Token from sts
Getting JWT Token from sts
Token generated successfully!
Retrieving component from Management
Application ID: 73
Getting waiver from: https://gluon.gs.corp/exceptionmanager/api/applications/73/quality-waiver
Retrieving waiver response from its API
response.data: []
response.status: 200
Error: 
The previous QA job may have failed for various reasons, please review it.❌
An attempt was made to check for active waivers, but none were found.❌
Run msg="Result Sonar Analysis FAILED"
Error: Result Sonar Analysis FAILED
Run santander-group-shared-assets/gln-alm-data-search-engine-application-action@1.5.0
Getting parameters
/usr/bin/git --no-pager show -s --format='%ae' HEAD
'daniel.sierpe@servexternos.santander.cl'
Notice: 👨‍💻 User Email: 'daniel.sierpe@servexternos.santander.cl'

Configuring metrics to be sent based on jobType = sonar
✅ DataSearchEngine URL: https://vpc-sgtp1airopsopsglngene001-x6ppfabqb6pap7telaeh64f6hq.eu-west-1.es.amazonaws.com/alm-nextgen-workflows-reusable/_doc

🚀 Sending data to DataSearchEngine... 
Payload: {"@timestamp":"2025-08-12T17:02:00.825Z","data":{"job":{"EXECUTION_TYPE":"build-sonar","RESULT":"success","URL":"https://gluon.gs.corp/sonarqube/api/qualitygates/project_status?analysisId=AZifOsAqkz2NlXmvnF5V","CRITERIA":"","WAIVER":"None","WAIVER_ID":"None","VERSION":"0.0.1-BETA","SONAR_PROJECT_KEY":"chl-dss-lowcodeportal","SONAR_PROPERTIES":"-Dsonar.sources=src -Dsonar.tests=. -Dsonar.test.inclusions=**/*.spec.ts,**/*.test.ts,**/*.test.tsx -Dsonar.javascript.coveragePlugin=lcov -Dsonar.javascript.lcov.reportPath=coverage/**/lcov.info -Dsonar.typescript.lcov.reportPaths=coverage/**/lcov.info -DtestExecutionReportPaths=test-result/ut_report.xml,ut_report.xml -Dsonar.projectVersion=0.0.1-BETA","SONAR_MAVEN_PLUGIN":"3.11.0.3922","SONAR_SCANNER_VERSION":"5.0.1.3006"},"global":{"JOB_TYPE":"sonar","GITHUB_URL":"https://github.com/santander-group-chile-gln","SELF_HOSTED_RUNNER_NAME":"npm-alm1-cmpn-rd-m79x9-wdd4j","WORKFLOW_RUN_ID":"16915361231","WORKFLOW_RUN_NUMBER":"16","WORKFLOW_NAME":"Quality","WORKFLOW_URL":"https://github.com/santander-group-chile-gln/chl-dss-lowcodeportal/actions/workflows/Quality.yml","WORKFLOW_EXECUTION_URL":"https://github.com/santander-group-chile-gln/chl-dss-lowcodeportal/actions/runs/16915361231","STAGE":"QG_result","GIT_REPOSITORY":"https://github.com/santander-group-chile-gln/chl-dss-lowcodeportal","GIT_ORGANIZATION":"santander-group-chile-gln","GIT_BRANCH_NAME":"4/merge","GIT_COMMIT":"ad0d4b568c4770df39ecb67b6349fa0ac327e987","USER":"id34181-corporativo_sangroup","USER_EMAIL":"'daniel.sierpe@servexternos.santander.cl'\n"},"USER_DATA":""}}
Authorization Header: Basic ***
🎉 Data sent successfully!
DataSearchEngine response status: 201

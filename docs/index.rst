*************************************************************
SonarQube \- Import Code Coverage For JavaScript & TypeScript
*************************************************************

We will config **a Jenkins job** to follow the below steps:

* Run test, generate unit test result and coverage report (lcov type).
* Import unit test and coverage report to SonarQube server.

JavaScript
##########

1. Run test & generate report:
***********************************************************

* For projects using **jest**:

	Add an **Execute Windows batch command** build step behind the step **Build a Visual Studio project or solution using MSBuild** in the Build section as below:

.. image:: _static/js-jest-coverage.png

::

"%JENKINS_HOME%\tools\Commands\js-jest-coverage.cmd" "%WORKSPACE%\src\Alpha.AccountInfo.Site"

with: 

	*"%WORKSPACE%\\src\\Alpha.AccountInfo.Site"*: the root test folder contains **package.json** config. You need to input the corresponding test folder in your project.
	
This command will run test, generate unit test result and coverage report (lcov).

* **For other frameworks and test runners**:

	You can ask NexOps for help.

2. Import report:
*****************

* In the step **SonarQube Scanner for MSBuild - Begin Analysis**, add additional arguments:

::

/d:sonar.javascript.lcov.reportPaths="${WORKSPACE}\js-lcov\lcov.info"
/d:sonar.testExecutionReportPaths="${WORKSPACE}\js-test-report.xml"
/d:sonar.tests=Features
/d:sonar.test.inclusions=**/*.test.jsx

with:

	**/d:sonar.javascript.lcov.reportPaths**: the coverage report path.
	
	**/d:sonar.testExecutionReportPaths**: the unit test result path.
	
	**/d:sonar.tests**: the root folder paths contains test files. You must input the corresponding test folders in your projects.
	
	**/d:sonar.test.inclusions**: the inclusion pattern for test files. You maybe change if needed.
	
.. note::
	
	* These arguments can contains multi values separated by comma.
	
	* You shall not need to change these two arguments **/d:sonar.javascript.lcov.reportPaths** and **/d:sonar.testExecutionReportPaths** unless you run jest by your own statement.
	
	* For source codes not included in project files .csproj, we need to input source folder as below:
	
		**/d:sonar.sources=src**
	
		SonarQube Scanner will scan additional source codes in this folder and import to server.
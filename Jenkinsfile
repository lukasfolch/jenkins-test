def org = "GHR-POD-NEX-13_AutoContentTestsystem"
def src = "./"

def devRoute = [hostname:"time-tracker-dev", domain:""]
def devEnv = [name:"jenkins-test", space: "development", cfcredid: "cf-inventorytool-dev-test", routes: [devRoute], envfile: "env.dev.json"]

def prodRoute = ""

pipeline {
	agent any
	tools {
		nodejs "nodejs_19"
	}
	stages {
		stage('deps') {
			steps {
				sh 'npm install --global yarn'
			}
		}
		stage('env') {
			steps {
				sh 'echo "REACT_APP_PORT=3000" >> .env'
				sh 'echo "REACT_APP_STAGE=production" >> .env'
			}
		}
        stage('Install') {
            steps {
                sh 'yarn'
            }
        }
		stage('build') {
			steps {
				sh 'yarn build'
			}
		}
		stage('deploy') {
			steps {
                sh "cf api https://api.scapp-console.swisscom.com --skip-ssl-validation"
                sh "cf auth"
                sh "cf target -o '${org}' -s ${devEnv.space}"
				sh 'cf push jenkinstest -b staticfile_buildpack -k 2G'
			}
		}
	}
}

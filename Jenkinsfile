def org = "GHR-POD-NEX-13_AutoContentTestsystem"
def src = "./"

def devEnv = [name:"jenkins-test", space: "development", cfcredid: "cf-inventorytool-dev-test", route: "", envfile: "env.dev.json"]

def prodRoute = ""

pipeline {
	agent any
	tools {
		nodejs "nodejs_21.6.2"
	}
	stages {
		stage('env') {
			steps {
				sh 'echo "REACT_APP_PORT=3000" >> .env'
				sh 'echo "REACT_APP_STAGE=production" >> .env'
			}
		}
        stage('Install') {
            steps {
                sh 'npm install'
            }
        }
		stage('build') {
			steps {
				sh 'npm run build'
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

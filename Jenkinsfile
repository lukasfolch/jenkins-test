def org = "INI-ONE-WSN-EIS-NWS_LAN-I_Tools"
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
				withCredentials([usernamePassword(credentialsId: "e8f83859-f3d5-4a1b-af8a-dc7f016295fc", passwordVariable: "CF_PASSWORD", usernameVariable: "CF_USERNAME")]) {
				sh "cf api https://api.scapp-console.swisscom.com --skip-ssl-validation"
				sh "cf auth"
				sh "cf target -o '${org}' -s ${devEnv.space}"
				sh 'cf push jenkinstest -b staticfile_buildpack -k 512M'
				}
                
			}
		}
	}
}

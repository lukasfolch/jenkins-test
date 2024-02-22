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
		stage('env') {
			steps {
				sh 'echo "REACT_APP_PORT=3000" >> .env'
				sh 'echo "REACT_APP_STAGE=production" >> .env'
			}
		}
		stage('prepare .npmrc') {
            steps {
				withCredentials([string(credentialsId: '4246882f-66fb-45c4-9560-8ce123ee56d4', variable: 'NPM_TOKEN')]) {
                    // Directly write the .npmrc content without exposing the token in logs
                    // Dynamically generate .npmrc with the correct token
                    sh """
                    echo "registry=https://bin.swisscom.com/artifactory/api/npm/swisscom-npm-virtual/" > .npmrc
                    echo "//bin.swisscom.com/artifactory/api/npm/swisscom-npm-virtual/:_authToken=\$NPM_TOKEN" >> .npmrc
                    """
                }
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

def org = "INI-ONE-WSN-EIS-NWS_LAN-I_Tools"
def src = "./"
def npmTokenId = "94384ee4-6d6c-4f96-9eb4-e94f3cfd4678"

def devRoute = [hostname:"jenkins-test", domain:"scapp-corp.swisscom.com"]
def devEnv = [name:"jenkins-test", space: "development", cfcredid: "e8f83859-f3d5-4a1b-af8a-dc7f016295fc", routes: [devRoute], envfile: "env.dev.json"]

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
        stage('Install') {
            steps {
				withCredentials([string(credentialsId: npmTokenId, variable: 'NPM_TOKEN')]) {
                    sh '''
                    echo "registry=https://bin.swisscom.com/artifactory/api/npm/swisscom-npm-virtual/" > .npmrc
                    echo "//bin.swisscom.com/artifactory/api/npm/swisscom-npm-virtual/:_authToken=${NPM_TOKEN}" >> .npmrc
                    npm install
                    '''
                }
            }
        }
		stage('build') {
			steps {
				sh 'npm run build'
			}
		}
		stage('deploy') {
			steps {
				withCredentials([usernamePassword(credentialsId: devEnv.cfcredid, passwordVariable: "CF_PASSWORD", usernameVariable: "CF_USERNAME")]) {
				sh "cf api https://api.scapp-console.swisscom.com --skip-ssl-validation"
				sh "cf auth"
				sh "cf target -o '${org}' -s ${devEnv.space}"
				sh 'cf push jenkinstest -b staticfile_buildpack -k 512M -m 128M --no-route'
				}
                
			}
		}
	}
}

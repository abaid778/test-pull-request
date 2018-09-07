pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                echo "This is BUILD step"
                ls -la && cat /etc/issue && /etc/init.d/jenkins status
            }
        }
		stage('DeploytoStaging') {
			when {
				branch 'master'
			}
			 steps {
            	withCredentials([usernamePassword(credentialsId: 'ssh_pass', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]){
                sshPublisher(
                failOnError:true,
                continueOnError:False,
                Publishers :[
                	configName: 'staging',
                	sshCredentials: [
                		username: "$USERNAME",
                		encryptedPassPhrase: "$USERPASS",
                	],
                	transfers: [
                		sshTransfer(
                			sourceFiles: 'index.html',
                			remoteDirectory: '/tmp'
                			execCommand: 'sudo cp /tmp/index.html /var/www/html/ && /etc/init.d/apache2 reload'
                		)
                	]
                  ]
                )
              }
            }
		
      	}

      		stage('DeploytoProduction') {
			when {
				branch 'master'
			}
            steps {

            	input 'Does the staging environment looks OK?'
            	milestore(1)

            	withCredentials([usernamePassword(credentialsId: 'ssh_pass', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]){
                sshPublisher(
                failOnError:true,
                continueOnError:False,
                Publishers :[
                	configName: 'production',
                	sshCredentials: [
                		username: "$USERNAME",
                		encryptedPassPhrase: "$USERPASS",
                	],
                	transfers: [
                		sshTransfer(
                			sourceFiles: 'index.html',
                			remoteDirectory: '/tmp'
                			execCommand: 'sudo cp /tmp/index.html /var/www/html/ && /etc/init.d/apache2 reload'
                		)
                	]
                  ]
                )
              }
            }	
      	}

	}
}

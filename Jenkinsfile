pipeline {
	agent any
	stages {
		stage('Edit project files') {
			steps {
				sh 'echo "echo \"version: ${BUILD_NUMBER}\"" >> /home/jenkins/rpms/my-tools-pack/root/usr/local/bin/stat.sh'
			}
		}
		stage('Increase release version') {
			steps {
			   sh 'sed -i "s/Release.*$/Release: ${BUILD_NUMBER}/" /home/jenkins/rpms/my-tools-pack/spec/header'
			}
		}
		stage('Build package'){
			steps {
				sh 'cd /home/jenkins/rpms/my-tools-pack/; togo build package'
			}
		}
		stage('Update package in system') {
			steps {
				sh 'sudo yum install /home/jenkins/rpms/my-tools-pack/rpms/my-tools-pack-1.0-${BUILD_NUMBER}.noarch.rpm -y'
			}
		}
		stage('Verification') {
			steps {
				sh 'echo "new package version: my-tools-pack-1.0-${BUILD_NUMBER}.noarch"'
				sh 'md5sum /home/jenkins/rpms/my-tools-pack/rpms/my-tools-pack-1.0-${BUILD_NUMBER}.noarch.rpm'
			}
		}
	}
	testing = sh (
		returnStdout: true,
		script: '''
		var=$(for i in $(seq 1 100); do echo testing $i; done)
		
		awk '{print $2 }' <<< "$var" |tail -n 1
		'''
		).trim()

	post {
		always {
			echo 'This will always run'
		}
		success {
			echo "Success build: ${BUILD_NUMBER}"
			echo 'Test installed application'
			sh '/usr/local/bin/stat.sh'
		}
		failure {
			echo 'This will run only if failed'
		}
		unstable {
			echo 'This will run only if the run was marked as unstable'
		}
		changed {
			echo 'New commit has been done'
		}
	}
}


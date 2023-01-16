node('') {
	stage ('checkout code'){
		checkout scm
	}
	
	stage ('Build'){
		sh "mvn clean install -Dmaven.test.skip=true"
	}

	stage ('Test Cases Execution'){
		sh "mvn clean org.jacoco:jacoco-maven-plugin:prepare-agent install -Pcoverage-per-test"
	}

	stage ('Sonar Analysis'){
		//sh 'mvn sonar:sonar -Dsonar.host.url=http://35.153.67.119:9000 -Dsonar.login=77467cfd2653653ad3b35463fbfdb09285f08be5'
	}

	stage ('Archive Artifacts'){
		archiveArtifacts artifacts: 'target/*.war'
	}
	stage('Docker Build'){
        sh ' docker --version '
        sh ' docker build -t mavenbuild . '
    }
    stage('Create Container '){
        sh ' docker run -d -p 9000:8080  --name dockercontainer mavenbuild '
    }
	
	
	
	stage ('Notification'){
		emailext (
		      subject: "Job Completed",
		      body: "Jenkins Pipeline Job for Maven Build got completed !!!",
		      to: "build-alerts@example.com"
		    )
	}
	stage ('Deployment'){
		ansiblePlaybook colorized: true, disableHostKeyChecking: true, playbook: 'deploy.yml'
	}
}

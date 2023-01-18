def containerName="mavenbuild"
def tag="latest"
def dockerHubUser="supriyagowda17"
def httpPort="8090" 
node{
	stage ('checkout code'){
		git credentialsId: '2aba396a-b26a-46c3-960b-d5d1ad46a797', url: 'https://github.com/supriyagowda17/MavenBuild.git'
	}
	
	stage ('Build'){
		sh "mvn clean install -Dmaven.test.skip=true"
	}

	stage ('Test Cases Execution'){
		sh "mvn clean org.jacoco:jacoco-maven-plugin:prepare-agent install -Pcoverage-per-test"
	}

	stage ('Sonar Analysis'){
		//sh 'mvn sonar:sonar -Dsonar.host.url=http://20.84.144.34:9000/ -Dsonar.login=ab82c41e2cf6565aa1faf6d437423c576c5ba792'
	}
	
	 stage ('Archive Artifacts'){
//         archiveArtifacts artifacts: 'target/*.war'
    }

	 stage("Image Prune"){
         sh "docker image prune -a -f"
         }

    stage('Image Build'){
        sh "docker build -t $containerName:$tag  -t $containerName --pull --no-cache ."
        echo "Image build complete"
        }

        stage('Push to Docker Registry'){
            withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', passwordVariable: 'dockerPassword', usernameVariable: 'dockerUser')]) {
                    sh "docker login -u $dockerUser -p $dockerPassword"
                    sh "docker tag $containerName:$tag $dockerUser/$containerName:$tag"
                    sh "docker push $dockerUser/$containerName:$tag"
                    echo "Image push complete"
        }
                }
        node('KubernetesMaster'){
            stage('Run App'){
                    sh """
              kubectl create deployment kubernetes-bootcamp01 --image=docker.io/supriyagowda17/$containerName:$tag --port=8090
                       kubectl get pods
                    """
                }
        }
    stage ('Notification'){
        emailext (
              subject: "Job Completed",
              body: "Jenkins Pipeline Job for Maven Build got completed supriya !!!",
              to: "rsupriya528@gmail.com"
            )
    }

//     stage ('Deployment'){
//         ansiblePlaybook colorized: true, disableHostKeyChecking: true, playbook: 'deploy.yml'
//     }
}

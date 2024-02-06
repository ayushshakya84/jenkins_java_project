pipeline {
    
    agent {
        label "jenkins_slave"
    }
    
    
    stages {
        stage('SCM') {
            steps {
                git 'https://github.com/ayushshakya84/end-end-pipeline-jenkins.git'
                
            }
            
        }
        
        stage('Build by Maven Package') {
            steps {
                sh 'mvn clean package'
            }
            
        }
        
        
        stage('Build Docker OWN image') {
            steps {
                sh "sudo docker build -t  ayush8410/javaweb:${BUILD_TAG}  ."
                //sh 'whoami'
            }
            
        }
        
        
        stage('Push Image to Docker HUB') {
            steps {
                
                withCredentials([string(credentialsId: 'DOCKER_HUB_PWD', variable: 'DOCKER_HUB_PASS_CODE')]) {
    // some block
                 sh "sudo docker login -u ayush8410 -p $DOCKER_HUB_PASS_CODE"
}
               
               sh "sudo docker push ayush8410/javaweb:${BUILD_TAG}"
            }
            
        }
        
        
        stage('Deploy webAPP in DEV Env') {
            steps {
                sh 'sudo docker rm -f myjavaapp'
                sh "sudo docker run  -d  -p  8080:8080 --name myjavaapp   ayush8410/javaweb:${BUILD_TAG}"
                //sh 'whoami'
            }
            
        }
        
        
        stage('Deploy webAPP in QA/Test Env') {
            steps {
               
               sshagent(['QA_ENV_SSH_CRED']) {
    
                    sh "ssh  -o  StrictHostKeyChecking=no ec2-user@13.233.100.238 sudo docker rm -f myjavaapp"
                    sh "ssh ec2-user@13.233.100.238 sudo docker run  -d  -p  8080:8080 --name myjavaapp   ayush8410/javaweb:${BUILD_TAG}"
                }

            }
            
        }
        
        
         stage('QAT Test') {
            steps {
                
               // sh 'curl --silent http://13.233.100.238:8080/java-web-app/ |  grep India'
                
                retry(10) {
                    sh 'curl --silent http://13.233.100.238:8080/java-web-app/ |  grep India'
                }
            
               
            }
        }
          
        
         
         
        stage('approval') {
            steps {
                
            
            script {
                Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
                echo 'userInput: ' + userInput

                if(userInput == true) {
                    // do action
                } else {
                    // not do action
                    echo "Action was aborted."
                }
            
                
            }
        }
        }
        
        
         
        
        stage('Deploy webAPP in Prod Env') {
            steps {
               
               sshagent(['QA_ENV_SSH_CRED']) {
    
                    
                    sh "ssh  -o  StrictHostKeyChecking=no ec2-user@13.232.250.244 sudo kubectl  delete    deployment myjavawebapp"
                    sh "ssh  ec2-user@13.232.250.244 sudo kubectl  create    deployment myjavawebapp  --image=ayush8410/javaweb:${BUILD_TAG}"
                    sh "ssh ec2-user@13.232.250.244 sudo wget https://raw.githubusercontent.com/ayushshakya84/jenkins-docker-maven-java-webapp/master/webappsvc.yml"
                    sh "ssh ec2-user@13.232.250.244 sudo kubectl  apply -f webappsvc.yml"
                    sh "ssh ec2-user@13.232.250.244 sudo kubectl  scale deployment myjavawebapp --replicas=5"
                }

            }
            
        } 
        
    
        
    }
    
  
        
     post {
         always {
             echo "You can always see me"
         }
         success {
              echo "I am running because the job ran successfully"
         }
         unstable {
              echo "Gear up ! The build is unstable. Try fix it"
         }
         failure {
             echo "OMG ! The build failed"
             mail bcc: '', body: 'hi check this ..', cc: '', from: '', replyTo: '', subject: 'job ete fail', to: 'ayushshakya8410@gmail.com'
         }
     }

    
    
}

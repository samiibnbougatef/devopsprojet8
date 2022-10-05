pipeline{
    agent any 
    environment{
        dockerhub=credentials('dockerhub')
    }
    tools{
        maven "M2"
    }
    stages{
        stage('clone'){
            steps{
                git credentialsId: 'gitlab', url: 'git@gitlab.com:samiibnbougatef/projet_j2e.git'
            }
        }
        stage('build'){
            steps{
                sh 'mvn clean install package'
            }
        }
        stage('docker image'){
            steps{
                sh 'cp -r /var/lib/jenkins/workspace/projet8/webapp/target/webapp.war /home/ansadmin/projet8/ressources'
                sh 'docker build -t samiibnbougatef/tomcat:tomcat${BUILD_NUMBER} /home/ansadmin/projet8/.'
                sh ' docker login -u $dockerhub_USR -p $dockerhub_PSW '  
                sh ' docker push samiibnbougatef/tomcat:tomcat${BUILD_NUMBER}' 
            }
        }
       
        stage('clean docker image'){
            steps{
                sh 'docker rmi -f samiibnbougatef/tomcat:tomcat${BUILD_NUMBER}'
            }
        }
         stage('k8s'){
            steps{
        
             sshagent (credentials: ['k9smaster']) {
              sh 'ssh -o StrictHostKeyChecking=no -l k8smaster  13.214.35.74 uname -a'
                sh ' ssh k8smaster@13.214.35.74 kubectl get pods'
                sh " ssh k8smaster@13.214.35.74 cat tomcat-deploy.yml "
                 sh " ssh k8smaster@13.214.35.74 sed -i 's+samiibnbougatef/tomcat.*+samiibnbougatef/tomcat:tomcat${BUILD_NUMBER}+g' tomcat-deploy.yml"
                   sh 'ssh k8smaster@13.214.35.74 kubectl apply -f tomcat-deploy.yml'
            }
        }
    }
}
    
}

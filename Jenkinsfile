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
                sh 'cp -r /var/lib/jenkins/workspace/projet7/webapp/target/webapp.war /home/ansadmin/projet8/ressources'
                sh 'docker build -t tomcat /home/ansadmin/projet8/ .'
                sh 'echo $dockerhub_PSW |  docker login -u $dockerhub_USR --password-stdin'  
                sh 'sudo docker push samiibnbougatef/tomcat:latest' 
            }
        }
        stage('k8s'){
            steps{
        
             sshagent (credentials: ['k9smaster']) {
              sh 'ssh -o StrictHostKeyChecking=no -l k8smaster  13.214.154.114 uname -a'
                sh ' ssh k8smaster@13.214.154.114 kubectl get pods'
               
                   sh 'ssh k8smaster@13.214.154.114 kubectl apply -f tomcat-deploy.yml'
                
               }
            }
        }
    }
}

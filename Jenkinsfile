pipeline {
  // agent any
  agent {label 'ec2-fleet'}  
  tools {nodejs "node"}
    
  stages {
        
    stage('Git') {
      steps {
        script {
           git credentialsId: 'wahaj123', url: 'https://github.com/wahaj123/Jenkins-autoscaling'
           // Do a ls -lart to view all the files are cloned. It will be clonned. This is just for you to be sure about it.
           sh "ls -lart ./*" 
           // List all branches in your repo. 
           sh "git branch -a"
          }
      }
    }
     
    stage('Build') {
      steps {
        sh 'pwd'
        sh 'npm install'
        sh 'npm install express'
      }
    }      
     stage('Deployment') {
        steps {
            script {
                sshPublisher(publishers: [
                    sshPublisherDesc(
                        configName: 'ec2-user',
                        transfers: [
                            sshTransfer(
                                sourceFiles: "app.js",
                                execCommand: "cd /home/ec2-user/jenkins/workspace/Jenkins-autoscaling; npm install; npm install express; export BUILD_ID=dontKillMe; nohup node app.js & ",
                                remoteDirectory: '/home/ec2-user/jenkins' 
                            )
                        ],
                        usePromotionTimestamp: false,
                        useWorkspaceInPromotion: false,
                        verbose: false
                    )]
                )
            }
        }
    }       
  }
}
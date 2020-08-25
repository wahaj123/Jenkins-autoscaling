def SH_WITH_RETRIES_AND_RETURN( String cmd, Integer retries=5, Integer sleepSeconds=10  ){
  def retriesCount = 0
  echo "sh[retries=${retries}]: ${cmd}"
  try {
    retry( retries ){
      retriesCount = retriesCount+1
      echo "attempt ${retriesCount}/$retries"
      if( retriesCount > 1 ){
        sleep sleepSeconds
        sleepSeconds = sleepSeconds+1
      }
      return sh( returnStdout: true, script: "${cmd}").trim()
    }
  } catch(e) {
    throw e
  }
}
pipeline {
   agent any
  // agent {label 'ec2-fleet'}  
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
        script{
          def output = SH_WITH_RETRIES_AND_RETURN("curl localhost:8080")
          echo "${output}"
        }
        sh 'pwd'
        // sh 'npm install'
        // sh 'npm install express'
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
                                execCommand: "npm install; npm install express; export BUILD_ID=dontKillMe; nohup node app.js & ",
                                // remoteDirectory: '/home/ec2-user/jenkins'  
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
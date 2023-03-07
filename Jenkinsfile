pipeline{
       agent {
        label 'ChildNode'
    }
      environment{
          BUILD_COMPLETE = false
      }
    parameters {
       // string(name: 'MAVEN_GOAL', defaultValue: 'package', description: 'maven goal to build the package' )
        choice(name: 'BRANCH_TO_BUILD', choices: [ 'master'], description: 'Branch to build')
    }
    stages{
       stage('GetCode'){
            steps{
                git 'https://github.com/Kullay851/hello-world.git'
              
            }
         }        
       stage('Build'){    
            steps{
                sh 'mvn clean package | tee output.log'
              }
          }
        stage('SonarQube analysis') {
        steps{
              catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE'){
        withSonarQubeEnv('Sonarqube_token') { 
         sh 'mvn sonar:sonar -Dsonar.projectKey=my-app3 -Dsonar.host.url=http://172.31.41.52:9000 -Dsonar.login=sqp_74637c971e7a93237e069a76c41186963e6e8ee9'
                }
              }}
        }
         stage('pushing artifacts to jfrog'){
         steps{
               catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE'){
               rtUpload (
                         serverId: 'JFrog-1',
                         spec: '''{
                         "files": [
                          {
                          "pattern": "/var/lib/jenkins/workspace/Jenkins-Ansible/target/*.jar",
                          "target": "Artifactory/"
                          }
                        ]
                  }''')
               }
       }
      }
          stage('deploy'){
                 steps{
                    ansiblePlaybook inventory: '/etc/ansible/hosts', playbook: '/home/jenkins/deploy.yaml'
                 }
          }
          
        stage('Completed'){
            steps{
                echo "completed"
            }
         }
    }
}

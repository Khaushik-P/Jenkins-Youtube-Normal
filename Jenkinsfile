pipeline{
    
    agent any 
     parameters{
        choice(name:'action' , choices:'create\ndelete' ,description:'Select create or destroy.')
        string(name:'DOCKER_HUB_USERNAME',defaultValue:'khaushik14',description:'Docker hub username')
        string(name:'IMAGE_NAME',defaultValue:'youtube',description:'Docker image name')
    }
    environment{
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('Clean Workspace'){
            steps{
            cleanWorkspace()
            }
        }
        
        stage('Git Checkout'){
            
            steps{
                
                script{
                    
                    git branch: 'main', url: 'https://github.com/Khaushik-P/Youtube-app-Devops.git'
                }
            }
        }
        stage('Static code analysis'){
            when { expression { params.action == 'create'}}    
            steps{
                
                script{
                    
                    withSonarQubeEnv(credentialsId: 'sonar-api') {
                        
                      sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Youtube1 -Dsonar.projectKey=Youtube1 '''
                    }
                   }
                    
                }
            }
            stage('Quality Gate Status'){
                when { expression { params.action == 'create'}}    
                steps{
                    
                    script{
                        
                        waitForQualityGate abortPipeline: false, credentialsId: 'sonar-api'
                    }
                }
            }
            stage('npm install'){
                when { expression { params.action == 'create'}}    
                steps{
                    sh 'npm install'
                    }
                }
            stage('OWASP FS SCAN'){
             steps{
                 dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
    }
 }
        

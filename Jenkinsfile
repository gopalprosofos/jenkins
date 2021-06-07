pipeline{
    agent any 
    stages{
        stage('Git Repository Clone'){
            agent any
             steps{
                git branch: 'main', url: 'https://github.com/gopalprosofos/jenkins.git'
                script{
                    properties([pipelineTriggers([pollSCM('0 */2 * * *')])])
                }
             }
        }
        stage('Install and Configure NPM') {
      steps {
        sh 'sudo cp -rf  * /nodejs'  
        sh 'npm config ls'
        sh 'npm install'
        sh 'npm test'
         }
        }  
    
        stage('Testing Stage'){
            steps{
                
                sh '''
               
                sudo cp -rf  * /demo
                if docker ps -a | grep demo-test
                    then
                    echo \'Already Running\'
                else
                    sudo docker run -dit -v /demo:/usr/local/apache2/htdocs/ -p 8082:80 --name demo-test node
                fi
                '''
                
            }
        }
        
        stage('Merge to Master Branch'){
            steps{
                script{
                    
        
                    withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh('''if curl jenkins.lesindian.com:8082
                        then
                        git checkout master
                        git branch --set-upstream-to=origin/master
                        git merge main
                        git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/gopalprosofos/jenkins.git master
                        fi
                        ''')
                    }
                }
            }
    
        }
        stage('Deploy to Production'){
            steps{
                git 'https://github.com/gopalprosofos/jenkins.git'
                script{
                    properties([pipelineTriggers([pollSCM('0 */2 * * *')])])
                }
                sh '''
                
                sudo cp -rf  * /prod
                if curl jenkins.lesindian.com:8082
                then
                    echo \'Working Fine\'
                    if docker ps -a | grep production
                        then
                        echo \'Already Running\'
                    else
                        sudo docker run -dit -v /prod:/usr/local/apache2/htdocs/ -p 8084:80 --name production node
                    fi
                fi
                '''
            }
        }
        
    }
} 
 
 

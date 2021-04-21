// Define variable
def version = 'v0.15'

pipeline {
    agent { label 'agent2' }
    stages {
        stage('Git clone') {
           steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']],
                //userRemoteConfigs: [[url: 'git@github.com:welcomenews/do14.git']]])
                userRemoteConfigs: [[url: 'https://github.com/nodejs/nodejs.org.git']]])
           }    
        }    
    //    stage('Install nginx') {
    //        steps {
    //            sh 'sudo apt install nginx -y'    
    //        }
    //    }
        
        stage('Install Node and project build') {
            steps {
                nvm(nvmInstallURL: 'https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh', 
                   nvmIoJsOrgMirror: 'https://iojs.org/dist',
                   nvmNodeJsOrgMirror: 'https://nodejs.org/dist', 
                   version: 'v14.15.4') {
                       sh "npm install"
                       echo "=========== Build main site distribution ========="
                       sh "npm run build"
                       archiveArtifacts artifacts: 'build/', fingerprint: true
                   }
            }
        }
                
///++++++        stage('Install Node and project build') {   
///            steps {
///                sh 'bash -l -c "touch ~/.bashrc"'
///                sh 'curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash'
///                //sh 'bash -l -c ". $HOME/.nvm/nvm.sh ; nvm install v14.15.4 ; nvm use v14.15.4"'
///                sh 'bash -l -c ". $HOME/.nvm/nvm.sh ; nvm use v14.15.4 || nvm install v14.15.4 && nvm use v14.15.4 ; cd /var/lib/jenkins/workspace/do-14Pre && npm install ; npm run build"'
///                archiveArtifacts artifacts: 'build/', fingerprint: true
///                sh '. ~/.bashrc'
                //sh 'bash -l -c "source ~/.bashrc"'
                
    //            sh '''#!/bin/bash
    //            source /etc/profile
    //            source ~/.bashrc
    //            '''
 ///           }    
 ///++++++++       }
       
//    post {
//        always {
//            archiveArtifacts artifacts: 'build/', fingerprint: true
//        }    
//    }
//        stage('pull artifact') {
//            steps {
//                copyArtifacts filter: 'build/', fingerprintArtifacts: true, target: '/var/www/html/index-simlink/'
//            }
//        }        
        
        stage('Copy static site') {
            steps {
                sh "sudo mkdir -p /var/www/html/releases/$version"
                sh "sudo cp -rf /var/lib/jenkins/workspace/do-14Pre/build/ /var/www/html/releases/$version"
                sh "sudo chown -R www-data:www-data /var/www/html/releases/$version/*"
                sh "sudo ln -sfT /var/www/html/releases/$version/ /var/www/html/index-simlink || true"
           }
        }
        
        stage('Rewrate index-simlink') {
            when { expression { return fileExists ('/var/www/html/index-simlink') } }
            steps {
                sh "sudo ln -sfT /var/www/html/releases/$version/ /var/www/html/index-simlink"
                sh 'sudo systemctl reload nginx.service'
            }  
        }
        stage('Remove old versions\'s folders') {
           steps {
                sh 'cd /var/www/html/releases/'
                sh 'ls -dtr /var/www/html/releases/*/ | head -n -5 | sudo xargs -r rm -rf --'
            }
        }
    } 
}

// Define variable
def version = "${BUILD_NUMBER}"

pipeline {
    agent any
    stages {
        stage('Git clone') {
           steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']],
                userRemoteConfigs: [[url: 'https://github.com/symfony/demo.git']]])
           }    
        }    
        
        stage('Install requirements') {
            steps {
                sh "cd ${workspace}/ && echo \"yes\" |composer update"
            }
        }
                       
        stage('Copy static site') {
            steps {
                sh "sudo mkdir -p /var/www/html/releases/${version}"
                sh "sudo cp -rf ${workspace}/. /var/www/html/releases/${version}"
                sh "sudo chown -R www-data:www-data /var/www/html/releases/${version}/*"
                sh "sudo ln -sfT /var/www/html/releases/${version}/ /var/www/html/index-simlink"
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

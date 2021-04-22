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
                sh 'bash -l -c "cd ${workspace}/phpProj && echo \"yes\" |composer update"'
            }
        }
                       
        stage('Copy static site') {
            steps {
                sh "sudo mkdir -p /var/www/html/releases/$version"
                sh "sudo cp -rf ${workspace}/phpProj/ /var/www/html/releases/${version}"
                sh "sudo chown -R www-data:www-data /var/www/html/releases/${version}/*"
                sh "sudo ln -sfT /var/www/html/releases/${version}/ /var/www/html/index-simlink || true"
           }
        }
        
        stage('Rewrate index-simlink') {
            when { expression { return fileExists ('/var/www/html/index-simlink') } }
            steps {
                sh "sudo ln -sfT /var/www/html/releases/${version}/ /var/www/html/index-simlink"
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

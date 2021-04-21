// Define variable
def version = 'v0.15'

pipeline {
    agent { label 'agent2' }
    stages {
        stage('Git clone') {
           steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']],
                userRemoteConfigs: [[url: 'https://github.com/symfony/demo.git']]])
           }    
        }    
    //    stage('Install nginx') {
    //        steps {
    //            sh 'sudo apt install nginx -y'    
    //        }
    //    }
        
        stage('Install requirements') {
            steps {
                sh 'bash -l -c "cd /var/lib/jenkins/workspace/phpProj && echo "yes" |composer update"'
            }
        }
                       
        stage('Copy static site') {
            steps {
                sh "sudo mkdir -p /var/www/html/releases/$version"
                sh "sudo cp -rf /var/lib/jenkins/workspace/phpProj/public/ /var/www/html/releases/$version"
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

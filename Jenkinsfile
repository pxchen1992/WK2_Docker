pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
                sh 'pwd'
            }
        }

         stage('Build Image') {
            steps {
                 sh '''
                    cd Cat && docker build -t willido/cat:JR10 .
                    cd ../helloworld && docker build -t willido/helloworld:JR10 .
                    cd ../web-nginx && docker build -t willido/mynginx:JR10 .
                    docker tag willido/cat:JR10 willido/cat:latest
                    docker tag willido/helloworld:JR10 willido/helloworld:latest
                    docker tag willido/mynginx:JR10 willido/mynginx:latest
                    docker images
	 	        '''
             }
         }

         stage('Push Image') {
            steps {
                 withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'PSWD', usernameVariable: 'USER')]) {
                     sh """                        
                         docker login -u $USER -p $PSWD
                         for img in willido/cat:JR10 willido/cat:latest willido/helloworld:JR10 willido/helloworld:latest \
                                willido/mynginx:JR10 willido/mynginx:latest; \
                         do docker push \$img; done;
                     """
                 }

             }
         }

         stage('Deploy Service') {
             steps {
                 sh '''
                     docker-compose down
                     docker-compose up -d
                     docker-compose ps
                 '''
             }
         }

    }
}

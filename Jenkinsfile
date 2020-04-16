pipeline {
    
    agent any

    tools { 
        maven 'Maven 3.6.3' 
        jdk 'jdk11' 
    }
    
    stages {
        
        stage ('Build Backend') {
            
            steps {
                
                sh '''
                
                  mvn clean package -DskipTests=true
                
                '''

                
            }
            
        }

    }

}

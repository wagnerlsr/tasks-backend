pipeline {
    
    agent any

    tools { 
        maven 'MAVEN_LOCAL' 
        jdk 'JAVA_LOCAL' 
    }
    
    stages {
        stage ('Build Backend') {
            steps {
                sh 'mvn clean package -DskipTests=true'
            }
        }

        stage ('Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }
    }
}

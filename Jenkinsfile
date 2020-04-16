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

        stage ('Sonar Analysis') {
            enviroment {
                scannerHome = tool 'SONAR_SCANNER'

            }

            steps {
                withSonarQubeEnv {
                    sh "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=1568dddaac2fdada4f53e069fa6a37d868565743 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**, **/src/test/**, **/model/**, **Application.java"
                }
            }
        }
    }
}

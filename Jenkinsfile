pipeline {
    
    agent any
    
    environment {
        PATH = "$PATH:/usr/local/bin"
    }

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
            environment {
                scannerHome = tool 'SONAR_SCANNER'

            }

            steps {
                withSonarQubeEnv ('SONAR_LOCAL') {
                    sh "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=1568dddaac2fdada4f53e069fa6a37d868565743 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java"
                }
            }
        }

        stage ('Quality gate') {
            steps {
                sleep(5)
                
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage ('Deploy Backend') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8888/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }

        stage ('API Test') {
            steps {
                dir('api-test') {
                    git 'https://github.com/wagnerlsr/tasks-api-test.git'
                    sh 'mvn test'
                }
            }
        }

        stage ('Deploy Frontend') {
            steps {
                dir('frontend') {
                    git 'https://github.com/wagnerlsr/tasks-frontend.git'
                    sh 'mvn clean package'
                    deploy adapters: [tomcat9(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8888/')], contextPath: 'tasks', war: 'target/tasks.war'
                }
            }
        }

        stage ('Functional Test') {
            steps {
                dir('functional-test') {
                    git 'https://github.com/wagnerlsr/tasks-functional-test.git'
                    sh 'mvn test'
                }
            }
        }

        stage ('Deploy Prod') {
            steps {
                sh 'docker-machine start default'
                sh 'docker-compose build'
                sh 'docker-compose up -d'
            }
        }

        stage ('Health Check') {
            steps {
                sleep(5)
                dir('functional-test') {
                    sh 'mvn verify -Dskip.surefire.tests'
                }
            }
        }
    }
}

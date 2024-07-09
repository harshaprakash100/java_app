pipeline {
    agent any

    tools {
        maven 'maven_tool'
    }

    environment {
        TOMCAT_URL = 'http://3.110.117.248:8080/'
    }

    stages {

        stage('Code Checkout with checkout') {
            steps {
                checkout([$class: 'GitSCM',
                        branches: [[name: 'main']],
                        userRemoteConfigs: [[url: 'https://github.com/harshaprakash100/java_app.git',
                        credentialsId: 'github_hp']]])
            }
        }

        stage('Unit Testing') {
            steps {
                sh '''
                    ls -lrt
                    cd ./calculator_app/
                    mvn clean test
                '''
            }
        }

        stage('Integration Test') {
            steps {
                sh '''
                    ls -lrt
                    cd ./calculator_app/
                    mvn jmeter:configure
                    mvn clean integration-test
                '''
            }
        }

        stage('Performance Test - JMeter') {
            steps {
                sh '''
                    ls -lrt
                    cd ./calculator_app/
                    mvn clean verify
                '''
            }
        }

        stage('Build Package') {
            steps {
                sh '''
                    ls -lrt
                    cd ./calculator_app/
                    mvn clean package -Dmaven.test.skip=true
                '''
            }
        }

        stage('Deploy-Tomcat') {
            steps {
                script {
                    deploy adapters: [tomcat9(credentialsId: 'tomcat_manager', path: '', url: "${env.TOMCAT_URL}")],
                                     contextPath: '/calculator',
                                     war: 'calculator_app/target/calculator.war'
                }
            }
        }
    }

}

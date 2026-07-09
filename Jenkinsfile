pipeline {
    agent any
    tools {
        nodejs 'Node-Installation'
    }
    environment {
        SONAR_HOME = tool 'venkaiah-sonar-scanner'
    }
    parameters {
    choice(name: 'BRACH', choices: ['master','main','dev','qa','ppd'], description: 'Brnaches to clone')
    booleanParam(name:'TEST', defaultValue: false, description: 'Do you want to run tets?')
    booleanParam(name: 'QUALITY', defaultValue:false, description: 'Do you need quality check?')
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: '$params.BRACH}', url: 'https://github.com/venkaiahkuncham123-tech/Node-Application.git'
                sh "echo Brach cloned is:${params.BRACH}"
            }
        }   
        stage('Compiling the Application') {
            steps {
               sh 'npm run build'
            }
        }
        stage('Testing the code') {
            when {
                expression {params.TEST == true}
            }
            steps {
                sh 'npm test'
            }
        }
        stage('Installin the dependencies') {
            steps {
                    sh 'npm install'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                   withSonarQubeEnv('venkaiah-sonar') {
                        sh '''$SONAR_HOME/bin/sonar-scanner -Dsonar.prject.properties=.'''
                }
            }
        }
        stage('Sonarqube Quality Gateway Check') {
            when {
                expression {params.QUALITY == true}
            }
            steps {
                
                timeout(10) {
                            waitForQualityGate abortPipeline: false, credentialsId: 'a679a1d6-9276-46ed-bd45-b4ae6ff5a2f3'

                            }
            }
        }
        stage('Running Application') {
            steps {
                sh 'npm start'
            }
        }  
    }
}

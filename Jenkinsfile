pipeline{
    agent any
    
    tools{
        maven 'Mvn'
    }

    stages{
        stage('checkout'){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'JenkinsDocker2', url: 'https://github.com/ErnestoJH90/CI.git']]])
            }
        }
        stage('Mvn version'){
            steps{
                bat 'mvn --version'
            }
        }
        stage('Build'){
            steps{
                bat 'cd Demo && mvn clean package'
                bat 'java -cp Demo/target/Demo-1.0-SNAPSHOT.jar com.Demo.app.App > Reports.txt'
            }
        }
        stage('SonarQube Scanner'){
            steps{
                script{
                    def scannerHome = tool 'SonarQubeScanner';
                    withSonarQubeEnv('SonarQube'){
                        bat "${tool('SonarQubeScanner')}/bin/sonar-scanner.bat -Dsonar.projectKey=CI -Dsonar.sources=. -Dsonar.host.url=http://localhost:9000 -Dsonar.login=ab0aaa5a2f0a92e0b97bf2e4686c221ae43859d5"
                        'mvn sonar:sonar \'
                        '-Dsonar.projectKey=CI \'
                        '-Dsonar.host.url=http://localhost:9000 \'
                        '-Dsonar.login=ab0aaa5a2f0a92e0b97bf2e4686c221ae43859d5'
                    }
                }
            }
        }
        stage('Delivery'){
            steps{
                archiveArtifacts artifacts: 'Reports.txt', followSymlinks: false
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
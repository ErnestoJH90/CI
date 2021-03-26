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
                        bat "${tool('SonarQubeScanner')}/bin/sonar-scanner.bat -Dsonar.projectKey=CI -Dsonar.sources=. -Dsonar.host.url=http://localhost:9000 -Dsonar.login=a3f2e353e5275c2e1d4bc365e4fa4cbd52e51d49"
                        bat 'mvn clean package'
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
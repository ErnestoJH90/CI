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
                    def scannerHome = tool 'SonarQube Scanner' 
                    withSonarQubeEnv('SonarQube'){
                        bat '${tool('SonarQube Scanner')}/bin/sonar-scanner.bat -Dsonar.projectKey=Demo -Dsonar.sources=. -Dsonar.host.url=http://localhost:9000 -Dsonar.login=7a9bc05e45a342efda0ddc1c9ceaf9a33e9fe497'
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
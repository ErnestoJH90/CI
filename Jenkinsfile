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
                        //bat 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.8.0.2131:sonar'
                        //bat 'mvn clean verify sonar:sonar'
                        bat  'mvn sonar:sonar \
                            -Dsonar.projectKey=com.Demo:Demo \
                            -Dsonar.host.url=http://localhost:9000 \
                            -Dsonar.login=c2be13a57c076853c36ce321a8d5061d15c71419' 
                        //bat 'mvn clean package sonar:sonar'

                    }
                }
            }
        }
        stage('Delivery'){
            steps{
                archiveArtifacts artifacts: 'Reports.txt', followSymlinks: false
            }
        }
        stage('E-mail'){
            steps{
                emailext body: '''Hello everyone
                The scanning report is generated''', subject: 'reports', to: 'ernesto.jimenez@softtek.com'
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
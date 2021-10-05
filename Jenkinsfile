def SonarQubeUrl = 'http://localhost:9000/dashboard?id='
pipeline{
    agent any
    
    tools{
        maven 'Mvn'
    }

    stages{
        stage('checkout'){
            steps {
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
                        bat 'sonar-scanner.bat -X -Dsonar.host.url=http://localhost:9000 \
                                -Dsonar.login=27260e67644bccebaf08bbb4fa5a1450218a965f \
                                -Dsonar.projectKey=CI \
                                -Dsonar.java.binaries=/tmp \
                                -Dsonar.java.libraries=. \
                                -Dsonar.language=java,js'
                        //bat 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.8.0.2131:sonar'
                        //bat 'mvn clean verify sonar:sonar'
                        //bat "${mvn}/bin/mvn clean verify sonar:sonar"
                        //bat 'mvn sonar:sonar -X \
                           //-Dsonar.projectKey=CI \
                            //-Dsonar.java.binaries=/tmp \
                            //-Dsonar.java.libraries=. \
                            //-Dsonar.language=java,js \
                            //-Dsonar.host.url=http://localhost:9000 \
                          //  -Dsonar.login=27260e67644bccebaf08bbb4fa5a1450218a965f'
                        //bat 'mvn clean package sonar:sonar -X'

                    }
                }
            }
            /*post {
                always{
                    dir("${WORKSPACE}") {
                        success {
                            to: 'ernesto.jimenez@softtek.com',
                            subject:"Test-SonarQube",
                            body:"Test-SonarQube is completed: "${WORKSPACE}", More details at: ${SonarQubeUrl}"
                        }
                        failure {
                            to: "ernesto.jimenez@softtek.com",
                            subject:"Test-SonarQube",
                            body:"Test-SonarQube is completed: "${WORKSPACE}", More details at: ${SonarQubeUrl}"
                        }
                    }
                }
            }*/
        }
        stage('Delivery'){
            steps{
                archiveArtifacts artifacts: 'Reports.txt', followSymlinks: false
            }
        }
        stage('E-mail'){
            steps{
                emailext body: 'Test-SonarQube is completed: ${WORKSPACE}, More details at: ${SonarQubeUrl}', subject: 'test sonarqube', to: 'ernesto.jimenez@softtek.com'
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}

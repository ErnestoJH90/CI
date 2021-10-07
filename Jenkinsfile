
pipeline{
    agent any
    
    tools{
        maven 'Mvn'
    }
    environment {
            POM_FILE = readMavenPom()
            ART_VERSION = "${params.TAG}"
            GRP_ID = POM_FILE.getGroupId()
            ART_ID = POM_FILE.getArtifactId()
            RUNTIME_VERSION = "${POM_FILE.properties['app.runtime']}"
            VERSION_ID = POM_FILE.getVersion()
            PACKAGING_ID = POM_FILE.getPackaging()
            DOWNLOAD_ARTIFACT_AUTH = credentials('DOWNLOAD_ARTIFACT_AUTH')
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
        //stage('Unit test') {
          //  when {
                //skip build
            //    not {
              //      changelog '\\[nb\\]'
                //}
            //}
            //steps {
              //  configFileProvider([configFile(fileId: "${env.MV_CONF}", variable: 'MAVEN_SETTINGS')]) {
                //        bat "mvn test -s" //${MAVEN_SETTINGS}"
                  //      dir('target/site/munit/coverage') {
                        //publishHTML([allowMissing: true, alwaysLinkToLastBuild: false, keepAll: false, reportDir: './', reportFiles: 'summary.html', reportName: 'HTML Summary Report', reportTitles: 'Summary'])
                    //}
                //}
            //}
        //}
        stage('SonarQube Scanner'){
            steps{
                script{
                    def scannerHome = tool 'SonarQubeScanner';
                    def SonarQubeUrl = 'http://localhost:9000/dashboard?id='; 
                    withSonarQubeEnv('SonarQube'){
                        bat 'sonar-scanner.bat -X -Dsonar.host.url=http://localhost:9000 \
                                -Dsonar.login=27260e67644bccebaf08bbb4fa5a1450218a965f \
                                -Dsonar.projectKey=$ART_ID \
                                -Dsonar.language=java,js \
                                -Dsonar.java.libraries=. \
                                -Dsonar.java.binaries=/tmp '
                                
                        bat 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.8.0.2131:sonar'
                        bat 'mvn clean verify sonar:sonar'
                        bat "${mvn}/bin/mvn clean verify sonar:sonar"
                        bat 'mvn sonar:sonar -X \
                                -Dsonar.projectKey=CI \
                                -Dsonar.sources=. \
                                -Dsonar.java.binaries=/tmp \
                                -Dsonar.java.libraries=. \
                                -Dsonar.language=java,js \
                                -Dsonar.host.url=http://localhost:9000 \
                                -Dsonar.login=27260e67644bccebaf08bbb4fa5a1450218a965f'
                        bat 'mvn clean package sonar:sonar -X'
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
                emailext body: 'More details at: http://localhost:9000/dashboard?id=ErnestoJH90_CI', subject: 'Test-SonarQube', to: 'ernesto.jimenez@softtek.com'
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}

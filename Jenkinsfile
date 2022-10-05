def SonarQubeUrl = 'http://localhost:9000/dashboard?id=ErnestoJH90_CI'
pipeline{
    agent any
    
    tools{
        maven 'Mvn'
        //groovy 'Groovy'
    }
    //environment {
            //POM_FILE = readMavenPom()
            //ART_VERSION = "${params.TAG}"
            //GRP_ID = POM_FILE.getGroupId()
            //ART_ID = POM_FILE.getArtifactId()
            //RUNTIME_VERSION = "${POM_FILE.properties['app.runtime']}"
            //VERSION_ID = POM_FILE.getVersion()
            //PACKAGING_ID = POM_FILE.getPackaging()
            //DOWNLOAD_ARTIFACT_AUTH = credentials('DOWNLOAD_ARTIFACT_AUTH')
        //}
    stages{
        stage('checkout'){
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'ErnestoJh', url: 'https://bitbucket.org/ErnestoJH/devops-pipeline/src/development/']]])
                //checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'JenkinsDocker2', url: 'https://github.com/ErnestoJH90/CI.git']]])
            }
        }
        stage('Mvn version'){
            steps{
                bat 'mvn --version'
                bat 'dotnet --version'
            }
        }
        stage('Build'){
            steps{
                bat 'cd Demo && mvn clean package'
                bat 'java -cp Demo/target/Demo-1.0-SNAPSHOT.jar com.Demo.app.App > Reports.txt'
            }
        }
        stage('Unit test') {
            steps {
              //  configFileProvider([configFile(fileId: "${env.MV_CONF}", variable: 'MAVEN_SETTINGS')]) {
                        bat "mvn test " //${MAVEN_SETTINGS}"
                  //      dir('target/site/munit/coverage') {
                        //publishHTML([allowMissing: true, alwaysLinkToLastBuild: false, keepAll: false, reportDir: './', reportFiles: 'summary.html', reportName: 'HTML Summary Report', reportTitles: 'Summary'])
                    //}
                //}
            }
        }
        stage('SonarQube'){
            steps{
                script{
                    def scannerHome = tool 'SonarQubeScanner';
                    //def SonarQubeUrl = 'http://localhost:9000/dashboard?id=';  
                    withSonarQubeEnv('SonarQube'){
                       bat 'mvn clean verify sonar:sonar \
                            -Dsonar.login=5c0c630e47d1263d7480869ee2a2d84afcb4096b \
                            -Dsonar.sources=Demo \
                            -Dsonar.projectKey=CI \
                            -Dsonar.language=java,js \
                            -Dsonar.java.libraries=. \
                            -Dsonar.java.binaries=Demo/target \
                            -Dsonar.java.binaries=Demo/src \
                            -Dsonar.jacoco.reportPath=target/site/jacoco/jacoco.xml \
                            -Dsonar.host.url=http://localhost:9000 '
                    }
                }
            }
            /*post {
                always{
                    dir("${WORKSPACE}") {
                        success {
                            //emailext to: 'ernesto.jimenez@softtek.com',
                              //       subject: 'Test-SonarQube',
                                //     body: 'Test-SonarQube is completed: '${WORKSPACE}', More details at: '${SonarQubeUrl}''
                                     
                            to: "ernesto.jimenez@softtek.com",
                            subject:"Test-SonarQube",
                            body:"Test-SonarQube is completed: "${WORKSPACE}", More details at: '${SonarQubeUrl}'"
                        }
                        failure {
                            emailext to: 'ernesto.jimenez@softtek.com',
                                     subject: 'Test-SonarQube',
                                     body: 'Test-SonarQube is completed: '${WORKSPACE}', More details at: '${SonarQubeUrl}''
                            //to: "ernesto.jimenez@softtek.com",
                            //subject:"Test-SonarQube"
                            //body:"Test-SonarQube is completed: "${WORKSPACE}", More details at: ${SonarQubeUrl}"
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
                emailext body: 'More details at:http://localhost:9000/dashboard?id=ErnestoJH90_CI', recipientProviders: [buildUser()], subject: 'Jenkins', to: 'ernesto.jimenez@softtek.com'
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
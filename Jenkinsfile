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
                bat 'cd my-app && mvn clean package'
                bat 'java -cp my-app/target/mycompany-1.0-SNAPSHOT.jar com.mycompany.app.App > Reports.txt'
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
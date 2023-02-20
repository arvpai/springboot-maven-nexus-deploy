pipeline {
    agent any
    tools {
        maven 'maven-3.9.0'
    }
    stages{
        stage('Build'){
            steps{
                 sh script: 'mvn clean package'
             }
        }
    stage('Sonarqube code quality check') {
             steps {
                    withSonarQubeEnv(installationName :'sonar-9', credentialsId : 'jenkins-maven-poc') {
                    sh 'mvn sonar:sonar'
                         }
                       }
    }
    stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
                       }
                  }
             }
        stage('Upload War To Nexus')
        {
            steps{
                script{
                    def mavenPom = readMavenPom file: 'pom.xml'
                    def nexusRepoName = mavenPom.version.endsWith("SNAPSHOT") ? "springboot-maven-course-micro-svc-snapshot" : "springboot-maven-course-micro-svc-release"
                nexusArtifactUploader artifacts:
                [
                    [
                        artifactId: 'springboot-maven-course-micro-svc',
                        classifier: '',
                        file: "target/springboot-maven-course-micro-svc-${mavenPom.version}.war",
                        type: 'jar'
                    ]
                ],
                        credentialsId: 'nexus3',
                        groupId: 'springboot-maven-course-micro-svc',
                        nexusUrl: '35.89.232.203:8081',
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        repository: nexusRepoName,
                        version: "${mavenPom.version}"
                    }
                }
        }
    }
}
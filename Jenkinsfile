pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded later
            }
        }
      stage('Unit Tests - JUnit and Jacoco ') {
            steps {
              sh "mvn test"
            }
            post {
              always {
                junit 'target/surefire-reports/*.xml'
                jacoco execPattern: 'target/jacoco.exec'
              }
            }
        }
        stage('Mutation Tests - PIT') {
            steps {
              sh "mvn org.pitest:pitest-maven:mutationCoverage"
            }
            post {
              always {
                pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
              }
            }
        }
        stage('SonarQube - SAST') {
            steps {
              sh "mvn clean verify sonar:sonar -Dsonar.projectKey=jenkins-pipeline -Dsonar.host.url=http://localhost:9000 -Dsonar.login=sqp_3f0edd888a8480d32ffee4bc58ac15a96246ee21"
            }
        }  
        stage('Docker Build and push') {
            steps {
              withDockerRegistry([credentialsId:"docker-hub", url: ""]){
                sh "printenv"
                sh 'docker build -t anand82msc/numeric-app:""$GIT_COMMIT"" .'
                sh 'docker push anand82msc/numeric-app:""$GIT_COMMIT""'
                sh 'docker rmi anand82msc/numeric-app:""$GIT_COMMIT""'  
              }
              
            }
        }   
    }
}
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
        // stage('SonarQube analysis') {
        //     steps {
        //       withSonarQubeEnv(credentialsId: 'sonarqube-auth-token', installationName: 'SonarQube') {
        //       sh "mvn clean verify sonar:sonar -Dsonar.projectKey=jenkins-pipeline -Dsonar.host.url=http://localhost:9000 -Dsonar.login=sqp_f1a40e7a6072a93a99bd1a4cc1765bce8eea500f"
        //     }
        //     timeout(time: 3, unit: 'MINUTES'){
        //       script{
        //         waitForQualityGate abortPipeline: true  
        //       }
        //     }
        //   }
        // }
        stage('SonarCube - SAST') {
            steps {
              sh "mvn clean verify sonar:sonar -Dsonar.projectKey=jenkins-pipeline -Dsonar.host.url=http://localhost:9000 -Dsonar.login=sqp_f1a40e7a6072a93a99bd1a4cc1765bce8eea500f"
            }
        }  
        // stage('Docker Build and push') {
        //     steps {
        //       withDockerRegistry([credentialsId:"docker-hub", url: ""]){
        //         sh "printenv"
        //         sh 'docker build -t anand82msc/numeric-app:""$GIT_COMMIT"" .'
        //         sh 'docker push anand82msc/numeric-app:""$GIT_COMMIT""'
        //         sh 'docker rmi anand82msc/numeric-app:""$GIT_COMMIT""'  
        //       }
              
        //     }
        // }   
    }
}
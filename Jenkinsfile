pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' 
            }
        }

      stage( 'Unit Tests - JUnit and Jacoco') {
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
            sh "mvn sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.projectName='numeric-application' -Dsonar.host.url=http://devsecops-projects.eastus.cloudapp.azure.com:9000 -Dsonar.token=sqp_b737f388ff1f4d1a72e31b101c403e58776ca9aa"
          }
        }

      stage( 'Vulnerability Scan - Docker') {
        steps {
          parallel(
            "Dependency Scan": {
                sh "mvn dependency-check:check"            
            },
           "Trivy Scan": {        
              sh "bash trivy-docker-image-scan.sh"           
            },        
           "OPA Conftest": {        
              sh 'docker run --rm -v $(pwd):/project openpolicyagent/conftest test --policy opa-docker-security.rego Dockerfile' 
            }  
          )  
       }
     }  

      stage('Docker Build and Push') {
        steps {
          withDockerRegistry([credentialsId: "docker-hub", url: ""]) {
            sh 'printenv'
            sh 'sudo docker build -t devsecopsproject/numeric-app:""$GIT_COMMIT"" .'
            sh 'docker push devsecopsproject/numeric-app:""$GIT_COMMIT""'
          }
        }
      }

      stage('Vulnerability Scan - Kubernetes') {
      steps {
        sh 'docker run --rm -v $(pwd):/project openpolicyagent/conftest test --policy opa-k8s-security.rego k8s_deployment_service.yaml'
      }
    }

      stage('Kubernetes Deployment - DEV') {
        steps {
          withKubeConfig([credentialsId: 'kubeconfig']) {
            sh "sed -i 's#replace#devsecopsproject/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
            sh "kubectl apply -f k8s_deployment_service.yaml"
          }
        }
      }    
    }
}
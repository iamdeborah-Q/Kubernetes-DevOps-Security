## INTEGRATING AND MONITORING SECURITY WITHIN APPLICATIONS, DOCKER, AND KUBERNETES USING DevSecOps APPROACH



This project aims to demonstrate the integration and monitoring of security practices within applications, Docker, and Kubernetes environments using the DevSecOps approach. By adopting this methodology, we can ensure robust security measures throughout the software development and deployment lifecycle (SDLC).


# Introduction
In today's digital landscape, security is a top concern for organizations. The integration and monitoring of security within applications, Docker, and Kubernetes environments play a vital role in ensuring the protection and integrity of software systems. This project focuses on leveraging the DevSecOps approach to seamlessly integrate security practices and monitor security throughout the entire software development and deployment process.

# Key Features
  1. Secure coding practices to minimize vulnerabilities within applications.
  2. Threat modeling exercises to identify potential risks and implement proactive security controls.
  3. Security testing, including static application security testing (SAST), dynamic application security testing (DAST), and penetration testing.
  4. Secure container image creation and management in Docker.
  5. Implementing container security policies within Kubernetes clusters.
  6. Secure management of sensitive information using Kubernetes secrets.
  7. Logging and monitoring solutions for detecting and responding to security events.
  8. Incident response planning and automation for efficient security incident handling.
  9. Continuous compliance checks to ensure adherence to security best practices and compliance standards.

# Tools and Technologies
This project utilizes the following tools and technologies:

1. Microservices: Springboot and Nodejs
2. Docker: Containerization platform for building and managing applications.
3. Kubernetes: Container orchestration platform for deployment and management of containers.
4. Jenkins:
5. Maven: Software project management and conprehension tool. 
6. Github
7. DevSecOps: Methodology that integrates security practices into the software development and deployment lifecycle.
Various security tools such as Talisman, OPA, PIT Test, SonarQube, OWASP ZAP, Istio, Kube-bench, Kiali, Trivy, Falco, JMeter, Denpendency, Slack, Prometheus, and Grafana etc.

# DevOps and DevSecOps




Intergrating Security into Every Stage of the SDLC
1. SAST
2. DAST
3. OPA
4. SCA
5. LAC

Tools to expolor in this project
i. Talisman
ii. P

# STEP 1: Set Up A  DevOps Pipeline
  
  a. Setup  a free Azure Account and create a virtual machine 
  
![image](https://github.com/iamdeborah-Q/Kubernetes-DevOps-Security/assets/122198373/f8abec5b-468f-459b-bccb-a587cb42ddbd)
 

  b. Install the following Software Docker, Kubernetes, Java/Maven and Jenkins/Plugins

    Jenkins Installation
          sudo apt update
          sudo apt install openjdk-11-jre
          java -version
          
          curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
            /usr/share/keyrings/jenkins-keyring.asc > /dev/null
          echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
            https://pkg.jenkins.io/debian binary/ | sudo tee \
            /etc/apt/sources.list.d/jenkins.list > /dev/null
          sudo apt-get update
          sudo apt-get install jenkins

          sudo apt install -y maven
          mvn -v 

![image](https://github.com/iamdeborah-Q/Kubernetes-DevOps-Security/assets/122198373/2745dd96-8d8f-49aa-82c7-6ebee93b3e4c)



  d. Configur Jenkins and install the following plugins 
          

        Intergrate Github into Jenkins using webhook to automate the build process
        Run Mavin build

![image](https://github.com/iamdeborah-Q/Kubernetes-DevOps-Security/assets/122198373/ddcadc2f-5244-41de-bb2c-b1750327145c)

Mavin Build Stage

![image](https://github.com/iamdeborah-Q/Kubernetes-DevOps-Security/assets/122198373/f1c79f61-5794-49af-973a-37926fae257b)


e. Run a Unit Test with JaCoCo plugin, Build and push a docker image within the Jenkins Pipeline and deploy Kuberntes cluster 
    
 Unit testing is done by Developers to ensure individual components funtionalities are working fine. This helps in finding early bugs, save cost and improves code quality.
 JaCoCo (Java Code Coverage) is used to measure how mny lines of code are tested and which lines are yet to be tested.
   Note: add docker credentials to jenkins

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

  Build and push a docker image 
  
                         stage('Docker Build and Push') {
                            steps {
                              withDockerRegistry([credentialsId: "docker-hub", url: ""]) {
                                sh 'printenv'
                                sh 'sudo docker build -t devsecopsproject/numeric-app:""$GIT_COMMIT"" .'
                                sh 'docker push devsecopsproject/numeric-app:""$GIT_COMMIT""'
                              }
                            } 

  KubernetesDeploymenty 

                        stage('K8S Deployment - DEV') {
                            steps {
                              parallel(
                                "Deployment": {
                                  withKubeConfig([credentialsId: 'kubeconfig']) {
                                    sh "bash k8s-deployment.sh"
                                  }
                                }
 
  ![image](https://github.com/iamdeborah-Q/Kubernetes-DevOps-Security/assets/122198373/a7cd073b-fc64-4894-b4dc-336e169de5cb)

  
  
  
  Complete DevOps pipeline

  ![image](https://github.com/iamdeborah-Q/Kubernetes-DevOps-Security/assets/122198373/54c3fa7a-59d3-4e08-b811-450f17473a73)



# Step 2: DevSecOps Pipeline (Adding Security into the DevOps Pipeline)

  The following tools will explore to secure the DevOps pipeline
  
              Git Hook - Talisman
              Testing:
                  Mutation testing 
                  Integrating
                  
              Security scan
                Dependency Check
                Trivy - images
                OPA - Dockerfile
                Kubesec - k8s
                
              Static Application Security Testing (SAST) - SonarQube
              Dynamic Application Security Testing (DAST)

    
   a. Git Hooks and Talisman


          Install Talisman to a single project

          Talisman Installation
                curl https://thoughtworks.github.io/talisman/install.sh > ~/install-talisman.sh
                chmod +x ~/install-talisman.sh
                cd your-git-project
                ~/install-talisman.sh

            Create sec_files
                mkdir sec_files && cd sec_files
                echo "username=sidd-harth" > file1
                echo "secure-password123" > password.txt
                echo "apikey=AizaSyCqhjgrPtr_La56sdUkjfav_laCqhjgrPtr_2s" > file2
                echo "base64encodedsecret=cGFzc3dvcmQtaXMtcXdlcnR5MTIzCg==" > file3

  ![image](https://github.com/iamdeborah-Q/Kubernetes-DevOps-Security/assets/122198373/c7bed0d2-feac-4586-b601-8841a1fd510e)

    
                                          
                                          
                      


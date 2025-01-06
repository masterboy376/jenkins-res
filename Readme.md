## **Jenkins Installation**

1. **Install Docker and Docker Compose**: Make sure you have Docker and Docker Compose installed on your machine. You can download Docker from [Docker's official website](https://www.docker.com/products/docker-desktop).

2. **Create a Docker Compose File**: Create a file named `docker-compose.yml` with the following configuration:

    ```yaml
    version: '3.7'
    services:
    jenkins:
        image: jenkins/jenkins:lts
        container_name: jenkins
        ports:
        - "8080:8080"
        - "50000:50000"
        volumes:
        - jenkins_home:/var/jenkins_home
        environment:
        - JAVA_OPTS=-Djenkins.install.runSetupWizard=false
        networks:
        - jenkins_network

        networks:
        jenkins_network:
            driver: bridge

        volumes:
        jenkins_home:
            driver: local
    ```
    OR you can create a jenkins server with an agent (along with some extra attributes)
    ```yaml
   version: '3.7'
    
    services:
      jenkins-master:
        image: jenkins/jenkins:lts-jdk17
        container_name: jenkins-master
        restart: unless-stopped
        user: 1000:1000
        ports:
          - "8080:8080"
          - "50000:50000"
        volumes:
          - jenkins_home:/var/jenkins_home:rw
        environment:
          - JAVA_OPTS=-Dhudson.security.csrf.GlobalCrumbIssuerStrategy=true -Djenkins.security.SystemReadPermission=true
        networks:
          - jenkins_network
        security_opt:
          - no-new-privileges:true
        read_only: true
        tmpfs:
          - /tmp:size=2G
        healthcheck:
          test: ["CMD-SHELL", "curl -f http://localhost:8080/login || exit 1"]
          interval: 1m30s
          timeout: 10s
          retries: 3
        deploy:
          resources:
            limits:
              cpus: '2'
              memory: 2G
            reservations:
              cpus: '1'
              memory: 1G
    
      jenkins-agent:
        image: jenkins/ssh-agent
        container_name: jenkins-agent
        restart: unless-stopped
        expose:
          - "22"
        volumes:
          - jenkins_agent:/home/jenkins/agent:rw
          - type: bind
            source: ./jenkins_agent_keys
            target: /home/jenkins/.ssh
        environment:
          - SSH_PUBLIC_KEY_DIR=/home/jenkins/.ssh
        networks:
          - jenkins_network
        security_opt:
          - no-new-privileges:true
        tmpfs:
          - /tmp:size=2G
        deploy:
          resources:
            limits:
              cpus: '1'
              memory: 1G
            reservations:
              cpus: '0.5'
              memory: 512M
    
    networks:
      jenkins_network:
        driver: bridge
    
    volumes:
      jenkins_home:
        driver: local
      jenkins_agent:
        driver: local
    ```

4. **Run Docker Compose**: Open your terminal, navigate to the directory containing your `docker-compose.yml` file, and run the following command:

    ```sh
    docker-compose up -d
    ```

5. **Access Jenkins**: Open your web browser and go to `http://localhost:8080`. You should see the Jenkins setup wizard.

6. **Unlock Jenkins**: The setup wizard will ask for an initial admin password, which can be found in the Docker container logs. Run the following command to get the password:

    ```sh
    docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
    ```

7. **Complete Setup**: Enter the password, install the recommended plugins, and set up your admin user.

8. **Start Using Jenkins**: You can now start creating jobs and configuring your CI/CD pipelines.

## **Jenkins pipelines**

1. "Pipeline with groovy syntax": a simple Jenkinsfile usually looks like:
    ```
    pipeline {
        agent { 
            node {
                label 'built-in'
                }
          }
        triggers {
            pollSCM '* * * * *'
        }
        stages {
            stage('Build') {
                steps {
                    echo "Building.."
                    sh '''
                    chmod +x dummy.sh
                    '''
                }
            }
            stage('Test') {
                steps {
                    echo "Testing.."
                    sh '''
                    bash dummy.sh
                    '''
                }
            }
            stage('Deliver') {
                steps {
                    echo 'Deliver....'
                    sh '''
                    echo "doing delivery stuff.."
                    '''
                }
            }
        }
    }
    ```

**Jenkins Installation**

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

3. **Run Docker Compose**: Open your terminal, navigate to the directory containing your `docker-compose.yml` file, and run the following command:

    ```sh
    docker-compose up -d
    ```

4. **Access Jenkins**: Open your web browser and go to `http://localhost:8080`. You should see the Jenkins setup wizard.

5. **Unlock Jenkins**: The setup wizard will ask for an initial admin password, which can be found in the Docker container logs. Run the following command to get the password:

    ```sh
    docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
    ```

6. **Complete Setup**: Enter the password, install the recommended plugins, and set up your admin user.

7. **Start Using Jenkins**: You can now start creating jobs and configuring your CI/CD pipelines.

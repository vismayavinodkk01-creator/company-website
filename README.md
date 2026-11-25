# TechNova Solutions

## Project Overview
A professional and responsive company landing page.

## Technologies Used
- HTML5
- CSS3
- Docker
- Nginx
- Jenkins
- GitHub

## Project Structure

company-website/
- index.html
- style.css
- Dockerfile
- Jenkinsfile
- README.md

## Docker Usage

The Dockerfile uses the official Nginx Alpine image.

It copies the website files into the Nginx web directory.

The application runs inside a Docker container.

## Jenkins CI/CD Process

1. Developer updates code in GitHub.
2. Jenkins fetches the latest code.
3. Build server builds the Docker image.
4. Docker image is pushed to Docker Hub.
5. Deployment server pulls the latest image.
6. Old container is stopped and removed.
7. New container is started.
8. Website is verified.

## Application
Company Landing Page developed using HTML and CSS.

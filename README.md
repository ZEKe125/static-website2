
# Pipeline for a Secure Static Website with CloudFront

This project guides you through the process of setting up a secure pipeline and deploying a secured static website to Amazon S3 with a CloudFront distribution.

## Technologies Implemented in the Project

- Jenkins
- GitHub
- Amazon S3
- CloudFront
- Route 53
- AWS Certificate Manager (ACM) SSL

## GitHub Steps

1. Find a static website you want to host (e.g., tooplate.com).
2. Copy or fork the project into a private repository hosted on GitHub.
3. Create a Personal Access Key for reading and writing to this repository; you will use it later in the Jenkins server.
4. Create a Webhook for this repository triggered when the `main` branch is modified; this will be the pipeline trigger.

## Amazon S3 Steps

1. Log into the AWS console with permissions to create and modify S3.
2. Choose a region that you will be working in; it's important to be in the same region as the other AWS services you will be working with. Stick with the chosen region for this project.
3. Create an S3 bucket with a name of your choice (must be globally unique). Leave public access blocked but enable versioning.
4. A bucket policy will be generated in the CloudFront steps.

## Route 53 Steps

1. Register a domain of your choice (recommended) or import it from your domain service of choice.
2. Create a public-facing hosted zone for your domain using defaults.

## ACM (AWS Certificate Manager) Steps

1. Request a new SSL Certificate from Amazon like this: "*.your-domain.com".

## CloudFront Steps

1. Create a CloudFront distribution with the origin set to `index.html` in your S3 bucket.
2. Add the SSL certificate to the distribution to enable secure HTTPS requests.
3. Copy the generated bucket policy into your S3 permissions/bucket-policy tab. This allows CloudFront to access the content in S3 without allowing public access.

## Route 53 Steps (Again)

1. Create A and AAAA Alias records for the CloudFront distribution with the subdomain of your choice (e.g., `web.your-domain.com`).
2. A-Record for IPv4 and AAAA-Record for IPv6.

## Jenkins Steps

1. Launch an EC2 instance (t2.micro should be enough) with an Amazon Linux AMI. Make sure you launch it in a VPC with an internet gateway.
2. Define the Security Group to allow TCP traffic on ports 80, 443, and 8080.
3. Install Java (a Jenkins prerequisite).
4. Install Jenkins.
5. Start Jenkins.
6. In your web browser, use the public IP address of the machine you are using (e.g., `http://77.77.32.2:8080/`). This will give you access to the visual interface for the Jenkins server.
7. Define a Jenkins user account for yourself.'
8. Install recommended plugins as well as Pipeline-AWS and Generic Webhooks plugins.
9. Navigate to manage jenkins tab, and define credentials for AWS and GitHub.
10. Define a new pipeline item
11. Configure the new pipeline to trigger with Webhook.
12. Define this a pieline or Jenkinsfile.

---

```Groovy
pipeline {
    agent any
    
    environment {
        REPO_URL = 'https://github.com/ZEKe125/static-website2.git'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM',
                    branches: [[name: 'main']],
                    userRemoteConfigs: [[credentialsId: 'your-github-credentials', url: REPO_URL]]
                ])
            }
        }

        stage('Build') {
            steps {
                // Replace with your actual build command
                // sh 'mvn clean package' 
            }
        }

        stage('Test') {
            steps {
                // Replace with your actual test command
                // sh 'mvn test' 
            }
        }

        stage('Deploy') {
            steps {
                script {
                    withAWS(region: 'your-region', credentials: 'your-aws-credentials') {
                        s3Upload(file: '/var/lib/jenkins/workspace/your-pipeline-name', bucket: 'your-bucket-name')
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful'
            // You can add notifications or other success actions here
        }
        failure {
            echo 'Deployment failed'
            // You can add notifications, cleanup tasks, or other failure actions here
        }
    }
}

```

----

# If you are a begginer here is supplemental documentation

## GitHub Steps

1. To find articles on GitHub setup and usage, visit the [GitHub Docs](https://docs.github.com/en).
2. You can also find tutorials on GitHub in various programming and DevOps blogs and forums.

## Amazon S3 Steps

1. For AWS S3 documentation, visit the [AWS S3 documentation page](https://docs.aws.amazon.com/s3/index.html).
2. AWS blogs and forums often have articles and guides on using S3.

## Route 53 Steps

1. For AWS Route 53 documentation, visit the [AWS Route 53 documentation page](https://docs.aws.amazon.com/route53/index.html).
2. AWS blogs and forums often have articles on DNS setup using Route 53.

## ACM (AWS Certificate Manager) Steps

1. To understand ACM SSL certificate management, visit the [AWS ACM documentation page](https://docs.aws.amazon.com/acm/index.html).
2. AWS blogs and forums often have articles on ACM and SSL certificate setup.

## CloudFront Steps

1. For CloudFront documentation, visit the [AWS CloudFront documentation page](https://docs.aws.amazon.com/cloudfront/index.html).
2. AWS blogs and forums often have articles on setting up CloudFront for static websites.

## Jenkins Steps

1. Jenkins documentation provides extensive information on installation and setup: [Jenkins Documentation](https://www.jenkins.io/doc/).
2. For Jenkins plugins like Pipeline-AWS and Generic Webhooks, check the [Jenkins Plugin Index](https://plugins.jenkins.io/).

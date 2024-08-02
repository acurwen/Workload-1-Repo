## Purpose of Workload

The purpose of this workload was to deploy a bank application using Elastic Beanstalk. I completed this workload twice and on the second round, improved my understanding of what these steps do and mean. I also worked with many classmates on troubleshooting the 502 error and testing our knowledge about application deployment using Elastic Beanstalk environments.

*****Need to rename GitHub repo to something more descriptive**

## Steps Taken & Why

1. **Cloned the Kura Lab Workload 1 repository to my personal GitHub.**

Cloning the Kura repository into my own repository ensured that I had a local copy of the application code. So, I am free to make any changes to the files in my local copy without affecting the original files in the Kura repository, which my classmates are also cloning.

1. For practice, I cloned the Kura repository twice:
    1. Manually imported the [Kura repository](https://github.com/kura-labs-org/C5-Deployment-Workload-1) URL to a new repository I created in GitHub called “[C5-Deployment-Instructions-Clone](https://github.com/acurwen/C5-Deployment-Instructions-Clone)”
    2. Utilized my Student Instance:
        1. **git init** - Initialized a new repository within my instance called /home/ubuntu/bankapp
        2.  **git pull [kura_repo_URL]** - pulled in the repository I wanted to clone 
        3. created a new repository in my GitHub called “[Workload-1-Repo](https://github.com/acurwen/Workload-1-Repo)”
        4. **git remote add origin [Workload-1-Repo URL]** - adding my new repository in Git called “origin”
        5. **git branch -M main** - setting the branch to main
        6. **git push -u origin main** - pushing my remote repository from my Student Instance into my Workload-1-Repo in GitHub (push your local branch main to the remote repository origin) ****edit this**

1. **Created an EC2**

We are creating this instance to host Jenkins, which we will install next.

Created a new EC2 Instance (called “EC2 for Hosting Bank Application”) following these [instructions](https://github.com/kura-labs-org/AWS-EC2-Quick-Start-Guide/blob/main/AWS%20EC2%20Quick%20Start%20Guide.pdf). Here I also made a security group with rules that included SSH (Port 22), HTTP (Port 80) and Jenkins (Port 8080).

1. **Installed Jenkins onto the EC2**

We are installing Jenkins onto our instance to help automate our code that we have added to our git repository.

Connected to new EC2 instance (EC2 for Hosting Bank Application) and installed Jenkins. Installation was successful.

*****Insert screenshot**

1. **Created a first admin user account on Jenkins**

Created a first admin user on Jenkins so that we could have access to Jenkins tools, specifically creating a multi-branch pipeline

I used the IP address for my EC2 instance (3.88.60.42) and the port for Jenkins (8080) to find the log in page. 3.88.60.42:8080 successfully took me to the ‘create a user’ Jenkins page.

The Jenkins page prompted me for the user admin password that I could find in my EC2 instance. In the instance I ran sudo cat /var/lib/jenkins/secrets/initialAdminPassword and located my password: c031d3069c35421dbfb54fac8c150781

Then I created an account.

1. **Created a Multi-Branch pipeline in Jenkins**

Created a pipeline for each branch in my git repository. This pipeline handles building, testing, and deploying that branch.

Named the multi-branch pipeline “MultiBranch Pipeline for Bank Application”. Used my GitHub username and personal access token (pulled and saved from GitHub) as a password

1. **Connected GitHub repository to Jenkins**

Adding Jenkins automation features to our application code

I entered the [URL](https://github.com/acurwen/Workload-1-Repo.git) of my “Workload-1-Repo” repository in GitHub. URL was validated!

*****Insert screenshot**

1. **Build Configuration**

I created the build c**onfiguration** and ensured the "Build Configuration" section said "Mode: by Jenkinsfile" and "Script Path: Jenkinsfile”.

Saved the build which triggered an automatic start.

The build completed successfully.

*****Insert screenshot of successful build**

*****Answer these questions**

Did the build stages successfully complete? If not, why? How did you resolve the issue? What did each stage do? The steps of the build were cloning the git repository, building and testing.

*****Insert screenshot of builds**

1. **Created Environment in Elastic Beanstalk**

We created this environment to host our banking application on.

First, I created my “IAM” service roles: aws-elasticbeanstalk-service-role and Elastic-EC2. These roles help grant permissions that are needed for the environment to host and manage the bank application.

I also added in my security group I created for my EC2 instance while creating this environment. The security group has rules that included SSH (Port 22), HTTP (Port 80) and Jenkins (Port 8080) which ensures the correct access is enforced for my environment.

1. **Uploaded application zip file to environment and launched**

I downloaded the contents of “[Workload-1-Repo](https://github.com/acurwen/Workload-1-Repo)” repository and rezipped the application files on my local machine’s file explorer to ensure there was no parent directory included. I then uploaded the edited zip file to my environment in AWS Elastic Beanstalk. Launch was a success!

*****Insert screenshot of successful launch**

## System Design Diagram

### created in draw.io

## Issues & Troubleshooting

### Jenkins:

Initially I was confused on how to log into Jenkins — I didn’t quite know where to go. After searching on Google, I learned that you just need your IP address, Jenkin’s port of 8080 and a “:” in between.

In my first iteration of this project, I created an account in Jenkins, but didn’t save my password. When I needed to log in again to take screenshots or understand the stages of the build, I was unable to (there is no recover password option.)  I even searched on Google for different Jenkins pages, found one and tried both recovering my account there and creating a new account. However, I realized I was on a discussions page of Jenkins — not where I was supposed to be. Because I did this project a second time, I made sure to save my password when creating a new account.

When I got into Jenkins, it prompted me for the user admin password that I could find in my EC2 (EC2 for Hosting Bank Application) at path: /var/lib/jenkins/secrets/initialAdminPassword. However, at first I couldn’t cd into that path because I didn’t have permission to enter the “secrets” directory. I found a workaround where I first cd’d into the root directory, and from there, ran a sudo cat command on the path: sudo cat /var/lib/jenkins/secrets/initialAdminPassword

### Elastic Beanstalk:

When trying to create the environment for the bank app, I got an error that managed monitoring was not available for 'BASIC' health reporting. The instructions stated to pick “BASIC” and not "ENHANCED", so I unchecked the option for managed monitoring .

*****Insert screenshot of error**

Received 502 Bad Gateway nginx error when trying to connect to my online bank application.

*****Insert screenshot of error**

Looked this error up and found that it means my proxy nginx could not get a proper response from the upstream server. I looked at my logs via the  Elastic Beanstalk environment page.

However, I had trouble accessing my logs at first because nothing showed up in that window. Then I realized I had to navigate to the drop-down menu and press ‘last 100 lines’ and then the logs were available for download.

I scanned my logs to understand what went wrong:

At first I looked at the very last lines and thought it was a port issue since the logs stated that a connection was refused while connecting to upstream. I tried looking at the config files within my EC2 instance to see which ports were being listened to, and even created and added in new security group that added port 443 to the environment configurations, but that wasn’t the issue.

Going back to the logs, I kept scanning to where I kept seeing ‘ERROR’. I noticed three process starting and stopping due to an error and they were all followed by the same line: 

`ModuleNotFoundError: No module named 'application'`

*****Insert screenshot of logs**

The only place I remember seeing application was in the repository for our application code and then Nicole provided a super helpful hint about how our upload files have to be configured.

The issue was that I downloaded the zip file directly from GitHub which included a parent directory. The application couldn’t be deployed because the right files couldn’t be reached. I fixed it by rezipping only the application files together, excluding the parent folder. Then I reuploaded my edited zip file and the application ran successfully.

*****Insert screenshot of  bank application**

## Optimization

What are the benefits of using managed services for cloud infrastructure?

1. Don’t have to worry about the code that creates the environment or your instance; you can just worry about your application code
2. Log files are also kept for you to keep track of changes and issues
3. If a new security group is added or deleted, or any other configuration changes are made to your environment or instance, the environment automatically starts up another instance for your application to limit disruptions

What are some issues that a retail bank would face choosing this method of deployment and how would you address/resolve them?

1. Customers not having the right PORT open; utilizing a commonly used port would fix that
2. Bank information is sensitive so ensuring access is secure

What are other disadvantages of using elastic beanstalk or similar managed services for deploying applications?

1. Less customization available for your specific type of application

## Conclusion

I had a lot of fun doing this project. I noticed that working through documentation helps me understand my work better and that it’d probably be helpful to start documentation immediately as I start a project. Working on this workload alongside the RBAC activity helped me become more comfortable with writing commands and scripts in Bash, and also demonstrated how tough main branch + elective branch would be.


#Instructions Below

# Kura Labs Cohort 5- Deployment Workload 1
## Intro to CI/CD

Welcome to Deployment Workload 1!  By now you’ve learned about system designs and the CI/CD Pipeline.  Let’s start putting it all together and see it in action.  

Be sure to document each step in the process and explain WHY each step is important to the pipeline.

## Instructions

1. Clone this repository to your GitHub account
2. Create an EC2

	a. Follow document: [AWS EC2 Quickstart Guide](https://github.com/kura-labs-org/AWS-EC2-Quick-Start-Guide/blob/main/AWS%20EC2%20Quick%20Start%20Guide.pdf) if needed
3. Install Jenkins onto the EC2

	a. Connect to the EC2 terminal

 	b. Enter the following commands to install Jenkins:

```
    $sudo apt update && sudo apt install fontconfig openjdk-17-jre software-properties-common && sudo add-apt-repository ppa:deadsnakes/ppa && sudo apt install python3.7 python3.7-venv
    $sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
    $echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
    $sudo apt-get update
    $sudo apt-get install jenkins
    $sudo systemctl start jenkins
    $sudo systemctl status jenkins

```

If successful, the last command should show the Jenkins service “active (running)”

4. Log into Jenkins

	a. Enter initial admin password

	b. Install suggested plugins

	c. Create first admin user

5. Create a Multi-Branch pipeline

	a. Click on “New Item” in the menu on the left of the page

	b. Enter a name for your pipeline
  
    c. Select “Multibranch Pipeline”
  
    d. Under “Branch Sources”, click “Add source” and select “GitHub”
  
    e. Click “+ Add” and select “Jenkins”
  
    f. Make sure “Kind” reads “Username and password”

    g. Under “Username”, enter your GitHub username

    h. Under “Password” ,enter your GitHub personal access token

To get the GitHub personal access token, first log into GitHub and click on your profile icon on the top right of the page.

i. On the dropdown menu, click on “Settings”

ii. Click on “<> Developer settings at the bottom of the menu on the left of the page

iii. Click on “Personal access tokens” on the menu on the left of the page and select “Tokens (classic)”

iv. Click “Generate new token” and select the classic option

v. Set an expiration date and then select the following "scopes": repo, admin:repo_hook

This token can only be viewed ONCE! Make sure you enter the token properly (or save it) before leaving the page otherwise a new token must be generated!

6. Connect GitHub repository to Jenkins

	a. Enter the repository HTTPS URL and click "Validate"
  
	b. Make sure that the "Build Configuration" section says "Mode: by Jenkinsfile" and "Script Path: Jenkinsfile"
  
	c. Click "Save" and a build should start automatically

Did the build stages successfully complete? If not, why? How did you resolve the issue?  What did each stage do?

7. After successfully completing the build (provide screenshot of successful build in documentation), download the contents of the repository (the one in your personal GitHub NOT the kuralabs repo!) and upload a zip file of the application it to AWS Elastic Beanstalk.
  
	a. First, follow the instructions in this [LINK](https://scribehow.com/shared/How_to_Create_an_AWS_IAM_Role_for_Elastic_Beanstalk_and_EC2__kTg4B7zRRxCp-aYTJc-WLg) for "How to Create an AWS IAM Role for Elastic Beanstalk and EC2" and create the two IAM roles as specified.

    b. Navigate to the AWS Elastic Beanstalk console page

    c. Navigate to the "Environments" page on the left side menu and click on "Create Environment"

    d. Create a "Web server environment" and enter the an Application name (Environment name should auto populate after that)

    e. Choose "Python 3.7" as the "Managed platform"

    f. "Upload your code" by choosing a "local file" and select the zipped application files you created earlier.

    g. Under "Presets", make sure that "Single instance (free tier eligible) is selected and then click "Next"

    h. Select the "Service role" and "EC2 profile" in the appropriate drop down menus and then click "Next"

    i. Select the default VPC and Subnet "us-east-1a" and then click "Next"

    j. Select "General Purpose (SSD) for "Root volume type" and assign it 10 GB.

    k. Ensure that "Single instance" is selected for the "Environment type" and that ONLY "t3.micro" is selected for instance types (remove all others if present) and then click "Next"

    l. Select 'BASIC' health reporting under the monitoring section. NOT "ENHANCED"!

    m. Continue to the "Review" page and then click "Submit".

    n. When the "environment is successfully launched", click on the link provided in the "Domain" and confirm that the application has deployed!

8. Document! All projects have documentation so that others can read and understand what was done and how it was done. Create a README.md file in your repository that describes:

	a. The "PURPOSE" of the Workload, 
	
	b. The "STEPS" taken (and why each was necessary/important, 
	
	c. A "SYSTEM DESIGN DIAGRAM" that is created in draw.io, 
	
	d. "ISSUES/TROUBLESHOOTING" that may or may have occured, 
	
	e. An "OPTIMIZATION" section for that answers the question: What are the benefits of using managed services for cloud infrastructure?  What are some issues that a retail bank would face choosing this method of deployment and how would you address/resolve them? What are other disadvantages of using elastic beanstalk or similar managed services for deploying applications?
	
	f. A "CONCLUSION" statement as well as any other sections you feel like you want to include.

The README.md is a markdown file that has unique formatting.  Be sure to look up how to write in markdown or use a txt to markdown converter. 

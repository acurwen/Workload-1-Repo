## Purpose of Workload

The purpose of this workload was to deploy a retail bank application using Elastic Beanstalk. I completed this workload twice and on the second round, improved my understanding of how we went about deploying the application and was also able to take better screenshots for documentation. I also worked with many classmates on troubleshooting the 502 error.


## Steps Taken & Why

1. **Cloned the Kura Lab Workload 1 repository to my personal GitHub.**

Cloning the Kura repository into my own repository ensured that I had a local copy of the application code. So, I am free to make any changes to the files in my local copy without affecting the original files in the Kura repository, which my classmates are also cloning.

1. For practice, I cloned the Kura repository twice:
    1. Manually imported the [Kura repository](https://github.com/kura-labs-org/C5-Deployment-Workload-1) URL to a new repository I created in GitHub called “C5-Deployment-Instructions-Clone”
    2. Utilized my Student Instance:
        1. **git init** - Initialized a new repository within my instance called /home/ubuntu/bankapp
        2.  **git pull [kura_repo_URL]** - pulled in the repository I wanted to clone 
        3. created a new repository in my GitHub called “[Workload-1-Repo](https://github.com/acurwen/Workload-1-Repo)”
        4. **git remote add origin [Workload-1-Repo URL]** - adding my new repository in git called “origin”
        5. **git branch -M main** - setting the branch to main
        6. **git push -u origin main** - pushing the main branch to the remote repository in git to set up tracking in the remote branch



2. **Created an EC2**

We are creating this instance to host Jenkins on, which we will install next. I created and launched a new EC2 Instance called “EC2 for Hosting Bank Application” following the [instructions](https://github.com/kura-labs-org/AWS-EC2-Quick-Start-Guide/blob/main/AWS%20EC2%20Quick%20Start%20Guide.pdf). I also made one security group with security rules that included SSH (Port 22), HTTP (Port 80) and Jenkins (Port 8080), and gave it a helpful name for me to remember what rules it had: "SSH/HTTP/CustomTCP for Jenkins - Security Group".

![image](https://github.com/user-attachments/assets/71ef6a60-387a-4684-88d9-1730e2a223cf)


3. **Installed Jenkins onto the EC2**

We install Jenkins onto our EC2 instance to give Jenkins access to our application code.

I connected to my new EC2 instance (EC2 for Hosting Bank Application) and installed Jenkins per the instructions. Installation was successful.

![image](https://github.com/user-attachments/assets/58ac7dba-61c4-4134-9874-27889d239e11)


4. **Created a first admin user account on Jenkins**

Created a first admin user on Jenkins so that we could have access to Jenkins tools via their web interface, specifically creating a multi-branch pipeline and configuring a build.

I used the IP address for my EC2 instance (3.88.60.42) and the port for Jenkins (8080) to find the log in page. 3.88.60.42:8080 successfully took me to the ‘create a user’ Jenkins page.

![image](https://github.com/user-attachments/assets/7735c816-0d7c-49e4-901d-e82665d769a2)

The Jenkins page prompted me for the user admin password that I could find in my EC2 instance. In the instance I ran sudo cat /var/lib/jenkins/secrets/initialAdminPassword and located my password. Then I created an account.


5. **Created a Multi-Branch pipeline in Jenkins**

Created a pipeline for each branch in my git repository (however, we only have and are focusing on 'main' branch). This pipeline is where Jenkins will handle building, testing, and deploying our application in that main branch and others if we had any.

Named the multi-branch pipeline “MultiBranch Pipeline for Bank Application”, and used my GitHub username and personal access token (pulled and saved from GitHub) as a password.

6. **Connected GitHub repository to Jenkins**

Here we are providing a source of code (Github repo code) to be tested.
I connected Jenkins to my GitHub repository by submitting the [URL](https://github.com/acurwen/Workload-1-Repo.git) of my “Workload-1-Repo” repository from GitHub and the URL was validated! 

![image](https://github.com/user-attachments/assets/8064daa4-3f00-4864-a475-8c75a8bbed59)


7. **Build Configuration**

While creating the build configuration, I ensured the "Build Configuration" section said "Mode: by Jenkinsfile" and "Script Path: Jenkinsfile”. I then saved the build which triggered an automatic start. The build completed successfully.

The steps of the build were cloning the repository, building and testing out the code. Per the log, the 'main' branch was checked, which had the Jenkinsfile present, and because all parameters were met, the build was successful.

![Screenshot 2024-08-01 003530](https://github.com/user-attachments/assets/1055c30b-22f0-4863-8d45-299e0b3a7f95)



8. **Created Environment in Elastic Beanstalk**

I created this environment to host our banking application. I named the environment "Retail Banking".

First, I created my “IAM” service roles: aws-elasticbeanstalk-service-role and Elastic-EC2. These roles help grant permissions that are needed for the environment to host and manage the bank application correctly.

*I also added in my key pair, but I don't think that was needed. I believe doing this requires that my public key pair shown below and my private one would be needed to access the application site.

![Screenshot 2024-08-01 010153](https://github.com/user-attachments/assets/5345ff28-6f82-44c1-885a-15829112d8aa)

While creating this environment, I also added in my security group that I created earlier for my EC2 instance. The security group has security rules that includes SSH (Port 22), HTTP (Port 80) and Jenkins (Port 8080) which ensures the correct ports are established and enforced for my environment.

![Screenshot 2024-08-01 011317](https://github.com/user-attachments/assets/4877b740-ff64-4213-ba48-11128156a0c3)


9. **Uploaded application zip file to environment and launched**

I downloaded the contents of “[Workload-1-Repo](https://github.com/acurwen/Workload-1-Repo)” repository and rezipped the application files on my local machine’s file explorer to ensure there was no parent directory included. I then uploaded the edited zip file to my environment in AWS Elastic Beanstalk and saved my environment. 

Received message that the evironment was successfully launched.
![image](https://github.com/user-attachments/assets/f648a388-087d-4524-be20-6b2079cba70f)


Success!
![image](https://github.com/user-attachments/assets/e6ca1bea-6574-4e62-8857-88a91a097ad4)


## System Design Diagram
![Workload 1 - Retail Bank App drawio](https://github.com/user-attachments/assets/844139cd-72ca-416d-8e5a-befd5444d7cf)
Diagraming this project helped me a lot in terms of understanding the 'why' behind the instructions.

## Issues & Troubleshooting

### Jenkins:

Initially I was confused on how to log into Jenkins — I didn’t quite know where to go. After searching on Google, I learned that you just need your IP address, Jenkin’s port of 8080 and a “:” in between.

In my first iteration of this project, I created an account in Jenkins, but didn’t save my password. When I needed to log in again to take screenshots or understand the stages of the build, I was unable to (there is no recover password option.)  I even searched on Google for different Jenkins pages, found one and tried both recovering my account there and creating a new account. However, I realized I was on a discussions/discord-like Jenkins site — so not where I was supposed to be. Because I did this project a second time, I made sure to save my password when creating a new account.

When I got into Jenkins, it prompted me for the user admin password that I could find in my EC2 at path: /var/lib/jenkins/secrets/initialAdminPassword. However, at first I couldn’t cd into that path because I didn’t have permission to enter the “secrets” directory. I found a workaround where I first cd’d into the root directory, and from there, ran a sudo cat command on the path: sudo cat /var/lib/jenkins/secrets/initialAdminPassword

My instance:
![Screenshot 2024-08-01 000028](https://github.com/user-attachments/assets/20781f1a-8768-44a0-9d46-90dbad5f7db6)

The first time around while creating my multi-branch pipeline, I also received this crumb error because I didn’t enter a name into the ‘Display Name’ field.
Error:
![image](https://github.com/user-attachments/assets/c41589cb-f21c-4752-9a03-d3a17dc1c50e)

### Elastic Beanstalk:

When trying to create the environment for the bank app, I got an error that managed platform updates was not available for 'BASIC' health reporting. The instructions stated to pick “BASIC” and not "ENHANCED", so I unchecked the option for managed monitoring.

Error Message:
![image](https://github.com/user-attachments/assets/3af1e96a-28e6-4bc4-85e5-53ff227a1405)

Received 502 Bad Gateway nginx error when trying to connect to my online bank application.

![image](https://github.com/user-attachments/assets/679f27ec-1ba0-4670-bfaf-2880c0c202b0)

Looked this error up and found that it means my proxy nginx could not get a proper response from the upstream server. Then from my classmates I learned that we should take a look at our logs. At first, I thought to look in my Beanstalk instance for the logs, but I couldn't locate them. I think I also looked in my EC2 instance, but that was the wrong place to look. Then I learned I could find them in the 'Log' tab on the Elastic Beanstalk environment page.

At first, I had trouble accessing my logs because nothing showed up in that 'Log' tab. Then I realized I had to navigate to the drop-down menu and press ‘last 100 lines’ and then the logs were available for download. I downloaded the logs and scanned the lines to try and understand what went wrong:

At first, I looked at the very last lines that said:
[error] 2763#2763: *2 connect() failed (111: Connection refused) while connecting to upstream, client: 104.28.236.175, server: , request: "GET / HTTP/1.1", upstream: "http://127.0.0.1:8000/", host: "retailbanking-env.eba-c92mtjdb.us-east-1.elasticbeanstalk.com"

Last lines of log page:
![image](https://github.com/user-attachments/assets/7d55b21c-88a4-4011-b2fc-74643003dda5)

Because this line stated that a connection was refused while connecting to upstream, which matched what Google said about the nginx error, I thought I was having a port issue that was blocking a connection.

I tried looking at the config files within my instance to see which ports were being listened to because I learned from classmates that the port configuration would be in those files. I scanned the conf.d file and saw that port 443 was being "listened at" and thought that was a clue. I went back to my Elastic Beanstalk environment and looked at the environment configuration review page and reviewed the EC2 security groups. I thought maybe I needed to create a new security group that included a rule for Port 443, so I created and added in a new one to the environment configurations, but that wasn’t the issue. :P 

But I did learn that when you make any changes to your environment, Elastic Beanstalk will terminate the existing instance and hurry to make a new one.

I also tried locating my nginx files within my instance for a clue, but ended up looking in te wrong instance for too long and couldn't find them. 

Going back to the logs, I kept scanning to where I kept seeing ‘ERROR’. I noticed three processes that would start and stop due to an error and they were all followed by the same line: `ModuleNotFoundError: No module named 'application'`

Pattern of process starting, stopping, and then that error message showing: 
![image](https://github.com/user-attachments/assets/2243bdaa-58f2-46af-967a-83dcc904a8f0)

The only place I remember seeing the word "application" was in the repository files for our application code and then Nicole provided a SUPER helpful hint in our Slack chat about how zip files have to be configured (Thanks Nicole!).

The issue was that I downloaded the zip file directly from GitHub which included a parent directory. The application couldn’t be deployed because the right files couldn’t be reached/read. 

Uploading the zipfile that includes the parent directory:
![image](https://github.com/user-attachments/assets/47567b9a-788e-419d-a22d-11646f918a56)

I fixed this by rezipping only the application sub files together, excluding the parent folder. Then I reuploaded my edited zip file into my application environment on Elastic Beanstalk and the application ran successfully!
![image](https://github.com/user-attachments/assets/da4ec6f6-21fa-4e0a-b4a3-645c6c3a12f0)

In our study room, I also learned from Jon where I could see my application files: /var/app/current
Before I made the zip file changes, I could only see the parent directory ("Workload-1-Repo-main") when I ls'd within that path. After I reuploaded, I could see that all my application files were there instead, so that was cool.

![image](https://github.com/user-attachments/assets/75c6f560-a388-451b-9733-9218715d29ef)

## Optimization

What are the benefits of using managed services for cloud infrastructure?

1. Don’t have to worry about the code that creates the environment or your instance; you can just worry about your application code
2. Log files are also kept for you to keep track of changes and issues
3. If a new security group is added or deleted, or any other configuration changes are made to your environment or instance, the environment automatically starts up another instance for your application to limit disruptions

What are some issues that a retail bank would face choosing this method of deployment and how would you address/resolve them?

1. Customers not having the right PORT open to reach the site; utilizing commonly used ports would help
2. Bank information is sensitive so ensuring access is secure and readily available is key, so automating any task needed to ensure the application is up and running would be helpful

What are other disadvantages of using elastic beanstalk or similar managed services for deploying applications?
1. Less customization available for your specific type of application since you only manage your application and data as a developer 

## Conclusion

I had a lot of fun doing this project. I noticed that working through documentation helps me understand my work better and that it’d probably be helpful to start documentation immediately as I start a project. Working on this workload alongside the RBAC activity helped me become more comfortable with writing commands and scripts in Bash, and also demonstrated how tough handling main branch + elective branch will be. I also feel closer to my classmates simply because of all the time we spent together in the study room stressing and working together. Thinking through things with each other really helped me complete this project. Big shoutout to Jordan, Jon, Kaia, Ke and Jeremy!


----------------------------------------------------------------

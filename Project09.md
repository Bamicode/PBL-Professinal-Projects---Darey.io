## TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION. INTRODUCTION TO JENKINS

### Task

To enhance the architecture prepared in Project 8 by adding a Jenkins server, configure a job to automatically deploy source codes changes from Git to NFS server.

### Prerequisites

![Instances](https://user-images.githubusercontent.com/96090546/169724579-99229d95-6c51-4b57-9072-62dc73814fda.PNG)

INSTALL AND CONFIGURE JENKINS SERVER

Step 1 – Install Jenkins server

Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"

Install JDK (since Jenkins is a Java-based application)

    sudo apt update
    sudo apt install default-jdk-headless

Install Jenkins

    wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
    sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
        /etc/apt/sources.list.d/jenkins.list'
    sudo apt update
    sudo apt-get install jenkins
    
Making sure Jenkins is up and running,

    sudo systemctl status jenkins

By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group


Perform initial Jenkins setup.
From the browser access http://Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080

You will be prompted to provide a default admin password

![Jenkins Installation](https://user-images.githubusercontent.com/96090546/169724713-adf19ce4-40b3-4560-9b18-ee06c50aa5a9.PNG)

Retrieve it from your server:

    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
  
Then you will be asked which plugings to install – choose suggested plugins.

![Jenkins](https://user-images.githubusercontent.com/96090546/169724709-1d5cc615-c672-4141-99bb-83233a5c1332.PNG)

Once plugins installation is done – create an admin user and you will get your Jenkins server address.

The installation is completed!

![Jenkins1](https://user-images.githubusercontent.com/96090546/169724711-3cd615c3-5b28-46d6-a68c-42f989beeb4a.PNG)

![Jenkins2](https://user-images.githubusercontent.com/96090546/169724712-679ca5c2-994e-45c7-a338-64fec52f9b3c.PNG)

### Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks

In this part, you will learn how to configure a simple Jenkins job/project (these two terms can be used interchangeably). This job will will be triggered by GitHub webhooks and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on Jenkins server.

1. Enable webhooks in your GitHub repository settings

![p9 webhook](https://user-images.githubusercontent.com/96090546/169724875-7a89b7f1-241e-458a-9f05-d03916eb1a0f.PNG)

2. Go to Jenkins web console, click "New Item" and create a "Freestyle project"
3. 

To connect your GitHub repository, you will need to provide its URL, you can copy from the repository itself


In configuration of your Jenkins freestyle project choose Git repository, provide there the link to your Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.


Save the configuration and let us try to run the build. For now we can only do it manually.
Click "Build Now" button, if you have configured everything correctly, the build will be successfull and you will see it under #1


You can open the build and check in "Console Output" if it has run successfully.

![Build success](https://user-images.githubusercontent.com/96090546/169725296-358f58ef-ccb1-44c8-a16f-c0f187725fc8.PNG)


But this build does not produce anything and it runs only when we trigger it manually. Let us fix it.

Click "Configure" your job/project and add these two configurations
Configure triggering the job from GitHub webhook:


Now, go ahead and make some change in any file in your GitHub repository (e.g. README.MD file) and push the changes to the master branch.

You will see that a new build has been launched automatically (by webhook) and you can see its results – artifacts, saved on Jenkins server as shown below.

![Push on Readme file](https://user-images.githubusercontent.com/96090546/169725197-652462b1-d0c1-4714-8b76-e49d8f88a932.PNG)


We have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as ‘push’ because the changes are being ‘pushed’ and files transfer is initiated by GitHub). There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.

By default, the artifacts are stored on Jenkins server locally

    ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/

## Step 3 - Configure Jenkins to copy files to NFS server via SSH

Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.

Jenkins is a highly extendable application and there are 1400+ plugins available. We will need a plugin that is called "Publish Over SSH".

1. Install "Publish Over SSH" plugin.

On main dashboard select "Manage Jenkins" and choose "Manage Plugins" menu item.

On "Available" tab search for "Publish Over SSH" plugin and install it

![plugin installation](https://user-images.githubusercontent.com/96090546/169727315-960feafa-7b63-4853-99ea-bd5645b2fc67.PNG)

2. Configure the job/project to copy artifacts over to NFS server.

On main dashboard select "Manage Jenkins" and choose "Configure System" menu item.

Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:

2a. Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)

2b. Arbitrary name

2c. Hostname – can be private IP address of your NFS server

2d. Username – ec2-user (since NFS server is based on EC2 with RHEL 8)

2e. Remote directory – /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server

Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.

![config success](https://user-images.githubusercontent.com/96090546/169728645-e2b0a42a-b9e4-4ff4-8006-262f2e8330fe.PNG)

Save the configuration, open your Jenkins job/project configuration page and add another one "Post-build Action"

![post-build action](https://user-images.githubusercontent.com/96090546/169732101-21d8e7d8-53aa-4bc4-b314-ef4738eaf986.PNG)


Configure it to send all files produced by the build into our previouslys define remote directory. In our case we want to copy all files and directories – so we use **.

![post-build action1](https://user-images.githubusercontent.com/96090546/169732105-6143aae9-49df-4645-aa25-266d41596a26.PNG)

Save this configuration and go ahead, change something in README.MD file in your GitHub Tooling repository.

Webhook will trigger a new job and in the "Console Output" of the job you will find something like this:

    SSH: Transferred 25 file(s)
    Finished: SUCCESS

Encountered error due to permission denied on the /mnt/apps folder
Resolved it by changing ownership in order for the NFS server to communicate with Jenkins server

        sudo chown -R nobody: /mnt/apps
        sudo chmod -R 777 /mnt/apps
        
   ![changed ownership before build success](https://user-images.githubusercontent.com/96090546/169732513-09167519-76d5-444e-a77b-db57afdd442a.PNG)

![Build success2](https://user-images.githubusercontent.com/96090546/169732252-a5fe8e56-e676-47b8-b8d2-0824be8cd4ff.PNG)

To make sure that the files in /mnt/apps have been udated – connect via SSH/Putty to your NFS server and check README.MD file

    cat /mnt/apps/README.md
    
  ![Update on README md file](https://user-images.githubusercontent.com/96090546/169732755-79e4feee-1281-4ea1-b915-1447d2aeb14b.PNG)
  
We have just implemented your first Continous Integration solution using Jenkins CI.

![CI solution using Jenkins](https://user-images.githubusercontent.com/96090546/169732991-3b3305e4-ba58-4e70-9794-d3b4e31583a6.PNG)




Thank You!!!








# project
maven java automation, web automation:

Steps for MavenJava Automation:
Maven Java Automation Steps:
 Step 1: Open Jenkins (localhost:8080)
   	 ├── Click on "New Item" (left side menu
Step 2: Create Freestyle Project (e.g., MavenJava_Build)
        	├── Enter project name (e.g., MavenJava_Build)
        	├── Click "OK"
└── Configure the project:
            		├── Description: "Java Build demo"
            		├── Source Code Management:
            			└── Git repository URL: [GitMavenJava repo URL]
            		├── Branches to build: */Main   or  */master
  		└── Build Steps:
               	     ├── Add Build Step -> "Invoke top-level Maven targets"
                  		└── Maven version: MAVEN_HOME
                 		└── Goals: clean
                	├── Add Build Step -> "Invoke top-level Maven targets"
                		└── Maven version: MAVEN_HOME
                		└── Goals: install
                	└── Post-build Actions:
                    	       ├── Add Post Build Action -> "Archive the artifacts"
                    			└── Files to archive: **/*
                    	     ├── Add Post Build Action -> "Build other projects"
                    			└── Projects to build: MavenJava_Test
                    			└── Trigger: Only if build is stable
                    	     └── Apply and Save


    └── Step 3: Create Freestyle Project (e.g., MavenJava_Test)
        	├── Enter project name (e.g., MavenJava_Test)
        	├── Click "OK"
              └── Configure the project:
             ├── Description: "Test demo"
             ├── Build Environment:
            		└── Check: "Delete the workspace before build starts"
            ├── Add Build Step -> "Copy artifacts from another project"
            		└── Project name: MavenJava_Build
            		└── Build: Stable build only  // tick at this
            		└── Artifacts to copy: **/*
            ├── Add Build Step -> "Invoke top-level Maven targets"
            		└── Maven version: MAVEN_HOME
            		└── Goals: test
            		└── Post-build Actions:
                ├── Add Post Build Action -> "Archive the artifacts"
                	└── Files to archive: **/*
                	└── Apply and Save

    └── Step 4: Create Pipeline View for Maven Java project
        ├── Click "+" beside "All" on the dashboard
        ├── Enter name: MavenJava_Pipeline
        ├── Select "Build pipeline view"         // tick here
         |--- create
        └── Pipeline Flow:
            ├── Layout: Based on upstream/downstream relationship
            ├── Initial job: MavenJava_Build
            └── Apply and Save OK

    └── Step 5: Run the Pipeline and Check Output
        ├── Click on the trigger to run the pipeline
        ├── click on the small black box to open the console to check if the build is success
            Note : 
If build is success and the test project is also automatically triggered with name       
                      “MavenJava_Test”
The pipeline is successful if it is in green color as shown ->check the console of the test project
The test project is successful and all the artifacts are archived successfully
II. Maven Web Automation Steps:
└── Step 1: Open Jenkins (localhost:8080)
    ├── Click on "New Item" (left side menu)
    
    └── Step 2: Create Freestyle Project (e.g., MavenWeb_Build)
        ├── Enter project name (e.g., MavenWeb_Build)
        ├── Click "OK"
        └── Configure the project:
            ├── Description: "Web Build demo"
            ├── Source Code Management:
            		└── Git repository URL: [GitMavenWeb repo URL]
            ├── Branches to build: */Main or master
            └── Build Steps:
                ├── Add Build Step -> "Invoke top-level Maven targets"
                	└── Maven version: MAVEN_HOME
                	 └── Goals: clean
                ├── Add Build Step -> "Invoke top-level Maven targets"
                	└── Maven version: MAVEN_HOME
                	└── Goals: install
                └── Post-build Actions:
                    ├── Add Post Build Action -> "Archive the artifacts"
                   	 └── Files to archive: **/*
                    ├── Add Post Build Action -> "Build other projects"
                    	└── Projects to build: MavenWeb_Test
                    	└── Trigger: Only if build is stable
                    └── Apply and Save

    └── Step 3: Create Freestyle Project (e.g., MavenWeb_Test)
        ├── Enter project name (e.g., MavenWeb_Test)
        ├── Click "OK"
        └── Configure the project:
            ├── Description: "Test demo"
            ├── Build Environment:
            		└── Check: "Delete the workspace before build starts"
            ├── Add Build Step -> "Copy artifacts from another project"
            		└── Project name: MavenWeb_Build
            		└── Build: Stable build only
            		└── Artifacts to copy: **/*
            ├── Add Build Step -> "Invoke top-level Maven targets"
           		└── Maven version: MAVEN_HOME
            		└── Goals: test
            └── Post-build Actions:
                ├── Add Post Build Action -> "Archive the artifacts"
                	└── Files to archive: **/*
                ├── Add Post Build Action -> "Build other projects"
                	└── Projects to build: MavenWeb_Deploy
                └── Apply and Save

    └── Step 4: Create Freestyle Project (e.g., MavenWeb_Deploy)
        ├── Enter project name (e.g., MavenWeb_Deploy)
        ├── Click "OK"
        └── Configure the project:
            ├── Description: "Web Code Deployment"
            ├── Build Environment:
            		└── Check: "Delete the workspace before build starts"
            ├── Add Build Step -> "Copy artifacts from another project"
            		└── Project name: MavenWeb_Test
            		└── Build: Stable build only
               	└── Artifacts to copy: **/*
            └── Post-build Actions:
                ├── Add Post Build Action -> "Deploy WAR/EAR to a container"
   └── WAR/EAR File: **/*.war
   └── Context path: Webpath
 └── Add container -> Tomcat 9.x remote
└── Credentials: Username: admin, Password: 1234
── Tomcat URL: https://localhost:8085/
                └── Apply and Save

    └── Step 5: Create Pipeline View for MavenWeb
        ├── Click "+" beside "All" on the dashboard
        ├── Enter name: MavenWeb_Pipeline
        ├── Select "Build pipeline view"
        └── Pipeline Flow:
            ├── Layout: Based on upstream/downstream relationship
            ├── Initial job: MavenWeb_Build
            └── Apply and Save

    └── Step 6: Run the Pipeline and Check Output
        ├── Click on the trigger “RUN” to run the pipeline
            Note: 
After Click on Run -> click on the small black box to open the console to check if the build is success
Now we see all the build has  success if it appears in green color
        ├── Open Tomcat homepage in another tab
        ├── Click on the "/webpath" option under the manager app
               Note:
 It ask for user credentials for login ,provide the credentials of tomcat.
It provide the page with out project name which is highlighted.
After clicking on our project we can see output.


pipeline creation using script: 

General :
 	Description :-  provide the description of the project
	Build triggers:  here we can provide with build triggers of you choice
Advance project option:
	Definition: 
		Choose pipeline script
 
Here code for pipeline -script
	
Copy the code there

script: 

pipeline {
    agent any
    tools {
        maven 'Maven3.9.11'
    }

    stages {

        stage('git repo & clean') {
            steps {
                bat "git clone https://github.com/Firesai/MavenJava.git"
                bat "mvn clean -f mavenjava"
            }
        }
        stage('install') {
            steps {
                bat "mvn install -f mavenjava"
            }
        }
        stage('test') {
            steps {
                bat "mvn test -f mavenjava"
            }
        }
        stage('package') {
            steps {
                bat "mvn package -f mavenjava"
            }
        }
    }
}

working with nagios and minikube:

Open a terminal (PowerShell or CMD). Do the following commands
Start Minikube with a specified driver (e.g., Hyper-V, Docker, or VirtualBox). For example:
Hyper-V: 
minikube start --driver=hyperv
Docker: 
minikube start --driver=docker
Verify Minikube is running:
minikube status

minikube kubectl -- get pods -A

incase minikube doesn't start:
Check the Minikube logs: 
minikube logs
Updating Minikube:
minikube update-check
minikube update

Step 1: Start Minikube Cluster
minikube start
kubectl get nodes
minikube status

Step 2: Create and Manage Deployment
kubectl create deployment mynginx --image=nginx
# If already created, update image
kubectl set image deployment/mynginx nginx=nginx:latest

# Verify deployment and pods
kubectl get deployments
kubectl get pods
kubectl describe pods

# Expose deployment as service
kubectl expose deployment mynginx --type=NodePort --port=80 --target-port=80

Step 3: Scale the Deployment
kubectl scale deployment mynginx --replicas=4
kubectl get service mynginx

Step 4: Access the Nginx App
kubectl port-forward svc/mynginx 8081:80
Access: http://localhost:8081

Step 5: Stop and Clean Up
kubectl delete deployment mynginx
kubectl delete service mynginx
minikube stop
minikube delete


NAGIOS AUTOMATION STEPS
Step 1: Pull Nagios Docker Image
docker pull jasonrivers/nagios:latest

Step 2: Run Nagiosdocker run --name nagiosdemo -p 8888:80 jasonrivers/nagios:latest

Step 3: Access Nagios Dashboard

Open browser → http://localhost:8888
Username: nagiosadmin
Password: nagios


Step 4: View Host Details

In Nagios web UI → go to:

Hosts → select any host

Check:

Host Status (UP/DOWN)

Metrics (CPU, Memory, Network)

Actions (Reschedule check, Disable alerts)

Step 5: Stop and Remove Nagios
docker stop nagiosdemo
docker rm nagiosdemo
docker images
docker rmi jasonrivers/nagios:latest

Setting Up  Jenkins CI------using GitHub Webhook with Jenkins 

Step 1: Configure Webhook in GitHub
Go to your GitHub repository.
Navigate to Settings → Webhooks.
Click “Add webhook”.
In the Payload URL field:
Enter the Jenkins webhook URL in the format:
http://<jenkins-server-url>/github-webhook/
Note: If Jenkins is running on localhost, GitHub cannot access it directly.
Use ngrok to expose your local Jenkins to the internet:

ngrok.exe http <Jenkins local host:8080>
Use the generated ngrok URL, e.g.:
http://abc123.ngrok.io/github-webhook/
Set Content type to:
application/json
Under “Which events would you like to trigger this webhook?”, select:
 Just the push event
Click “Add webhook” to save.

 Step 2: Configure Jenkins to Accept GitHub Webhooks
Open Jenkins Dashboard.
Select the job (freestyle or pipeline) you’ve already created.
Click Configure.
Scroll down to the Build Triggers section.
Check the box: ✅GitHub hook trigger for GITScm polling
Click Save.

 Step 3: Test the Setup
Make any code update in your local repo and push it to GitHub.
Once pushed, GitHub will trigger the webhook.
Jenkins will automatically detect the change and start the build pipeline.

Setting Up   Jenkins Email Notification Setup (Using Gmail with App Password)
Creation of app password
1. Gmail: Enable App Password (for 2-Step Verification)
i. Go to: https://myaccount.google.com
ii. Enable 2-Step Verification
Navigate to:
Security → 2-Step Verification
Turn it ON
Complete the OTP verification process (via phone/email)
iii. Generate App Password for Jenkins
Go to:
Security → App passwords
Select:
App: Other (Custom name)
Name: Jenkins-Demo
Click Generate
Copy the 16-digit app password
Save it in a secure location (e.g., Notepad)
                 2.  Jenkins Plugin Installation
i. Open Jenkins Dashboard
ii. Navigate to:
Manage Jenkins → Manage Plugins
iii. Install Plugin:
Search for and install:
Email Extension Plugin

3. Configure Jenkins Global Email Settings
i. Go to:
Manage Jenkins → Configure System

A. E-mail Notification Section
Field
Value
SMTP Server
smtp.gmail.com
Use SMTP Auth
✅ Enabled
User Name
Your Gmail ID (e.g.,bhargavikumbham@gmail.com)
Password
Paste the 16-digit App Password
Use SSL
✅ Enabled
SMTP Port
465
Reply-To Address
Your Gmail ID (same as above)

➤ Test Configuration
Click: Test configuration by sending test e-mail
Provide a valid email address to receive a test mail
✅ Should receive email from Jenkins

 Extended E-mail Notification Section
Field
Value
SMTP Server
smtp.gmail.com
SMTP Port
465
Use SSL
✅ Enabled
Credentials
Add Gmail ID and App Password as Jenkins credentials
Default Content Type
text/html or leave default
Default Recipients
Leave empty or provide default emails
Triggers
Select as per needs (e.g., Failure)


4.  Configure Email Notifications for a Jenkins Job
i. Go to:
Jenkins → Select a Job → Configure

ii. In the Post-build Actions section:
Click: Add post-build action → Editable Email Notification
A. Fill in the fields:
Field
Value
Project Recipient List
Add recipient email addresses (comma-separated)
Content Type
Default (text/plain) or text/html
Triggers
Select events (e.g., Failure, Success, etc.)
Attachments
(Optional) Add logs, reports, etc.


iii. Click Save

 Now your Jenkins job is set up to send email notifications based on the build status!


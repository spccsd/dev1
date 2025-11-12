Ass-1
Set A:
1. Create a Jenkins freestyle job to pull source code from a Git repository, build the project using Maven, and archive the generated artifact. use git Repository: https://github.com/hkhcoder/vprofile-project.git branch: main Steps to include: 
● Create a new freestyle job. 
● Configure the Git repository in the "Source Code Management" section. 
● In the "Build" step, invoke the top-level Maven target (e.g., clean install). 
● Add a "Post-build Action" to archive the artifacts (e.g., target/*.war).

Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

1. Create a New Freestyle Job
1.	From the Jenkins dashboard, click on "New Item" in the top-left corner.
2.	Enter an item name (e.g., vprofile-project-build).
3.	Select "Freestyle project" from the list.
4.	Click "OK" at the bottom of the page.

2. Configure Source Code Management (SCM)
1.	On the job configuration page, scroll down to the "Source Code Management" section.
2.	Select "Git".
3.	In the "Repository URL" field, enter:
4.	[https://github.com/hkhcoder/vprofile-project.git](https://github.com/hkhcoder/vprofile-project.git)
5.	In the "Branch Specifier (blank for 'any')" field, change */master to:
6.	*/main
Note: The default might be master, so ensure it is set to main to match the repository's primary branch.

3. Configure the Build Step (Maven)
1.	Scroll down to the "Build" section.
2.	Click the "Add build step" dropdown menu.
3.	Select "Invoke top-level Maven targets".
4.	If you have multiple Maven versions configured in Jenkins, select the one you wish to use from the "Maven Version" dropdown. If not, you can leave it as (Default).
5.	In the "Goals" field, enter:
6.	clean install
o	clean: Deletes any previously compiled code and artifacts from the target directory.
o	install: Compiles the code, runs tests, and packages the project, installing it into your local Maven repository. This process generates the .war file in the target directory.

4. Configure Post-build Action (Archive Artifacts)
1.	Scroll down to the "Post-build Actions" section.
2.	Click the "Add post-build action" dropdown menu.
3.	Select "Archive the artifacts".
4.	In the "Files to archive" field, enter:
5.	target/*.war
This tells Jenkins to find all files ending with .war inside the target directory (which is created by the Maven build) and save them with the build record.

5. Save and Run
1.	Click the "Save" button at the bottom of the page.
2.	You will be redirected to the job's main page.
3.	Click "Build Now" in the left-hand menu to run the job.
4.	After the build is complete (indicated by a blue or green ball in the "Build History"), you can click on the build number. The archived artifact (e.g., vprofile-v1.war) will be available on the build's page.

**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

2. Build a Jenkins freestyle project that automates the following tasks for a Maven project: 
use git Repository: https://github.com/hkhcoder/vprofile-project.git 
branch: main 
● Pull the source code from the Git repository. 
● Build the project using Maven to generate a .war file. 
● Implement a strategy to version the .war file with the format projectname-build_id- 
time_stamp.war and store it in a separate versions directory.

Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

1. Create a New Freestyle Job

From the Jenkins dashboard, click on "New Item".

Enter an item name (e.g., vprofile-build-and-version).

Select "Freestyle project" and click "OK".


2. Configure Source Code Management (SCM)

On the configuration page, scroll to "Source Code Management".

Select "Git".

Repository URL: https://github.com/hkhcoder/vprofile-project.git

Branch Specifier: */main


3. Build Step 1: Invoke Maven

This step builds the project and creates the original .war file in the target directory.

Scroll down to the "Build" section.

Click "Add build step" and select "Invoke top-level Maven targets".

Maven Version: Select the Maven installation you configured in Global Tool Configuration (e.g., Default_Maven or Maven-3.9.6). This is the step that fixes the mvn: not found error.

Goals: clean install


4. Build Step 2: Version and Move Artifact

This step runs after the Maven build is complete. It finds the .war file, renames it using Jenkins variables, and moves it to your versions directory.

In the same "Build" section, click "Add build step" (so you now have two build steps).

Select "Execute shell".

Paste the following script into the "Command" box:

echo "Starting artifact versioning..."

# Find the generated .war file in the target directory.
# This is safer than hardcoding the version number.
SOURCE_WAR_FILE=$(find target -maxdepth 1 -name "*.war" | head -n 1)

# Check if a .war file was actually found
if [ -z "$SOURCE_WAR_FILE" ]; then
    echo "ERROR: No .war file found in target directory!"
    exit 1
fi

echo "Found artifact: $SOURCE_WAR_FILE"

# --- Define new file name components ---

# 1. Project Name (as requested)
PROJECT_NAME="vprofile-project"

# 2. Build ID (this is a built-in Jenkins variable)
# e.g., "15"
BUILD_ID="$BUILD_ID"

# 3. Timestamp (formatted for file names)
# e.g., "20251110-225800"
TIME_STAMP=$(date +%Y%m%d-%H%M%S)

# --- Define target directory and file name ---
TARGET_DIR="versions"
TARGET_FILE_NAME="${PROJECT_NAME}-${BUILD_ID}-${TIME_STAMP}.war"
TARGET_PATH="${TARGET_DIR}/${TARGET_FILE_NAME}"

# Create the target directory if it doesn't exist
# The '-p' flag prevents errors if the directory already exists
echo "Creating target directory: $TARGET_DIR"
mkdir -p $TARGET_DIR

# Move and rename the artifact
echo "Moving $SOURCE_WAR_FILE to $TARGET_PATH"
mv $SOURCE_WAR_FILE $TARGET_PATH

echo "Artifact versioning complete. New file is at $TARGET_PATH"


5. Post-build Action: Archive the Versioned Artifact

This step is optional but highly recommended. It saves your newly-named .war file with the Jenkins build record, so you can easily download it from the Jenkins build page.

Scroll down to the "Post-build Actions" section.

Click "Add post-build action" and select "Archive the artifacts".

In the "Files to archive" field, enter:

versions/*.war


This tells Jenkins to look inside the versions directory (which your script created) and archive any .war files it finds.


6. Save and Run

Click "Save" at the bottom of the page.

Click "Build Now" to run your new job.

When the build finishes, click on the build number in the "Build History". You should see your versioned artifact (e.g., vprofile-project-1-20251110-225901.war) listed as a build artifact, ready to be downloaded.

**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************



3. Schedule above job to run periodically every 5 minutes. (i.e. Q2) 

Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

You can easily add a schedule to your existing job. Here’s how to set it up to run every 5 minutes.

Go to your Jenkins dashboard and click on your job (e.g., vprofile-build-and-version).

Click on "Configure" from the left-hand menu.

Scroll down to the "Build Triggers" section.

Check the box for "Build periodically".

In the "Schedule" text area that appears, paste the following cron expression:

H/5 * * * *
What this means:
H/5: The H is a Jenkins feature that means "hash" or "anywhere within." So, H/5 tells Jenkins to run the job once during every 5-minute period (e.g., at 11:01, 11:06, 11:11...). This is better than */5 as it helps spread out the load if you have many jobs running.

* * * *: These (four asterisks) represent "every hour," "every day of the month," "every month," and "every day of the week."

Finally, click "Save" at the bottom of the page. Jenkins will now automatically run this job approximately every 5 minutes.

**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

4. Edit above job to notify users via email whenever there is an unstable build. (i.e. Q2) 

Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

Part 1: Configure Jenkins to Send Email (System-Wide)
You only need to do this once, and it will work for all your jobs.

Go to System Configuration:

On the Jenkins dashboard, go to Manage Jenkins > System.

Set Jenkins Admin Email:

Scroll down to the "Jenkins Location" section.

Set the "System Admin e-mail" address. This is often used as the default "From" address for notifications (e.g., jenkins@your-company.com).

Configure SMTP Server (E-mail Notification):

Scroll to the very bottom to find the "E-mail Notification" section.

Fill in your mail server details. (This example uses Gmail, but you should use your organization's SMTP server if available).

SMTP server: smtp.gmail.com

Click the "Advanced..." button.

Check "Use SMTP Authentication".

User Name: Your full email address (e.g., your-email@gmail.com).

Password: Your email password.

Important (for Gmail/Google): You can't use your regular password. You must generate an "App Password" from your Google Account security settings and use that here.

Check "Use TLS" (if your server uses it, like Gmail).

SMTP Port: 587 (for TLS) or 465 (for SSL).

Test Your Configuration:

Check the box "Test configuration by sending test e-mail".

Enter your own email in "Test e-mail recipient".

Click the "Test configuration" button. You should receive a test email. If not, troubleshoot your SMTP settings before continuing.

Save your changes at the bottom of the System page.

Part 2: Configure Your Job to Send Notification
Now that Jenkins can send emails, you'll edit your vprofile-build-and-version job.

Go to Your Job:

From the dashboard, click on your job (e.g., vprofile-build-and-version).

Click "Configure".

Add the Post-build Action:

Scroll down to the "Post-build Actions" section.

Click the "Add post-build action" dropdown menu.

Select "E-mail Notification".

Configure the Trigger:

A new block will appear.

Recipients: Enter the email address (or multiple addresses separated by commas) of who should be notified (e.g., tejas@example.com, dev-team@example.com).

Check the box for "Send e-mail for every unstable build".

Save the job configuration.

That's it! Now, if your build completes but is marked as "unstable" (which typically happens if your code compiles but fails its own tests), Jenkins will automatically send an email to the recipients you listed.
**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

5. Build a jenkins freestyle job that incorporates the following elements: 
●  Pulls the latest code (The job should trigger automatically on code changes). 
●  Builds the Docker image using a Dockerfile located in the repository. 
●  Runs a container from the built image. 
●  Push Build image to Docker Hub with proper tags. 
Note: use your own GitHub account, use any backend technology (e.g. nodejs, Django, 
Flask, etc...)

Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

Part 1: Prerequisites (Do This First!)

Your Jenkins server (or agent) needs to be prepared.

1. Install Docker:
Ensure Docker is installed on the machine Jenkins is running on.

2. Give Jenkins Docker Permissions (CRITICAL):
By default, Jenkins cannot run Docker commands. You must add the jenkins user to the docker group.

# Run this on your Jenkins server
sudo usermod -aG docker jenkins



IMPORTANT: You must restart Jenkins after running this command for the new permissions to take effect.

(On Linux with systemd): sudo systemctl restart jenkins

(Or restart via the Jenkins UI): Go to http://<your-jenkins-url>/safeRestart

3. Store Docker Hub Credentials in Jenkins:
Never hardcode your password in a job!

Go to Manage Jenkins > Credentials.

Click on (global) under the "Stores scoped to Jenkins" section.

Click "Add Credentials" on the left.

Kind: Select "Username with password".

Scope: Global.

Username: Your Docker Hub username (e.g., my-docker-user).

Password: Your Docker Hub password or an Access Token (Access Token is recommended).

ID: Give it a memorable, simple ID, like dockerhub-creds.

Click "OK".

Part 2: Your Sample Node.js Project

Create a new GitHub repository and add the following three files. This will be a simple "Hello World" web app.

File 1: server.js

const express = require('express');
const app = express();
const PORT = 3000;

app.get('/', (req, res) => {
  res.send('Hello from Jenkins Docker Build! This is version 2.');
});

app.listen(PORT, () => {
  console.log(`Server listening on port ${PORT}`);
});



File 2: package.json

{
  "name": "jenkins-docker-demo",
  "version": "1.0.0",
  "description": "Simple app for Jenkins",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.17.1"
  }
}



File 3: Dockerfile

# Use an official Node.js runtime as a parent image
FROM node:18-alpine

# Set the working directory in the container
WORKDIR /app

# Copy package.json and package-lock.json (if available)
COPY package*.json ./

# Install app dependencies
RUN npm install

# Bundle app's source code
COPY . .

# Expose port 3000
EXPOSE 3000

# Define environment variable (optional)
ENV NAME="JenkinsDemo"

# Run the app
CMD [ "npm", "start" ]



Commit and push these three files to your new GitHub repository (e.g., https://github.com/your-username/my-docker-app.git).

Part 3: Jenkins Job Configuration

1. Create the Freestyle Job:

Go to New Item > Freestyle project.

Name it (e.g., docker-build-and-push).

Click "OK".

2. Configure Source Code Management (SCM):

Select "Git".

Repository URL: Enter the URL of the GitHub repo you just created (e.g., https://github.com/your-username/my-docker-app.git).

Branch Specifier: */main (or */master depending on your repo).

3. Configure the Build Trigger (Automatic):

Go to the "Build Triggers" section.

Check the box for "GitHub hook trigger for GITScm polling".

Set up the Webhook in GitHub:

Go to your GitHub repository.

Click Settings > Webhooks > Add webhook.

Payload URL: http://<YOUR_JENKINS_URL>/github-webhook/ (Make sure the trailing slash / is there).

Content type: application/json.

Secret: Leave blank for now.

Which events...: Select "Just the push event."

Click "Add webhook". You should see a green checkmark if Jenkins is reachable from GitHub.

4. Configure the Build Environment (Credentials):
This securely injects your Docker Hub credentials into the build.

Go to the "Build Environment" section.

Check the box "Use secret text(s) or file(s)".

Click "Add" > "Username and password (separated)".

Credentials: Select the credential ID you created in Part 1 (e.g., dockerhub-creds).

Username Variable: DOCKER_USERNAME

Password Variable: DOCKER_PASSWORD

(Jenkins will now create two environment variables, $DOCKER_USERNAME and $DOCKER_PASSWORD, that your script can use).

5. Add the "Execute Shell" Build Step:
This is where all the logic happens.

Go to the "Build" section.

Click "Add build step" > "Execute shell".

Paste the following script into the "Command" box.

IMPORTANT: Change the DOCKERHUB_USERNAME variable to your actual Docker Hub username.

echo "--- Starting Docker Build and Push ---"

# --- 1. Define Variables ---
# !! CHANGE THIS to your Docker Hub username !!
DOCKERHUB_USERNAME="your-dockerhub-username"

# This will be the name of your image
IMAGE_NAME="my-node-app"

# We will tag the image with the Jenkins Build Number
# e.g., "my-node-app:1", "my-node-app:2"
IMAGE_TAG="v${BUILD_NUMBER}"

# This is the full name for Docker Hub
# e.g., "your-dockerhub-username/my-node-app:v1"
FULL_IMAGE_NAME="${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"

# This is the name for the running container
CONTAINER_NAME="my-node-app-container"

echo "Building image: ${FULL_IMAGE_NAME}"

# --- 2. Build the Docker Image ---
# The "." means "use the Dockerfile in the current directory"
docker build -t ${FULL_IMAGE_NAME} .

echo "--- Running Container (Test) ---"

# --- 3. Run the Container ---
# Stop and remove any old container with the same name
# "|| true" prevents the script from failing if the container doesn't exist
docker stop ${CONTAINER_NAME} || true
docker rm ${CONTAINER_NAME} || true

# Run the new container in detached mode (-d)
# Map port 3000 on the host to 3000 in the container
docker run -d -p 3000:3000 --name ${CONTAINER_NAME} ${FULL_IMAGE_NAME}

echo "Container is running. Pausing for 5 seconds to ensure it's stable..."
sleep 5

# Check if the container is actually running
docker ps -f "name=${CONTAINER_NAME}"
echo "Container test complete."

echo "--- Pushing Image to Docker Hub ---"

# --- 4. Log in and Push to Docker Hub ---
# Use the credentials we injected from the "Build Environment" step
# --password-stdin is the most secure way to log in
echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin

# Push the image
docker push ${FULL_IMAGE_NAME}

# Optional: Also push a "latest" tag
docker tag ${FULL_IMAGE_NAME} "${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest"
docker push "${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest"

echo "--- Build and Push Complete ---"

# Optional: Clean up local image to save space
docker rmi ${FULL_IMAGE_NAME}



6. Save and Run:

Click "Save".

Click "Build Now" to run it manually the first time.

Check the "Console Output" to watch the script run. You should see it build, run, and push.

Test the trigger: Go to your GitHub repo, edit server.js (e.g., change the "Hello World" message), and commit the change. A new Jenkins build should start automatically within a minute or two.
**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

Set B=

1. Create a Jenkins freestyle job that automates the following for a Maven project: 
use git Repository: https://github.com/hkhcoder/vprofile-project.git 
branch: atom 
● Pull the source code from a Git repository. 
● Build the project using Maven. The build process should execute a series of goals, 
including clean, package, and jacoco:report. 
● Implement a conditional build step that runs unit tests with a code coverage 
threshold. If code coverage falls below 70%, the build should be marked as unstable, 
but not failed. 
● Archive the generated .war file and the JaCoCo test reports. 
● Implement a versioning strategy for the .war file using a combination of the project 
name, the Jenkins build ID, and a custom build parameter (e.g., 
RELEASE_VERSION). The final artifact should be named 
projectname-RELEASE_VERSION-build_id.war and stored in a dedicated releases 
directory.
Answer=
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

1. Create Job & Add Build Parameter

First, create the job and add the RELEASE_VERSION parameter.

From the Jenkins dashboard, click on "New Item".

Enter an item name (e.g., vprofile-jacoco-build).

Select "Freestyle project" and click "OK".

On the configuration page, go to the "General" tab.

Check the box "This project is parameterized".

Click "Add Parameter" and select "String Parameter".

Fill in the fields:

Name: RELEASE_VERSION

Default Value: 1.0.0 (or 1.0-SNAPSHOT)

Description: "The release version for this build. This will be part of the artifact name."

2. Configure Source Code Management (SCM)

This step pulls the code from the specified atom branch.

Scroll down to the "Source Code Management" section.

Select "Git".

Repository URL: https://github.com/hkhcoder/vprofile-project.git

Branch Specifier: */atom (Note: The default is */main or */master, you must change it to */atom).

3. Configure Build Steps

You will add two build steps in sequence: one to build the project and one to rename the artifact.

Build Step 1: Invoke Maven Goals

Scroll down to the "Build" section.

Click "Add build step" and select "Invoke top-level Maven targets".

Maven Version: Select your configured Maven installation (e.g., Default_Maven).

Goals: clean package jacoco:report

clean: Deletes previous builds.

package: Compiles and creates the .war file.

jacoco:report: Generates the JaCoCo code coverage reports (usually in target/site/jacoco).

Build Step 2: Execute Shell (for Versioning)

Click "Add build step" (in the same "Build" section).

Select "Execute shell".

Paste the following script into the "Command" box. This script will find the .war file, create the releases directory, and rename the file using the Jenkins variables.

echo "--- Starting Artifact Versioning ---"

# --- 1. Define Variables ---

# This is the project name you want in the file
PROJECT_NAME="vprofile-project"

# These variables are automatically provided by Jenkins:
# $RELEASE_VERSION (from the build parameter you created)
# $BUILD_ID (the Jenkins build number, e.g., "1", "2")

echo "Release Version: $RELEASE_VERSION"
echo "Build ID: $BUILD_ID"

# --- 2. Find Source Artifact ---
# Find the .war file Maven created in the 'target' directory
SOURCE_WAR_FILE=$(find target -maxdepth 1 -name "*.war" | head -n 1)

if [ -z "$SOURCE_WAR_FILE" ]; then
    echo "ERROR: No .war file found in target directory. Aborting."
    exit 1
fi

echo "Found source artifact: $SOURCE_WAR_FILE"

# --- 3. Create Target Directory and New Name ---
TARGET_DIR="releases"
TARGET_FILE_NAME="${PROJECT_NAME}-${RELEASE_VERSION}-${BUILD_ID}.war"
TARGET_PATH="${TARGET_DIR}/${TARGET_FILE_NAME}"

echo "Creating directory: $TARGET_DIR"
mkdir -p $TARGET_DIR

# --- 4. Move and Rename Artifact ---
echo "Moving $SOURCE_WAR_FILE to $TARGET_PATH"
mv $SOURCE_WAR_FILE $TARGET_PATH

echo "--- Artifact Versioning Complete ---"


4. Configure Post-build Actions

This is where you will archive the files and, most importantly, set the code coverage threshold.

Post-build Action 1: Record JaCoCo Coverage Report (Conditional Step)

Scroll down to the "Post-build Actions" section.

Click "Add post-build action" and select "Record JaCoCo coverage report". (This option only appears if the JaCoCo plugin is installed).

In the "Path to JaCoCo reports" field, enter the path to the XML report. The default for jacoco:report is:
target/site/jacoco/jacoco.xml

Click the "Advanced..." button to show the threshold settings.

Find the "Set minimum coverage thresholds and build health" section.

You can set thresholds for different metrics (Instructions, Branch, Line, etc.). Let's use Line coverage.

In the row for Line, enter 70 in the "Unstable" column.

Leave the "Failed" column at 0.

This configuration means: If Line coverage is less than 70%, mark the build as "Unstable" (yellow), but don't fail it (red). This matches your requirement perfectly.

Post-build Action 2: Archive the Artifacts

Click "Add post-build action" again.

Select "Archive the artifacts".

In the "Files to archive" field, enter the paths to both the .war file and the reports, separated by a comma:

releases/*.war, target/site/jacoco/**


releases/*.war: Archives your newly named .war file.

target/site/jacoco/**: Archives the full HTML JaCoCo report so you can browse it from the Jenkins build page.

5. Save and Run

Click "Save".

You will be taken to the job's main page. Since it's parameterized, you must click "Build with Parameters".

Confirm the RELEASE_VERSION (or change it) and click "Build".

Summary of Job Flow

You click "Build with Parameters" and provide a RELEASE_VERSION.

Jenkins checks out the atom branch.

Maven runs clean package jacoco:report, creating target/vprofile-v1.war and target/site/jacoco/....

The shell script runs, moving target/vprofile-v1.war to releases/vprofile-project-1.0.0-1.war (assuming version 1.0.0 and build ID 1).

The JaCoCo plugin runs, reads target/site/jacoco/jacoco.xml, and checks the line coverage. If it's 65%, it marks the build as UNSTABLE.

The "Archive Artifacts" step saves both releases/vprofile-project-1.0.0-1.war and the target/site/jacoco directory for you to review.
**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************
2. Create a Jenkins freestyle job that extends the basic Docker workflow with more advanced 
features: 
● Configure the job to automatically trigger whenever new code is pushed to the main 
branch of your Git repository using a Git SCM. 
● Build a Docker image from a Dockerfile located in the repository. The build should 
include passing environment variables (e.g., BUILD_ID, GIT_COMMIT) to the 
Dockerfile as build arguments. 
● Implement a multi-stage Docker build within the Dockerfile to produce a smaller, 
optimized final image. 
● Tag the built image with a dynamic tag that includes the Git commit hash and the 
Jenkins build number (e.g., 
your-dockerhub-username/your-app:git-commit-hash-build-id). 
● Push the tagged image to Docker Hub. 
● Configure email notifications to be sent to a specific user group for both unstable 
and failed builds. The email body should include the build status, a link to the build 
console output, and a list of changes from the last successful build.
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
Part 1: Prerequisites (Plugins & System Config)
This job requires a more advanced email plugin.

Install the "Email Extension" Plugin:

Go to Manage Jenkins > Plugins > Available plugins.

Search for and install "Email Extension Plugin" (also called email-ext). This plugin is required to customize the email body with the changelog.

Configure System Email (SMTP):

You must have already configured Jenkins to send emails.

Go to Manage Jenkins > System.

Fill out the "E-mail Notification" section with your SMTP server details (as we did in a previous task).

Also fill out the "Extended E-mail Notification" section at the bottom (it's added by the plugin). You can often just copy your SMTP settings here. This is what the plugin will use.

Ensure Docker Credentials are Stored:

Make sure your Docker Hub credentials are stored in Manage Jenkins > Credentials with an ID like dockerhub-creds.

Part 2: The Multi-Stage Dockerfile
For this project, you need a new Dockerfile in your repository that uses multi-stage builds and accepts build arguments.

Here is a sample Node.js project you can use. Put these 3 files in your repository (the one from the previous task will work, just replace the Dockerfile).

File 1: Dockerfile (Multi-stage & with Build Args)

Dockerfile

# --- Stage 1: The Builder ---
# Use a full Node.js image to install dependencies
FROM node:18 AS builder

WORKDIR /app

# Copy package files and install dependencies
COPY package*.json ./
RUN npm install

# Copy the rest of the source code
COPY . .

# --- Stage 2: The Final Image ---
# Use a slim, alpine-based image for the smallest size
FROM node:18-alpine

WORKDIR /app

# --- Build Arguments ---
# These ARGs are passed in from the 'docker build' command
ARG BUILD_ID=unknown
ARG GIT_COMMIT=unknown

# --- Environment Variables ---
# Set ARGs as ENV so the running app can access them (optional)
ENV BUILD_ID=$BUILD_ID
ENV GIT_COMMIT=$GIT_COMMIT

# Copy the installed modules from the 'builder' stage
COPY --from=builder /app/node_modules ./node_modules

# Copy the application code
COPY server.js .

# Expose the port the app runs on
EXPOSE 3000

# Run the app
CMD [ "node", "server.js" ]
File 2: server.js (Updated to show build info)

JavaScript

const express = require('express');
const app = express();
const PORT = 3000;

// Read build info from environment variables
const buildId = process.env.BUILD_ID || 'Not Set';
const gitCommit = process.env.GIT_COMMIT || 'Not Set';

app.get('/', (req, res) => {
  res.send(`
    <h1>Hello from the Advanced Docker Build!</h1>
    <p>This app was built by Jenkins.</p>
    <ul>
      <li><b>Build ID:</b> ${buildId}</li>
      <li><b>Git Commit:</b> ${gitCommit}</li>
    </ul>
  `);
});

app.listen(PORT, () => {
  console.log(`Server listening on port ${PORT}`);
});
File 3: package.json

JSON

{
  "name": "jenkins-advanced-docker",
  "version": "1.0.0",
  "description": "App for advanced CI/CD",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.17.1"
  }
}
Commit and push these files to the main branch of your GitHub repository.

Part 3: Jenkins Job Configuration
1. Create Job and Configure SCM:

Create a new Freestyle project (e.g., advanced-docker-pipeline).

Source Code Management: Select "Git".

Repository URL: Your GitHub repo URL.

Branch Specifier: */main

2. Configure Build Trigger:

Go to the "Build Triggers" section.

Check "GitHub hook trigger for GITScm polling".

Set up the Webhook in your GitHub repo's Settings > Webhooks page. The Payload URL is http://<YOUR_JENKINS_URL>/github-webhook/. (This will trigger the job on every git push).

3. Configure Build Environment (Credentials):

Go to the "Build Environment" section.

Check "Use secret text(s) or file(s)".

Click "Add" > "Username and password (separated)".

Credentials: Select dockerhub-creds.

Username Variable: DOCKER_USERNAME

Password Variable: DOCKER_PASSWORD

4. Configure the Build Step (Execute Shell): This script is the core of the job. It gets the Git variables, defines the tag, and passes the build arguments.

Go to the "Build" section, click "Add build step" > "Execute shell".

Paste in the following script (remember to change the DOCKERHUB_USERNAME):

Bash

echo "--- Starting Advanced Docker Build ---"

# --- 1. Define Variables ---
# !! CHANGE THIS to your Docker Hub username !!
DOCKERHUB_USERNAME="your-dockerhub-username"
IMAGE_NAME="my-advanced-app"

# --- 2. Get Dynamic Variables ---
# These variables are injected by the Jenkins Git plugin
echo "Jenkins Build ID: $BUILD_ID"
echo "Git Commit Hash: $GIT_COMMIT"

# Create a short 7-character hash for the tag
GIT_HASH_SHORT=$(echo $GIT_COMMIT | cut -c1-7)
echo "Short Git Hash: $GIT_HASH_SHORT"

# --- 3. Define Dynamic Tag ---
# Format: git-commit-hash-build-id
DYNAMIC_TAG="git-${GIT_HASH_SHORT}-build-${BUILD_ID}"
FULL_IMAGE_NAME="${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${DYNAMIC_TAG}"
LATEST_IMAGE_NAME="${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest"

echo "Building image: $FULL_IMAGE_NAME"

# --- 4. Build the Docker Image ---
# This is the key step:
# --build-arg passes variables from Jenkins *into* the Dockerfile
docker build \
  --build-arg BUILD_ID=$BUILD_ID \
  --build-arg GIT_COMMIT=$GIT_COMMIT \
  -t $FULL_IMAGE_NAME \
  .

echo "Build complete."

# --- 5. Log in and Push to Docker Hub ---
echo "Logging into Docker Hub..."
echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin

echo "Pushing image: $FULL_IMAGE_NAME"
docker push $FULL_IMAGE_NAME

# Also tag this build as 'latest' and push it
echo "Tagging and pushing 'latest'..."
docker tag $FULL_IMAGE_NAME $LATEST_IMAGE_NAME
docker push $LATEST_IMAGE_NAME

echo "--- Build and Push Complete ---"
5. Configure Post-build Actions (Advanced Email): This is the final requirement.

Go to the "Post-build Actions" section.

Click "Add post-build action" and select "Editable Email Notification" (the one from the new plugin).

Project Recipient List: Enter the email address or group (e.g., tejas@example.com, dev-team@example.com).

Content Type: Select HTML (text/html).

Default Subject: Paste this: Build ${BUILD_STATUS}: ${PROJECT_NAME} - Build #${BUILD_NUMBER}

Default Content: This is the most important part. Paste this HTML/token script:

HTML

<h2>Build ${BUILD_STATUS} for ${PROJECT_NAME} (Build #${BUILD_NUMBER})</h2>
<hr>

<p>
  Check the console output for full details:
  <a href="${BUILD_URL}console">${BUILD_URL}console</a>
</p>

<h3>Changes Since Last Successful Build:</h3>

<ul>
  ${CHANGES_SINCE_LAST_SUCCESS, 
    format="<li><b>%c</b> (%a) - %m</li>",
    pathFormat="<a href='${FILE_LINK}'>%f</a><br>"
  }
</ul>

<hr>
<p><i>This is an automated notification from Jenkins.</i></p>
$BUILD_STATUS, $PROJECT_NAME, $BUILD_NUMBER, and $BUILD_URL are all tokens provided by the plugin.

${CHANGES_SINCE_LAST_SUCCESS} is the special token that lists the Git commits.

Add Triggers for Sending the Email:

At the bottom of the email block, click "Add Trigger".

Select "Unstable".

Click "Add Trigger" again.

Select "Failure - Any".

6. Save and Run:

Click "Save".

Push a new commit to your main branch.

The job will automatically start, build the image with the build args, tag it with the commit hash, and push it to Docker Hub.

If it fails, it will send the detailed email you just configured.
**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************
3. Create two linked Jenkins jobs to demonstrate a basic CI/CD pipeline: 

use git Repository: https://github.com/hkhcoder/vprofile-project.git 

branch: main 

I. Job A (Build Job): 

● Create a parameterized build job that takes a string parameter for 

GIT_BRANCH and a choice parameter for BUILD_ENV (e.g., development, 

staging). 

● Pull code from the specified Git branch. 

● Build the Maven project with a goal to generate a .jar or .war file. 

● Archive the build artifact. 

II. Job B (Deploy Job): 

● Create a second parameterized job that is triggered after a successful build of 

Job A. 

● The deploy job should receive the BUILD_ID and BUILD_ENV parameters 

from Job A. 

● Copy the archived artifact from Job A's workspace to a designated 

deployment directory. 
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

Part 1: Create Job A (Build Job)

This job will be parameterized, build the Maven project, and archive the resulting .war file.

Create the Job:

Click "New Item" on the Jenkins dashboard.

Enter the name job-a-build.

Select "Freestyle project" and click "OK".

Add Parameters (General tab):

In the "General" section, check the box "This project is parameterized".

Parameter 1:

Click "Add Parameter" > "String Parameter".

Name: GIT_BRANCH

Default Value: main

Description: "The Git branch to build (e.g., main, develop)."

Parameter 2:

Click "Add Parameter" > "Choice Parameter".

Name: BUILD_ENV

Choices: (Enter one per line)

development
staging


Description: "The target environment for this build."

Configure Source Code Management (SCM):

Scroll to "Source Code Management".

Select "Git".

Repository URL: https://github.com/hkhcoder/vprofile-project.git

Branch Specifier: $GIT_BRANCH

(This is the key: it uses the parameter you just created.)

Configure Build Step (Maven):

Scroll to the "Build" section.

Click "Add build step" > "Invoke top-level Maven targets".

Maven Version: Select your configured Maven (e.g., Default_Maven).

Goals: clean package

(This project builds a .war file, so package is the correct goal.)

Configure Post-Build Actions:
This job needs to do two things after building: archive its own artifact, and then trigger Job B.

Action 1: Archive the artifact

Click "Add post-build action" > "Archive the artifacts".

Files to archive: target/*.war

Action 2: Trigger the deploy job

Click "Add post-build action" > "Trigger/call builds on other projects".

Projects to build: job-b-deploy (You will create this job next, but type the name in now).

Trigger when build is: Stable.

Click the "Add Parameters" button and select "Predefined parameters".

In the "Parameters" box, type:

BUILD_ENV=$BUILD_ENV


(This will take the BUILD_ENV value from Job A and pass it to Job B.)

Save the job.

Part 2: Create Job B (Deploy Job)

This job will be triggered by Job A. It will copy the artifact from Job A's workspace and "deploy" it.

Create the Job:

Click "New Item" on the Jenkins dashboard.

Enter the name job-b-deploy.

Select "Freestyle project" and click "OK".

Add Parameters (General tab):

This job also needs parameters so it can receive them from Job A.

Check "This project is parameterized".

Parameter 1:

Click "Add Parameter" > "String Parameter".

Name: BUILD_ENV

Default Value: development

Description: "Passed automatically from job-a-build."

Configure Build Steps:
This job has two build steps: copy the artifact, then deploy it.

Step 1: Copy Artifact from Job A

Click "Add build step" > "Copy artifacts from another project". (This is from the Copy Artifact plugin).

Project name: job-a-build

Which build: Upstream build that triggered this project

(This is the magic link. It automatically finds the build from Job A that started this one.)

Artifacts to copy: target/*.war

Target directory: (Leave blank to copy to the current workspace.)

Step 2: Simulate Deployment (Execute Shell)

Click "Add build step" > "Execute shell".

Paste the following script into the "Command" box:

echo "--- Starting Deployment Job ---"

# This is the Build ID from Job A, provided by the Copy Artifact plugin
echo "Deploying artifact from Job A, Build ID: $COPY_ARTIFACT_UPSTREAM_BUILD_NUMBER"

# This is the environment passed as a parameter from Job A
echo "Target Environment: $BUILD_ENV"

# Find the artifact we just copied
WAR_FILE=$(find . -maxdepth 2 -name "*.war")

if [ -z "$WAR_FILE" ]; then
  echo "ERROR: No .war file was copied from Job A!"
  exit 1
fi

echo "Found artifact: $WAR_FILE"

# Create a simulated deployment directory
DEPLOY_DIR="/tmp/deployment/$BUILD_ENV"
mkdir -p $DEPLOY_DIR

echo "Copying artifact to $DEPLOY_DIR"
cp $WAR_FILE $DEPLOY_DIR/

echo "--- Deployment Complete ---"
ls -l $DEPLOY_DIR


Save the job.

How to Run Your Pipeline

Go to the dashboard and find job-a-build.

Click the "Build with Parameters" button on the left.

You can leave the GIT_BRANCH as main.

Choose either development or staging from the BUILD_ENV dropdown.

Click "Build".

What will happen:

Job A will start. It will build the main branch.

Once Job A finishes and is "Stable", it will archive its .war file.

Job A will then automatically trigger Job B, passing the BUILD_ENV parameter (e.g., "staging") to it.

Job B will start. Its "Copy Artifact" step will look for the upstream build (Job A) and copy the .war file.

Job B's shell script will then run, find the .war file, and move it to /tmp/deployment/staging.
**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

Set C: 
1. Design, implement, and document a fully automated CI/CD free style job for a sample web 
application using Jenkins. The free style job must leverage on-demand EC2 instances as 
dynamic build agents to demonstrate elastic scaling.

Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
Design Doc: Jenkins CI/CD with Dynamic EC2 Build Agents

This document outlines the design, implementation, and documentation for a Jenkins CI/CD pipeline that leverages on-demand Amazon EC2 instances as dynamic build agents. This provides "elastic scaling," where build agents are provisioned only when needed and terminated when idle, reducing cost.

We will use a sample Maven web application (vprofile-project) as the payload for our CI/CD job.

Part 1: Solution Design & Architecture

Jenkins Controller: A central Jenkins server (master). This can be running anywhere (on-prem, EC2, EKS, etc.), but it must be reachable by the build agents.

AWS (Amazon Web Services):

IAM: To grant Jenkins permission to create/delete EC2 instances.

EC2: To provide the on-demand compute instances.

AMI (Amazon Machine Image): A "template" for our build agents. This is pre-baked with Java, Git, and Maven.

Networking (VPC & Security Groups): To allow the Jenkins controller and EC2 agents to communicate securely.

Jenkins EC2 Plugin: The key component. This plugin, installed on the Jenkins controller, manages the entire lifecycle of the EC2 agents.

Jenkins Job (Freestyle): The CI/CD job. It is configured to only run on an agent with a specific "label" (e.g., ec2-maven-agent).

Workflow:

A build is triggered for the "Freestyle Job".

Jenkins sees the job requires the label ec2-maven-agent.

It checks its "Clouds" configuration and finds the EC2 Plugin can provision an agent with this label.

The EC2 Plugin uses the AWS IAM credentials to make an API call to ec2:RunInstances.

A new EC2 instance is launched from the pre-configured AMI.

Jenkins waits for the instance to boot, then connects via SSH (using a stored private key) to start the Jenkins agent process (agent.jar).

The EC2 agent connects to the controller, takes the job from the queue, and begins the build (e.g., git clone, mvn package).

After the build, the agent is idle.

After a configured "idle timeout" (e.g., 10 minutes), the EC2 Plugin terminates the instance (ec2:TerminateInstances) to save money.

Part 2: AWS Prerequisites (The "Infrastructure")

1. Create an IAM User for Jenkins:
This user will have programmatic access only.

Go to IAM > Users > Create user.

Name: jenkins-ec2-manager

Attach a new policy. Click "Create policy", go to the JSON editor, and paste this:

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:RunInstances",
                "ec2:DescribeInstances",
                "ec2:DescribeRegions",
                "ec2:DescribeSubnets",
                "ec2:DescribeImages",
                "ec2:DescribeKeyPairs",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeInstanceStatus",
                "ec2:TerminateInstances",
                "ec2:CreateTags",
                "ec2:StopInstances"
            ],
            "Resource": "*"
        }
    ]
}


Save the policy (e.g., JenkinsEC2ManagementPolicy) and attach it to the jenkins-ec2-manager user.

On the final screen, save the Access key ID and Secret access key.

2. Create an EC2 SSH Key Pair:
This is how Jenkins will SSH into the new agents.

Go to EC2 > Network & Security > Key Pairs.

Click "Create key pair".

Name: jenkins-agent-key

Format: .pem

Save the jenkins-agent-key.pem file.

3. Create Security Groups:

Agent Security Group (jenkins-agent-sg):

This group will be for the dynamic agents.

Inbound Rules:

Type: SSH (port 22)

Source: The public IP of your Jenkins controller (or its security group ID if both are in the same AWS account). This is critical for security.

Outbound Rules:

Allow all (so it can download Maven dependencies, etc.).

Controller Security Group (Modify existing):

Ensure your Jenkins controller's security group allows inbound traffic on port 8080 (or your Jenkins port) from the jenkins-agent-sg so the agents can connect back.

4. Create the Custom AMI (The "Golden Image"):
This is the most important step. We create a "template" for all our build agents.

Launch a new, basic EC2 instance (e.g., Amazon Linux, Ubuntu). Use the jenkins-agent-sg and jenkins-agent-key.

SSH into the instance: ssh -i jenkins-agent-key.pem ec2-user@<instance-ip>

Install all required build tools:

# Example for Amazon Linux
sudo yum update -y

# Install Java (required for agent.jar)
sudo yum install -y java-11-amazon-corretto-devel

# Install Git
sudo yum install -y git

# Install Maven
sudo yum install -y maven


Create a home directory for the Jenkins agent to work in.

sudo mkdir -p /opt/jenkins
sudo chown -R ec2-user:ec2-user /opt/jenkins


(Note: We are using the default ec2-user for simplicity. A better practice is to create a dedicated jenkins user.)

Go to the EC2 console, find this running instance, and select "Actions" > "Image & Templates" > "Create image".

Image name: jenkins-maven-agent-ami

Click "Create image". Once it's "Available", copy its AMI ID (e.g., ami-0abcdef123456789).

You can now terminate the instance you used to create the AMI.

Part 3: Jenkins Configuration (The "Controller")

1. Install the EC2 Plugin:

Go to Manage Jenkins > Plugins > Available plugins.

Search for and install "Amazon EC2" plugin.

Restart Jenkins.

2. Add AWS Credentials to Jenkins:

Go to Manage Jenkins > Credentials > System > (global).

Credential 1: AWS Credentials:

Click "Add Credentials".

Kind: AWS Credentials

ID: aws-ec2-creds

Access Key ID: The key you saved from Part 2, Step 1.

Secret Access Key: The secret you saved.

Credential 2: EC2 SSH Key:

Click "Add Credentials".

Kind: SSH Username with private key

ID: ec2-ssh-key

Username: ec2-user (or the user you set up on the AMI).

Private Key: Select "Enter directly" and paste the entire contents of your jenkins-agent-key.pem file.

3. Configure the EC2 Cloud:

Go to Manage Jenkins > System > Cloud (at the very bottom).

Click "Add a new cloud" > "Amazon EC2".

Name: AWS-EC2

AWS Credentials: Select aws-ec2-creds.

EC2 Region: Select the region where you created your AMI.

Click "Test Connection". You should see "Success".

AMI: Click "Add AMI".

AMI ID: ami-0abcdef123456789 (The one you copied).

Instance Type: t2.micro (or any you want).

Security group(s): jenkins-agent-sg

Remote root directory: /opt/jenkins (The folder you created on the AMI).

Credentials: Select ec2-ssh-key.

Labels: ec2-maven-agent (This is the key that links the job).

Usage: "Only build jobs with label expressions matching this node".

Idle termination time: 10 (minutes). This is the "elastic" part.

Click "Save".

Part 4: The Jenkins Freestyle Job (The "Implementation")

Now, create the job that will use this setup.

Go to New Item > Freestyle project.

Name: vprofile-ec2-build

General (Critical Step):

Check the box "Restrict where this project can be run".

Label Expression: ec2-maven-agent

(This tells Jenkins it must find an agent with this label. It will trigger the EC2 plugin.)

Source Code Management:

Git

Repository URL: https://github.com/hkhcoder/vprofile-project.git

Branch Specifier: */main

Build:

Add build step > "Invoke top-level Maven targets".

Goals: clean package

Post-build Actions:

Add post-build action > "Archive the artifacts".

Files to archive: target/*.war

Save the job.

Part 5: Run and Observe

Click "Build Now" on the vprofile-ec2-build job.

Go to the "Build Executor Status" on the left menu of the main dashboard.

You will see a new agent appear, "Provisioning (AWS-EC2)..." and then "Connecting...".

Go to your EC2 console. You will see a new t2.micro instance launching and running.

The build will run on that EC2 instance.

After the build finishes, the agent will be "Idle".

Wait 10 minutes (or whatever you set for the idle time). The EC2 plugin will terminate the instance, and it will disappear from your EC2 console.
**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

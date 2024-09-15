
---

### Step 1: Launch EC2 Instance on AWS

1. **Log in to AWS Console**: Go to the AWS Management Console and log in.
   
2. **Navigate to EC2**: 
   - Go to the **EC2 Dashboard**.
   - Click **Launch Instance**.

3. **Configure Instance**:
   - **Name** your instance (e.g., `jenkin-batch`).
   - **Select an AMI**: Choose **Ubuntu** (e.g., `Ubuntu 20.04 LTS`).
   - **Instance Type**: Choose the free-tier eligible instance type (e.g., `t2.micro`).
   
4. **Create Key Pair**:
   - In the **Key Pair** section, either choose an existing key pair or create a new one.
   - If creating a new key pair, download the `.pem` file, which will be required later to SSH into the instance.
   
5. **Network Settings**:
   - Ensure **SSH (port 22)** is enabled for access.
   
6. **Launch the Instance**: 
   - Once all the configurations are done, click **Launch** to start your instance.
   - Wait for the instance to be in the `Running` state.

### Step 2: Configure Security Group to Allow Port 8080

Before accessing Jenkins, you need to allow inbound traffic on port **8080** in the security group.

1. **Go to the EC2 Dashboard** and find your instance.
2. Click on the **Security Group** associated with your instance.
3. Click **Edit Inbound Rules**.
4. **Add a new rule**:
   - Type: **Custom TCP**
   - Port Range: **8080**
   - Source: **Anywhere** (0.0.0.0/0)
5. Click **Save Rules**.

This will allow external access to Jenkins on port **8080**.

### Step 3: Connect to the EC2 Instance

1. **Get SSH Instructions**:
   - Go to the **EC2 Dashboard**, and click on the instance ID to view instance details.
   - Click on the **Connect** button at the top.
   - Go to the **SSH client** tab, and you’ll see the SSH command, which looks something like:
     ```bash
     ssh -i "your-key.pem" ubuntu@ec2-XX-XXX-XX-XX.compute-1.amazonaws.com
     ```
   - This command will allow you to connect to the instance from your local machine.

2. **Set Permissions and SSH into the Instance**:

   - **Open Git Bash** on your local machine and navigate to the folder where the `.pem` file is stored.
   - **Set the proper file permissions** for the key pair by running the following command:
     ```bash
     chmod 400 your-key.pem
     ```

   - **SSH into the instance** using the command provided by AWS:
     ```bash
     ssh -i "your-key.pem" ubuntu@ec2-XX-XXX-XX-XX.compute-1.amazonaws.com
     ```
   - You will now be logged into your EC2 instance.

### Step 4: Install Jenkins on the EC2 Instance

1. **Update the System**:
   - Once logged in, first update the package list:
     ```bash
     sudo apt update
     ```

2. **Install Java**:
   - Jenkins requires Java, so install **OpenJDK 11**:
     ```bash
     sudo apt install openjdk-11-jre
     ```

3. **Add Jenkins Key and Repository**:
   - Download and add the Jenkins repository key:
     ```bash
     sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
       https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
     ```
   - Add the Jenkins repository:
     ```bash
     echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
       https://pkg.jenkins.io/debian-stable binary/" | sudo tee \
       /etc/apt/sources.list.d/jenkins.list > /dev/null
     ```

4. **Install Jenkins**:
   - Update your package list and install Jenkins:
     ```bash
     sudo apt-get update
     sudo apt-get install jenkins
     ```

5. **Start Jenkins and Check Status**:
   - Start Jenkins service:
     ```bash
     sudo systemctl start jenkins
     ```
   - Verify that Jenkins is running:
     ```bash
     systemctl status jenkins
     ```

### Step 5: Access Jenkins

1. **Access Jenkins on the Browser**:
   - Jenkins typically runs on port `8080`. To access Jenkins from a browser, open:
     ```bash
     http://<your-ec2-public-ip>:8080
     ```
   - Make sure your security group allows **inbound** traffic on port `8080** (as configured in Step 2).

2. **Unlock Jenkins**:
   - On your EC2 instance, find the initial admin password with the command:
     ```bash
     sudo cat /var/lib/jenkins/secrets/initialAdminPassword
     ```
   - Copy the password and paste it into the Jenkins setup page in your browser.

### Step 6: Complete Jenkins Setup

1. **Install Suggested Plugins**:
   - Once you unlock Jenkins, you'll be prompted to install plugins. Choose **Install Suggested Plugins**.
   
2. **Create First Admin User**:
   - Set up an admin username and password.

---

Here is the continuation of the steps after accessing Jenkins and creating a pipeline:

---

### Step 7: Finalize Jenkins Setup

1. **Save Configuration**:
   - After completing the initial setup, Jenkins will show your **IP address** and **port** in the configuration.
   - Click **Save and Finish**.
   
2. **Start Using Jenkins**:
   - After saving the configuration, click **Start using Jenkins**.
   - You will be redirected to the Jenkins **Dashboard**.

---

### Step 8: Create a New Pipeline Job

1. **Create a New Item**:
   - From the Jenkins dashboard, click on the **+ New Item** button (usually on the left sidebar).
   
2. **Enter the Job Name**:
   - In the next screen, under "Enter an item name," type a name for your project, for example, `django-cicd`.
   
3. **Select Pipeline**:
   - Below the item name, select **Pipeline** as the project type.
   
4. **Click OK**:
   - After entering the job name and selecting the pipeline option, click **OK** to create the pipeline.

---

### Step 9: Configure the Pipeline

1. **Go to Dashboard**:
   - Once the pipeline is created, you'll be taken back to the **Dashboard**.
   - Find and click on the newly created project (e.g., `django-cicd`).

2. **Click on Configure**:
   - After clicking on the project, navigate to the left sidebar and click **Configure**.

---

You are now inside the configuration options of your newly created pipeline project. From here, you can proceed with setting up your pipeline script, defining stages, and configuring any tools or plugins required for building and deploying your project (like Django in this case).




Here's a step-by-step guide based on your input to finalize the configuration for your Jenkins Pipeline project that builds and deploys a Django application:

---

### Step 10: General Configuration

1. **Description**:
   - Enter: `This CICD Pipeline Builds and Deploys a Django Application`.
   
2. **Discard Old Builds**:
   - **Strategy**: Select **Log Rotation**.
   - **Days to keep builds**: Enter `5` to retain build logs for 5 days.
   - **Max # of builds to keep**: Set this field to the maximum number of builds you wish to retain. For example, enter `10`.

3. **Do not allow concurrent builds**:
   - Check this option to prevent multiple builds from running at the same time.

4. **Do not allow the pipeline to resume if the controller restarts**:
   - Check this if you do not want Jenkins to resume a pipeline if the Jenkins controller is restarted.

5. **GitHub Project**:
   - **Project URL**: Enter `https://github.com/LondheShubham153/django-notes-app.git`.
   - **Advance\/**: 
     - **Display Name**: Enter `"Django Notes App"` 

6. **Advanced Project Options**:
   - **Display Name**: Enter `"Django Notes App CICD"` to give the project a custom display name.

---

### Pipeline Configuration

1. **Definition**:
   - Choose **Pipeline script** from the dropdown.

2. **Pipeline Script**:

   Paste the following pipeline script into the script section:

```groovy
pipeline {
    agent any
    stages {
        stage('Code') {
            steps {
                echo 'Code...'
            }
        }
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }
}
```

---

### Save and Build

- Once everything is set, click **Save** to store your configurations.
- You can now trigger your first build by going to the Jenkins **Dashboard** and clicking **Build Now** for your pipeline.

Let me know if you'd like to integrate GitHub Webhooks or extend the pipeline script to add more complex steps like Docker, testing frameworks, or deployment!

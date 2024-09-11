
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


General Configuration
Description:

Add a brief description of your project:
"This CICD Pipeline Builds and Deploys a Django Application."
Discard Old Builds:

You can enable this option to manage the build history and avoid using too much disk space:
Days to keep builds: Set to 5 (or any number based on your retention policy).
Max # of builds to keep: Set a number like 5 to retain only the most recent builds.
Do not allow concurrent builds:

Check this box to ensure that only one build runs at a time.
GitHub Project:

Enable this option if you are using a GitHub repository.
Project URL: Enter the URL of your GitHub repo:
https://github.com/LondheShubham153/django-notes-app.git/
Display Name (optional):

You can specify a display name to better identify the project: "Django Notes App CICD".
Pipeline Configuration
Definition:

Choose Pipeline script as the definition option.
Pipeline Script:

In the script section, you can define the steps to build, test, and deploy your Django application. Here’s an example of a simple pipeline script for Django:
groovy
Copy code
```bash
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
Save and Execute
Save the configuration.
you are able to see this screen
![image](https://github.com/user-attachments/assets/22174c41-2c1d-4a8d-84fe-0fe1b2335692)



Return to the Jenkins Dashboard.
Click on your pipeline project (e.g., django-cicd).
Click Build Now to start the build manually or let it trigger automatically based on your configuration.

After completing the Jenkins Pipeline configuration and running the build, follow these steps to ensure everything is functioning correctly:

### 1. Check Pipeline Stages
- Navigate to the Jenkins **Dashboard**.
- Click on your project, which is now named **Django Notes App CICD**.
- Go to the **Stage View** section.
- Ensure all stages (Clone, Build, Test, Package, Deploy) are running successfully.
- Each stage should show either a green checkmark (✓) for success or a red cross (✗) for failure.

### 2. Pipeline Running Properly
- If all stages show success (green checkmarks), it means the pipeline is running properly.
- You can click on individual stages to view detailed logs and status of each one.

### 3. If the Pipeline Fails
- In case a stage fails (red cross), click on the failed stage.
- Select **Console Output** to view the detailed log for that stage.
- Review the logs for any errors or issues that occurred during the build process.

### 4. Common Errors and Fixes
Here are some typical errors you might encounter and how to fix them:

- **Dependency Installation Fails (Build Stage)**:
   - Ensure that `requirements.txt` contains the correct packages and versions.
   - Verify that Python and `pip` are correctly installed on the system.

   **Fix**: 
   ```bash
   sudo apt install python3-pip
   pip install -r requirements.txt
   ```

- **Test Failures (Test Stage)**:
   - Check for any failing unit tests in the Django project.
   - You might need to update your test cases or environment to match the pipeline configuration.

   **Fix**: 
   ```bash
   python manage.py test
   ```

- **Docker Build/Run Issues (Package or Deploy Stage)**:
   - Make sure Docker is installed and running on the Jenkins server.
   - Ensure that the Dockerfile is properly configured for the Django project.

   **Fix**: 
   ```bash
   sudo apt install docker.io
   docker build -t django-notes-app .
   ```

### 5. Fixing Errors in Jenkins
- Once you've identified the error from the **Console Output**, take appropriate action:
   - Update your **pipeline script** in Jenkins if needed.
   - Make any necessary changes to your source code or project dependencies.

### 6. Rerun the Pipeline
- After making any fixes:
   - Go back to the Jenkins Dashboard.
   - Click on your pipeline project (e.g., `Django Notes App CICD`).
   - Click **Build Now** to rerun the pipeline.
   - Verify if the issues are resolved and all stages pass successfully.

By following these steps, you can troubleshoot and fix any errors that arise during the Jenkins pipeline execution. Let me know if you need help with specific errors or debugging steps!

specifying an agent is a crucial step in Jenkins pipelines, as it defines where the pipeline should execute. The agent directive in a Jenkins pipeline tells Jenkins on which machine or node the pipeline should run. This is especially important when you have a multi-node Jenkins setup or different environments for different jobs.

Here’s how the agent directive works and why you need it:

1. Single Jenkins Node (Master)
If you are running everything on a single Jenkins node (master), you can simply use:

groovy
Copy code
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
    }
}
agent any: This means that Jenkins can run the pipeline on any available node, including the master.
If you haven't set up additional nodes, Jenkins will use its own machine (master) to run the pipeline.
2. Using Specific Agents/Nodes
If you have specific nodes set up (e.g., for different environments or purposes), you might want to assign a particular node/agent for the pipeline to run on. You can specify an agent based on labels or node names:

groovy
Copy code
pipeline {
    agent {
        label 'ubuntu-agent'
    }
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
    }
}

            }
        }
        stage('Test') {
            agent { label 'test-agent' }
            steps {
                echo 'Testing...'
            }
        }
        stage('Deploy') {
            agent { label 'deploy-agent' }
            steps {
                echo 'Deploying...'
            }
        }
    }
}

# Why Use an Agent?

Yes, specifying an **agent** is a crucial step in Jenkins pipelines, as it defines where the pipeline should execute. The **agent** directive in a Jenkins pipeline tells Jenkins on which machine or node the pipeline should run. This is especially important when you have a multi-node Jenkins setup or different environments for different jobs.

Here’s how the **agent** directive works and why you need it:

### 1. **Single Jenkins Node (Master)**

If you are running everything on a single Jenkins node (master), you can simply use:

```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
    }
}
```

- `agent any`: This means that Jenkins can run the pipeline on any available node, including the master.
- If you haven't set up additional nodes, Jenkins will use its own machine (master) to run the pipeline.

### 2. **Using Specific Agents/Nodes**

If you have specific nodes set up (e.g., for different environments or purposes), you might want to assign a particular node/agent for the pipeline to run on. You can specify an **agent** based on labels or node names:

```groovy
pipeline {
    agent {
        label 'ubuntu-agent'
    }
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
    }
}
```

- `agent { label 'ubuntu-agent' }`: This ensures that the pipeline will only run on the Jenkins node labeled **ubuntu-agent**.
- This is useful if your Jenkins has multiple nodes (for example, separate nodes for Linux, Windows, or Mac environments).

### 3. **Running Specific Stages on Different Nodes**

You can also define different agents for different stages in the pipeline. For example, you might build on one machine but test or deploy on another:

```groovy
pipeline {
    agent none // No global agent
    stages {
        stage('Build') {
            agent { label 'build-agent' }
            steps {
                echo 'Building...'
            }
        }
        stage('Test') {
            agent { label 'test-agent' }
            steps {
                echo 'Testing...'
            }
        }
        stage('Deploy') {
            agent { label 'deploy-agent' }
            steps {
                echo 'Deploying...'
            }
        }
    }
}
```

### 4. **Why Use an Agent?**

Here’s how the agent directive works and why you need it:

1. Single Jenkins Node (Master)
If you are running everything on a single Jenkins node (master), you can simply use:

groovy
Copy code
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
    }
}
agent any: This means that Jenkins can run the pipeline on any available node, including the master.
If you haven't set up additional nodes, Jenkins will use its own machine (master) to run the pipeline.
2. Using Specific Agents/Nodes
If you have specific nodes set up (e.g., for different environments or purposes), you might want to assign a particular node/agent for the pipeline to run on. You can specify an agent based on labels or node names:

groovy
Copy code
pipeline {
    agent {
        label 'ubuntu-agent'
    }
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
    }
}
agent { label 'ubuntu-agent' }: This ensures that the pipeline will only run on the Jenkins node labeled ubuntu-agent.
This is useful if your Jenkins has multiple nodes (for example, separate nodes for Linux, Windows, or Mac environments).
3. Running Specific Stages on Different Nodes
You can also define different agents for different stages in the pipeline. For example, you might build on one machine but test or deploy on another:

groovy
Copy code
pipeline {
    agent none // No global agent
    stages {
        stage('Build') {
            agent { label 'build-agent' }
            steps {
                echo 'Building...'
            }
        }
        stage('Test') {
            agent { label 'test-agent' }
            steps {
                echo 'Testing...'
            }
        }
        stage('Deploy') {
            agent { label 'deploy-agent' }
            steps {
                echo 'Deploying...'
            }
        }
    }
}
4. Why Use an Agent?








Yes, specifying an **agent** is a crucial step in Jenkins pipelines, as it defines where the pipeline should execute. The **agent** directive in a Jenkins pipeline tells Jenkins on which machine or node the pipeline should run. This is especially important when you have a multi-node Jenkins setup or different environments for different jobs.

Here’s how the **agent** directive works and why you need it:

### 1. **Single Jenkins Node (Master)**

If you are running everything on a single Jenkins node (master), you can simply use:

```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
    }
}
```

- `agent any`: This means that Jenkins can run the pipeline on any available node, including the master.
- If you haven't set up additional nodes, Jenkins will use its own machine (master) to run the pipeline.

### 2. **Using Specific Agents/Nodes**

If you have specific nodes set up (e.g., for different environments or purposes), you might want to assign a particular node/agent for the pipeline to run on. You can specify an **agent** based on labels or node names:

```groovy
pipeline {
    agent {
        label 'ubuntu-agent'
    }
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
    }
}
```

- `agent { label 'ubuntu-agent' }`: This ensures that the pipeline will only run on the Jenkins node labeled **ubuntu-agent**.
- This is useful if your Jenkins has multiple nodes (for example, separate nodes for Linux, Windows, or Mac environments).

### 3. **Running Specific Stages on Different Nodes**

You can also define different agents for different stages in the pipeline. For example, you might build on one machine but test or deploy on another:

```groovy
pipeline {
    agent none // No global agent
    stages {
        stage('Build') {
            agent { label 'build-agent' }
            steps {
                echo 'Building...'
            }
        }
        stage('Test') {
            agent { label 'test-agent' }
            steps {
                echo 'Testing...'
            }
        }
        stage('Deploy') {
            agent { label 'deploy-agent' }
            steps {
                echo 'Deploying...'
            }
        }
    }
}
```

### 4. **Why Use an Agent?**

- **Work Distribution**: If you have multiple nodes, you can distribute work across different machines or environments (e.g., one for building, another for testing).
- **Isolation**: Different stages may require different environments (e.g., building might need a specific version of Java, while testing might need Python).
- **Scalability**: In larger setups, specific jobs or pipelines can be directed to nodes with more capacity or necessary resources.

### 5. **Agent Options**

You can customize agents further based on needs:

- **Docker Agents**: You can run pipeline stages inside Docker containers.
  ```groovy
  agent {
      docker { image 'python:3.8' }
  }
  ```
- **Node Blocks**: You can specify entire node blocks for complex scenarios.
  ```groovy
  node('test-agent') {
      echo 'Running on test-agent'
  }
  ```

### Summary:

- If your Jenkins is a single-node setup, using `agent any` will work fine, and Jenkins will run the pipeline on the available node (master).
- If you have multiple agents/nodes, or want specific jobs to run on certain environments, it's important to specify the correct agent or node.

In your case, if Jenkins is running on an **Ubuntu instance**, you can either use `agent any` or specify the **Ubuntu node** explicitly if your Jenkins is connected to multiple nodes. If the pipeline is failing to find an agent, you might need to verify your node setup in Jenkins.


---
To set up a Jenkins Agent on an EC2 instance and run commands using Git Bash, follow these steps:

1. Launch a New EC2 Instance for the Jenkins Agent
Step 1: Launch EC2 Instance
Go to the AWS Management Console and navigate to EC2.
Click Launch Instance.
AMI: Choose an Ubuntu-based image (Ubuntu 22.04 or 20.04 LTS).
Instance Type: Choose an instance type (e.g., t2.micro for testing).
Key Pair: Select an existing key pair or create a new one.
Network Settings: Make sure that SSH access is allowed (port 22).
Complete the instance configuration and launch the instance.
Step 2: Configure Security Groups
Ensure that the EC2 instance’s security group allows inbound traffic for SSH (port 22) and Jenkins (port 8080) if necessary.
2. Connect to the EC2 Instance Using Git Bash
Once the EC2 instance is running, connect to it using Git Bash from your local machine.

Step 1: Obtain the Public IP or DNS
Navigate to the EC2 Dashboard in AWS.
Select your agent instance and copy the public IP address or Public DNS.
Step 2: SSH into the Instance
Open Git Bash on your local machine.
Navigate to the directory where your .pem SSH key is located.
Run the following command to SSH into the instance:

```bash
Copy code
ssh -i /path/to/your-key.pem ubuntu@<public-ip-or-dns>
```
Replace /path/to/your-key.pem with the actual path to your .pem key file and <public-ip-or-dns> with the public IP or DNS of your EC2 instance.

3. Run Commands on the EC2 Instance
Once connected to the instance, you'll need to update the system and install Java for Jenkins to run on the agent instance.

Step 1: Update the Package List
```bash
sudo apt update
```
Step 2: Install OpenJDK 11
```bash
sudo apt install openjdk-11-jre
```
Step 3: Verify the Java Installation
```bash
java -version
```

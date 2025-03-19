To create a simple static web application using Node.js, set up a Git repository, automate the CI/CD pipeline using Jenkins, and deploy the web application to a production server using Multipass on macOS, you can follow these steps. 

### Steps Overview:
1. **Create a Node.js Static Web Application**
2. **Initialize Git Repository**
3. **Set Up a Jenkins Server**
4. **Create a Multipass VM for Production**
5. **Configure Jenkins CI/CD Pipeline**
6. **Deploy to Multipass VM on commit**

---

### 1. Create a Node.js Static Web Application

#### Step 1.1: Set up the basic structure

On your macOS, create the directory for your Node.js app.

```bash
mkdir simple-web-app
cd simple-web-app
npm init -y
```

#### Step 1.2: Install required packages

```bash
npm install express
```

#### Step 1.3: Create a simple Express server

Create a file `app.js`:

```js
// app.js
const express = require('express');
const path = require('path');
const app = express();

app.use(express.static(path.join(__dirname, 'public')));

app.get('/', (req, res) => {
  res.sendFile(path.join(__dirname, 'public', 'index.html'));
});

const PORT = 3000;
app.listen(PORT, () => {
  console.log(`Server is running on http://localhost:${PORT}`);
});
```

#### Step 1.4: Create a static HTML file

Create a `public/` directory, and inside it, create a simple `index.html` file.

```html
<!-- public/index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simple Web App</title>
</head>
<body>
    <h1>Welcome to the Simple Web App!</h1>
</body>
</html>
```

---

### 2. Initialize Git Repository

#### Step 2.1: Initialize Git

```bash
git init
git add .
git commit -m "Initial commit of static web app"
```

#### Step 2.2: Create a repository on GitHub (or any Git hosting service)

Create a new GitHub repository and link your local repository to the remote one.

```bash
git remote add origin https://github.com/yourusername/simple-web-app.git
git push -u origin master
```

---

### 3. Set Up Jenkins Server

#### Step 3.1: Install Jenkins on macOS (optional, if you need Jenkins on your Mac)

Follow these instructions to install Jenkins:
- Install Java (required for Jenkins) if not already installed:

```bash
brew install openjdk@11
export PATH="/usr/local/opt/openjdk@11/bin:$PATH"
```

- Install Jenkins using Homebrew:

```bash
brew install jenkins-lts
```

- Start Jenkins:

```bash
brew services start jenkins-lts
```

- Open Jenkins in a browser:

```
http://localhost:8080
```

#### Step 3.2: Set Up Jenkins on a Server (optional)

If you're setting up Jenkins on a separate server (e.g., on your Multipass VM), install Jenkins there following the Jenkins installation guide for your distribution.

---

### 4. Create a Multipass VM for Production Server

#### Step 4.1: Install Multipass

```bash
brew install --cask multipass
```

#### Step 4.2: Launch a VM instance

```bash
multipass launch --name production-vm --mem 4G --disk 20G
```

#### Step 4.3: SSH into the VM

```bash
multipass shell production-vm
```

#### Step 4.4: Install Node.js on the VM

You can install Node.js and other dependencies on the VM as follows:

```bash
sudo apt update
sudo apt install nodejs npm
```

#### Step 4.5: Clone the Web App from Git Repository

```bash
git clone https://github.com/yourusername/simple-web-app.git
cd simple-web-app
npm install
```

---

### 5. Configure Jenkins CI/CD Pipeline

#### Step 5.1: Install Necessary Jenkins Plugins

1. Go to **Manage Jenkins > Manage Plugins**.
2. Install plugins:
   - Git
   - NodeJS
   - SSH Agent (to deploy to the production server)

#### Step 5.2: Create a Jenkins Job

1. Go to **New Item**, then select **Freestyle project** and give it a name, such as `SimpleWebAppDeploy`.
2. In the **Source Code Management** section, select **Git** and add your repository URL.
3. Under **Build Triggers**, select **GitHub hook trigger for GITScm polling** (if you want GitHub webhooks to trigger builds on push).
4. In the **Build Environment** section, add **NodeJS** (install NodeJS on Jenkins if necessary), and then define the build steps.

#### Step 5.3: Build Steps

Under **Build**, add the following:

1. **Execute shell** to install dependencies and run tests (if any):

```bash
npm install
```

2. **Deploy the app** (example using SSH to your Multipass VM):

```bash
ssh -o StrictHostKeyChecking=no ubuntu@<IP_ADDRESS_OF_VM> << 'EOF'
  cd /path/to/simple-web-app
  git pull origin master
  npm install
  pm2 restart app.js
EOF
```

Replace `<IP_ADDRESS_OF_VM>` with the IP address of the VM created via Multipass.

#### Step 5.4: Configure GitHub Webhooks (optional)

Go to your GitHub repository and set up a webhook to notify Jenkins about new commits:

- URL: `http://<JENKINS_URL>/github-webhook/`
- Content type: `application/json`

---

### 6. Deploy to Multipass VM on Commit

#### Step 6.1: Test CI/CD Pipeline

Whenever you push changes to your Git repository, Jenkins should automatically trigger a build and deploy the changes to the Multipass VM.

To test:

1. Commit and push changes to your GitHub repository.
2. Jenkins should trigger the build and deploy the latest changes to the VM.
3. Visit your VM's IP address to see the deployed web app.

---

### Conclusion

By following these steps, you've:

1. Created a simple static web app using Node.js and Express.
2. Set up a Git repository for version control.
3. Installed Jenkins to automate the CI/CD pipeline.
4. Set up a Multipass VM as your production server.
5. Configured Jenkins to automatically deploy the app to your Multipass VM on every commit.

Now, your web app will automatically deploy whenever you push changes to your Git repository!


Create a .gitignore file: Inside your project root, create a file named .gitignore.

bash
Copy
touch .gitignore
Edit .gitignore: Open the .gitignore file in your preferred text editor (e.g., nano, vim, or VSCode), and add the following lines to ignore the node_modules folder and other common Node.js files/folders that are typically ignored:

txt
Copy
# Node.js dependencies
node_modules/

# Logs
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Environment variables
.env

# OS generated files
.DS_Store
Add .gitignore to Git and commit:

After creating the .gitignore file, add it to your repository and commit the changes.

bash
Copy
git add .gitignore
git commit -m "Add .gitignore to exclude node_modules and other files"
Push changes to GitHub:

Now push the changes to your remote Git repository.

bash
Copy
git push

 2289  brew install jenkins-lts\n
 2290  brew services start jenkins-lts\n
  2312  brew services stop jenkins-lts\n
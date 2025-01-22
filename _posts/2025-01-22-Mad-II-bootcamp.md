
# Setting Up Things for the Project

Follow the instructions given in the below site to install WSL:

[WSL Installation Guide](https://learn.microsoft.com/en-us/windows/wsl/install)

Make sure you reboot your system after installing WSL.

## Now Install Git in Your WSL Setup

```bash
sudo apt install git
git config --global user.name "Your Name"
git config --global user.email "Your Email"
```

## Git Initialization
```bash
cd /path/to/your/project
git init
git add .
git commit -m "Initial commit"
git remote add origin <your remote origin URL>
git branch --set-upstream-to=origin/main main  # Set the main branch
```

## Git ignre file

```bash
touch .gitignore
```


add the following content to the .gitignore file
```bash
# Node.js / Vue.js
/node_modules/
/dist/
/.env

# npm logs
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Vite cache
.vite/

# Python
*.pyc
*.pyo
*.pyd
__pycache__/
env/
venv/
*.env

# VS Code
.vscode/

# Mac OS
.DS_Store

# Windows
Thumbs.db
```



## Download and Install Node and npm

Download and install NVM (Node Version Manager):

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
```

Then, install Node.js:

```bash
nvm install 22
```

Verify the Node.js version:

```bash
node -v  # Should print "v22.13.1"
nvm current  # Should print "v22.13.1"
```

Verify npm version:

```bash
npm -v  # Should print "10.9.2"
```

Check your Node.js and npm version:

```bash
node -v
npm -v
```

Check your Python version (ensure you have Python installed and it's above 3.6):

```bash
python3 --version
```

# Initializing the Frontend and Backend Project

## Setting Up Frontend

Go to the root folder of your project:

```bash
cd /path/to/your/project
```

Create the Vue frontend project using Vite:

```bash
npm create vite@latest frontend --template vue
cd frontend
npm install
npm run dev
```

## Setting Up Backend

Create the backend directory and set up a Python virtual environment:

```bash
mkdir backend
cd backend
python -m venv env
```

## Push Your New Changes to the Remote Origin

Commit your changes:

```bash
git commit -m "Initial commit"
git push
```
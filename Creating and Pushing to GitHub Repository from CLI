# Creating and Pushing to GitHub Repository from CLI

## Prerequisites
• Git installed on your system
• GitHub CLI (gh) installed
• GitHub account with authentication set up

## Step-by-Step Instructions

### 1. Install GitHub CLI (if not already installed)
bash
# On Ubuntu/Debian
sudo apt install gh

# On macOS
brew install gh

# On other systems, visit: https://cli.github.com/


### 2. Authenticate with GitHub
bash
gh auth login

Follow the prompts to authenticate via browser or token.

### 3. Initialize Local Repository
bash
# Navigate to your project directory
cd /path/to/your/project

# Initialize git repository
git init

# Add files to staging
git add .

# Create initial commit
git commit -m "Initial commit"


### 4. Create GitHub Repository
bash
# Create public repository
gh repo create your-repo-name --public

# Or create private repository
gh repo create your-repo-name --private

# Create with description
gh repo create your-repo-name --description "Your project description" --public


### 5. Push to GitHub
bash
# Add remote origin (if not automatically added)
git remote add origin https://github.com/your-username/your-repo-name.git

# Push to main branch
git push -u origin main


## Alternative: Create Repository First, Then Clone

### Option A: Create Empty Repository
bash
# Create repository on GitHub
gh repo create your-repo-name --public

# Clone to local machine
gh repo clone your-username/your-repo-name

# Add your files and commit
cd your-repo-name
# ... add your files ...
git add .
git commit -m "Initial commit"
git push


## Useful Commands

### Check Repository Status
bash
# View repository info
gh repo view

# Check git status
git status

# View remotes
git remote -v


### Managing Branches
bash
# Create and switch to new branch
git checkout -b feature-branch

# Push new branch
git push -u origin feature-branch


That's it! Your local project is now connected to a GitHub repository and you can continue using standard git commands to push changes.

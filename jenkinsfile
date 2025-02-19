pipeline {
    agent any
 
    tools {
        nodejs "NodeJS_18"  // Ensure this matches the tool name in Jenkins settings
    }
 
    environment {
        NETLIFY_AUTH_TOKEN = credentials('netlify_token')
        NETLIFY_SITE_ID = '3d19b64f-abbf-46b6-ba18-c7f5f117c385'
        GITHUB_TOKEN = credentials('github_token')
        REPO_URL = "https://github.com/praneethgadagoni/demo1.git"
        MAIN_BRANCH = "main"
        PROD_BRANCH = "prod"
    }
 
    stages {
        stage('Checkout Code') {
            steps {
                script {
                    echo "🔄 Checking out code from GitHub..."
                    sh '''
                        rm -rf demo1 || true
                        git clone -b $MAIN_BRANCH $REPO_URL demo1 || { echo "❌ Git clone failed"; exit 1; }
                        echo "✅ Code checkout complete."
                    '''
                }
            }
        }
 
        stage('Verify Directory Structure') {
            steps {
                script {
                    sh '''
                        echo "📁 Checking if demo1 exists..."
                        ls -l demo1 || { echo "❌ Netlify repo not found!"; exit 1; }
                        echo "✅ Directory structure verified."
                    '''
                }
            }
        }
 
        stage('Verify Node.js & npm') {
            steps {
                script {
                    echo "🔍 Checking Node.js and npm versions..."
                    sh '''
                        echo "Using NodeJS from Jenkins tool config..."
                        which node || { echo "❌ Node.js not found!"; exit 1; }
                        which npm || { echo "❌ npm not found!"; exit 1; }
                        echo "✅ Node.js Version: $(node -v)"
                        echo "✅ npm Version: $(npm -v)"
                    '''
                }
            }
        }
 
        stage('Clean & Install Dependencies') {
            steps {
                script {
                    sh '''
                        echo "🧹 Cleaning old dependencies..."
                        cd demo1 
                        rm -rf node_modules package-lock.json
                        echo "📦 Installing dependencies..."
                        npm install || { echo "❌ Failed to install dependencies"; exit 1; }
                    '''
                }
            }
        }
 
        stage('Run Tests') {
            steps {
                script {
                    sh '''
                        echo "🧪 Running test cases..."
                        cd demo1 
                        npm test || { echo "❌ Tests failed"; exit 1; }
                        echo "✅ All tests passed!"
                    '''
                }
            }
        }
 
        stage('Deploy to demo1 (Test)') {
            steps {
                script {
                    echo "🚀 Deploying to Netlify (Test)..."
                    sh '''
                        cd demo1 # Ensure you're in the correct directory for deployment
                        git checkout main
                        git pull origin main
                        # Install dependencies and build the project
                        npm install
                        npm run build  # This generates the dist directory (or build folder depending on configuration)
 
                        # Install Netlify CLI
                        npm install -g netlify-cli
 
                        # Deploy to Netlify using the correct directory
                        npx netlify deploy --auth $NETLIFY_AUTH_TOKEN --site $NETLIFY_SITE_ID --dir build --message "Test deployment" || { echo "❌ Test deployment to Netlify failed"; exit 1; }
 
                        echo "✅ Test deployment successful!"
                    '''
                }
            }
        }


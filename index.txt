Prerequisites & Where to Create the Tokens
1. Where to create a Personal Access Token (PAT) in Docker Hub:
 * Log in to Docker Hub.
 * Click your username / profile avatar in the top-right corner and select Account Settings.
 * In the left sidebar, click Security (or Personal access tokens).
 * Click New Access Token (or Generate token).
 * Give the token a description (e.g., lab-ci-token) and set access permissions to Read, Write, Delete (or Read & Write).
 * Click Generate and copy the token immediately. You will use this token in place of your password when executing docker login.
2. Where to create a Personal Access Token (PAT) in GitHub:
 * Go to [suspicious link removed].
 * Scroll to the bottom of the left sidebar and select Developer settings.
 * Select Personal access tokens → Tokens (classic) (or Fine-grained tokens).
 * Click Generate new token.
 * Check the required scopes:
   * write:packages
   * read:packages
   * repo (to push the code)
 * Click Generate token and copy it.


Project Setup Code
Create a folder named Program-6 on your machine and navigate inside it:
mkdir Program-6 && cd Program-6

package.json
{
  "name": "program-6",
  "version": "1.0.0",
  "description": "DevOps automated build pipeline test",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}

index.js
const express = require('express');
const app = express();
const PORT = 3000;

app.get('/', (req, res) => {
  res.send('Automated build pipeline deployed successfully!');
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});

Dockerfile
# Use the official Node.js 18 lightweight Alpine Linux image as the base
FROM node:18-alpine

# Set the working directory inside the container to /app
WORKDIR /app

# Copy package.json and package-lock.json into the container
COPY package*.json ./

# Install project dependencies specified in package.json
RUN npm install

# Copy all remaining source code and files from the host into the container working directory
COPY . .

# Document that the application inside the container will listen on port 3000
EXPOSE 3000

# Specify the default command to execute when the container starts
CMD ["node", "index.js"]



Step-by-Step Execution Commands
> Replace <dockerhub-username> with your Docker Hub ID and <github-username> with your GitHub username.
> 
1. Build the Docker Image
docker build -t program-6 .

Verification: Run docker images to verify program-6 is listed.



2. Tag and Push to Docker Hub
docker tag program-6 <dockerhub-username>/program-6:latest

# Log in using your Docker Hub PAT as the password
docker login -u <dockerhub-username>

# Push image
docker push <dockerhub-username>/program-6:latest

Verification: Go to Docker Hub and verify that the program-6 repository has been updated with the latest tag.



3. Tag and Push to GitHub Container Registry (GHCR)
# Tag for GHCR
docker tag program-6 ghcr.io/<github-username>/program-6:latest

# Log in to GHCR using your GitHub PAT
echo "<YOUR_GITHUB_PAT>" | docker login ghcr.io -u <github-username> --password-stdin

# Push image to GHCR
docker push ghcr.io/<github-username>/program-6:latest

Verification: Check your GitHub profile under the Packages tab to ensure program-6 appears.



4. Push Source Code to GitHub
Ensure you have created an empty repository named program-6 on GitHub:
git init
git add .
git commit -m "Initial commit"
git branch -M main

# Link remote (if not already linked)
git remote add origin https://github.com/<github-username>/program-6.git

# If 'origin already exists' error appears:
git remote set-url origin https://github.com/<github-username>/program-6.git

# Verify remote configuration
git remote -v

# Push source code
git push -u origin main

Verification: When prompted for the GitHub password in the terminal, paste your GitHub PAT. The repository on GitHub will now reflect the code.

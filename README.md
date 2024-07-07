# Software-Engineering-Studio-ENGR5590G-Peresntation

Moein Abtahi 

## Example 1 :  Automating File Creation in a GitHub Repository with GitHub Actions

Step-by-Step Guide:
### 1. Create a Personal Access Token (PAT):
1. Go to your GitHub account settings.
2. Navigate to Developer settings > Personal access tokens.
3. Click on Generate new token.
4. Select the scopes (permissions) required, such as repo (for full control of private repositories).
5. Generate the token and copy it.
### 2. Add the PAT as a Secret in Your Repository:
1. Go to your GitHub repository.
2. Navigate to Settings > Secrets and variables > Actions.
3. Click on New repository secret.
4. Name the secret ACTIONS_PAT (or any name you prefer).
5. Paste the copied PAT into the value field and save it.
### 3. Grant GitHub Actions Workflow Permissions:
1. Go to your GitHub repository
2. Navigate to Settings > Actions > General.
3. Under Workflow permissions part, choose Read and write permissions/
4. Click on Save.
### 4. Create a yaml file
```yaml
name: Auto File Creation

on:
  push:
    branches:
      - main
  schedule:
    - cron: '0 0 * * *' # This will run the action daily at midnight UTC

jobs:
  create-file:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Create new file
        run: |
          mkdir -p new_folder
          echo "Software-Engineering-Studio-ENGR5590G-Presentation" > new_folder/newfile.txt

      - name: Commit and push changes
        env:
          GITHUB_TOKEN: ${{ secrets.ACTIONS_PAT }}
        run: |
          git config --global user.name 'github-actions'
          git config --global user.email 'github-actions@github.com'
          git add new_folder/newfile.txt
          git commit -m "Automated file creation"
          git push https://${{ secrets.ACTIONS_PAT }}@github.com/your_username/your_repo-name.git

```
### 5. Actual Example: Creating a File Based on LLM Response and Saving It in a Folder Path from a CSV File
I will demonstrate this in class : [https://github.com/MoeinAbtahi/Foundations.of.Software.Engineering]

## Example 2 : Automating Repository Uploads to Another Repository or Service

### 1. Docker Hub Credentials:
1. **Generate Docker Hub Access Token:**
   - Go to Docker Hub and log in to your account.
   - Navigate to Account Settings > Security > New Access Token.
   - Give it a name (e.g., GitHub Actions).
   - Select the required permissions (e.g., write:packages, read:packages for Docker Hub).
   - Generate the token and copy it.

2. **Add Docker Hub Credentials as Repository Secrets:**
   - Go to your GitHub repository.
   - Navigate to Settings > Secrets > New repository secret.
   - Name the first secret `DOCKER_USERNAME` (for your Docker Hub username).
   - Paste your Docker Hub username as the value.
   - Add another secret named `DOCKER_PASSWORD` and paste the Docker Hub access token you generated.
   - Save both secrets.

### 2. SSH Private Key for Deployment:
1. **Generate SSH Key Pair (if not already done):**
   - If you don't have an SSH key pair (public and private), generate one:
     ```bash
     ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
     ```
   - Follow the prompts to generate the key pair.

2. **Add SSH Private Key as a Repository Secret:**
   - Copy the contents of the private key file (`~/.ssh/id_rsa`).
   - Go to your GitHub repository.
   - Navigate to Settings > Secrets > New repository secret.
   - Name the secret `SSH_PRIVATE_KEY`.
   - Paste the contents of your private key file into the value field.
   - Save the secret.

### 3. GitHub Actions Workflow Permissions:
1. **Grant Read and Write Permissions to Workflows:**
   - Go to your GitHub repository.
   - Navigate to Settings > Actions > General.
   - Under "Workflow permissions," ensure "Read and write permissions" is selected.
   - Click on Save to apply the permissions.

### Summary of Secrets and Usernames:
- **Secrets:**
  - `DOCKER_USERNAME`: Your Docker Hub username.
  - `DOCKER_PASSWORD`: Docker Hub access token.
  - `SSH_PRIVATE_KEY`: SSH private key for deployment.



```yaml
name: Comprehensive CI/CD with Docker Deployment and Multi-Language Support

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

permissions:
  contents: read

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v3

    - name: Log in to Docker Hub
      uses: docker/login-action@v2
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}
          
    - name: Set up Python 3.10
      uses: actions/setup-python@v2
      with:
        python-version: "3.10"

    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install flake8 pytest
        if [ -f requirements.txt ]; then pip install -r requirements.txt; fi

    - name: Lint with flake8
      run: |
        # stop the build if there are Python syntax errors or undefined names
        flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
        # exit-zero treats all errors as warnings. The GitHub editor is 127 chars wide
        flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics

    - name: Unit Test Results
      run: mvn -B package --file ./GDA/pom.xml

    - name: Build with Maven
      run: mvn -B package --file ./GDA/pom.2.xml

    - name: Build and push Docker image
      env:
        REPOSITORY: cdaandgda
        IMAGE_TAG: latest
      run: |
        docker build -t ${{ secrets.DOCKER_USERNAME }}/$REPOSITORY:$IMAGE_TAG .
        docker push ${{ secrets.DOCKER_USERNAME }}/$REPOSITORY:$IMAGE_TAG

    - name: Deploy to Cloud Machine
      uses: appleboy/ssh-action@v1
      with:
        host: 20.106.166.187
        username: dev
        key: ${{ secrets.SSH_PRIVATE_KEY }}
        script: |
          sudo docker pull ${{ secrets.DOCKER_USERNAME }}/cdaandgda:latest
          sudo docker stop my-container || true
          sudo docker rm my-container || true
          sudo docker run -d --name my-container ${{ secrets.DOCKER_USERNAME }}/cdaandgda:latest



```
### 5. Actual Example: Execute Python File Testing, Maven Project Unit and Integration Tests, Create Docker Image, and Deploy to Azure Server
I will demonstrate this in class : [https://github.com/OmarSalah26/DEV-Project]

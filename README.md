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




```yaml
name: Upload Files to Another Repository

on:
  push:
    branches:
      - main  # Adjust this based on your branch name

jobs:
  upload:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout current repository
        uses: actions/checkout@v2

      - name: Set up Git
        run: |
          git config --global user.email "your-email@example.com"
          git config --global user.name "Your Name"

      - name: Copy files to destination repository
        run: |
          mkdir upload_temp
          cp -r * upload_temp/
          cd upload_temp
          git init
          git remote add destination-repo https://github.com/your-username/destination-repo.git
          git add .
          git commit -m "Upload files to destination repository"
          git push -f destination-repo main  # Adjust branch name if needed

```


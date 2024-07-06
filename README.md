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
### 3. Create a yaml file
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
        run: echo "This is a new file created by GitHub Actions" > newfile.txt

      - name: Commit and push changes
        env:
          GITHUB_TOKEN: ${{ secrets.ACTIONS_PAT }}
        run: |
          git config --global user.name 'github-actions'
          git config --global user.email 'github-actions@github.com'
          git add newfile.txt
          git commit -m "Automated file creation"
          git push https://${{ secrets.ACTIONS_PAT }}@github.com/your_username/you_repo-name.git
```

# Le pipeline 
Ceci est un pipeline standart qui sert à construire et deployer le deployement plus rapidement car cela le fait les deux en même temps. 

#### Les dièses sont la pour expliquer ce que c'est.
```
# C'est le nom du pipeline
name: Deploy Documentation
# C'est la gachette qui demarre le pipeline
on:
  push:
  # La branche avec le quel on veut déployer
    branches: [ main ]
  
permissions:
  contents: read
  pages: write
  id-token: write
# Ce qu'il doit faire 
jobs:
# La tâche qu'il fait en même temps que deploy
  build:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
      with:
        fetch-depth: 0
    
    - name: Setup Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.11'
    
    - name: Install dependencies
      run: pip install -r requirements.txt
    
    - name: Build documentation
      run: mkdocs build
    
    - name: Upload artifact
      uses: actions/upload-pages-artifact@v4
      with:
        path: ./site
        name: github-pages
  
  deploy:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    
    steps:
    - name: Deploy to GitHub Pages
      id: deployment
      uses: actions/deploy-pages@v4
      ```
# SSH Connection Workflow

This workflow is designed to automate the build process of a Java project using Maven. It is configured to run manually through GitHub's `workflow_dispatch` event.

## Workflow Steps

### 1. Checkout Repository
The workflow starts by checking out the repository's code to the GitHub runner. This is achieved using the `actions/checkout@v2` action, which clones the repository into the runner's environment.

```yaml
- name: Checkout repository
  uses: actions/checkout@v2
```

### 2. Set Up JDK 17
The workflow sets up JDK 17 on the runner using the actions/setup-java@v3 action. This ensures that the correct Java Development Kit version is available for the build process.

```yaml
- name: Set up JDK 17
  uses: actions/setup-java@v3
  with:
    java-version: '17'
```

### 3. Build with Maven
The workflow uses Maven to build the project. The mvn clean install command is run to compile the code, run tests, and package the application.

```yaml
- name: Build with Maven
  run: mvn clean install
```

### 4.Archive JAR
This step archives the generated JAR file as an artifact. The JAR file is located in the target directory and is uploaded as an artifact named java-app using the `actions/upload-artifact@v3` action.

```yaml
- name: Archive JAR
  uses: actions/upload-artifact@v3
  with:
    name: java-app
    path: target/*.jar
```

### 5. Create SSH Directory
This step will create the `.ssh` directory in the runner’s home directory. This directory is where SSH-related files, such as private keys and known hosts, will be stored.

```yaml
- name: Create SSH directory
  run: mkdir -p ~/.ssh
```

### 6. Install SSH Key
Next, the workflow installs the SSH private key required for authentication. The key is fetched from GitHub Secrets, written to the `~/.ssh/id_rsa` file, and its permissions are set to ensure security.

```yaml
- name: Install SSH key
  run: echo "$SSH_PRIVATE_KEY" > ~/.ssh/id_rsa && chmod 600 ~/.ssh/id_rsa
  env:
    SSH_PRIVATE_KEY: ${{ secrets.SSH_PRIVATE_KEY }}
```

### 7. Add Server SSH Fingerprint
To ensure secure connections, the workflow adds the SSH fingerprint of the server to the `known_hosts` file. This step helps prevent man-in-the-middle attacks by verifying the server's identity.

```yaml
- name: Add server SSH fingerprint
  run: ssh-keyscan -H ${{ secrets.SERVER_HOST }} >> ~/.ssh/known_hosts
```

### 8. Check SSH Connection
Before deploying the JAR file, the workflow checks the SSH connection to the server. This step ensures that the SSH key is correctly configured and that the server is accessible.

```yaml
- name: Check SSH connection
  run: ssh -v -o StrictHostKeyChecking=no -p 22 ${{ secrets.SERVER_USERNAME }}@${{ secrets.SERVER_HOST }} exit
```

### 9. Upload JAR
Finally, the workflow uploads the JAR file to the remote server using `scp`. The source path and destination path for the JAR file are specified through GitHub Secrets, making the deployment process both secure and flexible.

```yaml
- name: Upload JAR
  run: scp -v -P 22 -r ${{ secrets.JAR_SOURCE_PATH }} ${{ secrets.SERVER_USERNAME }}@${{ secrets.SERVER_HOST }}:${{ secrets.JAR_DESTINATION_PATH }}
```

### Triggering the Workflow
This workflow is configured to be triggered manually through GitHub's workflow_dispatch event. To run the workflow, navigate to the "Actions" tab in your GitHub repository, select this workflow, and click "Run workflow."
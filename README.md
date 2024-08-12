# Simple Workflow

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
Finally, the workflow uses Maven to build the project. The mvn clean install command is run to compile the code, run tests, and package the application.

```yaml
- name: Build with Maven
  run: mvn clean install
```

### Triggering the Workflow
This workflow is configured to be triggered manually through GitHub's workflow_dispatch event. To run the workflow, navigate to the "Actions" tab in your GitHub repository, select this workflow, and click "Run workflow."
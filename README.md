# github-action-implementation
This is repository for GitHub Action Implementation.

## WorkFlow Steps :
1. **Checkout code:** This step checks out the code from the repository using the actions/checkout@v2 action.
2. **Set up JDK:** The Java Development Kit (JDK) is set up using the actions/setup-java@v2 action. It specifies Java version 17 and the Temurin distribution.
3. **Build with maven:** The project is built using maven with the mvn clean install command.
4. **Archive JAR:** Archive build jar in workflow.


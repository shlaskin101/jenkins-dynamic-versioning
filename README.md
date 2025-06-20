# Dynamically Increment Application Version in Jenkins Pipeline

## Overview

This project demonstrates a fully automated Jenkins CI pipeline that dynamically increments the application version, builds a Java application, creates a Docker image, pushes it to DockerHub, and commits version changes back to the Git repository. It includes protections to avoid triggering CI loopbacks.

## 🔧 Technologies Used

- Jenkins (Declarative Pipeline)
- Docker
- GitLab (Source Control)
- Git
- Java
- Maven

## 📌 Features & CI Workflow

### ✅ Step 1: Increment Application Version

- Automatically parse the existing version in `pom.xml`
- Increment the patch version using `build-helper:parse-version`
- Dynamically generate and set a new version tag (e.g., `1.1.248`)

### ✅ Step 2: Build Java Application

- Clean and build the Maven-based Java application
- Remove old artifacts from the `target/` directory

### ✅ Step 3: Build Docker Image

- Build a Docker image using a dynamic tag composed of the incremented version and Jenkins build number
- Example tag format: `1.1.248-236`

### ✅ Step 4: Push Image to DockerHub

- Authenticate with DockerHub using secure credentials
- Push the versioned image to a private DockerHub repository

### ✅ Step 5: Commit Version Update to Git

- Configure Git credentials inside Jenkins
- Commit the new version number to the `pom.xml` file in the Git repository
- Push changes back to the same branch to keep version control in sync

### ✅ Step 6: Prevent Infinite CI Trigger Loops

- Ensure commits triggered by version bump do **not** recursively trigger Jenkins builds again
- Strategy: Use branch filtering or conditional triggers in Jenkins multibranch pipeline setup

---

## 🚀 How to Use This Project

1. Clone the repository:

   ```bash
   git clone https://github.com/<your-username>/<your-repo-name>.git
   ```

2. Navigate into the project directory:

   ```bash
   cd java-maven-app
   ```

3. Run the Jenkins pipeline manually from Jenkins UI or configure automated triggers (excluding loop-causing commits).

---

## 🤝 Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

---

## 📄 License

This project is open-source and available under the [MIT License](LICENSE).

---

## 🙌 Acknowledgments

Special thanks to the TechWorld with Nana Bootcamp and the DevOps community for educational support and open knowledge.

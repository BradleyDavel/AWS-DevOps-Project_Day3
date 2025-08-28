<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />
<p align="center">
  <a href="https://www.python.org/"><img src="https://img.shields.io/badge/Language-Java%20%7C%20Maven-blue?style=for-the-badge&logo=java&logoColor=white" alt="Language Badge"></a>
  <a href="https://aws.amazon.com/codeartifact/"><img src="https://img.shields.io/badge/AWS-CodeArtifact-orange?style=for-the-badge&logo=amazonaws&logoColor=white" alt="AWS CodeArtifact"></a>
  <a href="https://github.com/BradleyDavel"><img src="https://img.shields.io/badge/Maintainer-Bradley%20Davel-success?style=for-the-badge&logo=github" alt="Maintainer"></a>
  <a href="http://learn.nextwork.org/projects/aws-devops-codeartifact-updated"><img src="https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge&logo=checkmarx" alt="Project Status"></a>
  <a href="https://choosealicense.com/licenses/mit/"><img src="https://img.shields.io/badge/License-MIT-blueviolet?style=for-the-badge&logo=open-source-initiative&logoColor=white" alt="License"></a>
</p>
# Secure Packages with CodeArtifact

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-devops-codeartifact-updated)

**Author:** Bradley Davel  
**Email:** bradley.davel@outlook.com

---

![Image](http://learn.nextwork.org/sparkling_indigo_heroic_bat/uploads/aws-devops-codeartifact-updated_1d79e699)

---

## Introducing Today's Project!

In this project, I will do the following:

🗂️ Set up CodeArtifact as a repository for your project's dependencies.

🛡️ Use IAM roles and policies to give your web app access to CodeArtifact.

✅ Verify your web app's connection to CodeArtifact!

💎 Become a package uploader - create and add your own packages to your CodeArtifact repository!

### Key tools and concepts

🗂️ AWS CodeArtifact – I learned how to set up CodeArtifact as a private repository to securely store and manage my project’s dependencies. This showed me how dependency management can be centralized instead of relying only on public Maven repositories.

🛡️ IAM Roles and Policies – I practiced granting secure access to CodeArtifact by configuring IAM roles and attaching the correct policies to my EC2 instance. This reinforced the concept of least privilege and using roles instead of long-term credentials.

✅ Authentication and Connectivity – I verified my web application’s ability to connect to CodeArtifact using temporary tokens. This helped me understand how token-based authentication works in AWS and why automatic credential management improves security.

💎 Package Management – I uploaded and published custom packages to CodeArtifact, gaining hands-on experience with how artifacts are versioned and shared within a team environment.

### Project reflection

⏱️ Duration:
This project took me approximately 30 minutes to complete.

⚡ Challenge:
The most challenging part was when the authorization token didn’t export properly the first time, which caused permission errors with CodeArtifact. Troubleshooting that taught me the importance of verifying environment variables and IAM role permissions.

🏆 Rewarding Part:
It was most rewarding to finally see my web app successfully connect to CodeArtifact and pull dependencies securely. That moment confirmed that the IAM role, policy, and token configuration were all working as intended.

This project is part three of a series of DevOps projects where I'm building a CI/CD pipeline! I'll be working on the next project later today as well.

---

## CodeArtifact Repository

CodeArtifact is a secure, central place to store all your software packages. When you're building an application, you typically use dozens of external packages or libraries - things other developers have created that you don't want to build from scratch.

An artifact repository gives you a consistent, reliable place to store and retrieve these components. This gives you three big benefits:

1️⃣ Security: Everyone in a team retrieves packages from a secure repository (CodeArtifact), instead of downloading from unsafe sources on the internet (hello, security risks)!

2️⃣ Reliability: If public package websites go down, you have backups in your CodeArtifact repository.

3️⃣ Control: Your team can easily share and use the same versions of packages, instead of everyone working with a different version of the same package.

A CodeArtifact domain is like a folder that holds multiple repositories belonging to the same project or organization. We like using domains because they give you a single place to manage permissions and security settings that apply to all repositories inside it. This is much more convenient than setting up permissions for each repository separately, especially in large companies where many teams need access to different repositories.

With domains, you can ensure consistent security controls across all your package repositories in an efficient way. I Set the domain name to "nextwork".

CodeArtifact public upstream repositories are intermediate repositories that connect your repository to official package authorities, such as Maven Central Repository and NPM. Maven Central is essentially the App Store of the Java world - it's the most popular public repository where developers publish and share Java libraries. When you're building Java applications, chances are you'll need packages from Maven Central. It contains virtually every popular open-source Java library out there, from database connectors to testing frameworks and UI components.

By connecting our CodeArtifact repository to Maven Central, we're setting up a system where we get the best of both worlds: access to all these public libraries, but with the added benefits of caching, control, and consistency that come with our private CodeArtifact repository.

![Image](http://learn.nextwork.org/sparkling_indigo_heroic_bat/uploads/aws-devops-codeartifact-updated_n4o5p6q7)

---

## CodeArtifact Security

### Issue

To access CodeArtifact, we need an Authentication token. I ran into an error when retrieving a token because by default, your EC2 instance doesn't have permission to access your other AWS services (including CodeArtifact). This is intentional - AWS follows the "principle of least privilege," meaning resources only get the minimum permissions they need to function.

### Resolution

To resolve the error with my security token, I set up an IAM policy with the required CodeArtifact permissions and then attached it to the IAM role associated with my EC2 instance. This resolved the error because the AWS CLI on the EC2 instance could now assume the role and automatically retrieve temporary credentials. By doing this, I avoided using hard-coded credentials or running aws configure, while still granting the instance the least privileges needed to authenticate to CodeArtifact securely.

Using IAM roles is a best practice because they remove the risks of long-term credentials, enforce least privilege, and simplify secure access management across AWS services and accounts.

---

## The JSON policy attached to my role

The JSON policy I set up as follows:

The first part (codeartifact:* actions) gives permission to get authentication tokens, find repository locations, and read packages from repositories.



The second part (sts:GetServiceBearerToken) allows temporarily elevated access specifically for CodeArtifact operations.

The "Resource": "*" means these permissions apply to all relevant resources, while the Condition narrows the second permission to only work with CodeArtifact.

This follows the "principle of least privilege" - granting only the minimum permissions needed to perform the required tasks, enhancing your security posture.

![Image](http://learn.nextwork.org/sparkling_indigo_heroic_bat/uploads/aws-devops-codeartifact-updated_23rp7q8r9)

---

## Maven and CodeArtifact

### To test the connection between Maven and CodeArtifact, I compiled my web app using settings.xml

The settings.xml file is Maven's control center - it's where you tell Maven how to behave across all your projects.

By configuring settings.xml properly, we're creating a seamless connection between Maven and CodeArtifact. Your builds will automatically authenticate and pull dependencies from the right place without you having to think about it again. It's one of those "set it up once, benefit forever" kinds of configurations that make a developer's life much easier.

Compiling means converting human-readable source code into machine-readable instructions so that a computer can execute the program.

🔎 In simple terms:

You write code in a high-level language like Java, C, or C++.

The compiler translates it into bytecode (for Java) or binary machine code (for C/C++) that the CPU or runtime can actually run.

⚙️ In your Maven/Java context:

When you run mvn compile, Maven tells the Java Compiler (javac) to take your .java files and turn them into .class files (Java bytecode).

These .class files are what the Java Virtual Machine (JVM) executes.

📌 Why it matters:

Error Checking – The compiler catches syntax and type errors before you try to run the program.

Optimization – Compilers often optimize the code so it runs faster.

Translation – It bridges the gap between human code and computer execution.

![Image](http://learn.nextwork.org/sparkling_indigo_heroic_bat/uploads/aws-devops-codeartifact-updated_c17eace8)

---

## Verify Connection

After compiling, I checked nextwork-devops-cicd repository and noticed it had populated with the dependencies that Maven downloaded from Maven Central via CodeArtifact when we compiled our project.

![Image](http://learn.nextwork.org/sparkling_indigo_heroic_bat/uploads/aws-devops-codeartifact-updated_1d79e699)

---


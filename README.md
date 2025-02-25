# Use multiple repositories

Learn how to manage multiple Git repositories when working with Cloud Manager.

## Sync private Git repositories

Instead of directly working with Cloud Manager’s Git repository, customers can work with their own private Git repository or multiple own Git repositories. In these cases, set up an automated synchronization process to ensure that the Git repository in Cloud Manager is always kept up-to-date.

Depending on where the customer’s Git repository is hosted, a GitHub action or a continuous integration solution like Jenkins could be used to set up the automation. With an automation in place, every push to a customer-owned Git repository can be automatically forwarded to Cloud Manager’s Git repository.

While such an automation for a single customer-owned Git repository is straight forward, configuring it for multiple repositories requires an initial setup. The contents from multiple Git repositories must be mapped to different directories within the single Cloud Manager Git repository. Cloud Manager’s Git repository must be provisioned with a root Maven pom.xml, listing the different subprojects in the modules section.

The following is a sample pom.xml file for two customer-owned Git repositories.

The first project is put into the directory named project-a.
The second project is put into the directory named project-b.

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="https://maven.apache.org/POM/4.0.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="https://maven.apache.org/POM/4.0.0 https://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>customer.group.id</groupId>
    <artifactId>customer-reactor</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>pom</packaging>

    <modules>
        <module>project-a</module>
        <module>project-b</module>
    </modules>

</project>

Such a root pom.xml is pushed to a branch in Cloud Manager’s Git repository. Then, the two projects must be set up to forward changes automatically to Cloud Manager’s Git repository.

A possible solution would be the following.

Trigger a GitHub action by pushing to a branch in project A.
The action checks out project A and the Cloud Manager Git repository. Then it copies all contents of project A to the project-a directory in Cloud Manager’s Git repository.
Then the action commits-pushes the change.
For example, a change on the main branch in project A is automatically pushed to the main branch in Cloud Manager’s Git repository. There could be a mapping between branches like a push to a branch named dev in project A is pushed to a branch named development in Cloud Manager’s Git repository. Similar steps are required for project B.

Depending on the branching strategy and workflows, the syncing can be configured for different branches. If the used Git repository does not provide a concept similar to GitHub actions, an integration by way of Jenkins (or similar) is possible as well. In this case, a webhook triggers a Jenkins job, which does the work.

Follow these steps so you can add a new, third source or repository.

Add a GitHub action to the new repository, which pushes changes from that repository to Cloud Manager’s Git repository.
Perform that action at least once to ensure that project code is in Cloud Manager’s Git repository.
In the Cloud Manager Git repository, add a reference to the new directory in the root Maven pom.xml.

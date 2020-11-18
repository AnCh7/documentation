## Jenkins

**Jenkins** is an open source automation server written in Java. Jenkins helps to automate the non-human part of the software development process, with continuous integration and facilitating technical aspects of continuous delivery. It is a server-based system that runs in servlet containers such as Apache Tomcat. It supports version control tools, including AccuRev, CVS, Subversion, Git, Mercurial, Perforce, ClearCase and RTC, and can execute Apache Ant, Apache Maven and sbt based projects as well as arbitrary shell scripts and Windows batch commands.

Plugins have been released for Jenkins that extend its use to projects written in languages other than Java. Plugins are available for integrating Jenkins with most version control systems and bug databases. Many build tools are supported via their respective plugins. Plugins can also change the way Jenkins looks or add new functionality. There are a set of plugins dedicated for the purpose of unit testing that generate test reports in various formats (for example JUnit bundled with Jenkins, MSTest, NUnit etc.) and automated testing which supports automated tests. 

Builds can generate test reports in various formats supported by plugins (JUnit support is currently bundled) and Jenkins can display the reports and generate trends and render them in the GUI.

### Authenticate against GCP/GKE in Jenkins job (build script)

For Jenkins running in Openshift create a secret with your GCP SA keys:
- Export keys from GCP: Go to IAM --> Service Accounts --> Find a proper account (or create a new one) --> Action's Column --> Create key --> JSON format --> Press Create.
- Create and mount Openshift secret as a volume
- Update Jenkins build script:
```bash
# Authenticate
gcloud auth activate-service-account ${SERVICE_ACCOUNT} \
       --key-file=/home/jenkins/MOUNT_PATH/${SERVICE_ACCOUNT_FILE} \
       --project=project

# Connect to your cluster
gcloud container clusters get-credentials ${CLUSTER} --zone ${ZONE} --project ${PROJECT_ID}
gcloud config set account ${SERVICE_ACCOUNT}
gcloud config set project ${PROJECT_ID}
gcloud config set compute/zone ${ZONE}
```

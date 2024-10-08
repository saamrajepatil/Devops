# Install Jenkins on AWS EC2
Jenkins is a self-contained Java-based program, ready to run out-of-the-box, with packages for Windows, Mac OS X and other Unix-like operating systems. As an extensible automation server, Jenkins can be used as a simple CI server or turned into the continuous delivery hub for any project.



### Prerequisites
1. EC2 RHEL 7.x Instance 
   - With Internet Access
   - Security Group with Port `8080` open for internet
1. JDK 

## Install Java
We will be using open java for our demo, Get latest version from http://openjdk.java.net/install/ or for amazon linux node you can use below command
```sh
sudo yum install java-21-amazon-corretto
#yum install java-11-openjdk
```

### Confirm Java Version
Lets install java and set the java home
```sh
java -version
find /usr/lib/jvm/java* | head -n 3
#JAVA_HOME=/usr/lib/jvm/java-21-amazon-corretto.x86_64
export JAVA_HOME
PATH=$PATH:$JAVA_HOME
# To set it permanently update your .bash_profile
source ~/.bash_profile
```
_The output should be something like this,_
```
[root@~]# java -version
openjdk version "21.0.1" 2023-10-17 LTS
OpenJDK Runtime Environment Corretto-21.0.1.12.1 (build 21.0.1+12-LTS)
OpenJDK 64-Bit Server VM Corretto-21.0.1.12.1 (build 21.0.1+12-LTS, mixed mode, sharing)
```
Setting Up JAVA_HOME on Amazon EC2 with Amazon Corretto 17
To set up the JAVA_HOME environment variable for Amazon Corretto 17 on your Amazon Elastic Compute Cloud (EC2) instance, follow these steps:

Step 1: Verify Java Installation
First, ensure that Java is installed correctly by running:

java -version
Copy
This command should display the version of Java you have installed.

Step 2: Locate the Correct Java Installation Path
For Amazon Corretto 17, the correct path for JAVA_HOME is /usr/lib/jvm/java-17-amazon-corretto.x86_64, not /usr/lib/jvm/java-17-amazon-corretto.x86_64/lib.

Step 3: Set JAVA_HOME Environment Variable
Open or create a file named .bash_profile in your home directory:

nano ~/.bash_profile
Copy
Add the following lines to set JAVA_HOME and update your system's PATH:

export JAVA_HOME=/usr/lib/jvm/java-17-amazon-corretto.x86_64
export PATH=$PATH:$JAVA_HOME/bin
Copy
Save and close the file (in nano, press CTRL + X, then Y, and hit Enter).

Step 4: Apply Changes
To apply these changes immediately without restarting your session, run:

source ~/.bash_profile
Copy
Step 5: Verify JAVA_HOME Setup
Finally, verify that JAVA_HOME is set correctly by running:

echo $JAVA_HOME
Copy
This command should output /usr/lib/jvm/java-17-amazon-corretto.x86_64.

## Install Jenkins
You can install jenkins using the rpm or by setting up the repo. We will setup the repo so that we can update it easily in future.
Get latest version of jenkins from https://pkg.jenkins.io/redhat-stable/
```sh
yum -y install wget
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
yum -y install jenkins
```

### Start Jenkins
```sh
# Start jenkins service
systemctl start jenkins

# Setup Jenkins to start at boot,
systemctl enable jenkins
```

#### Accessing Jenkins
By default jenkins runs at port `8080`, You can access jenkins at
```sh
http://YOUR-SERVER-PUBLIC-IP:8080
```
#### Configure Jenkins
- The default Username is `admin`
- Grab the default password 
  - Password Location:`/var/lib/jenkins/secrets/initialAdminPassword`
- `Skip` Plugin Installation; _We can do it later_
- Change admin password
  - `Admin` > `Configure` > `Password`
- Configure `java` path
  - `Manage Jenkins` > `Global Tool Configuration` > `JDK`  
- Create another admin user id

### Test Jenkins Jobs
1. Create “new item”
1. Enter an item name – `My-First-Project`
   - Chose `Freestyle` project
1. Under Build section
	Execute shell : echo "Welcome to Jenkins Demo"
1. Save your job 
1. Build job
1. Check "console output"



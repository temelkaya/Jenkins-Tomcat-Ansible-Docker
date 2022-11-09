# jenkins-tomcat-ansible-docker
11/08/2022 Tuesday 

# Simple DevOps End to End Project 

1. What is Jenkins? Jenkins is a glue in the project. It's used for autometed builds and CI/CD. 
2. Spin up EC2 and configure as a Jenkins Server 
    2.1 sudo su - 
    2.2 vim /etc/hostname --> dd --> i --> jenkins-server --> wq --> reboot 
    2.3 wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
    2.4 rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
    2.5 yum upgrade
    2.6 amazon-linux-extras install java-openjdk11 -y
    2.7 amazon-linux-extras install epel  (Extra packages enterprise linux)
    2.8 systemctl enable jenkins
    2.9 systemctl start jenkins
    3.0 copy public ip of jenkins vm and <public-ip>:8080
    3.1 Make sure 8080 is open in SG level 
    3.2 cat /var/lib/jenkins/secrets/initialAdminPassword


Scenario: As a DevOps engineer, we work closely with Developers. Developers develop Java based apps. 
As a DevOps engineer we need to take Java code from GitHub repo, build war/jar build artifacts and deploy 
to Target Serves (EC2). 
    Steps for solution:

    Configure Maven 
    1. Configure Maven in Jenkins. Maven is a build management tool 
    2. cd /opt 
    3. wget https://dlcdn.apache.org/maven/maven-3/3.8.6/binaries/apache-maven-3.8.6-bin.tar.gz
    4. tar -xzvf apache-maven-3.8.6-bin.tar.gz 
    5. rm -Rf apache-maven-3.8.6-bin.tar.gz 
    6. mv apache-maven-3.8.6/ maven
    7. cd ~ --> go to root 
    8. vim .bash_profile 
        User specific environment and startup programs
        JAVA_HOME=/usr/lib/jvm/java-11-openjdk-11.0.16.0.8-1.amzn2.0.1.x86_64
        M2_HOME=/opt/maven
        M2=$M2_HOME/bin
        PATH=$PATH:$HOME/bin:$JAVA_HOME:$M2_HOME:$M2
    By configuring .bash_profile and exporting M2_HOME, M2 and JAVA_HOME --> commands can be executed from everywhere. 
    9. source .bash_profile
    10. Jenkins GUI --> Manage Jenkins --> Manage Plugins --> Maven Integration --> Install without restart 
    11. clean install --> each time job runs it cleans the cache/memory 
    12. in Jenkins VM jobs are stored under /var/lib/jenkins/workspace
# Tomcat 
    1. What is Tomcat?  Apache Tomcat is a web container. It allows the users to run Servlet and 
    JAVA Server Pages that are based on the web-applications
    2. cd /opt 
    3. wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.68/bin/apache-tomcat-9.0.68.tar.gz
    4. tar -xzvf apache-tomcat-9.0.68.tar.gz
    5. rm -Rf apache-tomcat-9.0.68.tar.gz
    6. mv apache-tomcat-9.0.68 tomcat 
    7. cd tomcat/bin and you will see lots of files 
    8. ./startup.sh is for starting up a tomcat server
    9. publicIP:8080
    10. Click on Manager App —> Access Denied 
    11. find / -name context.xml
        <!--  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|i0:0:0:0:0:0:0:1" /> -->
    12. cd conf —> vi tomcat-users.xml —> to the end
    <role rolename="manager-gui"/>
    <role rolename="manager-script"/>
    <role rolename="manager-jmx"/>
    <role rolename="manager-status"/>
    <user username="admin" password="admin" roles="manager-gui, manager-script, manager-jmx, manager-status"/>
    <user username="deployer" password="deployer" roles="manager-script"/>
    <user username="tomcat" password="s3cret" roles="manager-gui"/>
    </tomcat-users>
    13. cd ../bin —> ./shutdown.sh —> ./startup.sh 

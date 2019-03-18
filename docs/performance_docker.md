### Montagem do container para performance-test

adduser jenkins
apt-get update
apt-get install openssh-server
mkdir /var/run/sshd
apt-get update
sudo apt install openjdk-8-jdk
sudo apt install git -y
sudo apt install subversion -y
cd /tmp
wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.16/bin/apache-tomcat-9.0.16.tar.gz
su jenkins
mkdir /home/jenkins/tomcat
tar xzvf apache-tomcat-9.0.16.tar.gz -C /home/jenkins/tomcat --strip-components=1
exit
chmod -R 777 /home/jenkins/tomcat

mkdir /home/jenkins/workspace
chown -R jenkins:jenkins /home/jenkins/workspace

mkdir /opt/jmeter
cd /tmp
wget https://archive.apache.org/dist/jmeter/binaries/apache-jmeter-3.3.tgz

tar xzvf apache-jmeter-3.3.tgz -C /opt/jmeter --strip-components=1
chown -R jenkins:jenkins /opt/jmeter/
chmod -R 777 /opt/jmeter/

##Options
https://archive.apache.org/dist/jmeter/binaries/apache-jmeter-4.0.tgz
https://archive.apache.org/dist/jmeter/binaries/apache-jmeter-5.1.tgz

apt-get install curl

## exit container

## commit container
docker ps -a
docker commit <CONTAINER_ID> performance-test-agent-0.1


usermod -aG docker admin
usermod -aG docker jenkins


label docker-performance
name docker-node-performance
image performance-test-agent-0.1

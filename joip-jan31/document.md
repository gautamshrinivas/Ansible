Integrating ansible with jenkins
Create 2 instances on with jenkins as master and in node install ansible
Install jdk11 on jenkins master node
Jenkins installation script:
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
Give the administrator password by looking into the following path 
sudo cat /var/lib/jenkins/secrets/initialAdminPassword

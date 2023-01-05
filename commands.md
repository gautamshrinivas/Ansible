* Step by Step to Install & Configure WildFly (JBoss) on Ubuntu 20.04:
-----------------------------------------------------------------------
* Step 1: Update the system. `sudo apt update`
* Step 2: Install Java. `sudo apt install openjdk-11-jdk -y`
* Step 3: Add a user & group for wildfly.
  * `sudo groupadd -r wildfly`
  * `sudo useradd -r -g wildfly -d /opt/wildfly -s /sbin/nologin wildfly`
* Step 4: Download the WildFly using wget.
  * `cd /tmp`
  * `wget https://download.jboss.org/wildfly/16.0.0.Final/wildfly-16.0.0.Final.tar.gz`      
  * Extract the downloaded file.` tar xvf wildfly-22.0.1.Final.tar.gz`
  * Move the extracted folder to /opt/ `sudo mv wildfly-22.0.1.Final/ /opt/wildfly`
  * Provide the following permission: `sudo chown -RH wildfly: /opt/wildfly`
* Step 5: Create a folder. `sudo mkdir -p /etc/wildfly`
  * Copy the following files one place to another & provide executable permission to script files
    under /opt/wildfly/bin/ folder.
      `sudo cp /opt/wildfly/docs/contrib/scripts/systemd/wildfly.conf /etc/wildfly/`
      `sudo cp /opt/wildfly/docs/contrib/scripts/systemd/launch.sh /opt/wildfly/bin/`
      `sudo sh -c 'chmod +x /opt/wildfly/bin/*.sh'`
      `sudo cp /opt/wildfly/docs/contrib/scripts/systemd/wildfly.service /etc/systemd/system/`
* Step 6: Start & Enable the WildFly service.
  * `sudo systemctl start wildfly.service`
  * `sudo systemctl enable wildfly.service`
  * Check the status of WildFly. `sudo systemctl status wildfly.service`












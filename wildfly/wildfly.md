* Step by Step to Install & Configure WildFly (JBoss) on Ubuntu 22.04:
-----------------------------------------------------------------------
* Documentation link
  [Refer here: https://www.hackerxone.com/2021/09/01/step-by-step-to-install-configure-wildfly-jboss-on-ubuntu-20-04/#:~:text=Step%20by%20Step%20to%20Install%20%26%20Configure%20WildFly,the%20following%20port%20number%20on%20UFW%20firewall.%20]
* Step 1: Update the system. `sudo apt update`
* Step 2: Install Java. `sudo apt install openjdk-11-jdk -y`
* Step 3: Add a user & group for wildfly.
  * `sudo groupadd -r wildfly`
  * `sudo useradd -r -g wildfly -d /opt/wildfly -s /sbin/nologin wildfly`
* Step 4: Download the WildFly using wget.
  * `cd /tmp`
  * `wget https://download.jboss.org/wildfly/22.0.1.Final/wildfly-22.0.1.Final.tar.gz`      
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

# Wildfly-ubuntu.yaml
----------------------

```yaml
---
- name: installing wildfly in ubuntu 22.04
  hosts: all
  become: yes
  tasks: 
    - name: installing default java
      ansible.builtin.apt:
        name: default-jdk
        update_cache: true
        state: present

    - name: create a system account for wildfly group
      ansible.builtin.group:
        name: wildfly
        system: true
        state: present

    - name: create a system account for wildfly user
      ansible.builtin.user:
        name: wildfly
        group: wildfly
        system: true
        home: /opt/wildfly
        shell: /sbin/nologin
        state: present
        create_home: true

    - name: download wildfly tar file
      ansible.builtin.get_url:
        url: https://download.jboss.org/wildfly/22.0.1.Final/wildfly-22.0.1.Final.tar.gz
        dest: /tmp

    - name: untaring the tar file
      ansible.builtin.unarchive:
        src: /tmp/wildfly-22.0.1.Final.tar.gz
        dest: /tmp/
        remote_src: true

    - name: copy file with owner and permissions
      ansible.builtin.copy:
        src: /tmp/wildfly-22.0.1.Final/
        dest: /opt/wildfly
        owner: wildfly
        group: wildfly
        remote_src: true
        mode: '0777'

    - name: changing ownership of wildfly
      ansible.builtin.file:
        path: /opt/wildfly
        owner: wildfly
        group: wildfly
        recurse: true
        mode: '0777'

    - name: Create /etc/wildfly directory 
      ansible.builtin.file:
        path: /etc/wildfly
        state: directory

    - name: copying files to /etc/wildfly
      ansible.builtin.copy:
        src: /opt/wildfly/docs/contrib/scripts/systemd/wildfly.conf
        dest: /etc/wildfly/
        remote_src: true

    - name: copying files to /opt/wildfly/bin/
      ansible.builtin.copy:
        src: /opt/wildfly/docs/contrib/scripts/systemd/launch.sh
        dest: /opt/wildfly/bin/
        remote_src: true

    - name: Execute the command in remote shell
#      ansible.builtin.command: sh -c 'chmod +x /opt/wildfly/wildfly-22.0.1.Final/bin/*.sh'
      ansible.builtin.file:    
        path: /opt/wildfly/bin/launch.sh
        state: file
        mode: '0777'

    - name: copying files to /opt/wildfly/bin/
      ansible.builtin.copy:
        src: /opt/wildfly/docs/contrib/scripts/systemd/wildfly.service
        dest: /etc/systemd/system/
        remote_src: true

    - name: starting wildfly service
      ansible.builtin.systemd:
        name: wildfly.service
        state: started
        enabled: yes
        
    - name: restarting wildfly service
      ansible.builtin.systemd:
        name: wildfly.service
        state: restarted
        enabled: yes
```        
        
# Wildfly-ubuntu.yaml file with varibales
-----------------------------------------
```yaml
---
- name: installing wildfly in ubuntu 22.04
  hosts: all
  become: yes
  vars: 
    java_package: default-jdk
    group_name: wildfly
    user_name: wildfly
    user_shell: /sbin/nologin
    user_home: /opt/wildfly
    wildfly_version: "22.0.1"
    wildfly_service_name: wildfly.service
  tasks:
    - name: installing default java
      ansible.builtin.apt:
        name: "{{ java_package }}"
        update_cache: true
        state: present

    - name: create a system account for wildfly group
      ansible.builtin.group:
        name: "{{ group_name }}"
        system: true
        state: present

    - name: create a system account for wildfly user
      ansible.builtin.user:
        name: "{{ user_name }}"
        group: "{{ group_name }}"
        system: true
        home: "{{ user_home }}"
        shell: "{{ user_shell }}"
        state: present
        create_home: true

    - name: download wildfly tar file
      ansible.builtin.get_url:
        url: "https://download.jboss.org/wildfly/{{ wildfly_version }}.Final/wildfly-{{ wildfly_version }}.Final.tar.gz"
        dest: /tmp

    - name: untaring the tar file
      ansible.builtin.unarchive:
        src: "/tmp/wildfly-{{ wildfly_version }}.Final.tar.gz"
        dest: /tmp/
        remote_src: true

    - name: copy file with owner and permissions
      ansible.builtin.copy:
        src: "/tmp/wildfly-{{ wildfly_version }}.Final/"
        dest: /opt/wildfly
        owner: "{{ user_name }}"
        group: "{{ group_name }}"
        remote_src: true
        mode: '0777'

    - name: changing ownership of wildfly
      ansible.builtin.file:
        path: /opt/wildfly
        owner: "{{ user_name }}"
        group: "{{ group_name }}"
        recurse: true
        mode: '0777'
    - name: Create /etc/wildfly directory 
      ansible.builtin.file:
        path: /etc/wildfly
        state: directory

    - name: copying files to /etc/wildfly
      ansible.builtin.copy:
        src: /opt/wildfly/docs/contrib/scripts/systemd/wildfly.conf
        dest: /etc/wildfly/
        remote_src: true

    - name: copying files to /opt/wildfly/bin/
      ansible.builtin.copy:
        src: /opt/wildfly/docs/contrib/scripts/systemd/launch.sh
        dest: /opt/wildfly/bin/
        remote_src: true

    - name: Execute the command in remote shell
      ansible.builtin.file:    
        path: /opt/wildfly/bin/launch.sh
        state: file
        mode: '0777'

    - name: copying files to /opt/wildfly/bin/
      ansible.builtin.copy:
        src: "/opt/wildfly/docs/contrib/scripts/systemd/{{ wildfly_service_name }}"
        dest: /etc/systemd/system/
        remote_src: true

    - name: starting wildfly service
      ansible.builtin.systemd:
        name: "{{ wildfly_service_name }}"
        state: started
        enabled: yes
        
    - name: restarting wildfly service
      ansible.builtin.systemd:
        name: "{{ wildfly_service_name }}"
        state: restarted
        enabled: yes  
      
 ```        

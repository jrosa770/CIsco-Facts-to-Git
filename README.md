# CIsco-Facts-to-Git
Gathers Facts and Configurations from Cisco Devices and commits/ pushes them to a Git Repository

## Backing up Cisco Configurations Directly into A Git Repo Using Ansible

One of the most regular requests while working with network devices is: "Can you get the current running for the router and or switch?" Normally you can do the tried and true show run , then copy paste or redirect it to a file, then store mail or maybe mail the file.

There's also applications like RANCID that will keep configurations for you in CVS and you can even browse it via CVSWeb. But it requires you to setup the system and make sure it is running and has stored the latest and greatest. Something that I've experienced myself as a long term and heavy RANCID user. At the end there is a copy and paste element associated unless there is an export and or download the file type feature.

The other option is just a simple Ansible playbook, and this is what this article will cover. Most engineers and admins using Ansible will leverage in some way a Git repository, in either public form, like GitHub, private, via Bitbucket or you own internal Git Server. Regardless of repository destination the process is the same:

    1. Create a Repo
    2. Clone a local copy
    3. Save your new files in the local copy folder
    4. Stage your newly save files via "git add"
    5. Commit your files via "git commit -m"
    6. Push your new commit via "git push"

The problem that I found is that not many people had tied the gathering and the saving to Git in one single process. It also addresses some complexity associated with the may moving parts of systems like RANCID and that in some cases is not the route some will like to follow.

The playbook in my case is called cisco_fact_conf2git.yml. As the name implies it leverages the ios_facts module provided by the Ansible Network Modules to provide the output. More information about ios_facts can be found at: https://docs.ansible.com/ansible/latest/modules/ios_facts_module.html.

The other component is a jinja template that formats the file into a particular format and saves it as .txt, .yml, .cfg, or any format you provide. The template is contained in "role" format, but that is not completely necessary. The name of the template is dicovery.j2

The playbook folder structure:

```
ios_fact_conf2git.yml
|
conf2git/
|
roles/
  |
  discovery/
    |
    templates/
      discovery.j2
```

- An example of an output will look like this (Edited for brevity):

```yml
Hostname: rem1-rt-1


Model: CISCO2921/K9


Serial Number: FTXXXXXXXX


OS Version: 15.2(4)M6a


OS Image: flash0:c2900-universalk9-mz.SPA.152-4.M6a.bin


CDP Neighbors:
{'GigabitEthernet0/0.1': [{'host': 'rem1-sw-1', 'port': 'GigabitEthernet1/0/1'}]}


Running Configuration:
!
! No configuration change since last restart
version 15.2
service timestamps debug datetime msec localtime show-timezone
service timestamps log datetime msec localtime show-timezone
service password-encryption
!
hostname wan-rt1
!
```

The same can be done for other OS's like for example JUNOS:

[juniper:children]
junos

[junos:children]
junos_dev_grp

[junos:vars]
ansible_network_os=junos

The playbook basics:

- hosts: juniper

 tasks:

 - name: Gather Facts for JUNOS based devices
    junos_facts:
      gather_subset:
        - "!hardware"
    register: config_response
    when: ansible_network_os == "junos"

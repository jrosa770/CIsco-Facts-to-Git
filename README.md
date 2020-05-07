# CIsco-Facts-to-Git
Gathers Facts and Configurations from Cisco Devices and commits/ pushes them to a Git Repository

## Backing up Cisco Configurations Directly into A Git Repo Using Ansible

One of the most regular requests while working with network devices is: "Can you get the current running for the router and or switch?" Normally you can do the tried and true show run , then copy paste or redirect it to a file, then store mail or maybe mail the file.

There's also applications like RANCID that will keep configurations for you in CVS and you can even browse it via CVSWeb. But it requires you to setup the system and make sure it is running and has stored the latest and greatest. Something that I've experienced myself as a long term and heavy RANCID user. At the end there is a copy and paste element associated unless there is an export and or download the file type feature.

The other option is just a simple Ansible playbook, and this is what this article will cover. Most engineers and admins using Ansible will leverage in some way a Git repository, in either public form, like GitHub, private, via Bitbucket or you own internal Git Server. Regardless of repository destination the process is the same:

    Create a Repo
    Clone a local copy
    Save your new files in the local copy folder
    Stage your newly save files via "git add"
    Commit your files via "git commit -m"
    Push your new commit via "git push"

The problem that I found is that not many people had tied the gathering and the saving to Git in one single process. It also addresses some complexity associated with the may moving parts of systems like RANCID and that in some cases is not the route some will like to follow.

The playbook in my case is called ios_fact_conf2git.yml. As the name implies it leverages the ios_facts module provided by the Ansible Network Modules to provide the output. More information about ios_facts can be found at: https://docs.ansible.com/ansible/latest/modules/ios_facts_module.html.

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

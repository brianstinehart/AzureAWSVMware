<p align="center">
  <img width="832" height="416" src="https://www.interactive.com.au/wp-content/themes/true-theme/resources/assets/images/brand/share-1280-by-640.jpg">
</p>

<p align="center">
  <img width="416" height="512" src="https://upload.wikimedia.org/wikipedia/commons/thumb/2/24/Ansible_logo.svg/1200px-Ansible_logo.svg.png">
</p>

# Ansible Deployment at Interactive

These ansible playbooks provision and manage Linux and Windows VMs on VMware, Azure or AWS.  This provisions the following:

* Base Infrastructure
  * A Windows 2019 server
  * A Linux 7.5 server
* Optional Additions
  * A Microsoft Active Directory Server
  * An Ansible Tower control node
  * A Gitlab server
  * A Windows Workstation

The client pre-requisite to use this workshop is simply to have an RDP client which can connect to the Windows Workstation.  If RDP is not allowed through a corporate firewall, a HTML5 RDP client is also running on each Workstation.  As the HTML5 client is fairly slow, it is recommended that students use normal RDP if at all possible.  All other activities will take place from that Workstation.  The Workstation has Visual Studio Code, Chrome, Putty, and Git for Windows.  

## Usage

### Basic Requirements
Requirements for running the provisioner will vary based upon your environment and where you are provisioning to.  The basic requirements are the following

* Ansible >= 2.6
* Various Python Modules
  * pywinrm
  * boto >= 1.7 and boto3 (for AWS Provisioning)
  * ansible[azure] (for Azure Provisioning)
  * requests
  * requests-credssp
  * sshpass (use brew to install if OSX)
* Ansible Tower License (if Tower server is deployed)

### General Config

Beyond that, the main thing that needs to be edited is the *vars/main.yml* file which contains the configuration details specific to your environment.  You should instead copy this file to *vars/custom.yml* and it will use that in place of the main.yml file.  It will check for the custom.yml file first, and use main.yml if its not found.  This ensure your settings are not overridden if you update git, and are not committed to git (this file is ignored).

It is recommended that you utlilize the custom.yml to maintain the majority of the nonchanging settings, and then you can create a separate vars file (to be included in via extravars) with just the variables you need for the individual workshop.

```yaml
## Example extra vars file
instance_loc: azure
name_prefix: "skylight"
user_count: 1   #for number of servers to deploy in each flavour
towerversion: 3.5.2-1
```

That's it.  Easy, right ?!?   Now run it as you wish :)


### AWS

You will need to setup your AWS configuration and Credentials.  Note that a standard AWS account is limited to 20 ec2 instances, so update your quota in advance.  Also note that each student gets 3 machines.  Make certain your VPC is large enough.  

You can set it up like this.

~/.aws/config
```
[default]
region = us-east-1
output = table
```

~/.aws/credentials
```
[default]
aws_access_key_id = <your ec2 access key>
aws_secret_access_key = <your ec2 secret key>
```

### Azure

Azure CLI
```
sudo pip install azure
```

Possible need to register the provider
```
az provider register --namespace Microsoft.Compute
```

Firstly, login to the Azure CLI using:
```
az login
```

Once logged in - it's possible to list the Subscriptions associated with the account via:
```
az account list
```

The output (similar to below) will display one or more Subscriptions - with the ```id``` field being the ```subscription_id``` field.
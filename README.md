# README

`Mubashar Sadiq`  

---

## **Deploying a Web Server on Azure**

## **Introduction**

In this tutorial, I will provide three alternative methods for deploying a web server on Azure. Each alternative offers a different approach using Azure services and tools, including **Azure Portal (GUI), Cloud-init scripting, and ARM templates.**

## Table of Contents

- [**Inlämningsuppgift 1 - Azure**](#inlämningsuppgift-1---azure)
  - [**Deploying a Web Server on Azure**](#deploying-a-web-server-on-azure)
  - [**Introduction**](#introduction)
  - [Table of Contents](#table-of-contents)
    - [**Security Measures**](#security-measures)
    - [**Alternatives**](#alternatives)
    - [**Alternative 1: Azure Portal (GUI)**](#alternative-1-azure-portal-gui)
      - [Table of Contents](#table-of-contents-1)
      - [**Delmoment 1: Utveckla applikationen (GUI)**](#delmoment-1-utveckla-applikationen-gui)
      - [**Delmoment 2: Provisionera en värdmiljö (GUI)**](#delmoment-2-provisionera-en-värdmiljö-gui)
      - [**Delmoment 3: Konfigurera värdmiljön (GUI)**](#delmoment-3-konfigurera-värdmiljön-gui)
      - [**Delmoment 4: Driftsätt applikationen (GUI)**](#delmoment-4-driftsätt-applikationen-gui)
      - [**Delmoment 5: Verifiera lösningen (GUI)**](#delmoment-5-verifiera-lösningen-gui)
    - [**Alternative 2: Cloud-init Scripting**](#alternative-2-cloud-init-scripting)
      - [Table of Contents](#table-of-contents-2)
      - [**Delmoment 1: Utveckla applikationen (Cloud-init)**](#delmoment-1-utveckla-applikationen-cloud-init)
      - [**Delmoment 2: Provisionera en värdmiljö (Cloud-init)**](#delmoment-2-provisionera-en-värdmiljö-cloud-init)
      - [**Delmoment 3: Konfigurera värdmiljön (Cloud-init)**](#delmoment-3-konfigurera-värdmiljön-cloud-init)
      - [**Delmoment 4: Driftsätt applikationen (Cloud-init)**](#delmoment-4-driftsätt-applikationen-cloud-init)
      - [**Delmoment 5: Verifiera lösningen (Cloud-init)**](#delmoment-5-verifiera-lösningen-cloud-init)
    - [**Alternative 3: ARM Templates**](#alternative-3-arm-templates)
      - [Table of Contents](#table-of-contents-3)
      - [**Delmoment 1: Utveckla applikationen (ARM)**](#delmoment-1-utveckla-applikationen-arm)
      - [**Delmoment 2: Provisionera en värdmiljö (ARM)**](#delmoment-2-provisionera-en-värdmiljö-arm)
      - [**Delmoment 3: Konfigurera värdmiljön (ARM)**](#delmoment-3-konfigurera-värdmiljön-arm)
      - [**Delmoment 4: Driftsätt applikationen (ARM)**](#delmoment-4-driftsätt-applikationen-arm)
      - [**Delmoment 5: Verifiera lösningen (ARM)**](#delmoment-5-verifiera-lösningen-arm)
  - [**Conclusion**](#conclusion)
  - [**Scripts and Templates**](#scripts-and-templates)
    - [cloudinit-webserver.sh](#cloudinit-webserversh)
    - [webserver-VM.azcli](#webserver-vmazcli)
    - [deploy-webserver.sh](#deploy-webserversh)
    - [armTemplate-webserver.json](#armtemplate-webserverjson)
    - [deploy-armTemplate.sh](#deploy-armtemplatesh)
    - [index.html](#indexhtml)


### **Security Measures**

To ensure a secure environment, I have tried to implement the following measures:

**SSH for Remote Access:** SSH keys were used for secure communication, replacing password-based authentication.
**Firewall Configuration:** Network Security Groups (NSG) were set up to allow only essential inbound traffic (HTTP and SSH) while blocking all other access.

[Return to Main Table of Contents](#table-of-contents)

---

### **Alternatives**

### **Alternative 1: Azure Portal (GUI)**

#### Table of Contents

- [Delmoment 1: Utveckla applikationen](#delmoment-1-utveckla-applikationen-gui)
- [Delmoment 2: Provisionera en värdmiljö](#delmoment-2-provisionera-en-värdmiljö-gui)
- [Delmoment 3: Konfigurera värdmiljön](#delmoment-3-konfigurera-värdmiljön-gui)
- [Delmoment 4: Driftsätt applikationen](#delmoment-4-driftsätt-applikationen-gui)
- [Delmoment 5: Verifiera lösningen](#delmoment-5-verifiera-lösningen-gui)

#### **Delmoment 1: Utveckla applikationen (GUI)**

1. **Developing HTML web page**:
I have created a basic HTML web page named index.html. To view the page code, click the link below:

    **[index.html](#indexhtml)**

#### **Delmoment 2: Provisionera en värdmiljö (GUI)**

```
1. **Login to Azure Portal**: Go to the [Azure Portal](https://portal.azure.com).
2. **Create a Resource Group**.

    - `webserverRG`

3. **Create a Virtual Machine**:
    - resourceGROUP = `webserverRG`
    - Region = `northeurope`
    - vmNAME = `MyUbuntuWebServer`
    - vmSIZE = `Standard_B1ls`
    - IMAGE = `Ubuntu Server 24.04 LTS - x64 Gen2`
    - adminUSERNAME = `mobi`
    - authenticationType = `SSH`
5. **Inbound Port Rules**:
   - Open port **HTTP 80** and **SSH 22**.
6. **Review and create the VM**.
As soon as VM is created, SSH key will be downloaded to our local computer.

> `MyUbuntuWebServer-GUI_key.pem`
```

#### **Delmoment 3: Konfigurera värdmiljön (GUI)**

1. **SSH into the VM**:
    - We need the VM's public IP address. We can find it by going to the VM's network settings and copying the **public IP address** listed there.
    - Now, we need to use SSH to connect to the Ubuntu VM we created. First, open the terminal and run

    **NOTE:** for mac user like myself **`chmod 400 /path/to/your-key.pem`** to set file permission.
   ```
    > **`chmod 400 MyUbuntuWebServer-GUI_key.pem`**
    > **`ssh -i MyUbuntuWebServer-GUI_key.pem mobi@20.240.218.180`**
   ```


3. **Install Nginx**:
After successfully getting access, we will install Nginx server.
```
   > - **`sudo apt-get update`**
   > - **`sudo apt-get install -y nginx`**
```

5. **Verify Installation:**
   ```
   > - **`sudo systemctl status nginx`**
   ```

#### **Delmoment 4: Driftsätt applikationen (GUI)**

1. **Copy to VM**:
    - We need to copy the **`index.html`** file to our web server VM **`/var/www/html/`**
    For that we securely transfer a file **`index.html`** from your local machine to a remote server and then move that file to the web server’s root directory on the remote machine.
```
   > `scp -i <private-key-for-auth> <path/index.html> username@vm-public-ip:/var/www/html/`
```

    - Copy the **index.html** file from the user’s home directory to the web server’s root directory **/var/www/html**.
  ```
   > `ssh -i <private-key-for-auth> username@vm-public-ip sudo cp index.htm /var/www/html/`
  ```


   **Verify the process**

      Go to the web server and check if the our index.html file is there. 

2. **Restart Nginx**:

   > **`sudo systemctl restart nginx`**

#### **Delmoment 5: Verifiera lösningen (GUI)**

1. **Web Page**.
     To verify the web page's accessibility from the internet, I copied the VM's **public IP address** and pasted it into a web browser.


[Return to Main Table of Contents](#table-of-contents)

---

### **Alternative 2: Cloud-init Scripting**

#### Table of Contents

- [Delmoment 1: Utveckla applikationen](#delmoment-1-utveckla-applikationen-cloud-init)
- [Delmoment 2: Provisionera en värdmiljö](#delmoment-2-provisionera-en-värdmiljö-cloud-init)
- [Delmoment 3: Konfigurera värdmiljön](#delmoment-3-konfigurera-värdmiljön-cloud-init)
- [Delmoment 4: Driftsätt applikationen](#delmoment-4-driftsätt-applikationen-cloud-init)
- [Delmoment 5: Verifiera lösningen](#delmoment-5-verifiera-lösningen-cloud-init)

#### **Delmoment 1: Utveckla applikationen (Cloud-init)**

1. ****Developing HTML web page****:
  I have developed a simple **HTML** web page in alternative 1 (GUI). To view the page code, click the link below:

   > **[index.html](#indexhtml)**

#### **Delmoment 2: Provisionera en värdmiljö (Cloud-init)**

In this section, I will set up the environment for my web server using Azure CLI. The script below shows the process of creating a Resource Group, setting the location, choosing an OS, configuring storage and authentication methods, and setting up the network.

1. **Login to Azure CLI**
2. **Create a Resource Group**
3. **Create the VM**
    - `resource-group`
    - `nameVM`
    - `location`
    - `image`
    - `size`
    - `admin-username`
    - `generate-ssh-keys`
4. **Network Configuration**
5. **Verify**

To access the script, click the link below:
> **[webserver-VM.azcli](#webserver-vmazcli)**

#### **Delmoment 3: Konfigurera värdmiljön (Cloud-init)**

1. **Automate the provisioning process**:
In this section, we will configure the web server environment by making our script executable with **"shebang"** `(#!/bin/bash)`. This following script, **`cloudinit-webserver.sh`**, will streamline the provisioning process by installing Nginx and deploying the **index.html** file.

> **[cloudinit-webserver.sh](#cloudinit-webserversh)**

#### **Delmoment 4: Driftsätt applikationen (Cloud-init)**

1. **One Click Deployment**:
Click the link below to access the deploy.webserver.sh script. This script automates both provisioning and deployment of my web page and simplifying the entire process.

> **[deploy-webserver.sh](#deploy-webserversh)**

**To Deploy, Run:**
`Note: "I need to verify and make sure that I am in the correct dir"`
> **`./deploy-webserver.sh`**

#### **Delmoment 5: Verifiera lösningen (Cloud-init)**

In this step we will deploy and verify that everything is working as it should.

> **`index.html`is in correct dir in our VM** by running the following conmands:
```
**`sudo systemctl status nginx`**
**`cd /var/www/html`**
**`ls`**
```


> **Test web page accessing from internet**


[Return to Main Table of Contents](#table-of-contents)

---

### **Alternative 3: ARM Templates**

#### Table of Contents

- [Delmoment 1: Utveckla applikationen](#delmoment-1-utveckla-applikationen-arm)
- [Delmoment 2: Provisionera en värdmiljö](#delmoment-2-provisionera-en-värdmiljö-arm)
- [Delmoment 3: Konfigurera värdmiljön](#delmoment-3-konfigurera-värdmiljön-arm)
- [Delmoment 4: Driftsätt applikationen](#delmoment-4-driftsätt-applikationen-arm)
- [Delmoment 5: Verifiera lösningen](#delmoment-5-verifiera-lösningen-arm)

#### **Delmoment 1: Utveckla applikationen (ARM)**

1. ****Developing HTML web page****:
  I have developed a simple **HTML** web page in alternative 1 (GUI). To view the page code, click the link below:

   > **[index.html](#indexhtml)**

#### **Delmoment 2: Provisionera en värdmiljö (ARM)**

1. **ARM Template for Web Page Deployment**
I design this ARM template to deploy a web page. Following are some important bullet points:

**To access the ARM Template click below**:
> **[armTemplate-webserver.json](#armtemplate-webserverjson)**

**Resources Provisioned**:

- **vmName**: "MyUbuntuWebServer",
- **vmSize**: "Standard_B1ls",
- **OS**: Ubuntu 22.04 LTS
- **SSH-only access** (password authentication disabled)
- **vnetName**: "armVnet",
- **nsgName**: "armNSG",
- **nicName**: "MyUbuntuWebServerNIC",
- **publicIPName**: "MyUbuntuWebServerPublicIP"

**Parameters**:

- **CustomData**: Custom data script for VM initialization
- **Location**: Location for all resources (default: "northeurope")
- **AdminUsername**: Username for the Virtual Machine
- **AdminPublicKey**: SSH Key for the Virtual Machine

**Security considerations**:

- NSG with specific inbound rules
- SSH key authentication (password authentication disabled)

#### **Delmoment 3: Konfigurera värdmiljön (ARM)**

1. **Automate the provisioning process**:
    - In this section, I have developed a one click solution by creating the following deployment script. This script automates the deployment of the Azure Resource Manager (ARM) template, streamlining the setup of the host environment.

To access the **ARM Template** click below:
> **[deploy-armTemplate.sh](#deploy-armtemplatesh)**

#### **Delmoment 4: Driftsätt applikationen (ARM)**

1. **One Click Deployment**:
Click the link below to access the `deploy.armTemplate.sh` script. This script automates both provisioning and deployment of my web page, simplifying the entire process.

> **[deploy-armTemplate.sh](#deploy-armtemplatesh)**

To Deploye, Run:
> **`./deploy.armTemplate.sh`**  "I need to verify and make sure that I am in the correct dir"

#### **Delmoment 5: Verifiera lösningen (ARM)**

1. **Verify Nginx, index.html**.
**`sudo systemctl status nginx`**
**`cd /var/www/html`**
**`ls`**
**`cat index.html`**

1. **Web Page**.
Open browser and check we can access the our web page. 

[Return to Main Table of Contents](#table-of-contents)

## **Conclusion**

In this tutorial I have provided three alternative methods for deploying a web server on Azure, each with detailed steps for developing, provisioning, configuring, deploying, and verifying the solution.

[Return to Main Table of Contents](#table-of-contents)

---

## **Scripts and Templates**

This section includes links to the scripts and templates used throughout the document for easy access:

- **[cloudinit-webserver.sh](#cloudinit-webserversh)**
- **[webserver-VM.azcli](#webserver-vmazcli)**
- **[deploy-webserver.sh](#deploy-webserversh)**
- **[armTemplate-webserver.json](#armtemplate-webserverjson)**
- **[deploy-armTemplate.sh](#deploy-armtemplatesh)**
- **[index.html](#indexhtml)**

### cloudinit-webserver.sh

``` bash
#!/bin/bash

# Update package list and install Nginx
sudo apt-get update
sudo apt-get install -y nginx

# index.html
cat << EOT > /var/www/html/index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Welcome to Azure!</title>
    <style>
        body {
            background-color: #e0f7fa; /* Light teal background */
            color: #333;
            font-family: Arial, sans-serif;
            text-align: center;
            padding: 50px;
        }
        h1 {
            color: #007acc;
        }
        p {
            font-size: 1.2em;
        }
    </style>
</head>
<body>
    <h1>Welcome!</h1>
    <p>Hello, my name is Mubashar. You've successfully landed on my simple Web Page.</p>
    <p>Thank you for visiting</p>
</body>
</html>
EOT

# Start Nginx and enable it to run on startup
sudo systemctl start nginx
sudo systemctl enable nginx
```

[Return to Main Table of Contents](#table-of-contents)

### webserver-VM.azcli

``` bash
# Note for me: Don't forget to az login before running this script

# Variables
RESOURCE_GROUP="webserverRG"
LOCATION="northeurope"
VM_NAME="MyUbuntuWebServer"
VM_SIZE="Standard_B1ls"
IMAGE="Ubuntu2204"
ADMIN_USERNAME="mobi"

# Step 1: Create the Resource Group
az group create --name $RESOURCE_GROUP --location $LOCATION 

# Step 2: Create the VM with a Cloud-init script
az vm create \
    --resource-group $RESOURCE_GROUP \
    --name $VM_NAME \
    --location $LOCATION \
    --image $IMAGE \
    --size $VM_SIZE \
    --admin-username $ADMIN_USERNAME \
    --generate-ssh-keys

# Step 3: Open Port 80 for Web Traffic
echo "Opening port 80 for HTTP traffic"
az vm open-port --resource-group $RESOURCE_GROUP --name $VM_NAME --port 80
```

[Return to Main Table of Contents](#table-of-contents)

### deploy-webserver.sh

```bash
#!/bin/bash

# Variables
RESOURCE_GROUP="webserverRG"
LOCATION="northeurope"
VM_NAME="MyUbuntuWebServer"
VM_SIZE="Standard_B1ls"
IMAGE="Ubuntu2204"
ADMIN_USERNAME="mobi"

# Step 1: Here we will create the Resource Group
echo "Creating resource group: $RESOURCE_GROUP in $LOCATION"
az group create --name $RESOURCE_GROUP --location $LOCATION 

# Step 2: Now, we will create the VM with Cloudinit script 
echo "Creating virtual machine: $VM_NAME..."
az vm create \
    --resource-group $RESOURCE_GROUP \
    --name $VM_NAME \
    --location $LOCATION \
    --image $IMAGE \
    --size $VM_SIZE \
    --admin-username $ADMIN_USERNAME \
    --generate-ssh-keys \
    --custom-data @cloudinit-webserver.sh

# Step 4: Here we open Port 80 for Web Traffic
echo "Opening port 80 for HTTP traffic"
az vm open-port --resource-group $RESOURCE_GROUP --name $VM_NAME --port 80

```

[Return to Main Table of Contents](#table-of-contents)

### armTemplate-webserver.json

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "CustomData": {
        "type": "string",
        "metadata": {
          "description": "Custom data script for VM initialization"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "northeurope",
        "metadata": {
          "description": "Location for all resources."
        }
      },
      "adminUsername": {
        "type": "string",
        "metadata": {
          "description": "Username for the Virtual Machine."
        }
      },
      "adminPublicKey": {
        "type": "string",
        "metadata": {
          "description": "SSH Key for the Virtual Machine."
        }
      }
    },
    "variables": {
      "vmName": "MyUbuntuWebServer",
      "vmSize": "Standard_B1ls",
      "vnetName": "armVnet",
      "nsgName": "armNSG",
      "nicName": "MyUbuntuWebServerNIC",
      "publicIPName": "MyUbuntuWebServerPublicIP"
    },
    "resources": [
      {
        "type": "Microsoft.Network/publicIPAddresses",
        "apiVersion": "2021-02-01",
        "name": "[variables('publicIPName')]",
        "location": "[parameters('location')]",
        "properties": {
          "publicIPAllocationMethod": "Dynamic"
        }
      },
      {
        "type": "Microsoft.Network/virtualNetworks",
        "apiVersion": "2021-02-01",
        "name": "[variables('vnetName')]",
        "location": "[parameters('location')]",
        "properties": {
          "addressSpace": {
            "addressPrefixes": ["10.0.0.0/16"]
          },
          "subnets": [
            {
              "name": "default",
              "properties": {
                "addressPrefix": "10.0.0.0/24"
              }
            }
          ]
        }
      },
      {
        "type": "Microsoft.Network/networkSecurityGroups",
        "apiVersion": "2021-02-01",
        "name": "[variables('nsgName')]",
        "location": "[parameters('location')]",
        "properties": {
          "securityRules": [
            {
              "name": "AllowHTTP",
              "properties": {
                "protocol": "Tcp",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 100,
                "direction": "Inbound"
              }
            },
            {
              "name": "AllowSSH",
              "properties": {
                "protocol": "Tcp",
                "sourcePortRange": "*",
                "destinationPortRange": "22",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 110,
                "direction": "Inbound"
              }
            }
          ]
        }
      },
      {
        "type": "Microsoft.Network/networkInterfaces",
        "apiVersion": "2021-02-01",
        "name": "[variables('nicName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
          "[resourceId('Microsoft.Network/virtualNetworks', variables('vnetName'))]",
          "[resourceId('Microsoft.Network/networkSecurityGroups', variables('nsgName'))]",
          "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPName'))]"
        ],
        "properties": {
          "ipConfigurations": [
            {
              "name": "ipconfig1",
              "properties": {
                "privateIPAllocationMethod": "Dynamic",
                "publicIPAddress": {
                  "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPName'))]"
                },
                "subnet": {
                  "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('vnetName'), 'default')]"
                }
              }
            }
          ],
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('nsgName'))]"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "2021-03-01",
        "name": "[variables('vmName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
          "[resourceId('Microsoft.Network/networkInterfaces', variables('nicName'))]"
        ],
        "properties": {
          "hardwareProfile": {
            "vmSize": "[variables('vmSize')]"
          },
          "osProfile": {
            "computerName": "[variables('vmName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "customData": "[base64(parameters('CustomData'))]",
            "linuxConfiguration": {
              "disablePasswordAuthentication": true,
              "ssh": {
                "publicKeys": [
                  {
                    "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
                    "keyData": "[parameters('adminPublicKey')]"
                  }
                ]
              }
            }
          },
          "storageProfile": {
            "imageReference": {
              "publisher": "Canonical",
              "offer": "0001-com-ubuntu-server-jammy",
              "sku": "22_04-lts-gen2",
              "version": "latest"
            },
            "osDisk": {
              "createOption": "FromImage"
            }
          },
          "networkProfile": {
            "networkInterfaces": [
              {
                "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('nicName'))]"
              }
            ]
          }
        }
      }
    ]
  }
```

[Return to Main Table of Contents](#table-of-contents)

### deploy-armTemplate.sh

```bash
#!/bin/bash

az group create --name armRG --location northeurope

az deployment group create \
--resource-group armRG \
--template-file armTemplate-webserver.json \
--parameters adminUsername=mobi \
             adminPublicKey="$(cat ~/.ssh/id_rsa.pub)" \
             CustomData=@cloudinit-webserver.sh
```

[Return to Main Table of Contents](#table-of-contents)

### index.html

```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Welcome to Azure!</title>
        <style>
            body {
                background-color: #e0f7fa; /* Light teal background */
                color: #333;
                font-family: Arial, sans-serif;
                text-align: center;
                padding: 50px;
            }
            h1 {
                color: #007acc;
            }
            p {
                font-size: 1.2em;
            }
        </style>
    </head>
    <body>
        <h1>Welcome!</h1>
        <p>Hello, my name is Mubashar. You've successfully landed on my simple Page.</p>
        <p>Thank you for visiting</p>
    </body>
    </html>
```

[Return to Main Table of Contents](#table-of-contents)

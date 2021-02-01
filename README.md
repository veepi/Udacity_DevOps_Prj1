# Udacity DevOps with Azure - Project 1

### Introduction

This Readme and the accompanying Packer and Terraform files are part of the Udacity Cloud DevOps using Azure course.
Objective is to generate the requisite cloud infrastructure for a web application to be hosted in Microsoft Azure. 
The following are the main resources that will be created:
    1. Tagging policy in Azure that enforces all resources to have at least one tag.
    2. An Ubuntu Server virtual machine image.
    3. A virtual network and an internal subnet.
    4. One or more VMs based on that image as the application servers.
    5. Managed disks attached to each VM for data.
    6. A network security group and network security rules to allow internal traffic and prevent traffic from the internet.
    7. A public facing load balancer with an associated public IP address.
    8. An Azure Bastion Host with the associated subnet public IP address for management access to the VMs.

    All the above resources will be placed in one Azure resource group.
    All resources will be assigned ‘DevOps_Project_1’ as ‘project’ tag. Additional tags could be provided as variables to Terraform.

### Getting Started

Make sure you have the following:

1. An Azure account with privileges to create resources.
2. An environment with Azure CLI, Packer and Terraform installed. Refer the Dependencies section below for install instructions.
3. Download and extracted the project zip file (or cloned the repo). It contains the following subfolders:
    a. packer – packer image template (server.json)
    b. terraform – terraform files including main.tf and vars.tf
    c.  screenshots - Contains the screenshot of the tagging policy applied to the Azure subscription.

### Dependencies

1. Create an [Azure Account](https://portal.azure.com) 
2. Install the [Azure command line interface](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
3. Install [Packer](https://www.packer.io/downloads)
4. Install [Terraform](https://www.terraform.io/downloads.html)

### Instructions

1. Open command line, e.g., Bash, and navigate to the project folder. 
2. Log in to your Azure account in Azure CLI. (<https://docs.microsoft.com/en-us/cli/azure/authenticate-azure-cli>)
3. Use packer to create the virtual machine image in Azure:
    a. The Packer template accepts the following variables:
        i. Azure authentication info:
            • client_id – Azure client ID. Defaults to environment variable ARM_CLIENT_ID.
            • client_secret – Azure client secret.  Defaults to environment variable ARM_CLIENT_SECRET.
            • subscription_id – Azure subscription ID.  Defaults to environment variable ARM_SUBSCRIPTION_ID.
            You can set the environment variables or pass them as variables to packer build command.
            At least the subscription_id is required.
        ii. VM image info:
            • image_name – Name for the VM image. Default: “udevops_prj1_image”.
            • image_rg – Resource Group name for the VM image. Defaults: “udacity_devops_rg",
            • image_location – Azure location for the image. Default: "West US"
        To override the defaults, pass the variable to packer build command.

    b. Run packer build. (e.g., to use default values run: packer build ./packer/server.json)

    c. Verify if the image was created in the Azure Portal or in Azure CLI (az image list)

4. Use Terraform to create the cloud infrastructure:
    a. Navigate to terraform subfolder (e.g., cd ./terraform)

    b. Refer the vars.tf file for the variables used and their description.
        The defaults will create 2 VMs of ‘Standard_A1_V2’ size with ‘Standard_LRS’ disks for both OS and data.
        Data disks will be provisioned for 1 GB. All resources will be named with the prefix ‘Prj1’ by default, e.g. resource group will be named Prj1-rg.
        If required, modify the default values, or pass on the variables in command-line when executing terraform plan step below.

    c. Run the following commands in sequence:
        • terraform init
        • terraform plan -out solution.plan
        • terraform apply solution.plan

### Output

The ./terraform/solution.plan file will contain the terraform solution.

When successfully executed, you will have created the resources listed in the Introduction section above.
e.g., az resource list -g udacity_devops_rg -o table

    Name                    ResourceGroup    Location    Type                                     Status
    ----------------------  ---------------  ----------  ---------------------------------------  --------
    Prj1-availabilitySet_1  Prj1-rg          westus      Microsoft.Compute/availabilitySets
    Prj1-AppVM-0-OSDisk     PRJ1-RG          westus      Microsoft.Compute/disks
    Prj1-AppVM-1-OSDisk     PRJ1-RG          westus      Microsoft.Compute/disks
    Prj1-mdisk_0            Prj1-rg          westus      Microsoft.Compute/disks
    Prj1-mdisk_1            Prj1-rg          westus      Microsoft.Compute/disks
    Prj1-AppVM-0            Prj1-rg          westus      Microsoft.Compute/virtualMachines
    Prj1-AppVM-1            Prj1-rg          westus      Microsoft.Compute/virtualMachines
    Prj1-bhost              Prj1-rg          westus      Microsoft.Network/bastionHosts
    Prj1-lb_1               Prj1-rg          westus      Microsoft.Network/loadBalancers
    Prj1-vm_0-nic_1         Prj1-rg          westus      Microsoft.Network/networkInterfaces
    Prj1-vm_1-nic_1         Prj1-rg          westus      Microsoft.Network/networkInterfaces
    Prj1-nsg                Prj1-rg          westus      Microsoft.Network/networkSecurityGroups
    Prj1-bhost_public_ip    Prj1-rg          westus      Microsoft.Network/publicIPAddresses
    Prj1-lb_public_ip_1     Prj1-rg          westus      Microsoft.Network/publicIPAddresses
    Prj1-network            Prj1-rg          westus      Microsoft.Network/virtualNetworks

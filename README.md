# **Azure Gateway Load Balancer**

## **Requirements:**

- Minimum version of PAN-OS `10.1.4` and vm-series plugin `2.1.4` 

**Bootstrap Information**

  - After cloning the repository, extract the contents of **bootstrap.tgz** by `tar xvzf bootstrap.tgz`
  - The contents of extracted archive should be uploaded in the storage account as shown in the below step.
  - Create Storage Account for Bootstrapping. 
    
    How To: https://docs.paloaltonetworks.com/vm-series/10-1/vm-series-deployment/bootstrap-the-vm-series-firewall/bootstrap-the-vm-series-firewall-in-azure.html

  If you are using the Panorama Software Firewall License Plugin follow the following Guide:

  https://docs.paloaltonetworks.com/vm-series/9-1/vm-series-deployment/license-the-vm-series-firewall/use-panorama-based-software-firewall-license-management

## **Steps to deploy the template**

- ### **Provider Template Parameters**

Configure the template Parameters for your Azure GWLB deployment

1. **FirewallDnsName**

   Unique DNS Name for the Public IP used to access PAN Firewall VM.
   
2. **vmName**

   Name for the VM-Series Firewall
   
3. **adminUsername**
 
   The username for the account on the VM-Series firewall

4. **adminPassword**

   Password for the account for the VM-Series firewall. Make this parameter Optional if you choose to use any other Authentication Type

5. **bootstrapStorageAccount**

   The name of the storage account created in the Prerequisite step
   
7. **bootstrapStorageAccountAccessKey**

   The value of the storage account access key  created in the Prerequisite step
   
9. **bootstrapFileShare**

   The name of the storage account file share created in the Prerequisite step
   
11. **imageVersion**
   
    The Pan-OS image version. We support from `10.1.4` Add the required version in the allowed values to use Pan-OS version of your preference
   
13. **imageSKU**

    Licensing model - byol, bundle1, bundle2
   
15.**vmSize**
   
   Azure VMsize for the Firewall. Choose from the list allowed values.
 
16.**AddressPrefix**

   The CIDR range for the Provider network ex. "10.0.0.0/16"
   
17.**ManagementSubnet**
   
   Management Subnet Prefix for Provider management subnet ex "10.0.1.0/24"
   
18.**DataSubnet**

   Data Subnet Prefix for Provider management subnet ex "10.0.0.0/24"


Steps:

Create resource-group in a supported region of your choice.
az group create --name < resource-group > --location "eastus2"

Provider Deployment
az deployment group create --name providertemplate --resource-group < resource-group > --template-file provider-simple-lb.json --parameters adminUsername=< username > adminPassword=< password > vmName=< vmseries-name > FirewallDnsName=< dns name > bootstrapStorageAccount=< storagaccname > bootstrapStorageAccountAccessKey=< storageaccesskey > bootstrapFileShare=< filesharename >

Consumer side Deployment
az deployment group create --name consumertemplate --resource-group < resource-group > --template-file consumer-simple-lb.json --parameters adminUsername=ubuntu adminPassword=< password > providerResourceGroup=< resource-group >

## **NOTE**

`init-cfg.txt` in the bootstrap folder should include this:

To deploy the solution with default ports

`plugin-op-commands=azure-gwlb-inspect:enable`

To deploy the solution with custom ports edit the `init-cfg.txt` to

`plugin-op-commands=azure-gwlb-inspect:enable+internal-port-3000+external-port-3001+internal-vni-900,external-vni-901`



<p align="center">
<img src="https://github.com/PaloAltoNetworks/Azure-GWLB/blob/master/Images/azure_gwlb.webp">
https://www.paloaltonetworks.com/blog/network-security/vm-series-azure-gateway-load-balancer/
</p>

## **Part 1: Deploy Provider Resources**

[<img src="http://azuredeploy.net/deploybutton.png"/>](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FPaloAltoNetworks%2FAzure-GWLB%2Fmaster%2Fprovider-simple-lb.json)

## **Part 2: Deploy Consumer Resources**

[<img src="http://azuredeploy.net/deploybutton.png"/>](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FPaloAltoNetworks%2FAzure-GWLB%2Fmaster%2Fconsumer-simple-lb.json)




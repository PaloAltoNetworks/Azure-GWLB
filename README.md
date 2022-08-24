# **Azure Gateway Load Balancer**

**Bootstrap Information**

- Create Bootstrap Storage account and update the file from the the bootstrap folder into it
How To: https://docs.paloaltonetworks.com/vm-series/10-1/vm-series-deployment/bootstrap-the-vm-series-firewall/bootstrap-the-vm-series-firewall-in-azure.html

- If you are using the Panorama Software Firewall License Plugin follow the following Guide:
https://docs.paloaltonetworks.com/vm-series/9-1/vm-series-deployment/license-the-vm-series-firewall/use-panorama-based-software-firewall-license-management

Steps:

Create resource-group in a supported region of your choice.
az group create --name < resource-group > --location "eastus2"

Provider Deployment
az deployment group create --name providertemplate --resource-group < resource-group > --template-file provider-simple-lb.json --parameters adminUsername=< username > adminPassword=< password > vmName=< vmseries-name > FirewallDnsName=< dns name > bootstrapStorageAccount=< storagaccname > bootstrapStorageAccountAccessKey=< storageaccesskey > bootstrapFileShare=< filesharename >

Consumer side Deployment
az deployment group create --name consumertemplate --resource-group < resource-group > --template-file consumer-simple-lb.json --parameters adminUsername=ubuntu adminPassword=< password > providerResourceGroup=< resource-group >

Requirements:
Minimum of PAN-OS 10.1.4 and vm-series plugin 2.1.4 is required

init-cfg.txt should include this:

with default parameters: plugin-op-commands=azure-gwlb-inspect:enable

Otherwise, add as here : plugin-op-commands=azure-gwlb-inspect:enable+internal-port-3000+external-port-3001+internal-vni-900,external-vni-901



<p align="center">
<img src="https://github.com/PaloAltoNetworks/Azure-GWLB/blob/master/Images/azure_gwlb.webp">
https://www.paloaltonetworks.com/blog/network-security/vm-series-azure-gateway-load-balancer/
</p>

## **Part 1: Deploy Provider Ressources**

[<img src="http://azuredeploy.net/deploybutton.png"/>](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FPaloAltoNetworks%2FAzure-GWLB%2Fmaster%2Fprovider-simple-lb.json)

## **Part 1: Deploy Consumer Ressources**

[<img src="http://azuredeploy.net/deploybutton.png"/>](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FPaloAltoNetworks%2FAzure-GWLB%2Fmaster%2Fconsumer-simple-lb.json)


## **Requirements:**

- Minimum of PAN-OS 10.1.2 and vm-series plugin 2.1.2 is required

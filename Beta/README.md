# **Azure Gateway Load Balancer**

## **Phase:** Beta(preview)

**Steps:**
1. **Create resource-group in "eastus2euap" region** 

az group create --name < resource-group > --location "eastus2euap"

2. **Provider Deployment**

az deployment group create --name providertemplate --resource-group < resource-group > --template-file provider-simple-lb.json
--parameters adminUsername=< username > adminPassword=< password > vmName=< vmseries-name > FirewallDnsName=< dns name > 
bootstrapStorageAccount=< storagaccname > bootstrapStorageAccountAccessKey=< storageaccesskey > bootstrapFileShare=< filesharename >

3. **Consumer side Deployment**

az deployment group create --name consumertemplate --resource-group < resource-group > --template-file consumer-simple-lb.json 
--parameters adminUsername=ubuntu adminPassword=< password > providerResourceGroup=< resource-group >

## **Requirements:**

- Minimum of PAN-OS 10.1.2 and vm-series plugin 2.1.2 is required
- init-cfg.txt should include this: 

  with default parameters:
  plugin-op-commands=azure-gwlb-inspect:enable

  Otherwise, add as here : plugin-op-commands=azure-gwlb-inspect:enable+internal-port-3000+external-port-3001+internal-vni-900,external-vni-901




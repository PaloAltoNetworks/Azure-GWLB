# **Azure Gateway Load Balancer**

<p align="left">
<img src="https://github.com/PaloAltoNetworks/Azure-GWLB/blob/main/Images/azure_gwlb.webp">
 https://www.paloaltonetworks.com/blog/network-security/vm-series-azure-gateway-load-balancer/
</p>



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

 ### **Provider Template Parameters**

   Configure the template Parameters for your Azure GWLB deployment

- **FirewallDnsName**

    Unique DNS Name for the Public IP used to access PAN Firewall VM.
   
- **vmName**

    Name for the VM-Series Firewall
   
- **adminUsername**
 
    The username for the account on the VM-Series firewall

- **adminPassword**

    Password for the account for the VM-Series firewall. Make this parameter Optional if you choose to use any other Authentication Type

- **bootstrapStorageAccount**

    The name of the storage account created in the Prerequisite step
   
- **bootstrapStorageAccountAccessKey**

    The value of the storage account access key  created in the Prerequisite step
   
- **bootstrapFileShare**

    The name of the storage account file share created in the Prerequisite step
   
- **imageVersion**
   
    The Pan-OS image version. We support from `10.1.4` Add the required version in the allowed values to use Pan-OS version of your preference
   
- **imageSKU**

    Licensing model - byol, bundle1, bundle2
   
- **vmSize**
   
   Azure VMsize for the Firewall. Choose from the list allowed values.
 
- **AddressPrefix**

   The CIDR range for the Provider network ex. "10.0.0.0/16"
   
- **ManagementSubnet**
   
   Subnet Prefix for Provider management subnet ex "10.0.1.0/24"
   
- **DataSubnet**

   Subnet Prefix for Provider data subnet ex "10.0.0.0/24"

## **NOTE**

**`init-cfg.txt`** in the bootstrap folder should include this:

To deploy the solution with default ports

`plugin-op-commands=azure-gwlb-inspect:enable`

The port and VNI parameters when not specified will use the default values: Internal Port 2000 Internal VNI 800, External Port 2001 and External VNI 801. These parameters must match the GWLB backend pool tunnel interfaces properties to properly establish the service chain. If you use the custom ports, make sure to edit the security-stack.json with the custom ports in the below block.

To deploy the solution with custom ports edit the `init-cfg.txt` to

`plugin-op-commands=azure-gwlb-inspect:enable+internal-port-3000+external-port-3001+internal-vni-900,external-vni-901`


```
"backendAddressPools": [
          {
            "name": "BackendPool1",
            "properties": {
              "tunnelInterfaces": [
                {
                  "port": 2000,        # Change the internal port here to 3000
                  "Identifier": 800,   # Change the identifier to 900
                  "Protocol": "VxLan",
                  "Type": "Internal"
                },
                {
                  "port": 2001,       # Change the external port here to 900
                  "Identifier": 801,  # Change the identifier to 901
                  "Protocol": "VxLan",
                  "Type": "External"
                }
              ]
            }
          }
        ],
```

## **Part 1: Deploy Security Stack Resources**

[<img src="http://azuredeploy.net/deploybutton.png"/>](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FPaloAltoNetworks%2FAzure-GWLB%2Fmaster%2Fsecurity-stack.json)


The ARM template deploys the Security stack with Gateway Loadbalancer, VM-Series firewall with GWLB bootstrap configuration , VM-Series firewall added in the backend pool of the Gateway Loadbalancer.

## **Part 2: Deploy Application Stack Resources**

[<img src="http://azuredeploy.net/deploybutton.png"/>](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FPaloAltoNetworks%2FAzure-GWLB%2Fmaster%2Fapplication-stack.json)

The ARM template deploys the Application stack with the Loadbalancer configured with the default Load Balancer rules, Linux VM with simpleHTTP service.

You can use the **application-stack.json** to deploy multiple spokes / application stacks.

## **Traffic Test**
 
To test the ingress traffic,  issue the below command from a terminal

```
wget http://<FrontendIPofPublicLB:50000>

```
or

Use a browser, type in  **http://<FrontendIPofPublicLB:50000>**

You can see the secured ingress traffic sessions in the Firewall.

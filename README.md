# Managed Disks Double Encryption Preview 

The repository contains sample templates for enabling double encryption at rest for managed disks. Double encryption at rest is the continuation of server-side encryption (SSE) at rest with customer managed keys (CMK) that we recently announced in GA for managed disks. If you are hosting VM/VMSS with managed disks and already leveraging SSE with CMK capability, now you can also enable double encryption at rest.

To enable double encryption at rest for Managed Disks attached to VMs/VMSS, you must follow the steps below:

1. Create a DiskEncryptionSet with encryptionType set as EncryptionAtRestWithPlatformAndCustomerKeys. Please use the API version *2020-05-01* in the Azure Resource Manager (ARM) template. Please refer to the sample ARM template [CreateDiskEncryptionSetForDoubleEncryption.json](https://github.com/ramankumarlive/manageddisksdoubleencryptionpreview/blob/master/CreateDiskEncryptionSetForDoubleEncryption.json)

```PowerShell
New-AzResourceGroupDeployment -ResourceGroupName CMKTesting `
  -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddisksdoubleencryptionpreview/master/CreateDiskEncryptionSetForDoubleEncryption.json" `
  -diskEncryptionSetName "yourDESForDoubleEncryption" `
  -keyVaultId "subscriptions/dd80b94e-0463-4a65-8d04-c94f403879dc/resourceGroups/yourResourceGroupName/providers/Microsoft.KeyVault/vaults/yourKeyVaultName" `
  -keyVaultKeyUrl "https://yourKeyVaultName.vault.azure.net/keys/yourKeyName/403445136dee4a57af7068cab08f7d42" `
  -encryptionType "EncryptionAtRestWithPlatformAndCustomerKeys" `
  -region "CentralUSEUAP"

```
2. Create a VM with managed disks using the resource URI of the DiskEncryptionSet created in the step #1

```PowerShell
$password=ConvertTo-SecureString -String "yourPassword" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName yourResourceGroupName `
  -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddisksdoubleencryptionpreview/master/CreateVMWithDisksEncryptedWithDoubleEncryption.json" `
  -virtualMachineName "ramandssecmk1" `
  -adminPassword $password `
  -vmSize "Standard_DS3_V2" `
  -diskEncryptionSetId "/subscriptions/dd80b94e-0463-4a65-8d04-c94f403879dc/resourceGroups/yourResourceGroupName/providers/Microsoft.Compute/diskEncryptionSets/yourDESForDoubleEncryption" `
  -region "CentralUSEUAP"
```

3. Create a VMSS with managed disks using the resource URI of the DiskEncryptionSet created in the step #1

```PowerShell
$password=ConvertTo-SecureString -String "yourPassword" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName yourResourceGroupName `
  -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddisksdoubleencryptionpreview/master/CreateVMSSWithDisksEncryptedWithDoubleEncryption.json" `
  -vmssName "vmssde2" `
  -adminPassword $password `
  -vmSize "Standard_DS3_V2" `
  -diskEncryptionSetId "/subscriptions/dd80b94e-0463-4a65-8d04-c94f403879dc/resourceGroups/yourResourceGroupName/providers/Microsoft.Compute/diskEncryptionSets/yourDESForDoubleEncryption" `
  -region "CentralUSEUAP"
```

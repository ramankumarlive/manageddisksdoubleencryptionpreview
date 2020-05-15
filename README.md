# Managed Disks Double Encryption Preview 

The repository contains sample templates for enabling double encryption for managed disks. To enable double encryption for Managed Disks, you must follow the steps below:

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
$password=ConvertTo-SecureString -String "Password@123" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName CMKTesting `
  -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddisksdoubleencryptionpreview/master/CreateVMWithDisksEncryptedWithDoubleEncryption.json" `
  -virtualMachineName "ramandssecmk1" `
  -adminPassword $password `
  -vmSize "Standard_DS3_V2" `
  -diskEncryptionSetId "/subscriptions/dd80b94e-0463-4a65-8d04-c94f403879dc/resourceGroups/CMKTesting/providers/Microsoft.Compute/diskEncryptionSets/myDESForDoubleEncryption" `
  -region "CentralUSEUAP"
```

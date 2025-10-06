# Azure VM Deployment with Compute Gallery, Disk Encryption, and Trusted Launch

This ARM template project deploys an Azure Virtual Machine from a Compute Gallery image with advanced security features including disk encryption and trusted launch capabilities.

## Features

- **Compute Gallery Integration**: Deploy VMs from custom images stored in Azure Compute Gallery
- **Azure Disk Encryption**: Full disk encryption using Azure Key Vault
- **Trusted Launch**: Enhanced security with Secure Boot and vTPM
- **Flexible Authentication**: SSH key (recommended) or password authentication
- **Network Security**: Configured with Network Security Groups
- **Premium Storage**: Uses Premium SSD for better performance and encryption support

## Prerequisites

Before deploying this template, ensure you have:

1. **Azure CLI**: Install the latest Azure CLI
   ```bash
   # Install Azure CLI (if not already installed)
   # Follow instructions at: https://docs.microsoft.com/en-us/cli/azure/install-azure-cli
   ```

2. **Azure Account**: Access to an Azure subscription with appropriate permissions

3. **Compute Gallery**: An existing Azure Compute Gallery with image definitions

4. **Key Vault**: Azure Key Vault for disk encryption with encryption keys

## Quick Start

### 1. Login to Azure

```bash
az login
az account set --subscription "your-subscription-id"
```

### 2. Create Prerequisites (if needed)

Create a resource group for your Key Vault:
```bash
az group create --name "rg-security-resources" --location "East US"
```

Create a Key Vault for disk encryption:
```bash
az keyvault create --name "kv-myproject-001" --resource-group "rg-security-resources" --location "East US" --enabled-for-disk-encryption
```

Create an encryption key:
```bash
az keyvault key create --vault-name "kv-myproject-001" --name "disk-encryption-key" --protection software
```

### 3. Configure Parameters

Edit the `azuredeploy.parameters.json` file with your specific values:

```json
{
  "vmName": { "value": "vm-secure-gallery" },
  "adminUsername": { "value": "azureuser" },
  "adminPasswordOrKey": { "value": "YOUR_SSH_PUBLIC_KEY_HERE" },
  "computeGalleryName": { "value": "YOUR_COMPUTE_GALLERY_NAME" },
  "computeGalleryImageDefinition": { "value": "YOUR_IMAGE_DEFINITION_NAME" },
  "computeGalleryResourceGroup": { "value": "YOUR_GALLERY_RESOURCE_GROUP" },
  "keyVaultName": { "value": "YOUR_KEY_VAULT_NAME" },
  "keyVaultResourceGroup": { "value": "YOUR_KEY_VAULT_RESOURCE_GROUP" },
  "diskEncryptionKeyName": { "value": "YOUR_DISK_ENCRYPTION_KEY_NAME" }
}
```

### 4. Deploy the Template

Deploy using Azure CLI:

```bash
# Create resource group for deployment
az group create --name "rg-vm-deployment" --location "East US"

# Validate the template
az deployment group validate \
  --resource-group "rg-vm-deployment" \
  --template-file mainTemplate.json \
  --parameters @azuredeploy.parameters.json

# Deploy the template
az deployment group create \
  --resource-group "rg-vm-deployment" \
  --template-file mainTemplate.json \
  --parameters @azuredeploy.parameters.json \
  --name "vm-secure-deployment"
```

Alternatively, use VS Code tasks:
- Press `Ctrl+Shift+P` and type "Tasks: Run Task"
- Choose "Validate ARM Template" or "Deploy ARM Template"

## Template Parameters

| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| `vmName` | string | Name of the virtual machine | Required |
| `adminUsername` | string | Admin username for the VM | Required |
| `authenticationType` | string | Authentication type (sshPublicKey/password) | sshPublicKey |
| `adminPasswordOrKey` | securestring | SSH key or password | Required |
| `vmSize` | string | VM size (must support trusted launch) | Standard_D2s_v5 |
| `computeGalleryName` | string | Name of the compute gallery | Required |
| `computeGalleryImageDefinition` | string | Image definition name | Required |
| `computeGalleryImageVersion` | string | Image version | latest |
| `keyVaultName` | string | Key Vault name for encryption | Required |
| `diskEncryptionKeyName` | string | Encryption key name | Required |
| `enableSecureBoot` | bool | Enable secure boot | true |
| `enableVtpm` | bool | Enable vTPM | true |

## Security Features

### Trusted Launch
- **Secure Boot**: Ensures only signed bootloaders and OS kernels run
- **vTPM**: Virtual Trusted Platform Module for enhanced security
- **Boot Integrity Monitoring**: Detects rootkits and boot kits

### Disk Encryption
- **Azure Disk Encryption**: Encrypts OS and data disks using BitLocker (Windows) or dm-crypt (Linux)
- **Key Management**: Uses Azure Key Vault for encryption key management
- **Platform-Managed Keys**: Supports both customer-managed and platform-managed encryption keys

### Network Security
- **Network Security Groups**: Configured with minimal required access rules
- **SSH Access**: Port 22 access for Linux VMs (customize as needed)
- **Public IP**: Dynamic public IP with DNS label for external access

## VM Size Requirements

The template uses VM sizes that support trusted launch. Supported series include:
- **D-series v5**: Standard_D2s_v5, Standard_D4s_v5, etc.
- **E-series v5**: Standard_E2s_v5, Standard_E4s_v5, etc.
- **F-series v2**: Standard_F2s_v2, Standard_F4s_v2, etc.

## Troubleshooting

### Common Issues

1. **Template Validation Errors**
   - Verify all required parameters are provided
   - Ensure VM size supports trusted launch
   - Check that the compute gallery image exists

2. **Key Vault Access Issues**
   - Verify Key Vault is enabled for disk encryption
   - Ensure proper access policies are set
   - Check that the encryption key exists

3. **Deployment Failures**
   - Review the deployment error messages in Azure portal
   - Verify resource quotas and limits
   - Check regional availability of resources

### Validation Commands

```bash
# Test template validation
az deployment group validate \
  --resource-group "your-rg" \
  --template-file mainTemplate.json \
  --parameters @azuredeploy.parameters.json

# Check deployment status
az deployment group list --resource-group "your-rg"

# Get deployment details
az deployment group show --resource-group "your-rg" --name "deployment-name"
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test the template thoroughly
5. Submit a pull request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Support

For issues and questions:
- Check the troubleshooting section above
- Review Azure documentation for ARM templates
- Open an issue in this repository

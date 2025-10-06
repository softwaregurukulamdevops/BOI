<!-- Use this file to provide workspace-specific custom instructions to Copilot. For more details, visit https://code.visualstudio.com/docs/copilot/copilot-customization#_use-a-githubcopilotinstructionsmd-file -->

# Azure ARM Template Project Instructions

This is an Azure Resource Manager (ARM) template project for deploying virtual machines from Azure Compute Gallery images with advanced security features.

## Project Context
- **Purpose**: Deploy VMs with disk encryption and trusted launch security features
- **Template Type**: ARM JSON templates for Azure resource deployment
- **Security Focus**: Implement Azure Disk Encryption and Trusted Launch capabilities
- **Image Source**: Azure Compute Gallery (formerly Shared Image Gallery)

## Key Components
- `mainTemplate.json`: Main ARM template with VM, networking, and security configurations
- `azuredeploy.parameters.json`: Parameter file for template customization
- `deploy.ps1`: PowerShell deployment script with validation
- `setup-prerequisites.ps1`: Script to create required Key Vault and encryption keys

## Security Features Implemented
- **Trusted Launch**: Secure Boot and vTPM enabled for enhanced VM security
- **Azure Disk Encryption**: OS and data disk encryption using Azure Key Vault
- **Network Security**: NSG rules and secure networking configuration
- **Authentication**: SSH key-based authentication (preferred) or password-based

## Coding Guidelines
- Use latest ARM template schema versions
- Follow Azure naming conventions (e.g., prefixes like vm-, nic-, nsg-)
- Include comprehensive metadata descriptions for all parameters
- Implement proper dependency chains between resources
- Use variables for computed values and resource references
- Include meaningful outputs for deployment results

## Azure Best Practices
- Use Premium SSD storage for better performance and encryption support
- Implement least-privilege access principles
- Use managed identities where applicable
- Enable diagnostic logging and monitoring
- Follow Azure Well-Architected Framework principles

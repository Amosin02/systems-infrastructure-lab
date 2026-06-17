# Deploying Active Directory in AWS via Infrastructure as Code

In this project, I stepped away from the standard Windows Server Manager graphical interface and completely automated the deployment of a root Active Directory Domain Services (AD DS) forest inside an AWS EC2 cloud instance. By leveraging automated boot scripts, I reduced a multi-step configuration wizard down to a single, zero-touch execution block.

Here is the breakdown of how I designed and executed this automated deployment.

## Phase 1: Designing the PowerShell Automation Script

The foundation of this project relies on bypassing the visual Server Manager wizard. Windows Server allows administrators to control server roles programmatically using PowerShell.

To prevent syntax errors from hidden string spaces or line-break bugs (a common pitfall when handling multi-line arguments), I utilized a professional scripting pattern called Splatting. This maps all the required deployment arguments into a single, clean hash table.

```script```
<powershell>
# 1. Install the core Active Directory binaries and management modules
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools

# 2. Establish credentials and target parameters
$DomainName = "homelab.local"
$DSRMFilePassword = ConvertTo-SecureString "YourHighlyComplexPassword123!" -AsPlainText -Force

# 3. Consolidate installation arguments via a Splatting Hash Table
$ADParams = @{
    DomainName                    = $DomainName
    CreateDnsDelegation           = $false
    DatabasePath                  = "C:\Windows\NTDS"
    LogPath                       = "C:\Windows\NTDS"
    SysVolPath                    = "C:\Windows\SYSVOL"
    NoRebootOnCompletion          = $false
    Force                         = $true
    SafeModeAdministratorPassword = $DSRMFilePassword
}

# 4. Fire the deterministic deployment
Install-ADDSForest @ADParams
</powershell>
```script```

## Phase 2: Provisioning with AWS User Data

With the script ready, the next step was handling cloud-native integration. AWS EC2 features an advanced configuration field called User Data.

During the launch phase of a fresh Windows Server 2025 instance, I injected the complete PowerShell script directly into the instance's User Data metadata box. When the physical hardware initialized the virtual machine for the very first time, the internal AWS launch agent intercepted this code block and executed it in the background with full root-level administrative privileges.

## Phase 3: Infrastructure Verification

Once the instance crossed its initialization phase and passed its health checks, I connected to the server using a Remote Desktop Protocol (RDP) client.

Instead of seeing a plain, unconfigured standalone server, the results were instantaneous:

- The machine had completely transformed into a functioning Domain Controller.
- Active Directory Users and Computers were active and ready to handle directory management.

## Key Project Takeaways
1. The Power of Cloud Virtualization
This project completely re-emphasized why AWS EC2 instances are such magnificent tools for engineers. The ease of modern virtualization means you no longer need racks of heavy, loud physical servers in a room to simulate an enterprise network. With a few API calls, AWS handles the hypervisor provisioning, routing fabrics, and dynamic storage mapping instantly, allowing engineers to focus purely on architecture and configuration.

2. The Magic of User Data
Discovering how User Data functions was one of the most rewarding elements of this lab. The ability to pass a hidden block of commands to a cold virtual machine and watch it wake up fully configured is amazing. Because User Data acts as an automated boot pipeline, it can be scaled to handle numerous workflows—from pulling specific software repositories on start, updating security patches automatically, to bootstrapping cluster configurations without human intervention.

3. A Shift in Automation Mindset
Successfully executing this script completely shifts your perspective on system administration. Once you realize you can stand up something as complex as an Active Directory Forest using a small block of clean code, it completely opens your eyes to what else you can automate. From here, the path forward leads naturally into automated user onboarding, policy deployments, and full DevOps-style configuration management.

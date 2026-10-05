# Azure Entra ID

## Tenant and Entra ID

A tenant is a dedicated Microsoft Entra ID instance for an organization.

Entra ID is Microsoft's cloud-based identity and access management service. 

Azure consists of:
- Microsoft Entra ID responsible identity...
- Azure Resource Manager (ARM) responsible for provisioning and managing cloud resources. Some examples include:
	- Azure Key Vault a secure Azure service for storing and controlling access to sensitive values.

## Entra ID Components

Entra ID is composed of:

| Type | Description |
| --- | --- |
| Users | Representing individual identities within the tenant (employees, contractors...) |
| Groups | Simplify access management by bundling user accounts together |
| Administrative Units | Delegate management tasks by grouping users and resources. Role-based delegation so that admins can manage only the portion of the directory they are responsible for. |
| External Identities | Secure way of gratning access to vendors. |
| Roles | Define sets of permissions that can be assigned to users, groups and service principals |
| App Registrations | Allow application to integrate with Entra ID |
| App Proxy | Secure remote access to on-premises applications |
| Entra Connect | Synchronize on-premises AD with Entra ID |
| Conditional Access Policies | Define rules for when and how users can access resources. |

## Microsoft Intune

Microsoft Intune is a cloud-based endpoint management solution that helps organizations secure and manager user access to corporate resources.
- Apps represent all software that can be deployed in Intune. Admins can push apps directly to devices or make them available in a company app store. 
- Identities are the user and device accounts that define who or what can access organizational resources. Intune integrates with Entra. 
- Devices are the endpoints used by employees or administrators to access organizational resources. Devices must meet certain conditions.

## Office 365

Office 365 is a suite of cloud-based productivity and collaboration tools. 
- Some applications include Word, Excel, Powerpoint, Outlook, etc.
- Cloud services offered by 365:
	- Exchange Online: enterprise email and calendaring system
	- Sharepoint Online: Document management platform
	- Power BI: business analytics service that connects data sources
	- OneDrive: secure cloud storage
	- Microsoft Teams: chat, video meetings, file collab...

## Microsoft Graph

Microsoft Graph is a set of APIs that developers, administrators or attackers can query, interact with and manipulate across 365, Entra, or Windows services. Offered at a single endpoint "https://graph.microsoft.com".
- graph permissions controlled through a granular permissions model

2 main access models
- Delegated access where the app acts on behalf of a user. permissions constrained to what the user has. 
- app-only access the app operates independently of any user, it can access resources across the tenant if granted. Can lead to tenant-wide compromise

## Authentication and Authorization

Authentication for Microsoft is provided via the OpenID Connect Protocol.
- Built on top of OAuth, OIDC provide an ID token containing user identity claims. 

Authorization is provided via the OAuth 2.0 protocol.
- OAuth issues a bearer token that represents the user or app's identity and permissions.

Sign in using credentials, a service principal, a device code, or a managed identity.

```powershell
$Password = ConvertTo-SecureString '<ClientSecret>' -AsPlainText -Force

# for service principal
$Cred = New-Object System.Management.Automation.PSCredential('<ClientID>', $Password)

# Login using credentials
Connect-AzAccount -Tenant <TenantId> -Credential $credentials

# Login using service principal (app ID used for headless login)
Connect-AzAccount -Tenant <TenantId> -Credential $credentials

# Device code auth (MFA)
Connect-AzAccount -UseDeviceAuthentication

# PAsswordless authentication for managed identity azure resources like VMs, functions, etc.
Connect-AzAccount -Identity

# Access token only
Connect-AzAccount -AccessToken <accessToken> -AccountId <accountId> -TenantId <tenantId> -SubscriptionId <subscriptionId>

# access token and access to graph API
Connect-AzAccount -AccessToken <accessToken> -GraphAccessToken <graphAccessToken> -AccountId <accountId> -TenantId <tenantId>

# Access token with access to key vault
Connect-AzAccount -AccessToken <accessToken> -KeyVaultAccessToken <keyVaultAccessToken> -AccountId <accountId> -TenantId <tenantId>


```

```shell
# interactive login
az login

# login with credentials
az login -u <username> -p <password>

# device code login
az login --use-device-code

# service principal with client secret
az login --service-principal --username <appId> --password <clientSecret> --tenant <tenantId>

# managed identity
az login --identity
```

## Sign-in Flows

Several sign-in flows exist, some to note are
- On-Behalf-Of (OBO) is used when one service needs to call another downstreasm API while preserving user's identity. The service exchanges the user token for a new one scoped to the downstream API. 
- Implicit Grant, designed for Single Page Application (SPA) that cannot securely store a client secret. Instead of using an authorization code, it skips directly to the getting the access token. 
- Resource Owner Password Credentials allows applications to directly collect a user's username and password and exchange them for tokens. Flow bypasses modern security protections like MFA.

## Service Principals and Managed Identities

Service principals are non-interactive accounts used by applications or automation. 

A managed identity is an Azure-managed identity that lets a resource authenticate to other Azure services without storing passwords, client secrets, or certificates in your code (via client secret).

## Tokens

ID Token issued by the authorization server to the client application to prove the identity of the signed-in user and provide basic profile information.
Access Tokens issue by the authorization server and passed to the resource servers to specify scope of what the client can do.
Refresh Token used by the client to obtain a new ID and access tokens without requiring the user to reauthenticate. Very sensitive. Example: Outlook uses a refresh token to stay signed  in and fetch new access tokens when the old ones expire

Token lifetimes
- Access token: 60-90 minutes
- SAML Token: 60 minutes
- ID Token: Valid until sessions expiry
- Refresh Token: No fixed expiry.

Azure tokens are bearer tokens formatted as JSON web tokens (JWT), among those are: management token (ARM), graph token, vault token, storage token, AAD token, etc.

Primary Refresh Tokens (PRT) are special type of tokens that enables SSO. 
 - Issued only on registered or joined devices (e.g Azure AD-joined), allow seam access multiple application without re-authenticating
 - They contains several extra claims: Device ID (device that PRT was issued to), Session Key (proves the possession of the PRT when requesting tokens)
 - They are valid for 14 days. 

We may need to request an access token for specific resource types or resource URLs. 

```shell
# Request ARM Access token
az account get-access-token --resource-type arm

# Request Graph Access Token
az account get-access-token --resource-type ms-graph

## Powershell 
# arm token
$AccessToken = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR((Get-AzAccessToken -ResourceTypeName Arm -AsSecureString).Token))

$AccessToken

# RequesGraph access token
$GraphAccessToken = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR((Get-AzAccessToken -ResourceTypeName MSGraph -AsSecureString).Token))

$GraphAccessToken

# Access token based login, note: for access to other resources (key vaults, etc.) you need pass additional tokens withthe arm access token
$AccessToken = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR((Get-AzAccessToken -ResourceTypeName Arm -AsSecureString).Token))
Connect-AzAccount -AccessToken $AccessToken -AccountId <Anything>
```

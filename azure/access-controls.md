# Access Controls

Azure uses access control mechanisms to ensure that only authorized users, groups, applications, or managed identities can interact with resources.

## Role-Based Access Control (RBAC)

RBAC determines who has access to resources.
- Security principal is the identity requesting access that can be a user, group, service principal (identity for applications/services) or a managed identity (identity automatically managed by Azure for resources like VMs...)
- Role definition is a collection of permissions that specifies allowed or denied actions.
	- role defines the set of permissions
	- role assignment associates a role definition with a security principle at a scope
	- role definition acts as as the blueprint specifying what actions are permitted
	- some built-in role types include owner (full access), contributor (cannot grant access), reader, service-specific roles
- Scope is the boundary of access

Enumerate custom roles and service principal assignments in the Azure environment.

```powershell
# Enumerate all the custom roles in the Azure environment
Get-AzRoleDefinition -Custom

# Get the Object ID of the service principal
Get-AzADServicePrincipal -ApplicationId <Application ID>

# enumerate the permissions of the Service Principal
Get-AzRoleAssignment -ObjectId <Service Principal Object ID>
```

## Attribute-Based Access Control (ABAC)

ABAC builds on RBAC by adding conditions and attributes for fine-grained control.

## Subscription vs Entra ID Roles

Azure subscription roles control access to azure resources within a subscription. Entra ID roles control access to identity-related tasks, these are not RBAC roles and manage directory-wide operations.

Privileged Identity Management (PIM) provides just-in-time (JIT) access for privileged roles. These are time-bound access with automatic expiration roles.

Enumerate the custom Entra ID roles and the current identity's role assignments via MS Graph.

```powershell
# Enumerate the custom Entra ID roles via the MS Graph
$GraphAccessToken = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR((Get-AzAccessToken -ResourceTypeName MSGraph -AsSecureString).Token))

$Params = @{
    "URI"     = "https://graph.microsoft.com/v1.0/roleManagement/directory/roleDefinitions?`$filter=isBuiltIn eq false"
    "Method"  = "GET"
    "Headers" = @{
        "Authorization" = "Bearer $GraphAccessToken"
        "Content-Type"  = "application/json"
        }
    }

$EntraIDCustomRoles = Invoke-RestMethod @Params -UseBasicParsing
$EntraIDCustomRoles.value 

# Enumerate the permission of the current identity in Entra
## 1. get the user object ID
Get-AzADUser -UserPrincipalName <UPN>

## 2. leverage the MS Graph to enumerate the role assigned to the current identity
$GraphAccessToken = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR((Get-AzAccessToken -ResourceTypeName MSGraph -AsSecureString).Token))

$Params = @{
    "URI"     = "https://graph.microsoft.com/v1.0/roleManagement/directory/roleAssignments?`$filter=principalId eq '<User Object ID>'"
    "Method"  = "GET"
    "Headers" = @{
        "Authorization" = "Bearer $GraphAccessToken"
        "Content-Type"  = "application/json"
        }
    }

$EntraIDCustomRoles = Invoke-RestMethod @Params -UseBasicParsing
$EntraIDCustomRoles  
```

## Key Vault Access Policies

Can be configured into 2 ways:
- Azure RBAC where roles are assigned directly to users, some common roles are:
	- Key Vault Administrator: Full control of the Key Vault
	- Key Vault Contributor: Can manage vault settings but cannot access stored secrets
	- Key Vault Reader: can view vault configuration but not access secrets.
- Vault Access Policy is specific key vault service that controls the data plane operations policies and defines which identities can perform operations (secrets: get, list, set, delete; keys: sign, verify, etc.)

## Management vs Data Plane

Management Plane is used for administrative operations, such as creating, configuring, or deleting resources. Includes updating access policies and changing role assignment and retrieving resource metadata. 
Data Plane is used for operations that directly interact with the data stored inside a resource.

Key Vault and Storage Accounts are examples where both planes exist and require separate set of permissions.

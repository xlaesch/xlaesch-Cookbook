# Azure Enumeration

## Unauthenticated

Microsoft endpoints reveal whether a user accounts exists. For instance, during a login attempt the API response may have a flag `IfExistResult`.

Many Azure resources are discoverable via predictable domain patterns. Tools like Microburst can automate subdomain brute-forcing.

Public-facing Azures services may allow enumeration or direct exploitation. As well as OSINT can collect leaked credentials. 

## Authenticated

Given credentials or tokens, attacker can perform deep enumeration. Some tools include:
- RoadRecon gathers information about users, apps, and roles. 
- AzureHound is the extension of Bloodhound for Azure, mapping attack paths. 
- Microburst supports unauthenticated and authnenticated recon. 
- Stormspotter gives passive/active cloud reconnaissance. 
- Monkey 365 is a Microsoft 365 enumerator. 

We might encounter custom APIs or non-standard object types that might require some form of custom tooling. It's important to note that each API gives different uses
- Graph is the primary API for identity data.
- Azure Resource Manager is for resource data
- Service-specific endpoints require tokens issue for their resources.

We often need specific authentication options for tooling, therefore, we need to request the right access token. OAuth v2 (scope-based) requests return an Access Token for Graph, while OAuth v1 (Resource) requests return a token for ARM.

```shell
# OAuth v2 (scope-based) access token request
# returns an Access Token for Graph
POST https://login.microsoftonline.com/<TENANT>/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded
client_id=<CLIENT_ID>&
client_secret=<CLIENT_SECRET>&
grant_type=client_credentials&
scope=https://graph.microsoft.com/.default

# OAuth v1 (Resource)
# returns a token for ARM
POST https://login.microsoftonline.com/<TENANT>/oauth2/token
Content-Type: application/x-www-form-urlencoded
grant_type=client_credentials&
client_id=<CLIENT_ID>&
client_secret=<CLIENT_SECRET>&
resource=https://management.azure.com/

# graph token
az login

az account get-access-token --resource https://graph.microsoft.com

# ARM token
az account get-access-token --resource https://management.azure.com

# call Graph REST Api with token
Invoke-RestMethod -Uri https://graph.microsoft.com/v1.0/users -Headers @{ Authorization = Bearer $token }
```

## Enumerating Resources

A typical approach to enumeration would be
1. Start with identity: enumerate apps, service principals, etc.
2. Map Role Assignments: Use ARM to list roleAssignments scoped at subscription/resource
3. Enumerate Resources: List resource types and probe public endpoints discovered from asset enumeration
4. Correlate: Identity --> Role --> resource to find attack paths
5. Pivot to data plane: Request data-plane tokens to get to key vault / storage

```shell
# enumerate Azure resources 
az resource list -o table
Get-AzResource
```

## Security Controls

Conditional Access Policies (CAP) is a control engine that evaluates sign-in and session context (location, device, ...) and enforces actions such as MFA, block, or require device. It has risk-based policies, MFA enforcement, device compliance checks, location-based control, conditional access app control, etc.

Azure policy is a governance engine that enforces rules and effects on Azure resources ensuring resources comply with standards. 

TLDR; CAP is the access-time decision and Azure Policy is the resource configuration guardrail. 

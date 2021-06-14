# Role Based Access Control (RBAC) in the Vendor Portal
RBAC is a way of defining policies with a set of permissions and then applying that policy to select team members. This permits following the principles of least privilege.

Some examples of least privilege use cases are the following:
* A developer who has the ability to create and promote releases, but cannot create customers and licenses
* An auditor who only has read access to view everything
* An admin user with full read/write access - even managing other team members

New Vendor Portal accounts come with two policies:
* **Read Only** - read permission to all resources on the team except for API tokens.
* **Admin** - read/write permissions to all resources on the team.

See the [documentation](https://help.replicated.com/docs/vendor-rbac/resource-names/) for a full list of resource names.

>Note: This tutorial applies to RBAC in the Vendor Portal - not to be confused with RBAC in Kubernetes.

# In this Tutorial

Learn how to create a developer policy and apply it to members on the vendor team

# Prerequisites

* Logged into a [vendor account](todo) as a member with an `admin` policy
* Additional [team members](members.md) where policies will be applied

# Instructions

## Create a Permissions Policy
Developer
A non-admin Read/Write role

```json
{
  "v1": {
    "name": "Developer",
    "resources": {
      "allowed": [
        "platform/**/*",
        "kots/**/*"
      ],
      "denied": [
        "**/*"
      ]
    }
  }
}
```

## Edit a Permissions Policy

## Apply a policy to a team member

See the Change a member's permissions section in the [Members tutorial](members.md)

# See Also
<!-- OPTIONAL. A list of related tutorials -->

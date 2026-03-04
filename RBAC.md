

📌 MemoryPoint: Kubernetes RBAC, Service Accounts, OIDC Authentication & EKS IAM Integration

   


---

📑 Table of Contents

Trigger Recall (What I Learned)

Core Concept Explained

Key Components / Framework

Concept Dependency Graph

Practical Examples

Common Mistakes / My Confusions

Implementation Pattern

Commands to Remember

One-Sentence Compression

Personal Memory Trigger

Revision Checkpoints



---

Trigger Recall (What I Learned)

When a user wants to access Kubernetes, the process follows this order:

1. Authentication – Who are you?

IAM (EKS)

OIDC (Self-managed clusters)



2. Authorization – What can you do?

Kubernetes RBAC (Role, ClusterRole)



3. Binding – Who gets those permissions?

RoleBinding

ClusterRoleBinding



4. Access Method

kubeconfig file

kubectl from local machine



5. Tokens / Credentials

IAM temporary token (EKS)

OIDC token (self-managed)

Service Account token (automation)




Your real-world example:

> A senior DevOps engineer grants limited access to a new engineer, first in Dev namespace, then gradually increases permissions if the engineer proves reliable.




---

Core Concept Explained

Kubernetes access control is divided into two major systems:

1️⃣ Authentication (Identity Verification)

Kubernetes does not create users internally.

Instead it trusts external identity providers:

Examples:

Authentication Method	Where Used

IAM	AWS EKS
OIDC	Self-managed clusters
Service Accounts	Pods / automation
Certificates	Advanced clusters


If authentication succeeds → Kubernetes recognizes the username or group.

But the user still has zero permissions.


---

2️⃣ Authorization (RBAC)

RBAC defines what actions a user can perform.

RBAC uses:

Component	Purpose

Role	Namespace-level permissions
ClusterRole	Cluster-wide permissions
RoleBinding	Assign role inside namespace
ClusterRoleBinding	Assign role cluster-wide


Example permission rule:

User → allowed to create Pods in dev namespace


---

Key Components / Framework

RBAC Architecture

flowchart LR

User --> Authentication
Authentication --> KubernetesAPI
KubernetesAPI --> Authorization
Authorization --> Role
Authorization --> ClusterRole
Role --> RoleBinding
ClusterRole --> ClusterRoleBinding
RoleBinding --> PermissionGranted
ClusterRoleBinding --> PermissionGranted

Explanation

User first authenticates.

Kubernetes API receives the request.

RBAC checks the roles and bindings.

If allowed → action succeeds.



---

Concept Dependency Graph

This graph shows how all the concepts discussed connect together.

graph TD

Authentication --> IAM
Authentication --> OIDC
Authentication --> ServiceAccount

IAM --> EKS
OIDC --> SelfManagedCluster

Authorization --> RBAC

RBAC --> Role
RBAC --> ClusterRole
RBAC --> RoleBinding
RBAC --> ClusterRoleBinding

RoleBinding --> NamespaceAccess
ClusterRoleBinding --> ClusterAccess

ServiceAccount --> AutomationAccess

IAM --> awsAuthConfigMap
awsAuthConfigMap --> RBAC

Explanation

Authentication methods feed identities into Kubernetes.

RBAC determines permissions.

In EKS, aws-auth ConfigMap connects IAM with RBAC.



---

Practical Examples

Example 1 — Namespace Developer Access

Scenario from your discussion.

New engineer joins team.

Goal:

Give Dev environment access only.

Step 1 — Create Role

apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: dev-pod-manager
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get","list","create","delete"]


---

Step 2 — Bind Role to User

apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: dev-access
  namespace: dev
subjects:
- kind: User
  name: new-engineer
roleRef:
  kind: Role
  name: dev-pod-manager
  apiGroup: rbac.authorization.k8s.io

Now the user can manage pods only in dev namespace.


---

Example 2 — Using Service Account for Automation

Your instructor example.

Create service account:

kubectl create serviceaccount deployment-bot

Bind permissions.

Extract token.

Use token inside kubeconfig.

Automation system can now deploy resources.


---

Example 3 — EKS IAM Access Flow

sequenceDiagram

participant Engineer
participant AWS_IAM
participant EKS
participant aws_auth_ConfigMap
participant Kubernetes_RBAC

Engineer->>AWS_IAM: Login (MFA)
AWS_IAM->>Engineer: Temporary credentials
Engineer->>EKS: kubectl request
EKS->>aws_auth_ConfigMap: Map IAM identity
aws_auth_ConfigMap->>Kubernetes_RBAC: Assign group
Kubernetes_RBAC->>EKS: Check roles
EKS->>Engineer: Allow or Deny request

Explanation

1. Engineer logs in with IAM.


2. IAM identity mapped inside aws-auth ConfigMap.


3. RBAC roles determine permissions.




---

Common Mistakes / My Confusions

Confusion 1

"Does Kubernetes create users?"

❌ No

Kubernetes only recognizes external identities.


---

Confusion 2

"Do users get permissions automatically?"

❌ No.

Default permission = zero access.


---

Confusion 3

"Service Account = Human User?"

❌ No.

Service accounts are mainly for:

Pods

automation

CI/CD



---

Confusion 4

"ClusterRole always means cluster-wide access"

Not always.

You can bind a ClusterRole inside a namespace using RoleBinding.


---

Implementation Pattern

Real DevOps workflow you described:

Step 1 — Create IAM user

Engineer receives:

AWS account

MFA enabled

IAM permissions



---

Step 2 — Map IAM user to Kubernetes

Edit:

aws-auth ConfigMap

Example:

mapUsers:
- userarn: arn:aws:iam::123:user/dev-engineer
  username: dev-engineer
  groups:
    - dev-group


---

Step 3 — Create RBAC roles

dev namespace → limited permissions


---

Step 4 — Developer accesses cluster

Developer runs:

kubectl get pods

Access works only for dev namespace.


---

Step 5 — Promote Access

If engineer proves reliable:

Grant access to:

staging

production



---

Commands to Remember

Check Roles

kubectl get roles -n dev

Check RoleBindings

kubectl get rolebindings -n dev

Check ClusterRoles

kubectl get clusterroles

Check ClusterRoleBindings

kubectl get clusterrolebindings

Extract Service Account Token

kubectl describe secret <secret-name>

Update EKS kubeconfig

aws eks update-kubeconfig --region region --name cluster-name


---

One-Sentence Compression

Kubernetes security works by authenticating users externally (IAM/OIDC) and authorizing them internally using RBAC roles and bindings.


---

Personal Memory Trigger

Remember the DevOps onboarding scenario:

> New engineer joins → IAM account → aws-auth mapping → RBAC role → dev namespace access → later promoted to production.



This mirrors real-world DevOps security practices.


---

Revision Checkpoints

Review schedule for long-term retention:

Day	Goal

Day 1	Understand RBAC components
Day 3	Practice creating roles
Day 7	Simulate new engineer onboarding
Day 30	Implement RBAC in real cluster



---

✅ If you want, I can also create a second MemoryPoint specifically for:

“Kubernetes Rollback Strategy (Deployments, ReplicaSets, kubectl rollout)”

That topic was mentioned at the beginning but not captured fully in the transcript, and it's very important for DevOps interviews.
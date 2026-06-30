# Azure From Scratch

## 1. Definition

Microsoft Azure is a cloud computing platform that provides services for compute, storage, databases, networking, analytics, security, artificial intelligence, and DevOps.

Instead of buying and maintaining physical servers, companies use Azure to create and manage cloud resources on demand.

In simple words:

```text
Azure = Microsoft's cloud platform for building, storing, processing, securing, and monitoring applications and data.
```

---

## 2. Why Azure Matters

Azure matters because companies need scalable, secure, and cost-controlled infrastructure without managing physical servers manually.

Azure helps companies:

- Reduce physical infrastructure cost
- Scale resources up or down based on demand
- Store large amounts of structured and unstructured data
- Build data pipelines
- Run applications and APIs
- Secure users, applications, and data
- Monitor systems and detect failures
- Support reporting, analytics, and machine learning

For data engineering, Azure is important because it provides services such as:

- Azure Data Factory
- Azure Data Lake Storage Gen2
- Azure Databricks
- Azure Synapse Analytics
- Azure SQL Database
- Power BI
- Azure Key Vault
- Azure Monitor

---

## 3. Azure Resource Hierarchy

Azure resources are organized in a hierarchy.

```text
Microsoft Entra ID
        ↓
Tenant
        ↓
Management Group
        ↓
Subscription
        ↓
Resource Group
        ↓
Resource
```

This hierarchy is important because Azure is not just a group of random services. Every Azure resource belongs to a specific scope for identity, billing, access control, and management.

---

## 4. What Each Layer Means

### Microsoft Entra ID

Microsoft Entra ID is Azure's identity and access management system.

It manages:

- Users
- Groups
- Applications
- Authentication
- Authorization
- Single sign-on
- Multi-factor authentication
- Role-based access control

Example:

```text
A company employee logs into Azure using their company account managed by Microsoft Entra ID.
```

---

### Tenant

A tenant represents an organization in Microsoft Entra ID.

Example:

```text
company.com
```

A tenant is the identity boundary for the organization. Users, groups, and application identities are managed inside the tenant.

---

### Management Group

A management group is used to organize multiple subscriptions.

Example:

```text
Company Management Group
├── Production Subscription
├── Development Subscription
└── Testing Subscription
```

Large companies use management groups to apply governance, policies, and access control across many subscriptions.

---

### Subscription

A subscription is a billing and access boundary in Azure.

Azure resources are created inside a subscription.

Example:

```text
Azure Subscription = Billing account + resource boundary
```

A company may have separate subscriptions for:

- Development
- Testing
- Production
- Security
- Data platforms

---

### Resource Group

A resource group is a logical container for related Azure resources.

Example:

```text
rg-healthcare-pipeline-dev
```

A resource group may contain:

- Storage Account
- Data Factory
- Key Vault
- Databricks Workspace
- Azure SQL Database

Resource groups make it easier to manage, monitor, secure, and delete related resources together.

---

### Resource

A resource is an actual Azure service.

Examples:

- Storage Account
- Virtual Machine
- Azure SQL Database
- Azure Data Factory
- Key Vault
- Databricks Workspace
- Virtual Network
- Azure Function

---

## 5. Core Azure Service Categories

Azure has many services, the main categories are.

```text
Azure
│
├── Compute
│   ├── Virtual Machines
│   ├── App Service
│   ├── Azure Functions
│   └── Azure Kubernetes Service
│
├── Storage
│   ├── Blob Storage
│   ├── Azure Data Lake Storage Gen2
│   ├── Azure Files
│   └── Managed Disks
│
├── Databases
│   ├── Azure SQL Database
│   ├── Azure Database for PostgreSQL
│   ├── Azure Database for MySQL
│   └── Cosmos DB
│
├── Networking
│   ├── Virtual Network
│   ├── Subnet
│   ├── Network Security Group
│   ├── Private Endpoint
│   └── Load Balancer
│
├── Security
│   ├── Microsoft Entra ID
│   ├── Azure RBAC
│   ├── Managed Identity
│   ├── Key Vault
│   └── Defender for Cloud
│
├── Data Engineering
│   ├── Azure Data Factory
│   ├── Azure Data Lake Storage Gen2
│   ├── Azure Databricks
│   ├── Azure Synapse Analytics
│   └── Microsoft Fabric
│
└── Monitoring
    ├── Azure Monitor
    ├── Log Analytics
    ├── Application Insights
    └── Alerts
```

---

## 6. Compute Services

Compute means the place where code or applications run.

Common Azure compute services:

| Service | Meaning | Use Case |
|---|---|---|
| Virtual Machine | Full server in the cloud | When full OS control is needed |
| App Service | Managed hosting for web apps and APIs | Web applications |
| Azure Functions | Serverless event-driven code | Run code when a file arrives or API is called |
| AKS | Managed Kubernetes | Containerized applications |
| Container Apps | Serverless containers | Running containers without managing Kubernetes |

Simple decision:

```text
Need full server control?       → Virtual Machine
Need web app or API hosting?    → App Service
Need event-driven code?         → Azure Functions
Need Kubernetes?                → AKS
Need simple container hosting?  → Container Apps
```

---

## 7. Storage Services

Storage means where files, objects, disks, and data are stored.

Common Azure storage services:

| Service | Meaning | Use Case |
|---|---|---|
| Blob Storage | Object storage for files | CSV, JSON, images, logs |
| ADLS Gen2 | Data lake storage | Big data analytics |
| Azure Files | Shared file system | File shares |
| Managed Disks | VM disk storage | Virtual Machine disks |
| Queue Storage | Simple message queue | Decoupling applications |

For data engineering, the most important storage service is usually:

```text
Azure Data Lake Storage Gen2
```

It is commonly used to organize data into:

```text
Bronze  → Raw data
Silver  → Cleaned data
Gold    → Business-ready data
```

---

## 8. Database Services

Database services store structured, semi-structured, or NoSQL data.

Common Azure database services:

| Service | Type | Use Case |
|---|---|---|
| Azure SQL Database | Relational | SQL Server-style cloud database |
| Azure SQL Managed Instance | Relational | More SQL Server compatibility |
| Azure Database for PostgreSQL | Relational | PostgreSQL applications |
| Azure Database for MySQL | Relational | MySQL applications |
| Cosmos DB | NoSQL | Globally distributed applications |
| Azure Cache for Redis | Cache | Fast temporary data access |

Simple decision:

```text
Need SQL Server database?   → Azure SQL Database
Need PostgreSQL?            → Azure Database for PostgreSQL
Need globally distributed NoSQL? → Cosmos DB
Need cache?                 → Azure Cache for Redis
```

---

## 9. Networking Services

Networking controls how Azure resources communicate with each other, the internet, and private systems.

Common Azure networking services:

| Service | Meaning |
|---|---|
| Virtual Network | Private network in Azure |
| Subnet | Smaller network inside a VNet |
| Network Security Group | Firewall rules for traffic |
| Public IP | Internet-facing IP address |
| Private IP | Internal network IP address |
| Load Balancer | Distributes traffic |
| Application Gateway | HTTP/HTTPS load balancer |
| Private Endpoint | Private access to Azure services |
| VPN Gateway | Connects on-premises network to Azure |
| ExpressRoute | Private dedicated network connection |

Basic Azure network picture:

```text
Internet
   ↓
Public IP / Load Balancer
   ↓
Virtual Network
   ├── Public Subnet
   │      └── Web/API Layer
   │
   └── Private Subnet
          ├── Database
          ├── Private Endpoint
          └── Data Processing Services
```



---

## 10. Security and Identity

Azure security answers three major questions:

```text
Who are you?      → Authentication
What can you do?  → Authorization
Where can you do it? → Scope
```

Important Azure security services:

| Service | Purpose |
|---|---|
| Microsoft Entra ID | Identity and access management |
| Azure RBAC | Permission control |
| Managed Identity | Lets Azure services authenticate without passwords |
| Key Vault | Stores secrets, keys, and certificates |
| Azure Policy | Enforces organizational rules |
| Defender for Cloud | Security posture and threat protection |
| Private Endpoint | Keeps service traffic private |

Example:

```text
Azure Data Factory uses Managed Identity to access ADLS Gen2.
The access is controlled using Azure RBAC.
Secrets are stored in Key Vault instead of hardcoding passwords.
```

---

## 11. Azure Data Engineering Services

For data engineering, these services are especially important:

| Service | Purpose |
|---|---|
| Azure Data Factory | Data ingestion and pipeline orchestration |
| ADLS Gen2 | Data lake storage |
| Azure Databricks | Spark-based transformation and lakehouse processing |
| Azure Synapse Analytics | Data warehouse and analytics platform |
| Azure SQL Database | Relational serving layer |
| Microsoft Fabric | End-to-end analytics platform |
| Power BI | Dashboard and reporting |
| Key Vault | Secret management |
| Azure Monitor | Pipeline and service monitoring |

---

## 12. Azure Data Engineering Flow

A common Azure data engineering pipeline looks like this:

```text
Source System
        ↓
Azure Data Factory
        ↓
ADLS Gen2 Bronze Layer
        ↓
Azure Databricks / Synapse Spark
        ↓
ADLS Gen2 Silver Layer
        ↓
ADLS Gen2 Gold Layer
        ↓
Power BI / Reporting
```

Explanation:

1. Source systems provide raw data.
2. Azure Data Factory ingests or copies data.
3. ADLS Gen2 stores raw data in the Bronze layer.
4. Databricks or Synapse transforms the data.
5. Cleaned data is stored in the Silver layer.
6. Business-ready data is stored in the Gold layer.
7. Power BI or SQL tools consume the curated data.

---

## 13. Real-World Use Case

A healthcare company may receive patient, claims, provider, and insurance data from multiple systems.

Azure can be used like this:

1. Azure Data Factory ingests files from external systems.
2. ADLS Gen2 stores raw files in the Bronze layer.
3. Azure Databricks cleans and transforms the data.
4. Cleaned data is stored in the Silver layer.
5. Business-ready summary tables are stored in the Gold layer.
6. Power BI connects to the Gold layer for reporting.
7. Key Vault stores passwords, keys, and secrets.
8. Azure Monitor tracks pipeline failures and performance.

Architecture:

```text
Hospital Systems / APIs / CSV Files
        ↓
Azure Data Factory
        ↓
ADLS Gen2 Bronze
        ↓
Azure Databricks
        ↓
ADLS Gen2 Silver
        ↓
ADLS Gen2 Gold
        ↓
Power BI Dashboard
```

---

## 14. Azure vs AWS Basic Mapping

Since many cloud concepts are similar, it helps to compare Azure with AWS.

| AWS | Azure |
|---|---|
| IAM | Microsoft Entra ID + Azure RBAC |
| S3 | Blob Storage / ADLS Gen2 |
| EC2 | Azure Virtual Machines |
| Lambda | Azure Functions |
| Glue | Azure Data Factory + Databricks/Synapse |
| Athena | Synapse Serverless SQL / Databricks SQL |
| Redshift | Synapse Dedicated SQL Pool / Fabric Warehouse |
| CloudWatch | Azure Monitor |
| Secrets Manager / KMS | Azure Key Vault |
| VPC | Azure Virtual Network |
| Security Groups | Network Security Groups |
| EKS | AKS |
| CloudFormation | ARM Templates / Bicep |

---

## 15. Interview-Ready Explanation

Microsoft Azure is Microsoft's cloud platform that provides services for compute, storage, databases, networking, analytics, security, AI, and DevOps. Azure resources are organized through Microsoft Entra ID, tenants, subscriptions, resource groups, and individual resources. For data engineering, a common Azure architecture uses Azure Data Factory for ingestion, ADLS Gen2 for data lake storage, Databricks or Synapse for transformation, and Power BI for reporting. Security is handled using Microsoft Entra ID, RBAC, Managed Identity, and Key Vault, while monitoring is handled using Azure Monitor.

---



## 16. Simple Memory Tricks

```text
Tenant = Organization identity
Subscription = Billing and access boundary
Resource Group = Project folder
Resource = Actual Azure service
```

```text
Compute = Where code runs
Storage = Where data/files live
Database = Where structured data lives
Networking = How resources communicate
Security = Who can access what
Monitoring = How failures are tracked
```

```text
Bronze = Raw data
Silver = Cleaned data
Gold = Business-ready data
```

---

## 17. Final Summary

Azure is not one single tool. It is a complete cloud ecosystem.

For data engineering, the most important Azure path is:

```text
Azure Fundamentals
        ↓
Resource Hierarchy
        ↓
Storage Account / ADLS Gen2
        ↓
Azure Data Factory
        ↓
Databricks / Synapse
        ↓
Power BI
        ↓
Key Vault / RBAC / Monitor
```

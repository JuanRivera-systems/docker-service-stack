Storage Architecture
Overview
The storage infrastructure is engineered to balance performance, redundancy, and scalability across virtualized workloads, application data, media libraries, and system services. By segregating storage resources based on workload characteristics, the environment ensures reliable operation while maximizing both capacity and I/O throughput.

The architecture leverages a hybrid approach, combining enterprise-grade SAS drives for high-density storage with NVMe/SSD pools for latency-sensitive operations.

Design Objectives
Fault Tolerance: Implement robust redundancy to protect against drive failures.
Capacity Scaling: Support large-scale media and archival data retention.
Performance Optimization: Deliver low-latency I/O for virtual machines and databases.
Expandability: Enable seamless capacity growth without service interruption.
Operational Simplicity: Streamline backup, recovery, and maintenance workflows.
Minimized Disruption: Ensure maintenance activities do not impact service availability.
Storage Tiers
Storage Tier	Technology	Primary Purpose
High-Capacity	RAID6 SAS Array	Media libraries, long-term archives, backup repositories
High-Performance	RAID1 SSD Pool	Virtual machines, databases, container root filesystems
System Boot	NVMe SSD	Proxmox host OS, hypervisor services
Physical Platform
Enterprise Storage Array (RAID6)
The primary data plane consists of a 20-drive enterprise SAS array configured in RAID6. This tier handles bulk storage requirements, offering a balance of massive capacity and dual-drive fault tolerance.

Workloads: Media streaming, shared file storage, backup repositories, cold data retention.
Key Benefits: High usable capacity, resilience against simultaneous drive failures, cost-effective scaling.
SSD Performance Pool (RAID1)
A dedicated four-drive RAID1 SSD pool serves as the high-speed tier for performance-critical workloads. This layer minimizes I/O bottlenecks for active applications.

Workloads: Virtual Machine disks, database files, container layers, authentication services.
Key Benefits: Sub-millisecond latency, rapid VM provisioning, improved application responsiveness.
Boot Storage (NVMe)
The Proxmox hypervisor operates on a dedicated NVMe SSD, ensuring fast boot times and isolating host operations from data plane I/O contention.

Key Benefits: Rapid system recovery, isolated host logs, simplified maintenance without affecting user data.
Architecture Flow

[ Proxmox VE Hypervisor ]
     |
+--------------------------------------+
|  SSD Pool  |  RAID6 Array  |  NVMe  |
+--------------------------------------+
     |              |           |
     v              v           v
[ VMs/DBs ]    [ Media/Backups ] [ Host OS ]
RAID Configuration Details
RAID6 SAS Array
The primary storage tier utilizes RAID6 to provide dual-drive fault tolerance. This configuration is ideal for large media libraries where capacity is paramount, yet data safety cannot be compromised.

Advantages:
Survives two simultaneous drive failures.
Maximizes usable capacity for large datasets.
Robust resiliency during long rebuild operations.
Operational Insight: Experience with RAID expansion and migration projects highlighted the critical need for proactive drive health monitoring and careful planning during capacity upgrades to avoid performance degradation.
RAID1 SSD Pool
Critical workloads reside on a RAID1 mirrored pool of four SSDs. This setup prioritizes read/write speed and availability over raw capacity.

Advantages:
Instant failover if a drive fails.
Significant reduction in VM boot and application load times.
Consistent low-latency performance for database operations.
Data Allocation Strategy
Storage resources are allocated dynamically based on workload I/O profiles:

Workload Type	Examples	Storage Tier
High Capacity	Media Libraries, Archives, Backups	RAID6 SAS Array
High Performance	VMs, Auth Services, Containers	SSD RAID1 Pool
System	Hypervisor, Logs, Configs	NVMe Boot
Data Protection Strategy
A multi-layered approach ensures data integrity and availability:

RAID Redundancy: Hardware-level protection against drive failure.
Health Monitoring: Proactive SMART data analysis and alerting.
Segmentation: Logical separation of data types to prevent cascading failures.
Service Isolation: Dedicated storage paths for critical services.
Backup Planning: Regular snapshots and offsite replication strategies.
Documentation: Detailed maps of storage layouts and recovery procedures.
Scalability & Growth
The platform was designed with future expansion as a core requirement:

Capacity: Room for additional drive bays and larger capacity drives.
Performance: Ability to add more SSDs to the high-performance tier.
Workloads: Support for growing VM counts and larger media libraries.
Retention: Extended backup retention windows without impacting primary storage.
Challenges & Lessons Learned
Managing enterprise-grade storage provided deep insights into RAID lifecycle management, drive replacement procedures, and fault recovery. Key takeaways include:

Planning is Critical: Storage migrations and expansions require meticulous planning to avoid downtime.
Monitoring is Non-Negotiable: Early detection of failing drives is essential for maintaining redundancy.
Documentation Saves Time: Clear records of storage topology accelerate troubleshooting and recovery.
Balance is Key: The optimal design balances performance, reliability, and maintainability rather than optimizing for a single metric.
Future Improvements
Automated Monitoring: Implement advanced predictive analytics for drive health.
Backup Validation: Automated periodic testing of restore procedures.
Capacity Forecasting: Data-driven projections for future storage needs.
Performance Reporting: Granular I/O metrics for capacity planning.
Disaster Recovery: Regular DR drills to validate recovery time objectives (RTO).
Enhanced Documentation: Interactive diagrams and runbooks for storage operations.

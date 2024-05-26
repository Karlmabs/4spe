# Project Infrastructure Documentation

## Overview
This project aims to set up a virtualized infrastructure using XCP-ng and Xen Orchestra on VMware Workstation Pro 17. The infrastructure consists of two datacenters with replication and failover capabilities to ensure high availability and resilience. Additionally, VM high availability and load balancing are configured to enhance the infrastructure's reliability and performance.

## Infrastructure Details

### Datacenter 1
Datacenter 1 will have the following components:
- **Hosts**: 3
- **Common Storage**: TrueNAS with NFS sharing for all 3 hosts
- **Backup Storage**: TrueNAS with NFS to save data from the common storage outside of the two datacenters
- **VM**: Ubuntu Server installed on one of the VMs
- **VM High Availability**: Configured in Xen Orchestra to restart VMs on an available host if a host crashes
- **Load Balancing**: Configured in Xen Orchestra with a performance plan and a CPU threshold of 70%

### Datacenter 2
Datacenter 2 will have the following components:
- **Hosts**: 2
- **Common Storage**: TrueNAS with NFS sharing for both hosts
- **Replication**: Data from Datacenter 1’s storage will be replicated to Datacenter 2’s storage

## Tools and Technologies
- **XCP-ng**: An open-source hypervisor that will be used to manage the VMs.
- **Xen Orchestra**: A web-based management interface for XCP-ng.
- **TrueNAS**: A network-attached storage (NAS) solution that will be used for NFS storage.
- **VMware Workstation Pro 17**: A virtualization platform to simulate the entire infrastructure.

## Setup Instructions

### Prerequisites
1. VMware Workstation Pro 17 installed on your machine.
2. Download the ISO files for XCP-ng and Ubuntu Server.
3. Download and install TrueNAS on VMware Workstation.

### Datacenter 1 Setup

#### Step 1: Setting up XCP-ng Hosts
1. Create three VMs in VMware Workstation for XCP-ng hosts.
2. Allocate resources (CPU, RAM, Disk) appropriately for each host.
3. Install XCP-ng on each VM using the downloaded ISO.

#### Step 2: Configuring TrueNAS
1. Create a VM for TrueNAS in VMware Workstation.
2. Allocate resources for the TrueNAS VM.
3. Install TrueNAS and configure NFS sharing.
4. Create NFS shares for use by the XCP-ng hosts.

#### Step 3: Setting up Xen Orchestra
1. Create a VM for Xen Orchestra.
2. Install Xen Orchestra using the appropriate script or installation method.
3. Connect Xen Orchestra to the XCP-ng hosts.

#### Step 4: Configuring High Availability and Load Balancing
1. In Xen Orchestra, configure VM high availability to ensure that VMs restart on an available host if the host crashes.
2. Set up load balancing in Xen Orchestra with a performance plan that has a CPU threshold of 70%.

#### Step 5: Creating and Configuring the Ubuntu Server VM
1. Using Xen Orchestra, create a new VM on one of the XCP-ng hosts.
2. Install Ubuntu Server on the VM.
3. Configure the necessary services and applications on the Ubuntu Server.

#### Step 6: Backup Storage Configuration
1. Create another VM for TrueNAS in VMware Workstation for backup storage.
2. Configure NFS sharing for backup purposes.
3. Set up a backup schedule to save data from the common storage to this backup storage.

### Datacenter 2 Setup

#### Step 1: Setting up XCP-ng Hosts
1. Create two VMs in VMware Workstation for XCP-ng hosts.
2. Allocate resources (CPU, RAM, Disk) appropriately for each host.
3. Install XCP-ng on each VM using the downloaded ISO.

#### Step 2: Configuring TrueNAS
1. Create a VM for TrueNAS in VMware Workstation.
2. Allocate resources for the TrueNAS VM.
3. Install TrueNAS and configure NFS sharing.
4. Create NFS shares for use by the XCP-ng hosts.

#### Step 3: Replication Setup
1. Configure replication from the TrueNAS storage in Datacenter 1 to the TrueNAS storage in Datacenter 2.
2. Ensure that data replication is scheduled and tested for consistency.

### Failover Procedure in Case of Incident in Datacenter 1
1. Ensure that replication from Datacenter 1 to Datacenter 2 is up-to-date.
2. In the event of an incident in Datacenter 1, verify the last successful replication.
3. Switch to Datacenter 2 by starting the necessary VMs using Xen Orchestra.
4. Validate the integrity and availability of the services in Datacenter 2.

## Additional Documentation
For detailed instructions, including screenshots and step-by-step guidance, refer to the accompanying document in the repository: XCP_Documentation.pdf. This document provides comprehensive steps to replicate the setup, ensuring clarity and ease of implementation.

## Conclusion
This documentation provides a detailed guide to setting up a resilient and high-availability infrastructure using XCP-ng, Xen Orchestra, TrueNAS, and VMware Workstation Pro 17. By following the steps outlined, you can ensure that your infrastructure is capable of handling incidents with minimal downtime, ensuring continuous availability of services. High availability and load balancing configurations further enhance the infrastructure's robustness and performance.

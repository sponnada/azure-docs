---
title: Maintenance and updates 
description: Overview of maintenance and updates for virtual machines running in Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 10/06/2021
ms.author: shants
#pmcontact:shants
---
# Maintenance for virtual machines in Azure

**Applies to:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: Flexible scale sets :heavy_check_mark: Uniform scale sets

Azure periodically updates its platform to improve the reliability, performance, and security of the host infrastructure for virtual machines. The purpose of these updates ranges from patching software components in the hosting environment to upgrading networking components or decommissioning hardware. 

Updates rarely affect the hosted VMs. When updates do have an effect, Azure chooses the least impactful method for updates:

- If the update doesn't require a reboot, the VM is paused while the host is updated, or the VM is live-migrated to an already updated host. 
- If maintenance requires a reboot, you're notified of the planned maintenance. Azure also provides a time window in which you can start the maintenance yourself, at a time that works for you. The self-maintenance window is typically 35 days unless the maintenance is urgent. Azure is investing in technologies to reduce the number of cases in which planned platform maintenance requires the VMs to be rebooted. For instructions on managing planned maintenance, see Handling planned maintenance notifications using the Azure [CLI](maintenance-notifications-cli.md), [PowerShell](maintenance-notifications-powershell.md) or [portal](maintenance-notifications-portal.md).

This page describes how Azure performs both types of maintenance. For more information about unplanned events (outages), see [Manage the availability of VMs for Windows](./availability.md) or the corresponding article for [Linux](./availability.md).

Within a VM, you can get notifications about upcoming maintenance by [using Scheduled Events for Windows](./windows/scheduled-events.md) or for [Linux](./linux/scheduled-events.md).



## Maintenance that doesn't require a reboot

Most platform updates don't affect customer VMs. When a no-impact update isn't possible, Azure chooses the update mechanism that's least impactful to customer VMs. 

Most nonzero-impact maintenance pauses the VM for less than 10 seconds. In certain cases, Azure uses memory-preserving maintenance mechanisms. These mechanisms pause the VM for typically up to 30 seconds and preserve the memory in RAM. The VM is then resumed, and its clock is automatically synchronized. 

Memory-preserving maintenance works for more than 90 percent of Azure VMs. It doesn't work for G, L, M, N, and H series. Azure increasingly uses live-migration technologies and improves memory-preserving maintenance mechanisms to reduce the pause durations.  

These maintenance operations that don't require a reboot are applied one fault domain at a time. They stop if they receive any warning health signals from platform monitoring tools. 

These types of updates can affect some applications. When the VM is live-migrated to a different host, some sensitive workloads might show a slight performance degradation in the few minutes leading up to the VM pause. To prepare for VM maintenance and reduce impact during Azure maintenance, try [using Scheduled Events for Windows](./windows/scheduled-events.md) or [Linux](./linux/scheduled-events.md) for such applications. 

For greater control on all maintenance activities including zero-impact and rebootless updates, you can use Maintenance Control feature. You must be using either [Azure Dedicated Hosts](./dedicated-hosts.md) or an [isolated VM](../security/fundamentals/isolation-choices.md). Maintenance control gives you the option to skip all platform updates and apply the updates at your choice of time within a 35-day rolling window. For more information, see [Control updates with Maintenance Control and the Azure CLI](maintenance-control.md).


### Live migration

Live migration is an operation that doesn't require a reboot and that preserves memory for the VM. It causes a pause or freeze, typically lasting no more than 5 seconds. Except for G, M, N, and H series, all infrastructure as a service (IaaS) VMs, are eligible for live migration. Eligible VMs represent more than 90 percent of the IaaS VMs that are deployed to the Azure fleet. 

> [!NOTE]
> You won't recieve a notification in the Azure portal for live migration operations that don't require a reboot. To see a list of live migrations that don't require a reboot, [query for scheduled events](./windows/scheduled-events.md#query-for-events).

The Azure platform starts live migration in the following scenarios:
- Planned maintenance
- Hardware failure
- Allocation optimizations

Some planned-maintenance scenarios use live migration, and you can use Scheduled Events to know in advance when live migration operations will start.

Live migration can also be used to move VMs when Azure Machine Learning algorithms predict an impending hardware failure or when you want to optimize VM allocations. For more information about predictive modeling that detects instances of degraded hardware, see [Improving Azure VM resiliency with predictive machine learning and live migration](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Live-migration notifications appear in the Azure portal in the Monitor and Service Health logs as well as in Scheduled Events if you use these services.



## Maintenance that requires a reboot

In the rare case where VMs need to be rebooted for planned maintenance, you'll be notified in advance. Planned maintenance has two phases: the self-service phase and a scheduled maintenance phase.

During the *self-service phase*, which typically lasts four weeks, you start the maintenance on your VMs. As part of the self-service, you can query each VM to see its status and the result of your last maintenance request.

When you start self-service maintenance, your VM is redeployed to an already updated node. Because the VM reboots, the temporary disk is lost and dynamic IP addresses associated with the virtual network interface are updated.

If an error arises during self-service maintenance, the operation stops, the VM isn't updated, and you get the option to retry the self-service maintenance. 

When the self-service phase ends, the *scheduled maintenance phase* begins. During this phase, you can still query for the maintenance phase, but you can't start the maintenance yourself.

For more information on managing maintenance that requires a reboot, see **Handling planned maintenance notifications** using the Azure [CLI](maintenance-notifications-cli.md), [PowerShell](maintenance-notifications-powershell.md) or [portal](maintenance-notifications-portal.md). 

### Availability considerations during scheduled maintenance 

If you decide to wait until the scheduled maintenance phase, there are a few things you should consider to maintain the highest availability of your VMs. 

#### Paired regions

Each Azure region is paired with another region within the same geographical vicinity. Together, they make a region pair. During the scheduled maintenance phase, Azure updates only the VMs in a single region of a region pair. For example, while updating the VM in North Central US, Azure doesn't update any VM in South Central US at the same time. However, other regions such as North Europe can be under maintenance at the same time as East US. Understanding how region pairs work can help you better distribute your VMs across regions. For more information, see [Azure region pairs](../availability-zones/cross-region-replication-azure.md).

#### Availability zones

Availability zones are unique physical locations within an Azure region. Each zone is made up of one or more datacenters equipped with independent power, cooling, and networking. To ensure resiliency, there’s a minimum of three separate zones in all enabled regions. 

An availability zone is a combination of a fault domain and an update domain. If you create three or more VMs across three zones in an Azure region, your VMs are effectively distributed across three fault domains and three update domains. The Azure platform recognizes this distribution across update domains to make sure that VMs in different zones are not updated at the same time.

Each infrastructure update rolls out zone by zone, within a single region. But, you can have deployment going on in Zone 1, and different deployment going in Zone 2, at the same time. Deployments are not all serialized. But, a single deployment only rolls out one zone at a time to reduce risk.

#### Virtual machine scale sets

Virtual machine scale sets in **Flexible** orchestration mode are an Azure compute resource allow you to combine the scalability of virtual machine scale sets in Uniform orchestration mode with the regional availability guarantees of availability sets.

With Flexible orchestration, you can choose whether your instances are spread across multiple zones, or spread across fault domains within a single region. 

#### Availability sets and Uniform scale sets

When deploying a workload on Azure VMs, you can create the VMs within an *availability set* to provide high availability to your application. Using availability sets, you can ensure that during either an outage or maintenance events that require a reboot, at least one VM is available.

Within an availability set, individual VMs are spread across up to 20 update domains. During scheduled maintenance, only one update domain is updated at any given time. Update domains aren't necessarily updated sequentially. 

Virtual machine *scale sets* in **Uniform** orchestration mode are an Azure compute resource that you can use to deploy and manage a set of identical VMs as a single resource. The scale set is automatically deployed across UDs, like VMs in an availability set. As with availability sets, when you use Uniform scale sets, only one UD is updated at any given time during scheduled maintenance.

For more information about setting up your VMs for high availability, see [Manage the availability of your VMs for Windows](./availability.md) or the corresponding article for [Linux](./availability.md).

## Next steps 

You can use the [Azure CLI](maintenance-notifications-cli.md), [Azure PowerShell](maintenance-notifications-powershell.md), or the [portal](maintenance-notifications-portal.md) to manage planned maintenance.

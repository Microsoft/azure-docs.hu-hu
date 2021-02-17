---
title: Megoldási architektúrák a Azure NetApp Files használatával | Microsoft Docs
description: Az Azure NetApp Filest használó megoldási architektúrák ajánlott eljárásaira mutató hivatkozásokat tartalmaz.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: b-juche
ms.openlocfilehash: e9e7451e411ed9160eace0a60c65b5b74bf8f750
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100559477"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Megoldásarchitektúrák az Azure NetApp Filesszal
Ez a cikk az ajánlott eljárásokra mutató hivatkozásokat tartalmaz, amelyek segítségével megismerheti a Azure NetApp Files használatának megoldási architektúráit.  

A következő ábra összefoglalja a Azure NetApp Files által kínált megoldási architektúrák kategóriáit:

![Megoldás-architektúra kategóriái](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Linux OSS-alkalmazások és adatbázis-megoldások

Ez a szakasz a Linux OSS-alkalmazások és-adatbázisok megoldásaira mutató hivatkozásokat tartalmaz. 

### <a name="oracle"></a>Oracle

* [Az Oracle-adatbázis teljesítménye az Azure NetApp Files önálló kötetein](performance-oracle-single-volumes.md)
* [Oracle az Azure-beli üzembe helyezéssel kapcsolatos ajánlott eljárási útmutató az Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf)
* [Oracle VM-rendszerképek és azok üzembe helyezése Microsoft Azure: megosztott tároló konfigurációs beállításai](../virtual-machines/workloads/oracle/oracle-vm-solutions.md#shared-storage-configuration-options)
* [Az Azure NetApp Files Oracle Database-szel való használatának előnyei](solutions-benefits-azure-netapp-files-oracle-database.md)

### <a name="machine-learning"></a>Machine Learning
*   [Cloudera Machine Learning](https://docs.cloudera.com/machine-learning/cloud/requirements-azure/topics/ml-requirements-azure.html)

## <a name="windows-apps-and-sql-server-solutions"></a>Windows-alkalmazások és-SQL Server megoldások

Ez a szakasz a Windows-alkalmazásokra és SQL Server-megoldásokra mutató hivatkozásokat tartalmaz.

### <a name="file-sharing-and-global-file-caching"></a>Fájlmegosztás és globális fájlok gyorsítótárazása

* [Saját Azure NFS-t épít? Birkózó Linux-fájlmegosztás a felhőbe](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares)
* [Globális fájlok gyorsítótára/Azure NetApp Files üzembe helyezése](https://youtu.be/91LKb1qsLIM)
* [A felhő megfelelősége Azure NetApp Files](https://cloud.netapp.com/hubfs/Cloud%20Compliance%20for%20Azure%20NetApp%20Files%20-%20November%202020.pdf)

### <a name="sql-server"></a>SQL Server

* [SQL Server üzembe helyezése SMB-kapcsolaton keresztül Azure NetApp Files](https://www.youtube.com/watch?v=x7udfcYbibs)
<!-- * [Deploy SQL Server Always-On Failover Cluster over SMB with Azure NetApp Files](https://www.youtube.com/watch?v=zuNJ5E07e8Q) --> 
<!-- * [Deploy Always-On Availability Groups with Azure NetApp Files](https://www.youtube.com/watch?v=y3VQmzzeyvc) --> 

## <a name="sap-on-azure-solutions"></a>SAP Azure-megoldásokkal

Ez a szakasz az SAP Azure-megoldásokra mutató hivatkozásokat tartalmaz. 

### <a name="generic-sap-and-sap-netweaver"></a>Általános SAP és SAP NetWeaver 

* [SAP-alkalmazások Microsoft Azure a Azure NetApp Files használatával](https://www.netapp.com/us/media/tr-4746.pdf)
* [Magas rendelkezésre állás az Azure-beli virtuális gépeken futó SAP NetWeaver-hez SUSE Linux Enterprise Serveron Azure NetApp Files SAP-alkalmazásokhoz](../virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files.md)
* [Magas rendelkezésre állás az Azure-beli virtuális gépeken futó SAP NetWeaver-hez Red Hat Enterprise Linuxon Azure NetApp Files SAP-alkalmazásokhoz](../virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files.md)
* [Magas rendelkezésre állás a Windows rendszerű Azure-beli virtuális gépeken futó SAP NetWeaver számára az SAP-alkalmazások Azure NetApp Files (SMB) szolgáltatásával](../virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb.md)
* [Magas rendelkezésre állás az SAP NetWeaver Azure-beli virtuális gépeken Red Hat Enterprise Linux for SAP Applications multi-SID Guide](../virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid.md)

### <a name="sap-hana"></a>SAP HANA 

* [SAP HANA Azure-beli virtuális gépek tárkonfigurációi](../virtual-machines/workloads/sap/hana-vm-operations-storage.md)
* [NFS 4.1-es verziójú kötetek az SAP HANA-hoz készült Azure NetApp Filesban](../virtual-machines/workloads/sap/hana-vm-operations-netapp.md)
* [A SAP HANA skálázás magas rendelkezésre állása Azure NetApp Filesekkel Red Hat Enterprise Linux](../virtual-machines/workloads/sap/sap-hana-high-availability-netapp-files-red-hat.md)
* [SAP HANA az Azure-beli virtuális gépek készenléti csomópontjaival, a Azure NetApp Files a SUSE Linux Enterprise Server](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse.md)
* [SAP HANA az Azure-beli virtuális gépek készenléti csomópontjaival, a Azure NetApp Files a Red Hat Enterprise Linux](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel.md)
* [Azure Application konzisztens pillanatkép-eszköz (AzAcSnap)](azacsnap-introduction.md)

### <a name="sap-anydb"></a>SAP-AnyDB

* [SAP-AnyDB (Oracle 19c) üzembe helyezése Azure NetApp Files](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-anydb-oracle-19c-with-azure-netapp-files/ba-p/2064043)

### <a name="sap-iq-nls"></a>SAP IQ – NLS

*   [SAP IQ – NLS HA-megoldás üzembe helyezése Azure NetApp Files használatával SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172#.X2tDfpNzBh4.linkedin)

### <a name="sap-tech-community-and-blog-posts"></a>SAP-technikai Közösség és blogbejegyzések 

* [Azure NetApp Files – SAP HANA biztonsági mentés másodpercek alatt](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files – a HANA-adatbázis visszaállítása pillanatkép biztonsági másolatból](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp Files – SAP HANA a biztonsági mentés kiszervezése a Cloud Sync szolgáltatással](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [A SAP HANA rendszermásolatok felgyorsítása Azure NetApp Files használatával](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Felhőbeli kötetek ONTAP és Azure NetApp Files: SAP HANA rendszer áttelepítése egyszerűen](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)
* [ANF-befektetések maximalizálása a HANA N + M Scale-Out architektúrában – 1. rész – Microsoft technikai Közösség](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2078737)
* [Az ANF-befektetések maximalizálása a HANA N + M Scale-Out architektúrában – 2. rész – Microsoft technikai Közösség](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2117130)

## <a name="azure-vmware-solutions"></a>Azure VMware-megoldások

* [Azure NetApp Files Azure VMware-megoldással – vendég operációs rendszer Csatlakoztatásai](../azure-vmware/netapp-files-with-azure-vmware-solution.md)

## <a name="virtual-desktop-infrastructure-solutions"></a>Virtuális asztali infrastruktúra-megoldások

Ez a szakasz a virtuális asztali infrastruktúra-megoldásokra mutató hivatkozásokat tartalmaz.

### <a name="windows-virtual-desktop"></a>Windows virtuális asztal

* [Az Azure NetApp Files és a Windows Virtual Desktop használatának előnyei](solutions-windows-virtual-desktop.md)
* [Tárolási beállítások a Windows rendszerű virtuális asztali FSLogix-profilok tárolói számára](../virtual-desktop/store-fslogix-profile.md#azure-platform-details)
* [FSLogix-profil tárolójának létrehozása a Azure NetApp Files használatával](../virtual-desktop/create-fslogix-profile-container.md)
* [Vállalati szintű Windows Virtual Desktop](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
* [Microsoft FSLogix a nagyvállalati Azure NetApp Files – ajánlott eljárások](/azure/architecture/example-scenario/wvd/windows-virtual-desktop-fslogix#azure-netapp-files-best-practices)
* [Azure NetApp Files beállítása a MSIX-alkalmazás csatolásához](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/setting-up-azure-netapp-files-for-msix-app-attach-step-by-step/m-p/1990021)

## <a name="hpc-solutions"></a>HPC-megoldások

Ez a szakasz a nagy teljesítményű számítástechnikai (HPC) megoldásokra mutató hivatkozásokat tartalmaz. 

### <a name="generic-hpc"></a>Általános HPC

* [Azure NetApp Files: a lehető legtöbbet hozza ki a felhőalapú tárterületről](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [MPI-munkaterhelések futtatása Azure Batch és Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Azure Cycle Cloud: CycleCloud HPC-környezetek Azure NetApp Files](/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>Olaj és gáz

* [Nagy teljesítményű számítástechnika (HPC): olaj és gáz az Azure-ban](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [A Reservoir szimulációs szoftver futtatása az Azure-ban](/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>Elektronikus tervezési automatizálás (EDA)

* [Az Azure NetApp elektronikus tervautomatizálással való használatának előnyei](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud: EDA HPC Lab Azure NetApp Files](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)
* [Azure a félvezető ipar számára](https://azurecomcdn.azureedge.net/cvt-f40f39cd9de2d875ab0c198a8d7b186350cf0bca161e80d7896941389685d012/mediahandler/files/resourcefiles/azure-for-the-semiconductor-industry/Azure_for_the_Semiconductor_Industry.pdf)

### <a name="analytics"></a>Elemzés

* [Azure NetApp Files: egy megosztott fájlrendszer, amelyet SAS-rácshoz kell használni Microsoft Azure](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/705192)
* [Ajánlott eljárások a Microsoft Azure SAS-vel való használatához®](https://communities.sas.com/t5/Administration-and-Deployment/Best-Practices-for-Using-Microsoft-Azure-with-SAS/m-p/676833#M19680)

## <a name="azure-platform-services-solutions"></a>Azure platform Services-megoldások

Ez a szakasz az Azure platform szolgáltatásainak megoldásait ismerteti. 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Azure Kubernetes-szolgáltatások és Kubernetes

* [Azure NetApp Files integrálása az Azure Kubernetes szolgáltatással](../aks/azure-netapp-files.md)
* [Globális Kubernetes teljesítmény az Azure-ban Azure NetApp Files](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Azure NetApp Files + Trident = dinamikus és állandó tárterület a Kubernetes](https://anfcommunity.com/2021/02/16/azure-netapp-files-trident-dynamic-and-persistent-storage-for-kubernetes/)
* [Trident – tárolási Orchestrator tárolók számára](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)
* [Magento e-kereskedelmi platform az Azure Kubernetes Service-ben (ak)](/azure/architecture/example-scenario/magento/magento-azure)

### <a name="azure-batch"></a>Azure Batch

* [MPI-munkaterhelések futtatása Azure Batch és Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)

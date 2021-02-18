---
title: Azure-beli virtuális gépek az SAP NW-hez Windows rendszeren Azure NetApp Files (SMB) | Microsoft Docs
description: Magas rendelkezésre állás a Windows rendszerű Azure-beli virtuális gépeken futó SAP NetWeaver számára az SAP-alkalmazások Azure NetApp Files (SMB) szolgáltatásával
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.openlocfilehash: b04dacfdedded417e2557d1568e01bc9fa8f5745
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590121"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>Magas rendelkezésre állás a Windows rendszerű Azure-beli virtuális gépeken futó SAP NetWeaver számára az SAP-alkalmazások Azure NetApp Files (SMB) szolgáltatásával

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Ez a cikk leírja, hogyan telepítheti, konfigurálhatja a virtuális gépeket, telepítheti a fürtöt, és telepítheti a kiválóan elérhető SAP NetWeaver 7,50 rendszert a Windows rendszerű virtuális gépeken az [SMB](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) használatával [Azure NetApp Fileson](../../../azure-netapp-files/azure-netapp-files-introduction.md).  

Ebben a cikkben nem szerepel részletesen az adatbázis rétege. Feltételezzük, hogy az Azure-beli [virtuális hálózat](../../../virtual-network/virtual-networks-overview.md) már létrejött.  

Először olvassa el a következő SAP-megjegyzéseket és dokumentumokat:

* [Azure NetApp Files dokumentáció][anf-azure-doc] 
* SAP-Megjegyzés [1928533][1928533], amely a következőket tartalmazza:  
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája
  * Fontos kapacitási információk Azure-beli virtuális gépek méreteihez
  * Támogatott SAP-szoftverek és operációs rendszerek (OS) és adatbázis-kombinációk
  * A Windows rendszerhez szükséges SAP kernel verziója Microsoft Azure
* Az SAP Note [2015553][2015553] az SAP által támogatott SAP-szoftverek Azure-beli üzembe helyezésének előfeltételeit sorolja fel.
* Az [2178632][2178632] -es SAP-Megjegyzés részletes információkat tartalmaz az Azure-beli SAP-ban jelentett összes figyelési mérőszámról.
* Az SAP Megjegyzés [1999351][1999351] további hibaelhárítási információkat tartalmaz az SAP-hez készült Azure Enhanced monitoring bővítménnyel kapcsolatban.
* Az SAP Note [2287140](https://launchpad.support.sap.com/#/notes/2287140) az SMB 3. x protokoll SAP által támogatott hitelesítésszolgáltatói funkciójának előfeltételeit sorolja fel.
* A [2802770](https://launchpad.support.sap.com/#/notes/2802770) -es SAP-Megjegyzés hibaelhárítási információkkal szolgál a lassú futású SAP-tranzakciós AL11 Windows 2012 és 2016 rendszeren.
* Az [1911507](https://launchpad.support.sap.com/#/notes/1911507) -es SAP-Megjegyzés a Windows Server SMB 3,0 protokollt használó fájlmegosztás transzparens feladatátvételi funkcióját ismerteti.
* A [662452](https://launchpad.support.sap.com/#/notes/662452) -es SAP-megjegyzésben az adathozzáférés során a fájlrendszer gyenge teljesítményének/hibáinak elhárítása érdekében javasolt a 8,3-név létrehozásának inaktiválása.
* [Az SAP NetWeaver magas rendelkezésre állásának telepítése Windows feladatátvevő fürtön és fájlmegosztás az Azure-beli SAP ASCS/SCS-példányok esetén](./sap-high-availability-installation-wsfc-file-share.md) 
* [Azure Virtual Machines magas rendelkezésre állású architektúra és forgatókönyvek az SAP NetWeaver-hoz](./sap-high-availability-architecture-scenarios.md)
* [Mintavételi Port hozzáadása a ASCS-fürt konfigurációjában](sap-high-availability-installation-wsfc-file-share.md)
* [Egy (A) SCS-példány telepítése feladatátvevő fürtön](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [SMB-kötet létrehozása az Azure NetApp Files számára](../../../azure-netapp-files/create-active-directory-connections.md#requirements-for-active-directory-connections)
* [NetApp SAP-alkalmazások Microsoft Azure a Azure NetApp Files használatával][anf-sap-applications-azure]

## <a name="overview"></a>Áttekintés

Az SAP új megközelítést fejlesztett ki, és egy alternatívát a fürt megosztott lemezei számára egy SAP ASCS/SCS-példány fürtözésére egy Windows feladatátvevő fürtön. A fürt megosztott lemezei helyett az egyik SMB-fájlmegosztás használatával telepítheti az SAP globális gazdagép fájljait. Azure NetApp Files támogatja a SMBv3 (az NFS-sel együtt) az NTFS ACL-lel az Active Directory használatával. A Azure NetApp Files automatikusan elérhető (mivel ez egy Pásti szolgáltatás). Ezek a funkciók Azure NetApp Files nagyszerű lehetőséget biztosítanak az SMB-fájlmegosztás az SAP globális számára való üzemeltetéséhez.  
A [Azure Active Directory (ad) tartományi szolgáltatások](../../../active-directory-domain-services/overview.md) és a [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) egyaránt támogatottak. A meglévő Active Directory tartományvezérlőket Azure NetApp Files használatával használhatja. A tartományvezérlők az Azure-ban virtuális gépekként vagy a helyszínen ExpressRoute vagy S2S VPN-en keresztül is lehetnek. Ebben a cikkben egy Azure-beli virtuális gép tartományvezérlőjét fogjuk használni.  
Az SAP NetWeaver Central Services magas rendelkezésre állása (HA) megosztott tárterületet igényel. Ahhoz, hogy a Windows rendszeren elérhető legyen, a SOFS-fürt vagy a fürt megosztott lemezének (például SIOS) használata szükséges. Most már lehetséges, hogy az SAP NetWeaver HA-t megosztott tároló használatával, Azure NetApp Fileson helyezi el. A megosztott tárolóhoz Azure NetApp Files használata szükségtelenné teszi a SOFS vagy a SIOS használatát.  

> [!NOTE]
> Az SAP ASCS/SCS-példányok egy fájlmegosztás használatával történő fürtözését az SAP NetWeaver 7,40 (és újabb) SAP kernel 7,49 (és újabb verziók) támogatják.  

![SAP ASCS/SCS HA architektúra SMB-megosztással](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

Az SMB-fájlmegosztás előfeltételei a következők:
* SMB 3,0 (vagy újabb) protokoll.
* Lehetőség Active Directory hozzáférés-vezérlési listák (ACL) beállítására Active Directory felhasználói csoportok és a számítógép $ Computer objektum számára.
* A fájlmegosztás csak akkor engedélyezhető, HA engedélyezve van.

A jelen hivatkozási architektúrában az SAP központi szolgáltatások megosztását Azure NetApp Files a következő kínálja:

![SAP ASCS/SCS HA architektúra SMB-megosztás részleteivel](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>SMB-kötet létrehozása és csatlakoztatása Azure NetApp Fileshoz

Hajtsa végre a következő lépéseket a Azure NetApp Files használatának előkészítéseként.  

1. [Azure NetApp Files regisztrálásához](../../../azure-netapp-files/azure-netapp-files-register.md) kövesse az alábbi lépéseket:  
2. Hozzon létre egy Azure NetApp-fiókot a [NetApp-fiók létrehozása](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md) című témakörben ismertetett lépéseket követve.  
3. A kapacitás készlet beállítása a [Kapacitási készlet beállítása](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md) című részben leírtak szerint
4. Azure NetApp Files erőforrásoknak a delegált alhálózatban kell lenniük. Delegált alhálózat létrehozásához kövesse az [alhálózat delegálása Azure NetApp Filesre](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md) című témakör utasításait.  

   > [!IMPORTANT]
   > SMB-kötet létrehozása előtt létre kell hoznia Active Directory kapcsolatokat. Tekintse át [Active Directory kapcsolatok követelményeit](../../../azure-netapp-files/create-active-directory-connections.md#requirements-for-active-directory-connections).  

5. Hozzon létre Active Directory-kapcsolatokat a [Active Directory-kapcsolatok létrehozása](../../../azure-netapp-files/create-active-directory-connections.md#create-an-active-directory-connection) című témakörben leírtak szerint.  
6. Hozzon létre SMB-Azure NetApp Files SMB-kötetet, kövesse az [SMB-kötet hozzáadása](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) című témakör utasításait.  
7. Csatlakoztassa az SMB-kötetet a Windows rendszerű virtuális gépén.

> [!TIP]
> A Azure NetApp Files kötet csatlakoztatására vonatkozó utasításokat megtalálja, ha az [Azure Portalon](https://portal.azure.com/#home) navigál az Azure NetApp Files objektumhoz, kattintson a **kötetek** panelre, majd a **csatlakoztatási utasítások** lehetőségre.  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Az infrastruktúra előkészítése az SAP HA-hez Windows feladatátvevő fürt használatával 

1. [Állítsa be az ASCS/SCS terheléselosztási szabályokat az Azure belső terheléselosztó számára](./sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716).
2. [Adja hozzá a Windows rendszerű virtuális gépeket a tartományhoz](./sap-high-availability-infrastructure-wsfc-shared-disk.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c).
3. [Beállításjegyzék-bejegyzések hozzáadása az SAP ASCS/SCS-példányhoz tartozó fürtcsomópontok esetében](./sap-high-availability-infrastructure-wsfc-shared-disk.md#661035b2-4d0f-4d31-86f8-dc0a50d78158)
4. [Windows Server feladatátvevő fürt beállítása SAP ASCS/SCS-példányhoz](./sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
5. Ha a Windows Server 2016-et használja, javasoljuk, hogy konfigurálja az [Azure Cloud tanúsító](/windows-server/failover-clustering/deploy-cloud-witness).


## <a name="install-sap-ascs-instance-on-both-nodes"></a>Az SAP ASCS-példány telepítése mindkét csomóponton

Az SAP-től a következő szoftverekre van szüksége:
   * Az SAP Software kiépítési kezelője (SWPM) telepítési eszközének SPS25 vagy újabb verziója.
   * SAP kernel 7,49 vagy újabb
   * Hozzon létre egy virtuális gazdagép nevét (a fürt hálózati nevét) a fürtözött SAP ASCS/SCS-példányhoz a fürtözött [SAP ASCS/SCS példány virtuális állomásneve létrehozása](./sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097)című témakörben leírtak szerint.

> [!NOTE]
> Az SAP ASCS/SCS-példányok egy fájlmegosztás használatával történő fürtözését az SAP NetWeaver 7,40 (és újabb) SAP kernel 7,49 (és újabb verziók) támogatják.  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>Telepítsen egy ASCS-/SCS-példányt az első ASCS/SCS-fürtcsomóponton

1. Telepítsen egy SAP ASCS/SCS-példányt az első fürtcsomóponton. Indítsa el az SAP SWPM telepítési eszközét, majd keresse meg a következőt: **termék**  >  **adatbázis-kezelője** > telepítés > Application Server ABAP (vagy Java) > High-Availability System > ASCS/SCS instance > első fürtcsomópont.  

2. Válassza ki a **fájlmegosztás fürtöt** a SWPM-ben.  
3. Ha a rendszer a **rendszerszintű SAP Rendszerfürt paramétereinek** megadását kéri, adja meg a korábban **fájlmegosztási állomásnévként** létrehozott Azure NetApp Files SMB-megosztás állomásnevét.  Ebben a példában az SMB-megosztás állomásneve a következő: **anfsmb-9562**. 

   > [!IMPORTANT]
   > Ha az előfeltétel-ellenőrző eredményei a SWPM a folyamatos rendelkezésre állási szolgáltatási feltétel nem teljesülnek, akkor a késleltetett hibaüzenet utasításait követve, [amikor olyan megosztott mappához próbál hozzáférni, amely már nem létezik a Windows rendszerben](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l).  

   > [!TIP]
   > Ha az előfeltétel-ellenőrző eredményei a SWPM a swap-méret feltétele nem teljesülnek, akkor a saját számítógép>a Rendszertulajdonságok>a teljesítmény beállításai> a speciális> virtuális memória> módosítása elemre.  

4. Konfiguráljon egy SAP-fürterőforrás, a mintavételi `SAP-SID-IP` portot a PowerShell használatával. Hajtsa végre ezt a konfigurációt az egyik SAP ASCS/SCS fürtcsomópontokon a mintavételi [port konfigurálása](./sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761)című cikkben leírtak szerint.

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>ASCS-/SCS-példány telepítése a második ASCS/SCS-fürtön

1. Telepítsen egy SAP ASCS/SCS-példányt a második fürtcsomóponton. Indítsa el az SAP SWPM telepítési eszközét, majd navigáljon a **termék**-  >  **adatbázis-kezelő** > telepítés > Application Server ABAP (vagy Java) > High-Availability System > ASCS/SCS instance > további fürtcsomópont.  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>Adatbázis-kezelő példány és SAP-alkalmazáskiszolgáló telepítése

Az SAP telepítésének befejezéséhez telepítse a következőket:

   * Egy adatbázis-kezelő példány  
   * Elsődleges SAP-alkalmazáskiszolgáló  
   * Egy további SAP-alkalmazáskiszolgáló  

## <a name="test-the-sap-ascsscs-instance-failover"></a>Az SAP-ASCS/SCS-példány feladatátvételének tesztelése 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>Feladatátvétel a fürt "A" csomópontja és a B csomópont között
Ebben a tesztelési forgatókönyvben a fürtcsomópont sapascs1 az A csomópontként, a csomópontok sapascs2 pedig a B csomópontként fogunk hivatkozni.

1. Ellenőrizze, hogy a fürt erőforrásai futnak-e az A csomóponton. ![ 1. ábra: az A csomóponton futó Windows Server feladatátvételi fürt erőforrásai a feladatátvételi teszt előtt](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. Az A fürtcsomópont újraindítása. Az SAP-fürt erőforrásai a B fürt csomópontra kerülnek át. ![ 2. ábra: a feladatátvételi teszt után a B csomóponton futó Windows Server feladatátvételi fürt erőforrásai](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>Bejegyzés zárolásának tesztelése

1. Ellenőrizze, hogy az SAP sorba helyezni replikációs kiszolgáló (ERS) aktív-e  
2. Jelentkezzen be az SAP-rendszerbe, futtassa a tranzakciós SU01, és nyisson meg egy felhasználói azonosítót a változási módban. Ekkor létrejön az SAP-zárolási bejegyzés.  
3. Az SAP-rendszerbe való bejelentkezéskor a zárolási bejegyzést a tranzakció ST12 navigálva jelenítheti meg.  
4. ASCS-erőforrások feladatátvétele a fürt "A" csomópontról a B csomópontra.  
5. Ellenőrizze, hogy az SAP-ASCS/SCS-fürterőforrás feladatátvétele előtt létrejött zárolási bejegyzés megmarad-e.  

![3. ábra: a zárolási bejegyzés megőrzése a feladatátvételi teszt után](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

További információ: a [sorba helyezni-feladatátvétel hibaelhárítása a ASCS-ben az ERS szolgáltatással](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)
## <a name="next-steps"></a>Következő lépések

* [Azure Virtual Machines az SAP tervezéséhez és megvalósításához][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-ban][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz][dbms-guide]
* Ismerje meg, hogyan hozhat létre magas rendelkezésre állást, és hogyan tervezze meg az SAP vész-helyreállítását 
* HANA az Azure-ban (nagyméretű példányok) a [magas rendelkezésre állást és a vész-helyreállítást az Azure-ban SAP HANA (nagyméretű példányok)](hana-overview-high-availability-disaster-recovery.md).
* A magas rendelkezésre állás és a SAP HANA Azure-beli virtuális gépeken történő vész-helyreállítási tervének megismeréséhez lásd: [Az Azure-beli SAP HANA magas rendelkezésre állása Virtual Machines (VM)][sap-hana-ha]

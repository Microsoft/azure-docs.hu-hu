---
title: Azure-beli virtuális gépek magas rendelkezésre állása az SAP NW számára a RHEL-ben Azure NetApp Files | Microsoft Docs
description: Magas rendelkezésre állást hozhat létre az SAP NW számára az Azure Virtual Machines (VM) RHEL Azure NetApp Filessal.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: b202613e6f24a5cd549267a1c8928e8e68caa232
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303593"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Az Azure Virtual Machines magas rendelkezésre állása az SAP NetWeaver számára a Red Hat Enterprise Linux SAP-alkalmazásokhoz Azure NetApp Files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Ez a cikk leírja, hogyan telepítheti a virtuális gépeket, konfigurálhatja a virtuális gépeket, telepítheti a fürtöt, és telepítheti a magasan elérhető SAP NetWeaver 7,50-rendszereket [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md)használatával.
A példában szereplő konfigurációk, telepítési parancsok stb. A ASCS-példány a 00-as számú, az ERS-példány értéke 01, az elsődleges Application instance (PAS) a 02, az Application instance (AAS) pedig 03. A rendszer az SAP rendszerazonosító QAS használja. 

Ebben a cikkben nem szerepel részletesen az adatbázis rétege.  

Először olvassa el a következő SAP-megjegyzéseket és dokumentumokat:

* [Azure NetApp Files dokumentáció][anf-azure-doc] 
* SAP-Megjegyzés [1928533], amely a következőket tartalmazta:
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája
  * Fontos kapacitási információk Azure-beli virtuális gépek méreteihez
  * Támogatott SAP-szoftverek és operációs rendszerek (OS) és adatbázis-kombinációk
  * A Windows és a Linux rendszerhez szükséges SAP kernel verziója Microsoft Azure

* Az SAP Note [2015553] az SAP által támogatott SAP-szoftverek Azure-beli üzembe helyezésének előfeltételeit sorolja fel.
* Az SAP Megjegyzés [2002167] ajánlott operációsrendszer-beállításokkal Red Hat Enterprise Linux
* A [2009879] -es SAP-Megjegyzés SAP HANA irányelvek a Red Hat Enterprise Linux
* Az [2178632] -es SAP-Megjegyzés részletes információkat tartalmaz az Azure-beli SAP-ban jelentett összes figyelési mérőszámról.
* A [2191498] -es SAP-Megjegyzés a szükséges SAP-gazdagép ügynökének verziója az Azure-ban linuxos.
* Az [2243692] -es SAP-Megjegyzés az Azure-beli Linuxon futó SAP-licenceléssel kapcsolatos információkat tartalmaz.
* Az SAP Megjegyzés [1999351] további hibaelhárítási információkat tartalmaz az SAP-hez készült Azure Enhanced monitoring bővítménnyel kapcsolatban.
* Az [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik minden szükséges SAP-megjegyzéssel a Linux rendszerhez.
* [Azure Virtual Machines tervezése és implementálása Linux rendszeren az SAP-ban][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-hez Linux rendszeren][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hez Linux rendszeren][dbms-guide]
* [SAP NetWeaver a pacemaker-fürtben](https://access.redhat.com/articles/3150081)
* Általános RHEL dokumentáció
  * [Magas rendelkezésre állású Add-On áttekintése](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású Add-On felügyelet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású Add-On referenciája](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Az SAP NetWeaver ASCS/ERS konfigurálása önálló erőforrásokkal a RHEL 7,5-ben](https://access.redhat.com/articles/3569681)
  * [Az SAP S/4HANA ASCS/ERS konfigurálása önálló sorba helyezni Server 2 (ENSA2) segítségével a RHEL-beli Pacemakerben ](https://access.redhat.com/articles/3974941)
* Az Azure-specifikus RHEL dokumentációja:
  * [A RHEL magas rendelkezésre állású fürtökre vonatkozó támogatási szabályzatok – Microsoft Azure Virtual Machines a fürt tagjai](https://access.redhat.com/articles/3131341)
  * [Red Hat Enterprise Linux 7,4 (és újabb) High-Availability fürt telepítése és konfigurálása Microsoft Azure](https://access.redhat.com/articles/3252491)
* [NetApp SAP-alkalmazások Microsoft Azure a Azure NetApp Files használatával][anf-sap-applications-azure]

## <a name="overview"></a>Áttekintés

Az SAP NetWeaver Central Services magas rendelkezésre állása (HA) megosztott tárterületet igényel.
Annak érdekében, hogy a Red Hat Linuxon eddig is szükség volt egy elkülönített, nagy rendelkezésre állású GlusterFS-fürt létrehozására. 

Most már lehetséges, hogy az SAP NetWeaver HA-t megosztott tároló használatával, Azure NetApp Fileson helyezi el. A megosztott tárolóhoz Azure NetApp Files használata nem igényel további GlusterFS- [fürtöt](./high-availability-guide-rhel-glusterfs.md). Az SAP NetWeaver Central Services (ASCS/SCS) esetében továbbra is a pacemaker szükséges.

![SAP NetWeaver – magas rendelkezésre állás – áttekintés](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

Az SAP NetWeaver ASCS, az SAP NetWeaver SCS, az SAP NetWeaver ERS és a SAP HANA adatbázis virtuális gazdagépeket és virtuális IP-címeket használ. Az Azure-ban a virtuális IP-címek használatához terheléselosztó szükséges. A [standard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)használatát javasoljuk. A következő lista a terheléselosztó konfigurációját mutatja be a (A) SCS és ERS különálló előtér-IP-címeivel.

### <a name="ascs"></a>Egy SCS

* Előtér-konfiguráció
  * IP-192.168.14.9
* Mintavételi port
  * Port 620<strong> &lt; nr &gt; </strong>
* Terheléselosztási szabályok
  * Ha standard Load Balancer használ, válassza a **hektár portok** elemet.
  * 32<strong> &lt; nr &gt; </strong> TCP
  * 36<strong> &lt; nr &gt; </strong> TCP
  * 39<strong> &lt; nr &gt; </strong> TCP
  * 81<strong> &lt; nr &gt; </strong> TCP
  * 5<strong> &lt; nr &gt; </strong>13 TCP
  * 5<strong> &lt; nr &gt; </strong>14 TCP
  * 5<strong> &lt; nr &gt; </strong>16 TCP

### <a name="ers"></a>ERS

* Előtér-konfiguráció
  * IP-192.168.14.10
* Mintavételi port
  * Port 621<strong> &lt; nr &gt; </strong>
* Terheléselosztási szabályok
  * Ha standard Load Balancer használ, válassza a **hektár portok** elemet.
  * 32<strong> &lt; nr &gt; </strong> TCP
  * 33<strong> &lt; nr &gt; </strong> TCP
  * 5<strong> &lt; nr &gt; </strong>13 TCP
  * 5<strong> &lt; nr &gt; </strong>14 TCP
  * 5<strong> &lt; nr &gt; </strong>16 TCP

* Háttér-konfiguráció
  * Az (A) SCS/ERS-fürt részét képező összes virtuális gép elsődleges hálózati adapteréhez csatlakozik

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>A Azure NetApp Files-infrastruktúra beállítása 

Az SAP NetWeaver megosztott tárterületet igényel az átvitelhez és a profilhoz.  Mielőtt folytatná az Azure NetApp-fájlok infrastruktúrájának telepítését, ismerkedjen meg az [Azure NetApp Files dokumentációval][anf-azure-doc]. Ellenőrizze, hogy a kiválasztott Azure-régió kínál-e Azure NetApp Files. A következő hivatkozás az Azure-régió Azure NetApp Files rendelkezésre állását mutatja: az [Azure-régió Azure NetApp Files rendelkezésre állása][anf-avail-matrix].

Az Azure NetApp-fájlok több [Azure-régióban](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)is elérhetők. Azure NetApp Files üzembe helyezése előtt a bevezetést a Azure NetApp Filesra kell kérnie, az [Azure NetApp-fájlok regisztrálásával kapcsolatos utasításokat][anf-register]követve. 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files erőforrások üzembe helyezése  

A lépések azt feltételezik, hogy már telepítette az [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)-t. A Azure NetApp Files erőforrásokat és a virtuális gépeket, ahol a Azure NetApp Files erőforrásokat csatlakoztatni kell ugyanazon az Azure-Virtual Network vagy az Azure-beli virtuális hálózatokban.  

1. Ha eddig még nem tette meg, kérje [a Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md)bevezetését.  
2. Hozza létre a NetApp-fiókot a kiválasztott Azure-régióban, és kövesse a [NetApp-fiók létrehozásához szükséges utasításokat](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).  
3. Azure NetApp Files kapacitás-készlet beállítása a [Azure NetApp Files kapacitás készletének beállításához szükséges útmutatás alapján](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).  
A jelen cikkben bemutatott SAP NetWeaver architektúra egy Azure NetApp Files kapacitási készletet, prémium SKU-t használ. Javasoljuk, hogy az Azure-ban az SAP NetWeaver alkalmazás számítási feladataihoz Azure NetApp Files Premium SKU-t.  
4. Az alhálózat delegálása az Azure NetApp-fájlokba az [alhálózat delegálása Azure NetApp Filesra](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)című részben leírtak szerint.  

5. Azure NetApp Files kötetek telepítéséhez kövesse az [utasításokat, amelyekkel Azure NetApp Files kötetet hozhat létre](../../../azure-netapp-files/azure-netapp-files-create-volumes.md). Telepítse a köteteket a kijelölt Azure NetApp Files [alhálózatban](/rest/api/virtualnetwork/subnets). Az Azure NetApp-kötetek IP-címeit a rendszer automatikusan hozzárendeli. Ne feledje, hogy a Azure NetApp Files erőforrásoknak és az Azure-beli virtuális gépeknek ugyanabban az Azure-Virtual Networkban vagy az Azure-beli virtuális hálózatokban kell lenniük. Ebben a példában két Azure NetApp Files kötetet használunk: az SAP<b>QAS</b> és a transSAP. A megfelelő csatlakoztatási pontokhoz csatlakoztatott fájlelérési utak a következők:/usrsap<b>QAS</b>/sapmnt<b>QAS</b>,/usrsap<b>QAS</b>/usrsap<b>QAS sys stb</b>.  

   1. mennyiségi SAP-<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/sapmnt<b>QAS</b>)
   2. mennyiségi SAP-<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ASCs)
   3. mennyiségi SAP-<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys)
   4. mennyiségi SAP-<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ERS)
   5. mennyiségi transSAP (nfs://192.168.24.4/transSAP)
   6. mennyiségi SAP-<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>Pas)
   7. mennyiségi SAP-<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>AAS)
  
Ebben a példában az összes SAP NetWeaver fájlrendszer Azure NetApp Files használták, hogy bemutassa, hogyan használható a Azure NetApp Files. Az NFS-en keresztül nem szükséges SAP-fájlrendszerek is üzembe helyezhetők az [Azure Disk Storage](../../disks-types.md#premium-ssd) szolgáltatásban. Ebben a példában az <b>a-e</b> Azure NetApp Files és <b>f-g</b> (azaz/usr/SAP/<b>QAS</b>/d<b>02</b>,/usr/SAP/<b>QAS</b>/d<b>03</b>) telepíthető Azure Disk Storage-ként. 

### <a name="important-considerations"></a>Fontos szempontok

Az SAP NetWeaver SUSE magas rendelkezésre állású architektúrán való Azure NetApp Filesának megfontolásakor vegye figyelembe a következő fontos szempontokat:

- A minimális kapacitási készlet 4 TiB. A kapacitási készlet mérete 1 TiB-os növekményekben is növelhető.
- A minimális kötet 100 GiB
- Azure NetApp Files és az összes olyan virtuális gép, amelyben Azure NetApp Files köteteket csatlakoztatni kell, ugyanabban az Azure-Virtual Network vagy egymással azonos régióban lévő [virtuális hálózatokban](../../../virtual-network/virtual-network-peering-overview.md) kell lennie. A VNET-társítások ugyanazon régióban való elérésének Azure NetApp Files jelenleg támogatott. Az Azure NetApp a globális társon keresztüli hozzáférése még nem támogatott.
- A kiválasztott virtuális hálózatnak rendelkeznie kell egy, a Azure NetApp Files delegált alhálózattal.
- Azure NetApp Files az [exportálási szabályzatot](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md): szabályozhatja az engedélyezett ügyfeleket, a hozzáférési típust (olvasási&írás, csak olvasható stb.). 
- Azure NetApp Files a szolgáltatás még nem ismeri a zónát. Jelenleg Azure NetApp Files funkció nincs telepítve az Azure-régió összes rendelkezésre állási zónájában. Vegye figyelembe, hogy egyes Azure-régiókban lehetséges a késés következményei. 
- Azure NetApp Files kötetek NFSv3 vagy NFSv 4.1 kötetként telepíthetők. Mindkét protokoll támogatott az SAP Application Layer (ASCS/ERS, SAP Application Servers) esetében. 

## <a name="setting-up-ascs"></a>Az (A) SCS beállítása

Ebben a példában az erőforrásokat manuálisan telepítették a [Azure Portal](https://portal.azure.com/#home)használatával.

### <a name="deploy-linux-manually-via-azure-portal"></a>A Linux telepítése manuálisan Azure Portal használatával

Először létre kell hoznia a Azure NetApp Files köteteket. Telepítse a virtuális gépeket. Ezt követően hozzon létre egy terheléselosztó-t, és használja a virtuális gépeket a háttér-készletben.

1. Load Balancer létrehozása (belső, standard):  
   1. Az előtérbeli IP-címek létrehozása
      1. A ASCS IP-192.168.14.9
         1. Nyissa meg a terheléselosztó-t, válassza a előtéri IP-készlet lehetőséget, majd kattintson a Hozzáadás gombra.
         1. Adja meg az új előtér-IP-készlet nevét (például a **frontend. QAS. ASCS**)
         1. Állítsa a hozzárendelést statikus értékre, és adja meg az IP-címet (például **192.168.14.9**).
         1. Kattintson az OK gombra
      1. A ASCS IP-192.168.14.10
         * Ismételje meg a fenti lépéseket az "a" alatt, és hozzon létre egy IP-címet az ERS számára (például **192.168.14.10** és **frontend. QAS. ERS**)
   1. A háttérkészlet létrehozása
      1. Nyissa meg a Load balancert, válassza a háttérbeli készletek elemet, majd kattintson a Hozzáadás gombra.
      1. Adja meg az új háttér-készlet nevét (például **háttér. QAS**)
      1. Kattintson a virtuális gép hozzáadása elemre.
      1. Válassza a Virtuális gép lehetőséget. 
      1. Válassza ki az (A) SCS-fürthöz tartozó virtuális gépeket és azok IP-címeit.
      1. Kattintson az Add (Hozzáadás) parancsra
   1. Az állapot-mintavételek létrehozása
      1. A ASCS 620 **00** portja
         1. Nyissa meg a terheléselosztó-t, válassza az állapot-tesztek elemet, majd kattintson a Hozzáadás gombra.
         1. Adja meg az új állapotadatok (például az **állapot) nevét. QAS. ASCS**)
         1. Válassza a TCP protokollt, a 620 **00** portot, az 5. időközt és a nem megfelelő állapotú küszöbértéket 2
         1. Kattintson az OK gombra
      1. Port 621 **01** ASCS-eseknél
            * A "c" alatt a fenti lépések megismétlésével hozzon létre egy állapot-mintavételt a ERS számára (például 621 **01** és **Health. QAS. ERS**)
   1. Terheléselosztási szabályok
      1. Terheléselosztási szabályok ASCS
         1. Nyissa meg a Load balancert, válassza a terheléselosztási szabályok elemet, majd kattintson a Hozzáadás gombra.
         1. Adja meg az új terheléselosztó szabály nevét (például **LB. QAS. ASCS**)
         1. Válassza ki a korábban létrehozott ASCS, háttér-készlet és állapot-mintavételi felület IP-címét (például a **frontendet). QAS. ASCS**, **háttérrendszer. QAS** és- **állapot. QAS. ASCS**)
         1. **Ha portok** kiválasztása
         1. **Ügyeljen arra, hogy a lebegő IP-címet engedélyezze**
         1. Kattintson az OK gombra
         * A fenti lépések megismétlésével hozzon létre terheléselosztási szabályokat az ERS számára (például **LB). QAS. ERS**)
1. Ha a forgatókönyvben alapszintű terheléselosztó (belső) szükséges, kövesse az alábbi lépéseket:  
   1. Az előtérbeli IP-címek létrehozása
      1. A ASCS IP-192.168.14.9
         1. Nyissa meg a terheléselosztó-t, válassza a előtéri IP-készlet lehetőséget, majd kattintson a Hozzáadás gombra.
         1. Adja meg az új előtér-IP-készlet nevét (például a **frontend. QAS. ASCS**)
         1. Állítsa a hozzárendelést statikus értékre, és adja meg az IP-címet (például **192.168.14.9**).
         1. Kattintson az OK gombra
      1. A ASCS IP-192.168.14.10
         * Ismételje meg a fenti lépéseket az "a" alatt, és hozzon létre egy IP-címet az ERS számára (például **192.168.14.10** és **frontend. QAS. ERS**)
   1. A háttérkészlet létrehozása
      1. Nyissa meg a Load balancert, válassza a háttérbeli készletek elemet, majd kattintson a Hozzáadás gombra.
      1. Adja meg az új háttér-készlet nevét (például **háttér. QAS**)
      1. Kattintson a virtuális gép hozzáadása elemre.
      1. Válassza ki a ASCS korábban létrehozott rendelkezésre állási készletet 
      1. Válassza ki az (A) SCS-fürthöz tartozó virtuális gépeket.
      1. Kattintson az OK gombra
   1. Az állapot-mintavételek létrehozása
      1. A ASCS 620 **00** portja
         1. Nyissa meg a terheléselosztó-t, válassza az állapot-tesztek elemet, majd kattintson a Hozzáadás gombra.
         1. Adja meg az új állapotadatok (például az **állapot) nevét. QAS. ASCS**)
         1. Válassza a TCP protokollt, a 620 **00** portot, az 5. időközt és a nem megfelelő állapotú küszöbértéket 2
         1. Kattintson az OK gombra
      1. Port 621 **01** ASCS-eseknél
            * A "c" alatt a fenti lépések megismétlésével hozzon létre egy állapot-mintavételt a ERS számára (például 621 **01** és **Health. QAS. ERS**)
   1. Terheléselosztási szabályok
      1. 32 **00** TCP a ASCS
         1. Nyissa meg a Load balancert, válassza a terheléselosztási szabályok elemet, majd kattintson a Hozzáadás gombra.
         1. Adja meg az új terheléselosztó szabály nevét (például **LB. QAS. ASCS. 3200**)
         1. Válassza ki a korábban létrehozott ASCS, háttér-készlet és állapot-mintavételi felület IP-címét (például a **frontendet). QAS. ASCS**)
         1. Tartsa meg a protokoll **TCP**-t, írja be a **3200** portot
         1. Üresjárati időkorlát 30 percre növelve
         1. **Ügyeljen arra, hogy a lebegő IP-címet engedélyezze**
         1. Kattintson az OK gombra
      1. További portok a ASCS
         * Ismételje meg a fenti lépéseket a "d" alatt a 36 **00**, 39 **00**, 81 **00**, 5 **00** 13, 5 **00** 14, 5 **00** 16 és TCP ASCS
      1. További portok a ASCS-ESEK számára
         * Ismételje meg a fenti lépéseket a "d" alatt a 32 **01**, 33 **01**, 5 **01** 13, 5 **01** 14, 5 **01** 16 és TCP ASCS-eseknél.

      > [!IMPORTANT]
      > A lebegő IP-címek nem támogatottak a terheléselosztási helyzetekben a hálózati adapter másodlagos IP-konfigurációjában. További részletek: az [Azure Load Balancer korlátozásai](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Ha a virtuális gép további IP-címére van szüksége, helyezzen üzembe egy második hálózati adaptert.  

      > [!Note]
      > Ha a nyilvános IP-címek nélküli virtuális gépek a belső (nincs nyilvános IP-cím) standard Azure Load Balancer háttér-készletbe kerülnek, nem lesz kimenő internetkapcsolat, kivéve, ha további konfigurálást végeznek a nyilvános végpontok útválasztásának engedélyezéséhez. A kimenő kapcsolatok elérésével kapcsolatos részletekért lásd: [nyilvános végpontú kapcsolat Virtual Machines az Azure standard Load Balancer használata az SAP magas rendelkezésre állási helyzetekben](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

      > [!IMPORTANT]
      > Ne engedélyezze a TCP-időbélyegeket a Azure Load Balancer mögött elhelyezett Azure-beli virtuális gépeken. A TCP-időbélyegek engedélyezése az állapot-mintavételek meghibásodását eredményezi. Állítsa a paramétert a **0** értékre **net.IPv4.tcp_timestamps** . Részletekért lásd: [Load Balancer Health](../../../load-balancer/load-balancer-custom-probe-overview.md)-tesztek.

## <a name="disable-id-mapping-if-using-nfsv41"></a>AZONOSÍTÓ-hozzárendelés letiltása (ha a NFSv 4.1-et használja)

Az ebben a szakaszban szereplő utasítások csak akkor használhatók, ha Azure NetApp Files köteteket használ a NFSv 4.1 protokollal. Minden virtuális gépen végezze el a konfigurálást, ahol Azure NetApp Files NFSv 4.1 kötet lesz csatlakoztatva.  

1. Ellenőrizze az NFS-tartomány beállítását. Győződjön meg arról, hogy a tartomány alapértelmezett Azure NetApp Files-tartományként van konfigurálva, azaz **`defaultv4iddomain.com`** a leképezés értéke **senki**.  

    > [!IMPORTANT]
    > Ügyeljen arra, hogy az NFS-tartományt a virtuális gépen állítsa be, `/etc/idmapd.conf` hogy az megfeleljen az alapértelmezett tartományi konfigurációnak Azure NetApp Files: **`defaultv4iddomain.com`** . Ha az NFS-ügyfél (azaz a virtuális gép) és az NFS-kiszolgáló (például az Azure NetApp-konfiguráció) közötti eltérés nem egyezik, akkor a virtuális gépekre csatlakoztatott Azure NetApp-köteteken található fájlok engedélyei a következőképpen jelennek meg `nobody` .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** ellenőrzés `nfs4_disable_idmapping` . Értékeként az **Y** értéknek kell lennie. A-t tartalmazó könyvtár-struktúra létrehozásához `nfs4_disable_idmapping` hajtsa végre a csatlakoztatási parancsot. Nem lehet manuálisan létrehozni a könyvtárat a/sys/modules alatt, mivel a hozzáférés a kernel/illesztőprogramok számára van fenntartva.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 192.168.24.5:/sap<b>QAS</b>
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   További információ a `nfs4_disable_idmapping` Paraméterek módosításáról: https://access.redhat.com/solutions/1749883 .

### <a name="create-pacemaker-cluster"></a>Pacemaker-fürt létrehozása

Kövesse a [pacemaker beállítása Red Hat Enterprise Linux az Azure-ban](high-availability-guide-rhel-pacemaker.md) című témakör lépéseit egy alapszintű pacemaker-fürt létrehozásához ehhez A (a) SCS-kiszolgálóhoz.

### <a name="prepare-for-sap-netweaver-installation"></a>Felkészülés az SAP NetWeaver telepítésére

A következő elemek a **[a]** előtaggal vannak ellátva, amelyek az összes csomópontra érvényesek, **[1]** – csak az 1. vagy **[2]** csomópontra érvényesek, csak a 2. csomópontra.

1. **[A]** telepítési állomásnév feloldása

   Használhat DNS-kiszolgálót, vagy módosíthatja a/etc/hosts az összes csomóponton. Ez a példa a/etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és a gazdagépet a következő parancsokra

    ```
    sudo vi /etc/hosts
    ```

   Szúrja be a következő sorokat a/etc/hosts. Az IP-cím és az állomásnév módosítása a környezetnek megfelelően

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]** hozzon létre SAP-címtárakat a Azure NetApp Files köteten.  
   Csatlakoztassa ideiglenesen a Azure NetApp Files kötetet az egyik virtuális gépen, és hozza létre az SAP-címtárakat (fájlelérési utakat).  

    ```
     # mount temporarily the volume
     sudo mkdir -p /saptmp
     # If using NFSv3
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # If using NFSv4.1
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp
    ``` 

1. **[A]** a megosztott könyvtárak létrehozása

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** NFS-ügyfél és egyéb követelmények telepítése

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** az erőforrás-ügynökök verziójának ellenõrzése – SAP

   Győződjön meg arról, hogy a telepített erőforrás-ügynökök-SAP-csomag verziószáma legalább 3.9.5 -124. el7
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]** csatlakoztatási bejegyzések hozzáadása

   NFSv3 használata esetén:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   NFSv 4.1 használata esetén:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > Győződjön meg arról, hogy a kötetek csatlakoztatásakor meg kell egyeznie a Azure NetApp Files kötetek NFS-protokolljának verziójával. Ha a Azure NetApp Files kötetek NFSv3-kötetként jönnek létre, használja a megfelelő NFSv3-konfigurációt. Ha a Azure NetApp Files kötetek NFSv 4.1-es kötetként jönnek létre, kövesse az AZONOSÍTÓk leképezésének letiltására vonatkozó utasításokat, és ügyeljen arra, hogy a megfelelő NFSv 4.1-es konfigurációt használja. Ebben a példában a Azure NetApp Files kötetek NFSv3-kötetként lettek létrehozva.  

   Az új megosztások csatlakoztatása

   ```
   sudo mount -a  
   ```

1. **[A] A** swap-fájl konfigurálása

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Az ügynök újraindítása a módosítás aktiválásához

   ```
   sudo service waagent restart
   ```

1. **[A]** RHEL-konfiguráció

   Konfigurálja a RHEL az SAP Note [2002167] -es verziójában leírtak szerint

### <a name="installing-sap-netweaver-ascsers"></a>Az SAP NetWeaver ASCS/ERS telepítése

1. **[1]** virtuális IP-erőforrás és állapot-mintavétel létrehozása a ASCS-példányhoz

   ```
   sudo pcs node standby anftstsapcl2
   # If using NFSv3
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás el van indítva. Nem fontos, hogy az erőforrások melyik csomóponton futnak.

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** az SAP NetWeaver ASCS telepítése  

   Telepítse az SAP NetWeaver ASCS-t root-ként az első csomóponton egy olyan virtuális állomásnév használatával, amely a ASCS terheléselosztó-felületi konfigurációjának IP-címét képezi le, például a <b>anftstsapvh</b>, a <b>192.168.14.9</b> és a terheléselosztó mintavételéhez használt példány számát (például <b>00</b>).

   A sapinst paraméterrel SAPINST_REMOTE_ACCESS_USER engedélyezheti, hogy a nem root felhasználó csatlakozhasson a sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Ha a telepítés során nem sikerül almappát létrehozni a/usr/SAP/**QAS**/ASCS **00**-ben, próbálja meg beállítani a ASCS **00** mappa tulajdonosát és csoportját, és próbálkozzon újra.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** virtuális IP-erőforrás és állapot-mintavétel létrehozása az ERS-példányhoz

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   # If using NFSv3
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás el van indítva. Nem fontos, hogy az erőforrások melyik csomóponton futnak.

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** SAP NETWEAVER-ERS telepítése  

   Telepítse az SAP NetWeaver-ket root-ként a második csomóponton egy olyan virtuális állomásnév használatával, amely a hálózati terheléselosztási felület konfigurációjának IP-címét képezi le, például a <b>anftstsapers</b>, a <b>192.168.14.10</b> és a terheléselosztó mintavételéhez használt példány számát, például: <b>01</b>.

   A sapinst paraméterrel SAPINST_REMOTE_ACCESS_USER engedélyezheti, hogy a nem root felhasználó csatlakozhasson a sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Ha a telepítés során nem sikerül almappát létrehozni a/usr/SAP/**QAS**/ERS **01**-ben, próbálja meg beállítani az ERS **01** mappa tulajdonosát és csoportját, és próbálkozzon újra.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** a ASCS/SCS és az ERS instance-profilok átalakítása

   * ASCS/SCS-profil

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   ```

   A ENSA1 és a ENSA2 esetében ügyeljen arra, hogy az `keepalive` operációs rendszer paramétereinek beállítása a [1410736](https://launchpad.support.sap.com/#/notes/1410736)-es SAP-megjegyzésben leírtak szerint történjen.  

   * ERS-profil

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A] A** Keep Alive konfigurálása

   Az SAP NetWeaver Application Server és a ASCS/SCS közötti kommunikáció egy szoftveres terheléselosztó használatával irányítható át. A terheléselosztó konfigurálható időtúllépés után leválasztja az inaktív kapcsolatokat. Ennek megelőzése érdekében be kell állítania egy paramétert az SAP NetWeaver ASCS/SCS profilban, ha a ENSA1-t használja, és minden SAP-kiszolgálón módosítania kell a Linux rendszer `keepalive` beállításait mind a ENSA1-, mind a ENSA2-hez. További információért olvassa el az [SAP megjegyzés 1410736][1410736] .

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   ```

1. **[A]** a/usr/SAP/sapservices fájl frissítése

   Ha meg szeretné akadályozni, hogy a sapinit indítási parancsfájlja elindítsa a példányokat, a pacemaker által kezelt összes példányt ki kell kommentálni a/usr/SAP/sapservices-fájlból. Ne tegye megjegyzésbe a SAP HANA példányt, ha a HANA SR-vel fog használni.

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** az SAP-fürt erőforrásainak létrehozása

   Ha a sorba helyezni Server 1 architektúráját (ENSA1) használja, az erőforrásokat az alábbiak szerint határozza meg:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Az SAP bevezette a 2. sorba helyezni-kiszolgáló, beleértve a replikálást, az SAP NW 7,52-támogatását. A ABAP platform 1809-től kezdődően a sorba helyezni Server 2 alapértelmezés szerint telepítve van. Lásd: SAP-Megjegyzés [2630416](https://launchpad.support.sap.com/#/notes/2630416) a sorba helyezni Server 2 támogatásához.
   Ha a sorba helyezni Server 2 architektúráját ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) használja, telepítse az erőforrás-ügynök erőforrás-ügynökök-SAP-4.1.1-12.el7.x86_64 vagy újabb verzióját, és adja meg az erőforrásokat az alábbiak szerint:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-QAS_ASCS then stop g-QAS_AERS symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Ha egy régebbi verzióról frissít, és átvált a 2. sorba helyezni-kiszolgálóra, tekintse meg a következőt: SAP Note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > A fenti konfiguráció időtúllépései csak példák, és előfordulhat, hogy az adott SAP-beállításhoz kell igazítani őket. 

   Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás el van indítva. Nem fontos, hogy az erőforrások melyik csomóponton futnak.

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]** tűzfalszabályok hozzáadása a ASCS és az ERS-hoz mindkét csomóponton adja hozzá a ASCS és a ERS-hoz tartozó tűzfalszabályok mindkét csomóponton.
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3201/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3201/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Az SAP NetWeaver Application Server előkészítése

   Egyes adatbázisokhoz szükséges, hogy az adatbázis példányának telepítése egy alkalmazáskiszolgáló legyen végrehajtva. Készítse elő az alkalmazáskiszolgáló virtuális gépeket, hogy azok használni tudják őket ezekben az esetekben.  

   Az ordító lépések azt feltételezik, hogy az alkalmazáskiszolgáló a ASCS/SCS és HANA kiszolgálóktól eltérő kiszolgálóra van telepítve. Ellenkező esetben az alábbi lépések (például az állomásnév-feloldás konfigurálása) nem szükségesek.  

   A (z) **[a]** előtaggal rendelkező következő elemek a Pas és az AAS esetében egyaránt érvényesek **[P]** – csak a Pas vagy **[S]** esetében érvényesek.  

1. **[A]** a telepítési állomásnév feloldása esetén használhat DNS-kiszolgálót, vagy módosíthatja a/etc/hosts az összes csomóponton. Ez a példa a/etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és a gazdagépet a következő parancsokra:  

   ```
   sudo vi /etc/hosts
   ```

   Szúrja be a következő sorokat a/etc/hosts. Módosítsa az IP-címet és a gazdagépet úgy, hogy az megfeleljen a környezetének.

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** hozza létre a sapmnt könyvtárat a Sapmnt könyvtár létrehozásához.
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** NFS-ügyfél és egyéb követelmények telepítése  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** csatlakoztatási bejegyzések hozzáadása  
   NFSv3 használata esetén:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   NFSv 4.1 használata esetén:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   Az új megosztások csatlakoztatása

   ```
   sudo mount -a
   ```

1. **[P]** a Pas-címtár létrehozása és csatlakoztatása  
   NFSv3 használata esetén:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   NFSv 4.1 használata esetén:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[S]** az AAS-címtár létrehozása és csatlakoztatása  
   NFSv3 használata esetén:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   NFSv 4.1 használata esetén:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[A] A** swap-fájl konfigurálása
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Az ügynök újraindítása a módosítás aktiválásához

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>Az adatbázis telepítése

Ebben a példában az SAP NetWeaver SAP HANAra van telepítve. A telepítéshez minden támogatott adatbázist használhat. A SAP HANA Azure-beli telepítésével kapcsolatos további információkért lásd: a [SAP HANA magas rendelkezésre állása az Azure-beli virtuális gépeken Red Hat Enterprise Linuxon][sap-hana-ha] . For a list of supported databases, see [SAP Note 1928533][1928533] .

1. Az SAP Database-példány telepítésének futtatása

   Telepítse az SAP NetWeaver Database-példányt root-ként egy olyan virtuális állomásnév használatával, amely az adatbázishoz tartozó terheléselosztó előtér-konfigurációjának IP-címére van leképezve.

   A sapinst paraméterrel SAPINST_REMOTE_ACCESS_USER engedélyezheti, hogy a nem root felhasználó csatlakozhasson a sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver alkalmazáskiszolgáló telepítése

Az SAP-alkalmazáskiszolgáló telepítéséhez kövesse az alábbi lépéseket.

1. Alkalmazáskiszolgáló előkészítése

   Az alkalmazáskiszolgáló előkészítéséhez kövesse az alábbi, az [SAP NetWeaver Application Server-előkészítés](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) című fejezet lépéseit.

1. Az SAP NetWeaver Application Server telepítése

   Telepítsen egy elsődleges vagy további SAP NetWeaver Application Servert.

   A sapinst paraméterrel SAPINST_REMOTE_ACCESS_USER engedélyezheti, hogy a nem root felhasználó csatlakozhasson a sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. SAP HANA Biztonságos tár frissítése

   Frissítse a SAP HANA biztonságos tárolót, hogy az SAP HANA rendszerreplikáció beállításának virtuális nevére mutasson.

   Futtassa a következő parancsot a bejegyzések adm-ként való listázásához. \<sapsid>

   ```
   hdbuserstore List
   ```

   Ennek az összes bejegyzést fel kell sorolnia, és a következőhöz hasonlóan kell kinéznie
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   A kimenet azt mutatja, hogy az alapértelmezett bejegyzés IP-címe a virtuális gépre mutat, nem pedig a terheléselosztó IP-címére. Ezt a bejegyzést úgy kell módosítani, hogy a terheléselosztó virtuális állomásneve mutasson. Ügyeljen arra, hogy ugyanazt a portot használja (a fenti kimenetben **30313** ) és az adatbázis nevét (a fenti kimenet **QAS** )!

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>A fürt beállításának tesztelése

1. A ASCS-példány manuális áttelepíteni

   Erőforrás állapota a teszt elindítása előtt:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Futtassa a következő parancsokat root-ként a ASCS-példány áttelepíteni.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Erőforrás állapota a teszt után:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Csomópont összeomlásának szimulálása

   Erőforrás állapota a teszt elindítása előtt:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Futtassa a következő parancsot gyökérként azon a csomóponton, amelyen a ASCS-példány fut

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   A csomópont újraindítása utáni állapotnak így kell kinéznie.

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   A sikertelen erőforrások tisztításához használja az alábbi parancsot.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Erőforrás állapota a teszt után:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Az üzenetküldési kiszolgáló folyamatának leölése

   Erőforrás állapota a teszt elindítása előtt:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   Futtassa az alábbi parancsokat root-ként az üzenet-kiszolgáló folyamatának azonosításához és a megöléséhez.

   ```
   [root@anftstsapcl1 ~]# pgrep -f ms.sapQAS | xargs kill -9
   ```

   Ha csak egyszer fogja megölni az üzenetet kiszolgálóját, a rendszer újraindítja `sapstart` . Ha elég gyakran megölni, a pacemaker végül áthelyezi a ASCS-példányt a másik csomópontra. A teszt után futtassa a következő parancsokat root-ként a ASCS és az ERS-példány erőforrás-állapotának tisztításához.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Erőforrás állapota a teszt után:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Sorba helyezni-kiszolgáló folyamatának leölése

   Erőforrás állapota a teszt elindítása előtt:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Futtassa a következő parancsokat gyökérként azon a csomóponton, amelyen a ASCS-példány fut, hogy megöli a sorba helyezni-kiszolgálót.

   ```
   #If using ENSA1
   [root@anftstsapcl2 ~]# pgrep -f en.sapQAS | xargs kill -9
   #If using ENSA2
   [root@anftstsapcl2 ~]# pgrep -f enq.sapQAS | xargs kill -9
   ```

   A ASCS-példánynak azonnal át kell vennie a feladatátvételt a másik csomópontra. Az ERS-példánynak a ASCS-példány elindítása után is feladatátvételt kell tennie. A teszt után futtassa a következő parancsokat root-ként a ASCS és az ERS-példány erőforrás-állapotának tisztításához.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Erőforrás állapota a teszt után:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Sorba helyezni-replikációs kiszolgáló folyamatának leölése

   Erőforrás állapota a teszt elindítása előtt:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Futtassa a következő parancsot gyökérként azon a csomóponton, amelyen az ERS-példány fut, hogy megöli a sorba helyezni replikációs kiszolgáló folyamatát.

   ```
   #If using ENSA1
   [root@anftstsapcl2 ~]# pgrep -f er.sapQAS | xargs kill -9
   #If using ENSA2
   [root@anftstsapcl2 ~]# pgrep -f enqr.sapQAS | xargs kill -9
   ```

   Ha csak egyszer futtatja a parancsot, `sapstart` a automatikusan újraindítja a folyamatot. Ha elég gyakran fut, a `sapstart` nem indítja újra a folyamatot, és az erőforrás leállított állapotba kerül. A teszt után futtassa a következő parancsokat root-ként az ERS-példány erőforrás-állapotának tisztításához.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Erőforrás állapota a teszt után:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Sorba helyezni sapstartsrv folyamatának leölése

   Erőforrás állapota a teszt elindítása előtt:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Futtassa a következő parancsokat gyökérként azon a csomóponton, amelyen a ASCS fut.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   A sapstartsrv folyamatot mindig újra kell indítani a pacemaker erőforrás-ügynöknek a figyelés részeként. Erőforrás állapota a teszt után:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>Következő lépések

* [HA SAP NW Azure-beli virtuális gépeken, RHEL for SAP Applications multi-SID útmutató](./high-availability-guide-rhel-multi-sid.md)
* [Azure Virtual Machines az SAP tervezéséhez és megvalósításához][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-ban][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz][dbms-guide]
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és hogyan tervezheti meg az Azure-beli SAP HANA vész-helyreállítását (nagyméretű példányok), tekintse meg [a SAP HANA (nagyméretű példányok) magas rendelkezésre állását és a](hana-overview-high-availability-disaster-recovery.md)
* A magas rendelkezésre állás és a SAP HANA Azure-beli virtuális gépeken történő vész-helyreállítási tervének megismeréséhez lásd: [Az Azure-beli SAP HANA magas rendelkezésre állása Virtual Machines (VM)][sap-hana-ha]
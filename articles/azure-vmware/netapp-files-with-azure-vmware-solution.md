---
title: Azure NetApp Files Azure VMware-megoldással
description: A Azure NetApp Files és az Azure VMware megoldás virtuális gépei segítségével áttelepítheti és szinkronizálhatja a helyszíni kiszolgálók, az Azure VMware-megoldás virtuális gépei és a Felhőbeli infrastruktúrák közötti adatátvitelt.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 69d4e3a99de28d55b2fd95b1fc05c04c2ae0a37b
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988655"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>Azure NetApp Files Azure VMware-megoldással

Ebben a cikkben végigvezeti a Azure NetApp Files integrálásának lépésein az Azure VMware megoldás-alapú számítási feladatokkal. A vendég operációs rendszer a virtuális gépeken (VM) belül fut Azure NetApp Files kötetek eléréséhez. 

## <a name="azure-netapp-files-overview"></a>Azure NetApp Files áttekintése

[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) egy Azure-szolgáltatás áttelepítésre és a legigényesebb vállalati fájl-számítási feladatok futtatására a felhőben. Ilyenek például az adatbázisok, az SAP és a nagy teljesítményű számítástechnikai alkalmazások, amelyek nem módosítanak programkódot.

### <a name="features"></a>Funkciók
(Szolgáltatások, ahol a Azure NetApp Files használatban vannak.)

- **Active Directory kapcsolatok**: a Azure NetApp Files támogatja [a Active Directory Domain Services és a Azure Active Directory Domain Services](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#decide-which-domain-services-to-use).

- **Megosztási protokoll**: a Azure NetApp Files támogatja a Server Message Block (SMB) és a hálózati fájlrendszer (NFS) protokollokat. Ez a támogatás azt jelenti, hogy a kötetek csatlakoztathatók a Linux-ügyfélhez, és a Windows-ügyfélen képezhetők le.

- **Azure VMware-megoldás**: Azure NetApp Files-megosztások csatlakoztathatók az Azure VMware-megoldási környezetben létrehozott virtuális gépekről.

Azure NetApp Files számos Azure-régióban elérhető, és támogatja a régiók közötti replikálást. További információ a Azure NetApp Files konfigurációs módszerekről: [Azure NetApp Files tárolási hierarchiája](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).

## <a name="reference-architecture"></a>Referenciaarchitektúra

Az alábbi ábra az Azure ExpressRoute keresztüli, egy Azure VMware-megoldáshoz tartozó privát felhőhöz való kapcsolódást ábrázolja. Az Azure VMware-megoldás környezete hozzáfér a Azure NetApp Files-megosztáshoz, amely az Azure VMware-megoldás virtuális gépekre van csatlakoztatva.

![Az Azure VMware megoldás architektúrájának NetApp-fájljait bemutató ábra.](media/net-app-files/net-app-files-topology.png)

Ez a cikk útmutatást nyújt a Azure NetApp Files kötet beállításához, teszteléséhez és ellenőrzéséhez az Azure VMware-megoldás virtuális gépei számára. Ebben az esetben az NFS protokollt használtuk. A Azure NetApp Files és az Azure VMware-megoldás ugyanabban az Azure-régióban jön létre.

## <a name="prerequisites"></a>Előfeltételek 

> [!div class="checklist"]
> * Azure-előfizetés Azure NetApp Files engedélyezve
> * Azure NetApp Files alhálózata
> * Linux virtuális gép Azure VMware-megoldással
> * Windows rendszerű virtuális gépek az Azure VMware megoldásban

## <a name="regions-supported"></a>Támogatott régiók

A támogatott régiók listája az [Azure Products By Region (régió) területen](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all)érhető el.

## <a name="verify-pre-configured-azure-netapp-files"></a>Előre konfigurált Azure NetApp Files ellenőrzése 

Az alábbi cikkekben ismertetett részletes útmutatást követve Azure NetApp Files köteteket hozhat létre és csatlakoztathat az Azure VMware-megoldás virtuális gépei számára.

- [NetApp-fiók létrehozása](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [Kapacitáskészlet beállítása](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [SMB-kötet létrehozása az Azure NetApp Files számára](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [NFS-kötet létrehozása az Azure NetApp Files számára](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [Alhálózat delegálása az Azure NetApp Fileshoz](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

A következő lépések bemutatják az Azure-ban az Azure NetApp Files Premium Service szintjén létrehozott előre konfigurált Azure NetApp Files ellenőrzését.

1. A Azure Portal **tároló** területén válassza a **Azure NetApp Files** lehetőséget. A konfigurált Azure NetApp Files listája jelenik meg. 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Az előre konfigurált Azure NetApp Files listáját bemutató képernyőkép."::: 

2. A beállítások megtekintéséhez válasszon ki egy konfigurált NetApp-fájl fiókot. Válassza például a **contoso-anf2** lehetőséget. 

3. Válassza a **Kapacitási készletek** elemet a konfigurált készlet ellenőrzéséhez. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Képernyőfelvétel a konfigurált NetApp-fájlokhoz tartozó kapacitási készletek és kötetek megtekintéséhez.":::

    Megnyílik a kapacitási készletek lap, amely a kapacitás és a szolgáltatás szintjét mutatja. Ebben a példában a Storage-készlet 4 TiB-ként van konfigurálva prémium szintű szolgáltatási szinttel.

4. Válassza a **kötetek** lehetőséget a kapacitási készletben létrehozott kötetek megtekintéséhez. (Lásd az előző képernyőképet.)

5. Válasszon ki egy kötetet a konfiguráció megtekintéséhez.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Képernyőfelvétel: a kapacitási készlet alatt létrehozott kötetek.":::

    Megnyílik egy ablak, amely a kötet konfigurációs részleteit jeleníti meg.

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="A kötet konfigurációs adatait bemutató képernyőkép.":::

    Láthatja, hogy a anfvolume kötet 200 GiB méretű, és a Capacity Pool anfpool1 van.  A rendszer NFS-fájlmegosztásként exportálja a 10.22.3.4-on keresztül:/ANFVOLUME. A rendszer létrehoz egy magánhálózati IP-címet az Azure Virtual Networkról (VNet) a Azure NetApp Fileshez és a virtuális gépen csatlakoztatni kívánt NFS-útvonalhoz. Ha többet szeretne megtudni a Azure NetApp Files kötet teljesítményéről vagy a "kvóta" értékről, tekintse meg [a Azure NetApp Files teljesítményével kapcsolatos szempontokat](../azure-netapp-files/azure-netapp-files-performance-considerations.md). 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>Az előre konfigurált Azure VMware-megoldás virtuálisgép-megosztási leképezésének ellenőrzése

Ahhoz, hogy egy Azure NetApp Files-megosztás elérhető legyen egy Azure VMware-megoldás virtuális gépe számára, fontos megérteni az SMB-és NFS-megosztások leképezését. Csak az SMB-vagy NFS-kötetek konfigurálása után csatlakoztathatók az itt dokumentált módon.

- SMB-megosztás: hozzon létre egy Active Directory-kapcsolatokat az SMB-kötet telepítése előtt. A sikeres kapcsolatok eléréséhez a megadott tartományvezérlőknek a Azure NetApp Files delegált alhálózatának kell elérhetőnek lennie. Miután a Active Directory konfigurálva lett a Azure NetApp Files-fiókban, az SMB-kötetek létrehozásakor választható elemként fog megjelenni.

- NFS-megosztás: a Azure NetApp Files a kötetek NFS vagy Dual Protocol (NFS és SMB) használatával történő létrehozását segíti elő. A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába. Az NFS-t a parancssori vagy az/etc/fstab-bejegyzések használatával lehet csatlakoztatni a Linux-kiszolgálóhoz.

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>Azure NetApp Files használata az Azure VMware-megoldással

A következőkben csak néhány meggyőző Azure NetApp Files használati eset van. 
- Horizon-profilok kezelése
- Citrix-profilok kezelése
- Távoli asztali szolgáltatások profil kezelése
- Fájlmegosztás az Azure VMware-megoldásban

## <a name="next-steps"></a>Következő lépések

Most, hogy az Azure VMware-megoldás számítási feladataival kezelte Azure NetApp Files integrálását, érdemes megismernie az alábbiakat:

- [A Azure NetApp Files erőforrás-korlátai](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits).
- [Útmutatás Azure NetApp Files hálózati tervezéshez](../azure-netapp-files/azure-netapp-files-network-topologies.md).
- [Azure NetApp Files kötetek régiók közötti replikációja](../azure-netapp-files/cross-region-replication-introduction.md). 
- [Azure NetApp Filesekkel kapcsolatos gyakori kérdések](../azure-netapp-files/azure-netapp-files-faqs.md).

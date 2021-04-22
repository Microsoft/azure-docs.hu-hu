---
title: Azure NetApp Files és Azure VMware Solution
description: A Azure NetApp Files virtuális gépekkel Azure VMware Solution helyszíni kiszolgálók, virtuális gépek és felhőalapú infrastruktúrák közötti adatokat Azure VMware Solution és szinkronizálhatja.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 09b9ba2ff6b95e12558b1ac49e401ce6dede4942
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870984"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>Azure NetApp Files és Azure VMware Solution

Ebben a cikkben a felhőalapú számítási feladatokkal Azure NetApp Files Azure VMware Solution lépéseit. A vendég operációs rendszer virtuális gépeken fut, amelyek Azure NetApp Files elérést. 

## <a name="azure-netapp-files-overview"></a>Azure NetApp Files áttekintés

[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) azure-szolgáltatás a migráláshoz és a legnagyobb erőforrás-igényű vállalati fájl-számítási feladatok felhőbeli futtatásához: adatbázisokhoz, SAP-hez és nagy teljesítményű számítási alkalmazásokhoz, kódváltozások nélkül.

### <a name="features"></a>Funkciók
(A szolgáltatások, Azure NetApp Files használják az szolgáltatásokat.)

- **Active Directory kapcsolatok:** a Azure NetApp Files támogatja [a Active Directory Domain Services és Azure Active Directory Domain Services.](../azure-netapp-files/create-active-directory-connections.md#decide-which-domain-services-to-use)

- **Share Protocol**: Azure NetApp Files támogatja a Server Message Block (SMB) és a hálózati fájlrendszer (NFS) protokolljait. Ez a támogatás azt jelenti, hogy a kötetek csatlakoztathatóak a Linux-ügyfélhez, és leképezhetőek Windows-ügyfélen.

- **Azure VMware Solution:** Azure NetApp Files-megosztások a virtuális környezetben létrehozott virtuális gépekről Azure VMware Solution csatlakoztathatóak.

Azure NetApp Files Azure-régióban érhető el, és támogatja a régiók közötti replikációt. További információ a Azure NetApp Files módszerekről: [Storage hierarchy of Azure NetApp Files.](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md)

## <a name="reference-architecture"></a>Referenciaarchitektúra

Az alábbi ábra a magánfelhőhöz Azure ExpressRoute kapcsolat Azure VMware Solution szemlélteti. A Azure VMware Solution környezet hozzáfér Azure NetApp Files virtuális gépekhez csatlakoztatott Azure VMware Solution megosztáshoz.

![Az architektúra NetApp Files Azure VMware Solution ábrája.](media/net-app-files/net-app-files-topology.png)

Ez a cikk a kötet fájlmegosztásként való beállítás Azure NetApp Files ának, tesztelésének és ellenőrzésének Azure VMware Solution útmutatását tartalmazza. Ebben a forgatókönyvben az NFS protokollt használtjuk. Azure NetApp Files és Azure VMware Solution azonos Azure-régióban vannak létrehozva.

## <a name="prerequisites"></a>Előfeltételek 

> [!div class="checklist"]
> * Azure-előfizetés engedélyezett Azure NetApp Files esetén
> * Alhálózat a Azure NetApp Files
> * Linux rendszerű virtuális gép a Azure VMware Solution
> * Windows rendszerű virtuális gépek a Azure VMware Solution

## <a name="regions-supported"></a>Támogatott régiók

A támogatott régiók listája az Azure-termékek régiók [szerint területen található.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all)

## <a name="verify-pre-configured-azure-netapp-files"></a>Ellenőrizze az előre konfigurált Azure NetApp Files 

Kövesse a következő cikkekben található részletes utasításokat a kötetek létrehozásához és Azure NetApp Files virtuális gépekhez Azure VMware Solution csatlakoztatáshoz.

- [NetApp-fiók létrehozása](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [Kapacitáskészlet beállítása](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [SMB-kötet létrehozása az Azure NetApp Files számára](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [NFS-kötet létrehozása az Azure NetApp Files számára](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [Alhálózat delegálása az Azure NetApp Fileshoz](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

A következő lépések tartalmazzák az Azure-ban Azure NetApp Files előre konfigurált erőforrások Azure NetApp Files Prémium szolgáltatási szinten.

1. A Azure Portal STORAGE **(TÁROLÁS) alatt** válassza **a** Azure NetApp Files lehetőséget. Megjelenik a konfigurált Azure NetApp Files listája. 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Képernyőkép az előre konfigurált Azure NetApp Files."::: 

2. Válasszon ki egy konfigurált NetApp Files-fiókot a beállításainak megtekintéséhez. Válassza például a **Contoso-anf2 lehetőséget.** 

3. A **konfigurált készlet ellenőrzéséhez** válassza a Kapacitáskészletek lehetőséget. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Képernyőkép a konfigurált NetApp Files-fiók kapacitáskészletei és kötetei megtekintésének lehetőségeiről.":::

    Megnyílik a Kapacitáskészletek lap, amely a kapacitást és a szolgáltatási szintet mutatja. Ebben a példában a tárolókészlet 4 TiB-ként van konfigurálva prémium szolgáltatási szinttel.

4. Válassza **a Kötetek** lehetőséget a kapacitáskészlet alatt létrehozott kötetek megtekintéséhez. (Lásd az előző képernyőképet.)

5. Válasszon ki egy kötetet a konfiguráció megtekintéséhez.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="A kapacitáskészlet alatt létrehozott köteteket bemutató képernyőkép.":::

    Megnyílik egy ablak, amely a kötet konfigurációs adatait mutatja.

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Képernyőkép egy kötet konfigurációs részleteiről.":::

    Láthatja, hogy az anfvolume 200 GiB méretű, és az anfpool1 kapacitáskészletben található. NFS-fájlmegosztásként lesz exportálva a 10.22.3.4:/ANFVOLUME-n keresztül. Az Azure Virtual Network (VNet) egy magánhálózati IP-címet hozott létre Azure NetApp Files és az NFS-útvonalat a virtuális géphez való csatlakoztatáshoz.

    A kötetteljesítmény méret vagy Azure NetApp Files "Kvóta" alapján való teljesítményével kapcsolatos további információkért lásd: Teljesítménybeli [megfontolások a Azure NetApp Files.](../azure-netapp-files/azure-netapp-files-performance-considerations.md) 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>Előre konfigurált virtuálisgép-megosztások Azure VMware Solution ellenőrzése

Ahhoz, hogy Azure NetApp Files a virtuális gépe Azure VMware Solution számára, tisztában kell lennie az SMB- és NFS-megosztásleképezéssel. Csak az SMB- vagy NFS-kötetek konfigurálása után csatlakoztathatja őket az itt dokumentáltak szerint.

- SMB-megosztás: Hozzon létre Active Directory kapcsolatot az SMB-kötet üzembe helyezése előtt. A sikeres csatlakozás érdekében a megadott tartományvezérlőknek elérhetőnek kell lenniük a Azure NetApp Files alhálózata számára. Miután a Active Directory konfigurálva van a Azure NetApp Files-fiókban, az kiválasztható elemként fog megjelenni az SMB-kötetek létrehozásakor.

- NFS-megosztás: Azure NetApp Files nfs vagy kettős protokoll (NFS és SMB) használatával segít a kötetek létrehozásában. A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába. Az NFS a Linux-kiszolgálóhoz a parancssorok vagy az /etc/fstab bejegyzésekkel csatlakoztatható.

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>Az alkalmazással Azure NetApp Files esetek Azure VMware Solution

Az alábbiakban csupán néhány meggyőző Azure NetApp Files közül. 
- Horizontprofil-kezelés
- Citrix-profilkezelés
- Távoli asztali szolgáltatások profilkezelés
- Fájlmegosztások a Azure VMware Solution

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan integrálhatja Azure NetApp Files a Azure VMware Solution számítási feladatokkal, a következővel ismerkedhet meg:

- [Erőforráskorlátok a Azure NetApp Files](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits)
- [Az Azure NetApp Files hálózattervezési irányelvei](../azure-netapp-files/azure-netapp-files-network-topologies.md)
- [Kötetek régiók közötti Azure NetApp Files replikálása](../azure-netapp-files/cross-region-replication-introduction.md) 
- [Gyakori kérdések az Azure NetApp Filesról](../azure-netapp-files/azure-netapp-files-faqs.md)

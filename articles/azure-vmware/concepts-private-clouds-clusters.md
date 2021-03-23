---
title: Fogalmak – privát felhők és fürtök
description: Ismerje meg az Azure VMware Solution szoftver által meghatározott adatközpontok és vSphere-fürtök főbb képességeit.
ms.topic: conceptual
ms.date: 03/13/2021
ms.openlocfilehash: aff66e01ae4b056eb082d2000611718b1a5cf66a
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773920"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Azure VMware-megoldás – saját felhő és fürt – fogalmak

Az Azure VMware-megoldás VMware-alapú privát felhőket biztosít az Azure-ban. A privát felhők dedikált, operációs rendszer nélküli Azure-gazdagépekkel létrehozott fürtöket tartalmaznak. Ezeket a Azure Portal, a CLI vagy a PowerShell segítségével telepítik és kezelik.  A privát felhőkben kiépített fürtök közé tartozik a VMware vSphere, a vCenter, a vSAN és a NSX szoftver. Az Azure VMware megoldás saját Felhőbeli hardveres és szoftveres üzembe helyezése teljesen integrált és automatizált az Azure-ban.

Logikai kapcsolat áll fenn az Azure-előfizetések, az Azure VMware megoldás privát felhők, a vSAN-fürtök és a gazdagépek között. A diagram egyetlen Azure-előfizetést mutat be két privát felhőből, amelyek a fejlesztési és a termelési környezetet képviselik.  Ezekben a privát felhőkben két fürt található. 

Ez a cikk az alábbi fogalmakat ismerteti.

![Az ügyfél-előfizetés két privát felhők képe](./media/hosts-clusters-private-clouds-final.png)


## <a name="private-clouds"></a>Magánfelhők

A privát felhők dedikált, operációs rendszer nélküli Azure-gazdagépekkel létrehozott vSAN-fürtöket tartalmaznak. Mindegyik privát felhő több, azonos vCenter-kiszolgálóval és NSX-T kezelővel felügyelt fürttel is rendelkezhet. A privát felhők üzembe helyezése és kezelése a portálon, a CLI-ben vagy a PowerShellben végezhető el. 

Más erőforrásokhoz hasonlóan a privát felhők telepítése és felügyelete egy Azure-előfizetésen belül történik. Az előfizetésen belüli privát felhők száma méretezhető. Kezdetben egy privát felhő korlátja van egy előfizetéshez.

## <a name="clusters"></a>Fürtök
Minden létrehozott privát felhőhöz alapértelmezés szerint egy vSAN-fürt van. A fürtöket a Azure Portal vagy az API segítségével adhatja hozzá, törölheti és méretezheti.  Az összes fürt alapértelmezett mérete három gazdagép, és akár 16 gazdagép is méretezhető. Saját felhőben legfeljebb négy fürt lehet.

A próbaverziós fürtök kiértékelésre és három gazdagépre korlátozódnak. Privát felhőben egyetlen próbaverziós fürt van. A próbaidőszak alatt egyetlen gazdagépen is méretezheti a próbaverziós fürtöt.

A vSphere és a NSX-T Manager használatával kezelheti a fürtkonfiguráció vagy a műveletnek a legtöbb egyéb aspektusát. A fürt minden gazdagépének helyi tárterülete a vSAN felügyelete alatt áll.

## <a name="hosts"></a>Hosts

Az Azure VMware megoldási fürtök Hyper-konvergens, operációs rendszer nélküli infrastruktúrán alapulnak. A következő táblázat a gazdagép RAM-, CPU-és lemezterület-kapacitását mutatja be.

| Állomás típusa              |             CPU             |   Memória (GB)   |  vSAN NVMe gyorsítótárazási szintje (TB, nyers)  |  vSAN SSD kapacitási szintje (TB, nyers)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| AVS36          |  Dual Intel 18 Core 2,3 GHz  |     576      |                3.2               |                15,20               |

A fürtök létrehozásához vagy méretezéséhez használt gazdagépek a gazdagépek elkülönített készletéből származnak. Ezek a gazdagépek hardveres teszteket adtak át, és minden adattal biztonságosan törölve lettek. 

## <a name="vmware-software-versions"></a>VMware-szoftverek verziói

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]

## <a name="update-frequency"></a>Frissítési gyakoriság

[!INCLUDE [vmware-software-update-frequency](includes/vmware-software-update-frequency.md)]

## <a name="host-maintenance-and-lifecycle-management"></a>Gazdagép-karbantartás és életciklus-kezelés

A gazdagép-karbantartási és életciklus-kezelés nincs hatással a saját Felhőbeli fürtök kapacitására vagy teljesítményére.  Az automatizált gazdagépek karbantartására például a belső vezérlőprogram frissítése és a hardver javítása vagy cseréje tartozik.

A Microsoft felelős a NSX-T készülékek életciklus-kezelésért, például a NSX-T Manager és a NSX-T Edge. A Microsoft feladata a hálózati konfiguráció beindításának elvégzése, például a 0. rétegbeli átjáró létrehozása és az North-South útválasztás engedélyezése. Ön felelős a NSX-T SDN-konfigurációért. Ilyenek például a hálózati szegmensek, az elosztott tűzfalszabályok, az 1. rétegbeli átjárók és a terheléselosztó.

## <a name="backup-and-restoration"></a>Biztonsági mentés és helyreállítás

A Private Cloud vCenter és a NSX-T konfigurációk óránkénti biztonsági mentési ütemtervtel rendelkeznek.  A biztonsági mentések három napig tartanak. Ha biztonsági másolatból kell visszaállítani, nyisson meg egy [támogatási kérést](https://rc.portal.azure.com/#create/Microsoft.Support) a Azure Portal a visszaállítás kéréséhez.

## <a name="next-steps"></a>Következő lépések

Most, hogy az Azure VMware-megoldás saját Felhőbeli fogalmait is érintette, érdemes megismernie a következőket: 

- [Az Azure VMware megoldás hálózatkezelési és összekapcsolási fogalmai](concepts-networking.md).
- [Azure VMware megoldás tárolási fogalmak](concepts-storage.md).
- [Az Azure VMware megoldás erőforrásának engedélyezése](enable-azure-vmware-solution.md).

<!-- LINKS - internal -->
[concepts-networking]: ./concepts-networking.md

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753


---
title: Fogalmak – privát felhők és fürtök
description: Ismerje meg az Azure VMware Solution szoftver által meghatározott adatközpontok és vSphere-fürtök főbb képességeit.
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 960187629c5dc1120b720ec9009276fa3f313e5d
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538924"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Azure VMware-megoldás – saját felhő és fürt – fogalmak

Az Azure VMware-megoldás VMware-alapú privát felhőket biztosít az Azure-ban. A privát felhők dedikált, operációs rendszer nélküli Azure-gazdagépekkel létrehozott fürtöket tartalmaznak. Ezeket a Azure Portal, a CLI vagy a PowerShell segítségével telepítik és kezelik.  A privát felhőkben kiépített fürtök közé tartozik a VMware vSphere, a vCenter, a vSAN és a NSX szoftver. Az Azure VMware megoldás saját Felhőbeli hardveres és szoftveres üzembe helyezése teljesen integrált és automatizált az Azure-ban.

Logikai kapcsolat áll fenn az Azure-előfizetések, az Azure VMware megoldás privát felhők, a vSAN-fürtök és a gazdagépek között. A diagram egyetlen Azure-előfizetést mutat be két privát felhőből, amelyek a fejlesztési és a termelési környezetet képviselik.  Ezekben a privát felhőkben két fürt található. 

Ez a cikk az alábbi fogalmakat ismerteti.

![Az ügyfél-előfizetés két privát felhők képe](./media/hosts-clusters-private-clouds-final.png)

>[!NOTE]
>A fejlesztési környezet alacsonyabb lehetséges igényei miatt alacsonyabb kapacitású gazdagépeket használó kisebb fürtöket használjon. 

## <a name="private-clouds"></a>Magánfelhők

A privát felhők dedikált, operációs rendszer nélküli Azure-gazdagépekkel létrehozott vSAN-fürtöket tartalmaznak. Mindegyik privát felhő több, azonos vCenter-kiszolgálóval és NSX-T kezelővel felügyelt fürttel is rendelkezhet. A privát felhők üzembe helyezése és kezelése a portálon, a CLI-ben vagy a PowerShellben végezhető el. 

Más erőforrásokhoz hasonlóan a privát felhők telepítése és felügyelete egy Azure-előfizetésen belül történik. Az előfizetésen belüli privát felhők száma méretezhető. Kezdetben egy privát felhő korlátja van egy előfizetéshez.

## <a name="clusters"></a>Fürtök
Minden létrehozott privát felhőhöz alapértelmezés szerint egy vSAN-fürt van. A fürtöket a Azure Portal vagy az API segítségével adhatja hozzá, törölheti és méretezheti.  Az összes fürt alapértelmezett mérete három gazdagép, és akár 16 gazdagép is méretezhető.  A fürtben használt gazdagépeknek azonos típusúnak kell lenniük.

A próbaverziós fürtök kiértékelésre és három gazdagépre korlátozódnak. Privát felhőben egyetlen próbaverziós fürt van. A próbaidőszak alatt egyetlen gazdagépen is méretezheti a próbaverziós fürtöt.

A vSphere és a NSX-T Manager használatával kezelheti a fürtkonfiguráció vagy a műveletnek a legtöbb egyéb aspektusát. A fürt minden gazdagépének helyi tárterülete a vSAN felügyelete alatt áll.

## <a name="hosts"></a>Hosts

Az Azure VMware-megoldás saját felhőalapú fürtök Hyper-konvergens, operációs rendszer nélküli infrastruktúra-gazdagépeket használnak. A következő táblázat a gazdagép RAM-, CPU-és lemezterület-kapacitását mutatja be. 

| Állomás típusa              |             CPU             |   Memória (GB)   |  vSAN NVMe gyorsítótárazási szintje (TB, nyers)  |  vSAN SSD kapacitási szintje (TB, nyers)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| High-End ()          |  Dual Intel 18 Core 2,3 GHz  |     576      |                3.2               |                15,20               |

A fürtök létrehozásához vagy méretezéséhez használt gazdagépek a gazdagépek elkülönített készletéből származnak. Ezek a gazdagépek hardveres teszteket adtak át, és minden adattal biztonságosan törölve lettek. 

## <a name="vmware-software-versions"></a>VMware-szoftverek verziói

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


## <a name="host-maintenance-and-lifecycle-management"></a>Gazdagép-karbantartás és életciklus-kezelés

A gazdagép-karbantartási és életciklus-kezelés nincs hatással a saját Felhőbeli fürtök kapacitására vagy teljesítményére.  Az automatizált gazdagépek karbantartására például a belső vezérlőprogram frissítése és a hardver javítása vagy cseréje tartozik.

A Microsoft felelős a NSX-T készülékek életciklus-kezelésért, például a NSX-T Manager és a NSX-T Edge. Emellett a hálózati konfiguráció rendszerindításához is felelősek, például a 0. rétegbeli átjáró létrehozása és a North-South útválasztás engedélyezése. Ön felelős a NSX-T SDN-konfigurációért. Ilyenek például a hálózati szegmensek, az elosztott tűzfalszabályok, az 1. rétegbeli átjárók és a terheléselosztó.

> [!IMPORTANT]
> Ne módosítsa a NSX-T Edge vagy a 0. rétegbeli átjáró konfigurációját, mivel ez a szolgáltatás elvesztését eredményezheti.

## <a name="backup-and-restoration"></a>Biztonsági mentés és helyreállítás

A Private Cloud vCenter és a NSX-T konfigurációk óránkénti biztonsági mentési ütemtervtel rendelkeznek.  A biztonsági mentések három napig tartanak. Ha biztonsági másolatból kell visszaállítani, nyisson meg egy [támogatási kérést](https://rc.portal.azure.com/#create/Microsoft.Support) a Azure Portal a visszaállítás kéréséhez.

## <a name="next-steps"></a>Következő lépések

Most, hogy az Azure VMware-megoldás saját Felhőbeli fogalmait is érintette, érdemes megismernie a következőket: 

- [Az Azure VMware megoldás hálózatkezelési és összekapcsolási fogalmai](concepts-networking.md).
- [Azure VMware megoldás tárolási fogalmak](concepts-storage.md).
- [Az Azure VMware megoldás erőforrásának engedélyezése](enable-azure-vmware-solution.md).

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753


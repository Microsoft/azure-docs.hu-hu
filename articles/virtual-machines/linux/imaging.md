---
title: Linux-rendszerképek létrehozása az Azure-ban – áttekintés
description: Hogyan hozhatja létre a linuxos virtuálisgép-lemezképeket, vagy létrehozhat új rendszerképeket az Azure-ban való használatra.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: a77c2bc69418b821933c0b62674500f7a32e40a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565206"
---
# <a name="bringing-and-creating-linux-images-in-azure"></a>Linux-rendszerképek készítése és létrehozása az Azure-ban

Ez az Áttekintés a képalkotással kapcsolatos alapvető fogalmakat és a Linux-lemezképek Azure-beli sikeres felépítését és használatát ismerteti. Mielőtt egyéni rendszerképet hozna az Azure-ba, tisztában kell lennie az Ön számára elérhető típusokkal és lehetőségekkel.

Ez a cikk a rendszerképekkel kapcsolatos döntési pontokon és követelményeken keresztül tárgyal, ismerteti a főbb fogalmakat, így követheti ezt, és saját egyéni rendszerképeit is létrehozhatja a specifikációban.

## <a name="difference-between-managed-disks-and-images"></a>A felügyelt lemezek és a lemezképek közötti különbség


Az Azure lehetővé teszi, hogy egy virtuális merevlemezt a platformra, [felügyelt lemezként](../faq-for-disks.md#managed-disks)használja, vagy a lemezkép forrásaként használja. 

Az Azure Managed Disks egyetlen virtuális merevlemez. Létrehozhat egy meglévő VHD-t, és létrehozhat egy felügyelt lemezt, vagy létrehozhat egy üres, felügyelt lemezt. Létrehozhat virtuális gépeket a felügyelt lemezekről úgy, hogy csatlakoztatja a lemezt a virtuális géphez, de csak egy virtuális géppel rendelkező virtuális merevlemezt tud használni. Az operációs rendszer tulajdonságai nem módosíthatók, az Azure csak a virtuális gép bekapcsolását és a lemez használatának megkezdését kísérli meg. 

Az Azure-lemezképek több operációsrendszer-lemezből és adatlemezből is készíthetők. Ha felügyelt rendszerképet használ a virtuális gép létrehozásához, a platform másolatot készít a rendszerképről, és ezt használja a virtuális gép létrehozásához, így a felügyelt rendszerkép támogatja ugyanazt a rendszerképet több virtuális gép esetében. Az Azure speciális felügyeleti funkciókat is biztosít a rendszerképekhez, például a globális replikáláshoz és a [megosztott](../shared-image-galleries.md)képkatalóguson keresztül történő verziószámozáshoz. 



## <a name="generalized-and-specialized"></a>Általánosított és specializált

Az Azure két fő képtípust kínál, általánosítva és specializálva. Az általánosított és specializált kifejezések eredetileg Windows-feltételek, amelyek az Azure-ba migrálva vannak. Ezek a típusok határozzák meg, hogy a platform hogyan fogja kezelni a virtuális gépet, amikor bekapcsolja. Mindkét típushoz előnyök és hátrányok tartoznak, és előfeltételei vannak. Az első lépések előtt tudnia kell, hogy milyen típusú rendszerképekre lesz szüksége. Az alábbi összefoglalja a forgatókönyveket és a szükséges típusokat:

| Eset      | Lemezkép típusa  | Tárolási lehetőségek |
| ------------- |:-------------:| :-------------:| 
| Hozzon létre egy olyan rendszerképet, amely több virtuális gép általi használatra is konfigurálható, és beállítható az állomásnév, rendszergazdai felhasználó hozzáadása és egyéb feladatok végrehajtása az első rendszerindítás során. | Általánosított | Megosztott képgyűjtemény vagy önálló felügyelt lemezképek |
| Rendszerkép létrehozása VM-pillanatképből vagy biztonsági másolatból | Specializált |Megosztott képgyűjtemény vagy felügyelt lemez |
| Gyorsan létrehozhat egy olyan rendszerképet, amely nem igényel konfigurációt több virtuális gép létrehozásához |Specializált |Megosztott rendszerkép-katalógus |


### <a name="generalized-images"></a>Általánosított rendszerképek

Az általánosított rendszerkép olyan rendszerkép, amely az első rendszerindításkor a telepítés befejezését igényli. Az első rendszerindításkor például az állomásnév, a rendszergazda felhasználó és más virtuálisgép-specifikus konfigurációk állíthatók be. Ez akkor hasznos, ha azt szeretné, hogy a rendszer többször is felhasználja a képet, és ha a létrehozás során paramétereket szeretne megadni a paraméterekben. Ha az általánosított rendszerkép tartalmazza az Azure-ügynököt, akkor az ügynök feldolgozza a paramétereket, és visszaküldi azt a platformra, amelyen a kezdeti konfiguráció befejeződött. Ezt a folyamatot [üzembe](./provisioning.md)helyezésnek nevezzük. 

A kiépítés megköveteli, hogy a rendszerkép tartalmazza a kiépítés részét. Két kiépítés létezik:
- [Azure Linux-ügynök](../extensions/agent-linux.md)
- [Cloud-init](./using-cloud-init.md)

Ezek a rendszerképek létrehozásának [előfeltételei](./create-upload-generic.md) .


### <a name="specialized-images"></a>Speciális rendszerképek
Ezek olyan rendszerképek, amelyek teljesen konfigurálva vannak, és nem igénylik a virtuális gépeket és a speciális paramétereket, a platform csak a virtuális gépet kapcsolja be, a virtuális gépen egyedivé kell tennie, mint például a hostname beállítása, hogy elkerülje a DNS-ütközéseket ugyanazon a VNET. 

Ezekhez a lemezképekhez nem szükségesek kiépítési ügynökök, azonban lehetséges, hogy a bővítmények kezelési képességei is rendelkezésre állnak. Telepítheti a Linux-ügynököt, de letilthatja a létesítési lehetőséget. Annak ellenére, hogy nincs szüksége kiépítési ügynökre, a rendszerképnek meg kell felelnie az Azure-lemezképek [előfeltételeinek](./create-upload-generic.md)  .


## <a name="image-storage-options"></a>Rendszerkép-tárolási beállítások
A Linux-rendszerkép létrehozásakor két lehetőség közül választhat:

- Felügyelt lemezképek egyszerű virtuális gépek létrehozásához fejlesztési és tesztelési környezetben.
- [Megosztott](../shared-image-galleries.md) képkatalógus a rendszerképek méretének létrehozásához és megosztásához.


### <a name="managed-images"></a>Felügyelt lemezképek

A felügyelt lemezképek több virtuális gép létrehozásához is használhatók, de számos korlátozással rendelkeznek. A felügyelt lemezképek csak általánosított forrásból (VM vagy VHD) hozhatók létre. A virtuális gépeket csak ugyanabban a régióban lehet létrehozni, és az előfizetések és a bérlők között nem oszthatók meg.

A felügyelt lemezképek fejlesztési és tesztelési környezetekhez használhatók, ahol néhány egyszerű általánosított lemezképre van szükség az egyetlen régióban és előfizetésben való használathoz. 

### <a name="azure-shared-image-gallery-sig"></a>Azure Shared Képgaléria (SIG)

A [megosztott képtárakat](../shared-image-galleries.md) a lemezképek nagy léptékű létrehozásához, kezeléséhez és megosztásához ajánljuk. A megosztott képtárak segítenek felépíteni a lemezképek körét a szerkezet és a szervezet számára.  

- Az általánosított és a speciális rendszerképek támogatása.
- Az 1. és a 2. generációs lemezképek támogatása.
- Lemezképek globális replikálása.
- A könnyebb felügyelet érdekében a rendszerképek verziószámozása és csoportosítása.
- Az Availability Zonest támogató régiókban található, a zóna redundáns tárolásával (ZRS) rendelkező, magasan elérhető rendszerképek. A ZRS nagyobb rugalmasságot biztosít a zónabeli hibákkal szemben.
- Az Azure RBAC használatával megoszthatja az előfizetéseket, és akár Active Directory (AD) bérlők között is.
- Az üzembe helyezések skálázása minden egyes régióban képreplikákkal.

Magas szinten létre kell hoznia egy SIG-t, amely az alábbiakból áll:
- Képdefiníciók – ezek olyan tárolók, amelyek képcsoportokat tárolnak.
- Lemezkép-verziók – ezek a tényleges lemezképek



## <a name="hyper-v-generation"></a>Hyper-V generáció

Az Azure támogatja a Hyper-V 1. generációs (Gen1) és a 2. generációs (Gen2), a Gen2 a legújabb generációt, és további funkciókat nyújt a Gen1-hoz. Például: megnövekedett memória, Intel Software Guard Extensions (Intel SGX ENKLÁVÉHOZ) és virtualizált állandó memória (vPMEM). A 2. generációs virtuális gépek a helyszínen futnak, az Azure-ban még nem támogatott funkciók vannak. További információ: szolgáltatások és képességek szakasz. További információkért tekintse meg ezt a [cikket](../generation-2.md). Ha további funkciókra van szüksége, hozzon létre Gen2-lemezképeket.

Ha továbbra is létre kell hoznia egy saját rendszerképet, győződjön meg arról, hogy megfelel a [rendszerkép előfeltételeinek](./create-upload-generic.md), és feltölti az Azure-ba. Terjesztési specifikus követelmények:


- [CentOS-alapú disztribúciók](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES és openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)


## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan hozhat létre [megosztott képtárat](tutorial-custom-images.md).
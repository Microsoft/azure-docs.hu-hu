---
title: Migrálás AWS-ről és más platformokról az Azure-ba való Managed Disks
description: Hozzon létre virtuális gépeket az Azure-ban más felhőktől (AWS vagy más virtualizációs platformok) feltöltött VHD-k használatával, és használja ki az Azure-Managed Disks
author: roygara
manager: twooley
ms.service: virtual-machines
ms.subervice: disks
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c817528ff26dd10112eaf69c7ad20f8fb5813695
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550399"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migrálás Amazon Web Servicesról (AWS) és más platformokról az Azure-ba Managed Disks

Az AWS-ből vagy helyszíni virtualizálási megoldásokból származó VHD-fájlokat az Azure-ba feltöltve olyan virtuális gépeket hozhat létre, amelyek kihasználják Managed Disks. Az Azure Managed Disks eltávolítja az Azure IaaS virtuális gépekhez tartozó Storage-fiókok felügyeletének szükségességét. Csak a szükséges típust (prémium vagy standard) és a lemez méretét kell megadnia, az Azure pedig létrehozza és kezeli a lemezt. 

Feltöltheti az általánosított és a speciális virtuális merevlemezeket is. 
- **Általánosított VHD** – az összes személyes fiók adata el lett távolítva a Sysprep használatával. 
- **Speciális VHD** – karbantartja az eredeti virtuális gépről származó felhasználói fiókokat, alkalmazásokat és egyéb állapotinformációkat. 

> [!IMPORTANT]
> A virtuális merevlemezek Azure-ba való feltöltése előtt kövesse az Azure-ba való [feltöltéshez szükséges Windows VHD vagy VHDX előkészítését](prepare-for-upload-vhd-image.md) ismertető témakört.
>
>


| Eset                                                                                                                         | Dokumentáció                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Meglévő AWS EC2-példányokkal rendelkezik, amelyeket felügyelt lemezek használatával szeretne áttelepíteni az Azure-beli virtuális gépekre                              | [Virtuális gép áthelyezése Amazon Web Servicesból (AWS) az Azure-ba](aws-to-azure.md)                           |
| Van olyan virtuális gépe, amelyet egy másik virtualizációs platformon szeretne használni több Azure-beli virtuális gép létrehozásához. | [Töltse fel az általánosított virtuális merevlemezt, és használja az új virtuális gép létrehozásához az Azure-ban](upload-generalized-managed.md) |
| Rendelkezik egy egyedileg testre szabott virtuális géppel, amelyet újra létre szeretne hozni az Azure-ban.                                                      | [Speciális virtuális merevlemez feltöltése az Azure-ba, és új virtuális gép létrehozása](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>A Managed Disks áttekintése

Az Azure Managed Disks leegyszerűsíti a virtuális gépek felügyeletét azáltal, hogy eltávolítja a Storage-fiókok felügyeletének szükségességét. A Managed Disks a rendelkezésre állási csoportokban lévő virtuális gépek jobb megbízhatóságát is kihasználhatja. Gondoskodik arról, hogy a rendelkezésre állási csoportokban lévő különböző virtuális gépek lemezei elég elszigeteltek legyenek egymástól, hogy elkerülje az egyes meghibásodási pontokat. A szolgáltatás automatikusan áthelyezi a különböző virtuális gépek lemezeit egy rendelkezésre állási csoportba a különböző tárolási skálázási egységekben (bélyegzők), ami korlátozza a hardveres és a szoftver meghibásodása miatti egyetlen tárolási skálázási egység meghibásodásának hatását.
Az igények alapján négyféle tárolási lehetőség közül választhat. A rendelkezésre álló lemez típusok megismeréséhez tekintse meg a [lemez típusának kiválasztása](../disks-types.md)című cikket.

## <a name="plan-for-the-migration-to-managed-disks"></a>A Managed Disksre való Migrálás megtervezése

Ez a szakasz segíti a legjobb döntést a virtuális gépek és a lemezek típusairól.

Ha nem felügyelt lemezekről felügyelt lemezekre kíván áttelepítést végrehajtani, vegye figyelembe, hogy a virtuálisgép-közreműködő szerepkörrel rendelkező felhasználók nem változtathatják meg a [virtuális gép](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) méretét (ahogy a konverzió megtörtént). Ennek az az oka, hogy a felügyelt lemezekkel rendelkező virtuális gépeken a felhasználónak rendelkeznie kell a Microsoft. számítás/lemezek/írási engedéllyel az operációsrendszer-lemezeken.

### <a name="location"></a>Hely

Válasszon ki egy helyet, ahol elérhetők az Azure Managed Disks. Ha prémium szintű Managed Disksra végez áttelepítést, győződjön meg arról, hogy a Premium Storage elérhető abban a régióban, ahol az áttelepítést tervezi. Tekintse meg az [Azure-szolgáltatások régiónként](https://azure.microsoft.com/regions/#services) az elérhető helyszínekről szóló naprakész információkat.

### <a name="vm-sizes"></a>A virtuális gépek mérete

Ha prémium szintű Managed Disksra végez áttelepítést, frissítenie kell a virtuális gép méretét, hogy Premium Storage a virtuális gép helyét tartalmazó régióban elérhető méretet. Tekintse át a Premium Storage képes virtuális gépek méretét. Az Azure-beli virtuális gép méretének specifikációi a [virtuális gépek méretei](../sizes.md)szerint vannak felsorolva.
Tekintse át a Premium Storaget használó virtuális gépek teljesítményének jellemzőit, és válassza ki a legmegfelelőbb virtuálisgép-méretet, amely legjobban megfelel a számítási feladatnak. Győződjön meg arról, hogy elegendő sávszélesség áll rendelkezésre a virtuális gépen a lemez forgalmának elvégzéséhez.

### <a name="disk-sizes"></a>Lemezek mérete

**Prémium Managed Disks**

A virtuális géppel, valamint a IOPs és az átviteli sebességével kapcsolatban hét különféle prémium szintű felügyelt lemez használható. Vegye figyelembe ezeket a korlátokat, amikor az alkalmazás igényeinek megfelelően kiválasztja a prémium szintű lemez típusát a kapacitás, a teljesítmény, a méretezhetőség és a maximális terhelés tekintetében.

| Prémium szintű lemezek típusa  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Lemezméret           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS-érték lemezenként       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| Adattovábbítás lemezenként | 25 MB/másodperc  | 50 MB/másodperc  | 100 MB/másodperc | 125 MB/másodperc |150 MB/másodperc | 200 MB/másodperc | 250 MB/másodperc | 250 MB/másodperc |

**Standard Managed Disks**

A virtuális géppel a standard szintű felügyelt lemezek hét típusa használható. Mindegyiknek külön kapacitása van, de azonos a IOPS és az átviteli sebesség korlátaival. Válassza ki a szabványos felügyelt lemezek típusát az alkalmazás kapacitási igényei alapján.

| Standard lemez típusa  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Lemezméret           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| IOPS-érték lemezenként       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Adattovábbítás lemezenként | 60 MB/másodperc | 60 MB/másodperc | 60 MB/másodperc | 60 MB/másodperc |60 MB/másodperc | 60 MB/másodperc | 60 MB/másodperc | 60 MB/másodperc | 

### <a name="disk-caching-policy"></a>Lemezes gyorsítótárazási házirend 

**Prémium Managed Disks**

Alapértelmezés szerint a lemezes gyorsítótárazási házirend a prémium szintű adatlemezek esetében *csak olvasható* , és a virtuális géphez csatlakoztatott prémium operációsrendszer-lemezre írható *írás* . Ez a konfigurációs beállítás ajánlott az alkalmazás IOs-es optimális teljesítményének eléréséhez. A írható vagy írható adatlemezek (például SQL Server naplófájlok) esetében tiltsa le a lemezes gyorsítótárazást, hogy jobban elérhető legyen az alkalmazás teljesítménye.

### <a name="pricing"></a>Díjszabás

Tekintse át a [Managed Disks díjszabását](https://azure.microsoft.com/pricing/details/managed-disks/). A prémium szintű Managed Disks díjszabása megegyezik a prémium nem felügyelt lemezekkel. A standard szintű Managed Disks díjszabása azonban eltér a szabványos nem felügyelt lemezektől.


## <a name="next-steps"></a>Következő lépések

- A virtuális merevlemezek Azure-ba való feltöltése előtt kövesse az Azure-ba való [feltöltéshez szükséges Windows VHD vagy VHDX előkészítését](prepare-for-upload-vhd-image.md) ismertető témakört.
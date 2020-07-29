---
title: Azure VMware-megoldás CloudSimple – a csomópontok áttekintése
description: Ismerje meg a CloudSimple-csomópontokat és-fogalmakat.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65afe26a98a53b00b72a1ea2b49799db2049b727
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77024925"
---
# <a name="cloudsimple-nodes-overview"></a>A CloudSimple-csomópontok áttekintése

A csomópontok a privát felhő építőelemei. Csomópont:

* Dedikált operációs rendszer nélküli számítási gazdagép, amelyen telepítve van egy VMware ESXi hypervisor  
* Saját felhők létrehozásához vagy lefoglalásához használható számítási egység
* Elérhető egy olyan régióban való üzembe helyezéshez vagy fenntartáshoz, ahol a CloudSimple szolgáltatás elérhető

Létre kell hoznia egy privát felhőt a kiépített csomópontokból. Privát felhő létrehozásához legalább három csomópontra van szükség ugyanahhoz az SKU-hoz. Privát felhő kibontásához adjon hozzá további csomópontokat.  Csomópontokat adhat hozzá egy meglévő fürthöz, vagy létrehozhat egy új fürtöt a Azure Portal csomópontjainak kiépítés, és társíthatja őket a CloudSimple szolgáltatáshoz.  Az összes kiépített csomópont látható a CloudSimple szolgáltatás alatt.  

## <a name="provisioned-nodes"></a>Kiépített csomópontok

A kiépített csomópontok utólagos elszámolású kapacitást biztosítanak. A kiépítési csomópontok segítségével gyorsan méretezheti igény szerint a VMware-fürtöt. Szükség szerint csomópontokat adhat hozzá, vagy törölhet egy kiépített csomópontot a VMware-fürt méretezéséhez. A kiépített csomópontok számlázása havonta történik, és a kiépített előfizetéshez kell fizetni.

* Ha hitelkártyával fizet az Azure-előfizetésért, a kártyát azonnal kiszámlázzák.
* Ha számlán számlázunk, a díjak a következő számlán jelennek meg.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware-megoldás CloudSimple-csomópontok SKU-ban

A következő típusú csomópontok érhetők el a kiépítés vagy a foglalás számára.

| Termékváltozat           | CS28 – csomópont                 | CS36 – csomópont                 | CS36m – csomópont                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Régió        | USA keleti régiója, USA nyugati régiója            | USA keleti régiója, USA nyugati régiója            | Nyugat-Európa                 |
| CPU           | 2x 2.2 GHz, 28 mag (56 HT) | 2x 2.3 GHz, 36 mag (72 HT) | 2x 2.3 GHz, 36 mag (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Lemez gyorsítótára    | 1,6 – TB NVMe                 | 3,2 – TB NVMe                 | 3,2 – TB NVMe                 |
| Kapacitás lemez | 5,625 TB nyers                | 11,25 TB nyers                | 15,36 TB nyers                |
| Tárhelytípusa  | Minden Flash                   | Minden Flash                   | Minden Flash                   |

## <a name="limits"></a>Korlátok

A következő csomópont-korlátok a privát felhőkre vonatkoznak.

| Erőforrás | Korlát |
|----------|-------|
| A privát felhő létrehozásához szükséges csomópontok minimális száma | 3 |
| Fürtben lévő csomópontok maximális száma egy privát felhőben | 16 |
| A privát felhőben lévő csomópontok maximális száma | 64 |
| Csomópontok minimális száma egy új fürtön | 3 |

## <a name="next-steps"></a>További lépések

* További információ a [csomópontok](create-nodes.md) kiépítéséről
* További információ a [privát felhőkről](cloudsimple-private-cloud.md)

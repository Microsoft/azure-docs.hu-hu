---
title: Azure VMware-megoldás CloudSimple – a csomópontok áttekintése
description: Ismerje meg a CloudSimple-fogalmakat, például a csomópontokat, a kiépített csomópontokat, a privát felhőket és a VMware-megoldásokat CloudSimple-csomópontok SKU-ban.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7de4b5faa997d909089daedab7e48e5d5a6de2e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88140836"
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
| Region        | USA keleti régiója, USA nyugati régiója            | USA keleti régiója, USA nyugati régiója            | Nyugat-Európa                 |
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

## <a name="next-steps"></a>Következő lépések

* További információ a [csomópontok](create-nodes.md) kiépítéséről
* További információ a [privát felhőkről](cloudsimple-private-cloud.md)

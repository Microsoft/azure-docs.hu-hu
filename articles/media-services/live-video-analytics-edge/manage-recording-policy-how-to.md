---
title: Rögzítési szabályzat kezelése – Azure
description: Ez a témakör a rögzítési szabályzat kezelését ismerteti.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: ec72f28496c1392b9d95134c343e1892998a0c28
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99224989"
---
# <a name="manage-recording-policy"></a>Felvételkészítési szabályzat kezelése

Élő videó-elemzést használhat IoT Edge a [folyamatos videofelvételek rögzítéséhez](continuous-video-recording-concept.md), amelyekkel hetek vagy hónapok szerint rögzíthet videókat a felhőbe. A Felhőbeli Archívum hossza (nap) az Azure Storage-ba beépített életciklus- [kezelési eszközökkel](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal) kezelhető.  

A Media Service-fiók egy Azure Storage-fiókhoz van csatolva, és amikor videót rögzít a felhőbe, a tartalmat egy Media Service- [eszközbe](../latest/assets-concept.md)írja a rendszer. Minden eszköz egy tárolóhoz van leképezve a Storage-fiókban. Az életciklus-kezelés lehetővé teszi, hogy megadjon egy [házirendet](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#policy) egy Storage-fiókhoz, amelyben megadhat egy [szabályt](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#rules) , például az alábbiakat.

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

A fenti szabály:

* A Storage-fiókban lévő összes blokk blobra vonatkozik.
* Azt határozza meg, hogy amikor a Blobok kora meghaladja a 30 napot, a rendszer áthelyezi őket a [gyors elérési szintről a ritka elérésű](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)
* És amikor a Blobok 90 napon túli korukat vesznek fel, azokat törölni kell.

Ha élő videó-elemzést használ egy eszközre való adatrögzítéshez, meg kell adnia egy `segmentLength` tulajdonságot, amely megadja, hogy a modul a felhőbe való írás előtt minimális időtartamú videót (másodpercben) összesítse. Az eszköz több szegmenst is tartalmaz, amelyek mindegyike egy olyan létrehozási időbélyeggel rendelkezik, amely `segmentLength` újabb, mint az előző. Ha az életciklus-kezelési házirend beindul, a a megadott küszöbértéknél régebbi szegmenseket töröl. A Media Service API-kon keresztül azonban továbbra is elérheti és lejátszhatja a fennmaradó szegmenseket. További információ: [felvételek visszajátszása](playback-recordings-how-to.md). 

## <a name="limitations"></a>Korlátozások

Az alábbiakban néhány ismert korlátozást talál az életciklus-kezeléssel kapcsolatban:

* A szabályzat legfeljebb 100 szabályt tartalmazhat, és minden szabály legfeljebb 10 tárolót határozhat meg. Tehát ha különböző rögzítési szabályzatokra van szüksége (például 3 napos Archívum a parkolóba kerülő kamerához, 30 nap a betöltési Dockon, a kamera pedig 180 nap), majd egy Media Service-fiókkal testreszabhatja a legtöbb 1000 kamera szabályait.
* Az életciklus-kezelési házirend frissítései nem azonnaliek. További részletekért tekintse meg [ezt a GYIK szakaszt](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq) .
* Ha úgy dönt, hogy olyan házirendet alkalmaz, amelyben a Blobok átkerülnek a lassú szintre, akkor az Archívum ezen részének lejátszására is hatással lehet. Előfordulhat, hogy további késések vagy szórványos hibák jelentkeznek. A Media Services nem támogatja a tartalom lejátszását az archiválási szinten.

## <a name="next-steps"></a>Következő lépések

[Felvételek lejátszása](playback-recordings-how-to.md)

---
title: A beszédfelismerési szolgáltatás inaktív adatok titkosítása
titleSuffix: Azure Cognitive Services
description: A Microsoft felügyeli a Microsoft által kezelt titkosítási kulcsokat, valamint lehetővé teszi az Cognitive Services-előfizetések kezelését a saját kulcsaival, az úgynevezett ügyfél által felügyelt kulcsokkal (CMK). Ez a cikk a beszédfelismerési szolgáltatásban tárolt adatok titkosítását ismerteti.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: aa0fe33dff0161767b74546aad49003d8fc70c16
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95015256"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>A beszédfelismerési szolgáltatás inaktív adatok titkosítása

A beszédfelismerési szolgáltatás automatikusan titkosítja az adatait, amikor azt megőrzi a felhőben. A beszédfelismerési szolgáltatás titkosítása védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

## <a name="about-cognitive-services-encryption"></a>Tudnivalók a Cognitive Services titkosításról

Az adattitkosítás és visszafejtés az [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) -kompatibilis [256 bites AES-](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) titkosítás használatával történik. A titkosítás és a visszafejtés átlátható, ami azt jelenti, hogy a titkosítás és a hozzáférés felügyelve van. Az adatok alapértelmezés szerint védettek, a titkosítás használatához pedig sem a kódot, sem az alkalmazást nem kell módosítania.

## <a name="about-encryption-key-management"></a>A titkosítási kulcsok kezelése

A Custom Speech és az egyéni hang használatakor a beszédfelismerési szolgáltatás a következő Felhőbeli adattárolást is elvégezheti:  

* Beszédfelismerési adat – csak akkor, ha a nyomkövetést bekapcsolja az egyéni végponthoz
* Feltöltött képzések és tesztelési célú adatfeldolgozás

Alapértelmezés szerint az adatai a Microsoft tárolójában tárolódnak, és az előfizetése a Microsoft által felügyelt titkosítási kulcsokat használja. Lehetősége van saját Storage-fiók előkészítésére is. Az áruházhoz való hozzáférést a felügyelt identitás kezeli, és a beszédfelismerési szolgáltatás nem tud közvetlenül hozzáférni a saját adataihoz, például a beszédfelismerési adatokhoz, a testreszabási adatokhoz és az egyéni modellekhez.

A felügyelt identitással kapcsolatos további információkért lásd: [Mik a felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Saját tároló (BYOS) használata a testreszabáshoz és a naplózáshoz

Ha hozzáférést szeretne kérni a saját tárterületének eléréséhez, töltse ki és küldje el a [beszédfelismerési szolgáltatást – a saját tároló (BYOS) kérelmi űrlapját](https://aka.ms/cogsvc-cmk). A jóváhagyást követően létre kell hoznia a saját Storage-fiókját a testreszabáshoz és a naplózáshoz szükséges adattároláshoz. Storage-fiók hozzáadásakor a Speech Service-erőforrás engedélyezi a rendszer által hozzárendelt felügyelt identitást. Ha engedélyezve van a rendszerhez rendelt felügyelt identitás, az erőforrás regisztrálva lesz Azure Active Directory (HRE). A regisztrálást követően a felügyelt identitás hozzáférést kap a Storage-fiókhoz. A felügyelt identitásokról itt talál további információt. A felügyelt identitással kapcsolatos további információkért lásd: [Mik a felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md).

> [!IMPORTANT]
> Ha letiltja a rendszerhez rendelt felügyelt identitásokat, a Storage-fiókhoz való hozzáférés el lesz távolítva. Ez azt eredményezi, hogy a beszédfelismerési szolgáltatás azon részei, amelyek hozzáférést igényelnek a Storage-fiókhoz a működésének leállásához.  

A beszédfelismerési szolgáltatás jelenleg nem támogatja a Ügyfélszéf. Az ügyféladatok azonban a BYOS használatával is tárolhatók, így a [Ügyfélszéfhoz](../../security/fundamentals/customer-lockbox-overview.md)hasonló adatellenőrzéseket érhet el. Ne feledje, hogy a beszédfelismerési szolgáltatás adatmennyisége és feldolgozása abban a régióban történik, ahol a beszédfelismerési erőforrás létrejött. Ez a nyugalmi állapotban tárolt adatokra és a tranzitban tárolt adatokra is vonatkozik. A testreszabási funkciók, például a Custom Speech és az egyéni hang használatakor az összes ügyfél-adat átvitele, tárolása és feldolgozása ugyanabban a régióban történik, ahol a BYOS (ha használatban van) és a Speech Service-erőforrás található.

> [!IMPORTANT]
> A Microsoft **nem** használja fel az ügyféladatokat, hogy javítsa a beszédfelismerési modelljeit. Emellett, ha a végpont-naplózás le van tiltva, és nem használ testreszabást, akkor a rendszer nem tárolja az ügyféladatokat. 

## <a name="next-steps"></a>Következő lépések

* [Beszédfelismerési szolgáltatás – saját tároló (BYOS) kérésének űrlapja](https://aka.ms/cogsvc-cmk)
* [Mik azok a felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md).
---
title: Azure Marketplace IoT Edge modul ajánlatok
description: Tudnivalók az Azure Marketplace-en elérhető IoT Edge-modulok közzétételéről.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 08/18/2020
ms.openlocfilehash: 492571ac76acfcf388954a4b714b6603aced7338
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107669"
---
# <a name="iot-edge-modules"></a>IoT Edge-modulok

A [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) platformot Microsoft Azure támogatja.  Ez a platform lehetővé teszi a felhasználók számára, hogy közvetlenül a IoT-eszközökön futtassák a Felhőbeli számítási feladatokat.  Az IoT Edge-modulok offline számítási feladatokat futtathatnak, és helyileg is végezhetik az adatelemzést. Ez az ajánlat típusa segít a sávszélesség megtakarításában, a helyi és bizalmas adatok védelmében, és alacsony késésű válaszidőt biztosít.  Ezzel a lehetőséggel kihasználhatja ezeket az előre elkészített számítási feladatokat. Eddig csak néhány, a Microsofttól származó, első féltől származó megoldás érhető el.  Az időt és az erőforrásokat a saját egyéni IoT-megoldásainak kiépítése során kellett befektetni.

[Az Azure Marketplace IoT Edge moduljaival](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)most már egyetlen célhelyet kínálunk a kiadók számára, hogy elérhetővé tegye és eladják megoldásait a IoT-közönség számára. A IoT-fejlesztők végső soron megtalálhatják és megvásárolhatják a megoldás-fejlesztés felgyorsításához szükséges képességeket.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>A IoT Edge-modulok főbb előnyei az Azure Marketplace-en

| **Közzétevők számára**    | **Ügyfelek számára (IoT-fejlesztők)**  |
| :------------------- | :-------------------|
| Több millió fejlesztőt érhet el IoT Edge megoldások létrehozásához és üzembe helyezéséhez.  | Hozzon létre egy IoT Edge megoldást a biztonságos és tesztelt összetevők használatának megbízhatóságával. |
| Tegye közzé egyszer, és futtassa az összes olyan IoT Edge hardvert, amely támogatja a tárolókat. | Csökkentse a piacra kerülési időt azáltal, hogy megkeresi és üzembe helyezi az 1. és a harmadik féltől származó IoT Edge-modulokat. |
| Pénzbevétel rugalmas számlázási lehetőségekkel <ul> <li> Ingyenes és saját licenc használata (BYOL). </li> </ul> | A vásárlásokat a választott számlázási modellekkel teheti meg. <ul> <li> Ingyenes és saját licenc használata (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Mi az IoT Edge modul?

Azure IoT Edge lehetővé teszi az üzleti logikák üzembe helyezését és kezelését modulok formájában. A Azure IoT Edge modulok a IoT Edge által kezelt legkisebb számítási egységek, és tartalmazhatnak Microsoft-szolgáltatásokat (például Azure Stream Analytics), harmadik féltől származó szolgáltatásokat vagy a saját megoldásra vonatkozó kódokat. IoT Edge modulokról további információt a [Azure IoT Edge modulok ismertetése](../iot-edge/iot-edge-modules.md)című témakörben talál.

**Mi a különbség a Container ajánlat típusa és az IoT Edge modul típusú ajánlat között?**

A IoT Edge modul ajánlat típusa egy IoT Edge eszközön futó adott típusú tároló. A szolgáltatás az alapértelmezett konfigurációs beállításokat használja a IoT Edge környezetben való futtatáshoz, és opcionálisan a IoT Edge modul SDK-val is integrálható a IoT Edge futtatókörnyezettel.

## <a name="publishing-your-iot-edge-module"></a>A IoT Edge-modul közzététele

**A megfelelő online áruház kiválasztása**

IoT Edge modulokat csak az Azure Marketplace-en tesszük közzé; A AppSource nem érvényes. További információ az online áruházak közötti különbségekről: [a közzétételi lehetőség meghatározása](determine-your-listing-type.md).

**Számlázási módok**

A piactér jelenleg **ingyenes** , és **saját licenc (BYOL)** számlázási lehetőségeket biztosít IoT Edge modulokhoz.

### <a name="publishing-options"></a>Közzétételi beállítások

IoT Edge moduloknak minden esetben ki kell választaniuk a **Transact** Publishing beállítást.  A közzétételi beállításokkal kapcsolatos további részletekért tekintse meg [a közzétételi lehetőség kiválasztása](determine-your-listing-type.md) című témakört.  

## <a name="eligibility-criteria"></a>Jogosultsági feltételek

A Microsoft Azure Marketplace-szerződések és-szabályzatok minden feltétele IoT Edge modul-ajánlatok esetében érvényes.  Emellett a IoT Edge modulokra vonatkozó előfeltételeket és műszaki követelményeket is megkövetelnek.  

### <a name="prerequisites"></a>Előfeltételek

IoT Edge modul Azure Marketplace-en való közzétételéhez meg kell felelnie a következő előfeltételeknek:

- Hozzáférés a partner központhoz. További információ: [kereskedelmi Piactéri fiók létrehozása a partner Centerben](create-account.md).
- IoT Edge moduljának üzemeltetése egy Azure Container Registry.
- A IoT Edge modul metaadatainak készen állnak, például (nem teljes lista):
    - Egy cím
    - Leírás (HTML formátumban)
    - Egy embléma képe (48 x 48 (opcionális), 90 x 90 (opcionális) és 216 x 216 – 350 x 350 px, mind PNG formátumban
    - Használati feltételek és adatvédelmi szabályzat
    - Alapértelmezett modul konfigurációja (Route, Twin kívánt tulajdonságok, createOptions, környezeti változók)
    - Dokumentáció
    - Támogatási kapcsolattartók

### <a name="technical-requirements"></a>Technikai követelmények

Az IoT Edge modul elsődleges műszaki követelményei, hogy az informatikai támogatás és közzététel az Azure Marketplace-en, részletesen megtalálhatók a [IoT Edge modul előkészítésének technikai eszközein](./partner-center-portal/create-iot-edge-module-asset.md).

## <a name="next-steps"></a>Következő lépések

- Az ajánlat létrehozásához vagy befejezéséhez jelentkezzen be a [partner Centerben](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) .
- [Hozzon létre egy IoT Edge modul ajánlatot](./partner-center-portal/azure-iot-edge-module-creation.md) a partner Centerben.

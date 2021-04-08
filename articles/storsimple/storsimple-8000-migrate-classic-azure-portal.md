---
title: StorSimple migrálása Eszközkezelő Storage-fiókok, előfizetések
description: Ismerje meg, hogyan telepítheti át a StorSimple Eszközkezelő-szolgáltatás előfizetéseit, a Storage-fiókokat.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 523788dc727fc5fa32b2f57250fb738352ff6d31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96491520"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>A StorSimple-eszközkezelő szolgáltatáshoz tartozó előfizetések és tárfiókok migrálása

Előfordulhat, hogy át kell helyeznie a StorSimple szolgáltatást egy új regisztrációra vagy egy új előfizetésre. Ezek az áttelepítési forgatókönyvek a fiókok változásai vagy az adatközpont változásai. A következő táblázat segítségével megtudhatja, hogy mely forgatókönyvek támogatottak, beleértve az áthelyezés részletes lépéseit.

## <a name="account-changes"></a>Fiók módosítása

| Áthelyezhető...| Támogatott| Állásidő| Azure-támogatási folyamat| Módszer|
|-----|-----|-----|-----|-----|
| Egy teljes előfizetés (tartalmazza a StorSimple és a Storage-fiókokat) egy másik beléptetésre? | Igen       | Nem       | **Beléptetési átvitel**<br>Használja<li>Új szerződés keretében új Azure-kötelezettségvállalás vásárlása esetén.</li><li>Az összes fiókot és előfizetést át szeretné telepíteni a régi regisztrációból az új verzióra. Ez magában foglalja az összes Azure-szolgáltatást a régi előfizetés alatt.</li> | **1. lépés: nyisson meg egy Azure Enterprise Operation támogatási jegyet.**<li>Ugrás a következőre: [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) .</li><li> Válassza a **beléptetési felügyelet** lehetőséget, majd válassza az **átvitel az egyik regisztrációból egy új regisztrációra** lehetőséget.<br>**2. lépés: a kért információk megadása**<br>Tartalmazza<li>forrás beléptetési száma</li><li> célhely beléptetési száma</li><li>átvitel érvényességi dátuma|
| StorSimple szolgáltatást egy meglévő fiókból egy új regisztrációra?    | Igen       | Nem       | **Fiók átvitele**<br>Használja<li>Ha nem szeretne teljes beléptetési átvitelt végezni.</li><li>Csak bizonyos fiókokat szeretne áthelyezni egy új regisztrációra.</li>| **1. lépés: nyisson meg egy Azure Enterprise Operation támogatási jegyet.**<li>Ugrás a következőre: [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) .</li><li>Válassza a **beléptetési felügyelet** lehetőséget, majd válassza **az EA-fiók átvitele új regisztrációra** lehetőséget.<br>**2. lépés: a kért információk megadása**<br>Tartalmazza<li>forrás beléptetési száma</li><li> célhely beléptetési száma</li><li>átvitel érvényességi dátuma|
| StorSimple szolgáltatást egy előfizetésből egy másik előfizetésbe?      | Nem        |    Igen         | Nincs, manuális folyamat|<li>Az adatáttelepítés a StorSimple-eszközről.</li><li>A gyári beállítások visszaállításával az eszközön minden helyi adat törlődik.</li><li>Regisztrálja az eszközt az új előfizetéssel egy StorSimple Eszközkezelő szolgáltatáshoz.</li><li>Telepítse újra az adatátvitelt az eszközre.|
|Átadhatom egy Azure-előfizetés tulajdonjogát egy másik könyvtárba? | Igen       | Nem       | Meglévő előfizetés hozzárendelése az Azure AD-címtárhoz | Tekintse át a [meglévő előfizetés hozzárendelése az Azure ad-címtárhoz](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)című témakört. Akár 10 percig is eltarthat, amíg minden megfelelően megjelenik.|
| StorSimple az eszközt az egyik StorSimple Eszközkezelő szolgáltatásból egy másik régióban lévő szolgáltatásba?      | Nem        | Igen            | Nincs, manuális folyamat |Lásd fentebb.|
| A Storage-fiók egy új előfizetéshez vagy erőforráscsoporthoz?     | Igen        | Nem             |A Storage-fiók áthelyezése másik előfizetésre vagy erőforráscsoport-csoportba |Ha az áthelyezés után a Storage-fiók hozzáférési kulcsai frissülnek, a felhasználónak manuálisan kell konfigurálnia a hozzáférési kulcsokat az áttelepített Storage-fiókhoz a StorSimple Eszközkezelő szolgáltatáson keresztül.|
| Klasszikus Storage-fiók Azure Resource Manager Storage-fiókhoz      | Igen        | Nem             |Migrálás a klasszikusból Azure Resource Manager-alapú környezetbe |<li>A Storage-fiókok klasszikusról Azure Resource Managerra való áttelepítésével kapcsolatos részletes információkért látogasson el [a klasszikus Storage-fiók áttelepítésére](../virtual-machines/migration-classic-resource-manager-ps.md#step-52-migrate-a-storage-account).</li><li> Ha a Storage-fiók hozzáférési kulcsainak frissítése az áttelepítés után történik, a felhasználónak szinkronizálnia kell az áttelepített Storage-fiók hozzáférési kulcsait a StorSimple Eszközkezelő szolgáltatáson keresztül. Ezzel biztosítható, hogy a StorSimple-eszközök továbbra is szabályosan működjenek, és képesek legyenek az elsődleges/biztonsági mentési adat az Azure-ba való beosztására. A hozzáférési kulcsok szinkronizálásával kapcsolatos részletes utasításokért lásd: [rotációs munkafolyamat](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> StorSimple Cloud Appliance esetén, ha a klasszikus Storage-fiókot áttelepítik, de a mögöttes virtuális gép továbbra is klasszikus állapotban marad, a berendezésnek megfelelően működnie kell. Ha a felhőalapú berendezéshez tartozó mögöttes virtuális gép át lett telepítve, akkor az Inaktiválás és a törlés funkció nem fog működni.</li><li> Létre kell hoznia egy új StorSimple felhőalapú készüléket a Azure Portal, majd a feladatátvételt a régebbi felhőalapú készülékekről. Klasszikus Storage-fiók használatával nem hozhat létre StorSimple Cloud Appliance az új Azure Portal, ezért Azure Resource Manager Storage-fiókkal kell rendelkeznie. További információért látogasson el a [StorSimple Cloud Appliance üzembe helyezéséhez és kezeléséhez](storsimple-8000-cloud-appliance-u2.md).</li>|

## <a name="datacenter-changes"></a>Adatközpont módosításai

| Áthelyezhető...| Támogatott|Állásidő| Azure-támogatási folyamat| Módszer|
|-----|-----|-----|-----|-----|
| Egy StorSimple szolgáltatás az egyik Azure-adatközpontból a másikba? | Nem | Igen |Nincs, manuális folyamat  |<li>Az adatáttelepítés a StorSimple-eszközről.</li><li>A gyári beállítások visszaállításával az eszközön minden helyi adat törlődik.</li><li>Regisztrálja az eszközt az új előfizetéssel egy új StorSimple Eszközkezelő szolgáltatáshoz.</li><li>Telepítse újra az adatátvitelt az eszközre.|
| Egy Azure-adatközpontból egy másikba tartozó Storage-fiók? | Nem |Igen  |Nincs, manuális folyamat  | Lásd fentebb.|

## <a name="next-steps"></a>Következő lépések

* [StorSimple Eszközkezelő szolgáltatás üzembe helyezése](storsimple-8000-manage-service.md)
* [A StorSimple 8000 Series eszköz üzembe helyezése Azure Portal](storsimple-8000-deployment-walkthrough-u2.md)
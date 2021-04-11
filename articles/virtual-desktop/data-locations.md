---
title: Adatterületek a Windows rendszerű virtuális asztali környezethez – Azure
description: Röviden áttekintheti, hogy a Windows rendszerű virtuális asztali adatok és metaadatok mely helyein vannak tárolva.
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a4c63cc686b08d179e20e6f3e3a7aa1efa69a5f8
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447080"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>A Windows rendszerű virtuális asztali adatok és metaadatok helyei

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/data-locations-2019.md).

A Windows virtuális asztal jelenleg minden földrajzi helyen elérhető. A rendszergazdák kiválaszthatják, hogy a virtuális gépek és a társított szolgáltatások, például a fájlkiszolgálók létrehozásakor hol tárolhatják a felhasználói adataikat. Tudjon meg többet az Azure földrajzi helyekről az [Azure Datacenter térképen](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>A Microsoft nem szabályozza és nem korlátozza azokat a régiókat, amelyeken Ön vagy felhasználói hozzáférhetnek a felhasználóhoz és az alkalmazásra vonatkozó adataihoz.

>[!IMPORTANT]
>A Windows rendszerű virtuális asztali szolgáltatás a globális metaadat-információkat, például a bérlői neveket, az alkalmazáskészletek nevét, az alkalmazás-csoportok nevét és az egyszerű felhasználóneveket tárolja egy adatközpontban Amikor egy ügyfél létrehoz egy szolgáltatási objektumot, meg kell adnia a szolgáltatás objektumának helyét. A megadott hely határozza meg, hogy az objektum metaadatai hol lesznek tárolva. Az ügyfél kiválaszt egy Azure-régiót, és a metaadatokat a kapcsolódó földrajz fogja tárolni. Az összes Azure-régió és kapcsolódó földrajzi terület listáját itt tekintheti meg: [Azure földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/)területek.

Jelenleg támogatjuk a metaadatok tárolását a következő földrajzi területeken:

- Egyesült Államok (USA) (általánosan elérhető)
- Európa (EU) (nyilvános előzetes verzió) 

>[!NOTE]
> Amikor kiválaszt egy régiót a Windows rendszerű virtuális asztali szolgáltatás objektumainak létrehozásához a alkalmazásban, az Egyesült Államokban és az EU földrajzi területein is megjelennek a régiók. Tekintse meg az [Azure globális infrastruktúra-térképét](https://azure.microsoft.com/global-infrastructure/geographies/#geographies), hogy megtudja, melyik régió működne a legjobban az üzembe helyezéshez.

A tárolt metaadatok a nyugalmi állapotban vannak titkosítva, és a Geo-redundáns tükrözések a földrajzon belül maradnak. Az összes vásárlói adat, például az Alkalmazásbeállítások és a felhasználói adat, az ügyfél által választott helyen található, és nem a szolgáltatás kezeli. A szolgáltatás növekedésével több földrajzi terület is elérhetővé válik.

A szolgáltatás metaadatainak replikálását az Azure-ban, a katasztrófa utáni helyreállítás érdekében replikálja a rendszer.
---
title: Adathelyek a Windows Virtual Desktop – Azure
description: Rövid áttekintés arról, hogy Windows Virtual Desktop adatok és metaadatok mely helyeken vannak tárolva.
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: eeba3cb579c6ef9158379403a3206f99a2cfb060
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830627"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Adatok és metaadatok helye Windows Virtual Desktop

>[!IMPORTANT]
>Ez a tartalom a Windows Virtual Desktop és Azure Resource Manager Windows Virtual Desktop vonatkozik. Ha a klasszikus (Windows Virtual Desktop klasszikus) Azure Resource Manager használ, tekintse meg [ezt a cikket.](./virtual-desktop-fall-2019/data-locations-2019.md)

Windows Virtual Desktop jelenleg minden földrajzi helyhez elérhető. A rendszergazdák kiválaszthatják a felhasználói adatok tárolására használt helyet a gazdagépkészlet virtuális gépei és a társított szolgáltatások, például fájlkiszolgálók létrehozásakor. Az Azure-beli földrajzi helyekkel kapcsolatos további információkért olvassa el az [Azure adatközpont-térképét.](https://azuredatacentermap.azurewebsites.net/)

>[!NOTE]
>A Microsoft nem korlátozza vagy korlátozza a régiókat, ahol Ön vagy a felhasználók hozzáférhetnek a felhasználóhoz és az alkalmazásspecifikus adatokhoz.

>[!IMPORTANT]
>Windows Virtual Desktop globális metaadatokat tárol, például a munkaterületek nevét, a gazdagépkészletek nevét, az alkalmazáscsoport-neveket és az egyszerű felhasználóneveket egy adatközpontban. Amikor egy ügyfél létrehoz egy szolgáltatásobjektumot, meg kell adnia a szolgáltatásobjektum helyét. A beírtak helye határozza meg, hogy a rendszer hol tárolja az objektum metaadatait. Az ügyfél egy Azure-régiót választ, a metaadatok pedig a kapcsolódó földrajzi helyen lesznek tárolva. Az összes Azure-régiót és a kapcsolódó földrajzi helyeket az Azure földrajzi [helyeket felsoroló listájában láthatja.](https://azure.microsoft.com/global-infrastructure/geographies/)

A metaadatok tárolása jelenleg a következő földrajzi helyen támogatott:

- Egyesült Államok (USA) (általánosan elérhető)
- Európa (EU) (nyilvános előzetes verzió) 

>[!NOTE]
> Amikor kiválaszt egy régiót, ahol szolgáltatásobjektumokat Windows Virtual Desktop, az Egyesült Államok és az EU földrajzi régiói alatt is megjelennek a régiók. Annak érdekében, hogy biztosan tudja, melyik régió lenne a legmegfelelőbb az üzembe helyezéshez, nézze meg az Azure globális [infrastruktúra-térképét.](https://azure.microsoft.com/global-infrastructure/geographies/#geographies)

A tárolt metaadatok titkosítva vannak, és a georedundáns tükrözés a földrajzi helyen belül van fenntartva. Minden ügyféladat, például az alkalmazásbeállítások és a felhasználói adatok az ügyfél által választott helyen található, és nem a szolgáltatás kezeli. A szolgáltatás növekedésével egyre több földrajzi hely lesz elérhető.

A szolgáltatás metaadatait vészhelyreállítási célokból az Azure földrajzi helyen replikálja a rendszer.

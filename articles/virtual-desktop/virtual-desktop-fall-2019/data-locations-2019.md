---
title: Adatterületek a Windows rendszerű virtuális asztali környezethez (klasszikus) – Azure
description: Röviden áttekintheti, hogy a Windows rendszerű virtuális asztali (klasszikus) adatok és metaadatok mely helyei vannak tárolva.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 869defde657c9cb4c8bea6bbacebb9458e5a2b96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008814"
---
# <a name="data-locations-for-windows-virtual-desktop-classic"></a>Adatterületek a Windows rendszerű virtuális asztali környezethez (klasszikus)

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat. Ha Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../data-locations.md).

A Windows virtuális asztal jelenleg minden földrajzi helyen elérhető. Kezdetben a szolgáltatás metaadatainak csak a Egyesült Államok (US) földrajzban tárolhatók. A rendszergazdák kiválaszthatják, hogy a virtuális gépek és a társított szolgáltatások, például a fájlkiszolgálók létrehozásakor hol tárolhatják a felhasználói adataikat. Tudjon meg többet az Azure földrajzi helyekről az [Azure Datacenter térképen](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>A Microsoft nem szabályozza és nem korlátozza azokat a régiókat, amelyeken Ön vagy felhasználói hozzáférhetnek a felhasználóhoz és az alkalmazásra vonatkozó adataihoz.

>[!IMPORTANT]
>A Windows rendszerű virtuális asztali szolgáltatás a globális metaadat-információkat, például a bérlői neveket, a gazdagépek nevét, az alkalmazások csoportjának nevét, valamint az egyszerű felhasználóneveket a Egyesült Államok található adatközpontban tárolja. A tárolt metaadatok a nyugalmi állapotban vannak titkosítva, és a Geo-redundáns tükrök a Egyesült Államokon belül maradnak. Az összes vásárlói adat, például az Alkalmazásbeállítások és a felhasználói adat, az ügyfél által választott helyen található, és nem a szolgáltatás kezeli.

A szolgáltatás metaadatait a rendszer a vész-helyreállítási célú Egyesült Államok replikálja.
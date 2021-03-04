---
title: Tároló rendszerképének tárolója
description: A tároló rendszerképeinek és egyéb összetevőinek Azure Container Registry, például a biztonsággal, a redundanciával és a kapacitással való tárolásának részletei.
ms.topic: article
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: 4bdffd111273e00b796e45f4e09bfac9ba6713e4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036010"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Tároló képtárolója Azure Container Registry

Minden [alapszintű, standard és prémium](container-registry-skus.md) szintű Azure Container Registry előnyt élvez az Azure Storage fejlett szolgáltatásait, például a titkosítást, a képadatok biztonságát és a Geo-redundanciát a képadatok védelme érdekében. A következő szakaszok a Azure Container Registry (ACR) képtára funkcióit és korlátait ismertetik.

## <a name="encryption-at-rest"></a>Titkosítás – Rest

A beállításjegyzékben található összes tároló-lemezkép és egyéb összetevő inaktív állapotban van titkosítva. Az Azure automatikusan titkosítja a rendszerképet a tárolás előtt, és visszafejti azt menet közben, amikor az alkalmazások és szolgáltatások lekérik a rendszerképet. Opcionálisan egy [ügyfél által felügyelt kulccsal](container-registry-customer-managed-keys.md)rendelkező extra titkosítási réteget is alkalmazhat.

## <a name="geo-redundant-storage"></a>Georedundáns tárolás

A legtöbb régióban üzembe helyezett tároló-nyilvántartások esetében az Azure egy geo-redundáns tárolási sémát használ a tároló-lemezképek és más összetevők elvesztésének biztosításához. A Azure Container Registry automatikusan replikálja a tároló lemezképeit több földrajzilag távoli adatközpontba, így megakadályozva a helyi tárolási hibák elvesztését.

> [!IMPORTANT]
> * Ha regionális tárolási hiba történik, a beállításjegyzék-adatait csak az Azure ügyfélszolgálatával lehet helyreállítani. 
> * A Dél-Brazíliában és Délkelet-Ázsiában található adattárolási követelmények miatt az ezekben a régiókban található Azure Container Registry-beli adat [csak a helyi geo](https://azure.microsoft.com/global-infrastructure/geographies/)-ben tárolódik. Délkelet-Ázsiában a rendszer az összes adattárolást Szingapúrban tárolja. Dél-Brazília esetében az összes adattal Brazíliában van tárolva. Ha egy jelentős katasztrófa miatt a régió elvész, a Microsoft nem fogja tudni helyreállítani Azure Container Registry adatait.

## <a name="geo-replication"></a>Georeplikáció

A még nagyobb rendelkezésre állást biztosító forgatókönyvek esetében érdemes lehet a prémium szintű kibocsátásiegység-forgalmi jegyzékek [geo-replikálás](container-registry-geo-replication.md) funkcióját használni. A Geo-replikáció a *teljes* regionális meghibásodás esetén megakadályozza a beállításjegyzékhez való hozzáférés elvesztését, nem csak a tárolási hibát. A Geo-replikáció más előnyöket is biztosít, például a hálózati bezárást, amely gyorsabb leküldést és lekérést tesz lehetővé elosztott fejlesztési vagy üzembe helyezési forgatókönyvekben.

## <a name="zone-redundancy"></a>Zónaredundancia

Rugalmas és magas rendelkezésre állású Azure Container Registry létrehozásához opcionálisan engedélyezze a [zóna-redundanciát](zone-redundancy.md) az Azure-régiók kiválasztása területen. A prémium szintű szolgáltatási csomag egyik funkciója, a Zone redundancia az Azure [rendelkezésre állási zónákat](../availability-zones/az-overview.md) használja a beállításjegyzék legalább három különálló zónába való replikálásához az egyes engedélyezett régiókban. A földrajzi replikálás és a zóna redundancia kombinációja a beállításjegyzék megbízhatóságának és teljesítményének növelése érdekében. 

## <a name="scalable-storage"></a>Skálázható tároló

Azure Container Registry lehetővé teszi, hogy annyi tárházat, képet, réteget vagy címkét hozzon létre, amennyire csak szüksége [van.](container-registry-skus.md#service-tier-features-and-limits) 

A nagy számú tárház és címke hatással lehet a beállításjegyzék teljesítményére. Rendszeresen törölje a nem használt adattárakat, címkéket és képeket a beállításjegyzék karbantartási rutinjának részeként, és opcionálisan állítsa be a címkézetlen jegyzékfájlok [megőrzési szabályát](container-registry-retention-policy.md) . A törölt beállításjegyzék-erőforrások, például adattárak, lemezképek és címkék törlés után *nem* állíthatók vissza. A beállításjegyzék-erőforrások törlésével kapcsolatos további információkért lásd: [tároló lemezképek törlése a Azure Container Registryban](container-registry-delete.md).

## <a name="storage-cost"></a>Tárolási költség

A díjszabással kapcsolatos részletes információkért tekintse meg a [Azure Container Registry díjszabását][pricing].

## <a name="next-steps"></a>Következő lépések

Az alapszintű, a standard és a Premium Container-jegyzékekkel kapcsolatos további információkért lásd: [Azure Container Registry szolgáltatási szintek](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->

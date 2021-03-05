---
title: Tároló rendszerképének tárolója
description: A tároló rendszerképeinek és egyéb összetevőinek Azure Container Registry, például a biztonsággal, a redundanciával és a kapacitással való tárolásának részletei.
ms.topic: article
ms.date: 03/03/2021
ms.custom: references_regions
ms.openlocfilehash: ec4328b44d5493b8d765fa30c548adc3d747d446
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183267"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Tároló képtárolója Azure Container Registry

Minden [alapszintű, standard és prémium](container-registry-skus.md) szintű Azure Container Registry előnyt élvez a fejlett Azure Storage-funkciókkal, többek között a titkosítással. A következő szakaszok ismertetik a Azure Container Registryban (ACR) található képtárolás funkcióit és korlátait.

## <a name="encryption-at-rest"></a>Titkosítás – Rest

A beállításjegyzékben található összes tároló-lemezkép és egyéb összetevő inaktív állapotban van titkosítva. Az Azure automatikusan titkosítja a rendszerképet a tárolás előtt, és visszafejti azt menet közben, amikor az alkalmazások és szolgáltatások lekérik a rendszerképet. Opcionálisan egy [ügyfél által felügyelt kulccsal](container-registry-customer-managed-keys.md)rendelkező extra titkosítási réteget is alkalmazhat.

## <a name="regional-storage"></a>Regionális tárterület

Azure Container Registry tárolja az adatait abban a régióban, ahol a beállításjegyzék létre lett hozva, hogy az ügyfelek megfeleljenek az adattárolási és megfelelőségi követelményeknek.

Az adatközpont-kimaradások elleni védelem érdekében egyes régiókban a [zónák redundancia](zone-redundancy.md)áll fenn, ahol az adatok replikálása egy adott régió több adatközpontjában történik.

Azok az ügyfelek, akik több régióban szeretnék tárolni az adataikat a különböző földrajzi területeken való jobb teljesítmény érdekében, vagy ha regionális kimaradás esetén rugalmasságot kívánnak biztosítani, a [földrajzi replikálást](container-registry-geo-replication.md)engedélyezni kell.

## <a name="geo-replication"></a>Georeplikáció

Magas rendelkezésre állású garanciát igénylő forgatókönyvek esetén érdemes lehet a prémium szintű kibocsátásiegység-forgalmi jegyzékek [geo-replikációs](container-registry-geo-replication.md) funkcióját használni. A Geo-replikáció segít megvédeni a beállításjegyzékhez való hozzáférés elvesztését regionális meghibásodás esetén. A Geo-replikáció más előnyöket is biztosít, például a hálózati bezárást, amely gyorsabb leküldést és lekérést tesz lehetővé elosztott fejlesztési vagy üzembe helyezési forgatókönyvekben.

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

---
title: Tárolócsoport manuális leállítása vagy elindítani
description: Megtudhatja, hogyan állíthat le vagy indítható el manuálisan egy tárolócsoport a Azure Container Instances.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 43498a7d31f0cb78751d2f318aede8523b7b9345
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786998"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Tárolók manuális leállítása vagy indítása az Azure Container Instancesben

A [tárolócsoport újraindítási](container-instances-restart-policy.md) szabályzatának beállítása határozza meg, hogy a tárolópéldányok hogyan indulnak el vagy leállnak alapértelmezés szerint. A tárolócsoport manuális leállításával vagy indításával felülbírálhatja az alapértelmezett beállítást.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="stop"></a>Leállítás

Manuálisan állítson le egy futó tárolócsoportot – például az [az container stop paranccsal][az-container-stop] vagy a Azure Portal. Bizonyos tárolók számítási feladatai esetében érdemes lehet leállítani egy hosszú ideig futó tárolócsoportot egy meghatározott időszak után a költségek csökkentése érdekében. 

*Amikor egy tárolócsoport Leállítva állapotba lép, az leáll, és a csoportban lévő összes tárolót újra lesz állítva. Nem őrzi meg a tároló állapotát.*

A tárolók újrahasznosítása után a [tárolócsoport](container-instances-container-groups.md#resource-allocation) erőforrásai fel vannak szabadodva, és a számlázás leáll.

A leállítási műveletnek nincs hatása, ha a tárolócsoport már leállt (Sikeres vagy Sikertelen állapotban van). A sikeresen lefutott, egyszer futtatott tárolófeladatokat tartalmazó tárolócsoport például Sikeres állapotban áll le. A csoport ilyen állapotban való leállítási kísérlete nem módosítja az állapotot. 

## <a name="start"></a>Indítás

Ha egy tárolócsoport leáll – vagy azért, mert a tárolók leálltak saját maga, vagy manuálisan leállította a csoportot – elindíthatja a tárolókat. Például az [az container start][az-container-start] paranccsal vagy a Azure Portal manuálisan indítsa el a csoportban található tárolókat. Ha bármely tároló tároló-rendszerképe frissül, a rendszer lekért egy új rendszerképet. 

A tárolócsoport indítása egy új üzembe helyezést kezd ugyanazokkal a tárolókonfigurációval. Ez a művelet segíthet a vártnak megfelelő ismert tárolócsoport-konfigurációk gyors újbóli felhasználásában. Nem kell új tárolócsoportot létrehoznia ugyanazon számítási feladat futtatásához.

Ez a művelet a tárolócsoport összes tárolóját elindítja. A csoport egy adott tárolója nem indítható el.

Miután manuálisan elindít vagy újraindít egy tárolócsoportot, a tárolócsoport a konfigurált újraindítási szabályzatnak megfelelően fut.
  
## <a name="restart"></a>Újraindítás

Futtatás közben újraindíthat egy tárolócsoportot – például az [az container restart paranccsal.][az-container-restart] Ez a művelet újraindítja a tárolócsoportban található összes tárolót. Ha bármely tároló tároló rendszerképe frissül, a rendszer lekért egy új rendszerképet. 

A tárolócsoport újraindítása akkor hasznos, ha üzembehelyezéssel kapcsolatos problémákat szeretne elhárítani. Ha például egy ideiglenes erőforrás-korlátozás megakadályozza a tárolók sikeres futtatását, a csoport újraindítása megoldhatja a problémát.

Ez a művelet a tárolócsoport összes tárolóját újraindítja. A csoport egy adott tárolója nem indítható újra.

Miután manuálisan újraindít egy tárolócsoportot, a tárolócsoport a konfigurált újraindítási szabályzatnak megfelelően fut.

## <a name="next-steps"></a>Következő lépések

További információ az [újraindítási szabályzat beállításairól a](container-instances-restart-policy.md) Azure Container Instances.

A meglévő konfigurációval a tárolócsoport manuális leállítása és [](container-instances-update.md) indítása mellett frissítheti a futó tárolócsoport beállításait is.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container#az_container_restart
[az-container-start]: /cli/azure/container#az_container_start
[az-container-stop]: /cli/azure/container#az_container_stop

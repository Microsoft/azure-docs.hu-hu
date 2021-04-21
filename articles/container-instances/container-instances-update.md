---
title: Tárolócsoport frissítése
description: Megtudhatja, hogyan frissítheti a futó tárolókat a Azure Container Instances tárolócsoportokban.
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: cbb2e830490d2591645b8156ee830856da0f9049
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786962"
---
# <a name="update-containers-in-azure-container-instances"></a>Tárolók frissítése az Azure Container Instancesben

A tárolópéldányok normál működése során szükség lehet a tárolócsoportban futó [tárolók frissítésére.](./container-instances-container-groups.md) Előfordulhat például, hogy frissíteni szeretne egy tulajdonságot, például egy rendszerképverziót, egy DNS-nevet vagy egy környezeti változót, vagy frissíteni szeretne egy tulajdonságot egy olyan tárolóban, amelynek az alkalmazása összeomlott.

Frissítse a futó tárolócsoportban lévő tárolókat egy meglévő csoport legalább egy módosított tulajdonsággal való ismételt telepítéséhez. Amikor frissít egy tárolócsoportot, a csoportban futó összes tároló a helyére kerül, általában ugyanazon a mögöttes tárológazdán.

> [!NOTE]
> A lezárt vagy törölt tárolócsoportok nem frissíthetők. Miután egy tárolócsoport leállt (Sikeres vagy Sikertelen állapotban van) vagy törölve lett, a csoportot újként kell üzembe helyezni. További [korlátozások:](#limitations).

## <a name="update-a-container-group"></a>Tárolócsoport frissítése

Meglévő tárolócsoport frissítése:

* Adja ki a create parancsot (vagy használja a Azure Portal), és adja meg egy meglévő csoport nevét 
* Módosítsa vagy adja hozzá a csoport legalább egy olyan tulajdonságát, amely támogatja a frissítést az újratelepítéskor. Bizonyos tulajdonságok [nem támogatják a frissítéseket.](#properties-that-require-container-delete)
* Állítson be más tulajdonságokat a korábban megadott értékekkel. Ha nem ad meg értéket egy tulajdonsághoz, az visszaáll az alapértelmezett értékre.

> [!TIP]
> A [YAML-fájl](./container-instances-container-groups.md#deployment) segít fenntartani a tárolócsoport üzembe helyezési konfigurációját, és kiindulási pontot biztosít a frissített csoportok üzembe helyezéséhez. Ha más módszert használt a csoport létrehozásához, a konfiguráció yaML-fájlba exportálható [az az container export használatával.][az-container-export] 

### <a name="example"></a>Példa

Az alábbi Azure CLI-példa egy új DNS-névcímkével frissíti a tárolócsoportot. Mivel a csoport DNS-névcímke-tulajdonsága frissíthető, a tárolócsoport újra üzembe lesz stb. és a tárolói újraindulnak.

Kezdeti üzembe helyezés a *myapplication-staging DNS-névcímkével:*

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Frissítse a tárolócsoportot egy új DNS-névcímkével *(myapplication)* és állítsa be a többi tulajdonságot a korábban használt értékekkel:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Frissítési előnyök

A meglévő tárolócsoport frissítésének elsődleges előnye a gyorsabb üzembe helyezés. Egy meglévő tárolócsoport újratelepítésekor annak tárolórendszerkép-rétegei az előző üzembe helyezés által gyorsítótárazott rétegekből vannak lekért adatok. Ahelyett, hogy az összes rendszerképréteget az új üzemelő példányok esetén is frissen leküldi a beállításjegyzékből, csak a módosított rétegeket (ha vannak) leküldi a rendszer.

A nagyobb tároló-rendszerképeken, például a Windows Server Core-on alapuló alkalmazások jelentős javulást láthatnak az üzembe helyezési sebességben, ha a frissítést az új törlése és üzembe helyezése helyett frissíti.

## <a name="limitations"></a>Korlátozások

* A tárolócsoport nem minden tulajdonsága támogatja a frissítéseket. A tárolócsoport egyes tulajdonságainak a módosítása után először törölnie kell, majd újra üzembe kell helyeznie a csoportot. Lásd: [Tároló törlését igénylő tulajdonságok.](#properties-that-require-container-delete)
* A tárolócsoport összes tárolója újraindul a tárolócsoport frissítésekekor. Többtárolós csoportban nem végezhet frissítést vagy nem végezheti el egy adott tároló újraindítását.
* A tárolócsoport IP-címe általában meg van őrizve a frissítések között, de nem garantált, hogy változatlan marad. Amíg a tárolócsoport ugyanazon a mögöttes gazdagépen van telepítve, a tárolócsoport megtartja az IP-címét. Bár ritkán fordul elő, vannak olyan belső Azure-események, amelyek egy másik gazdagépre való újratelepítést okozhatnak. A probléma megoldásához javasoljuk egy DNS-névcímke használatát a tárolópéldányokhoz.
* A lezárt vagy törölt tárolócsoportok nem frissíthetők. Ha egy tárolócsoport le lett  állítva (Leállított állapotban van) vagy törölve lett, a csoport újként lesz üzembe állítva.

## <a name="properties-that-require-container-delete"></a>Tároló törlését igénylő tulajdonságok

Nem minden tárolócsoport-tulajdonság frissíthető. Ha például módosítani szeretné egy tároló újraindítási szabályzatát, először törölnie kell a tárolócsoportot, majd újra létre kell hoznia.

Ezeknek a tulajdonságoknak a módosításaihoz a tárolócsoport törlésére van szükség az újratelepítés előtt:

* Operációs rendszer típusa
* CPU-, memória- vagy GPU-erőforrások
* Újraindítási szabályzat
* Hálózati profil

Amikor töröl és újra létrehoz egy tárolócsoportot, az nem lesz "újra üzembe hozzuk", hanem újat hozunk létre. A rendszer minden rendszerképréteget frissen lekért a beállításjegyzékből, nem pedig az előző üzembe helyezés által gyorsítótárazott rétegből. A tároló IP-címe is megváltozhat, mert egy másik mögöttes gazdagépen van üzembe helyezni.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben többször is említettük a **tárolócsoportot.** A Azure Container Instances tárolók egy tárolócsoportban helyezhetők üzembe, és a tárolócsoportok több tárolót is tartalmazhatnak.

[Tárolócsoportok az Azure Container Instancesben](./container-instances-container-groups.md)

[Többtárolós csoport üzembe helyezése](container-instances-multi-container-group.md)

[Tárolók manuális leállítása vagy indítása az Azure Container Instancesben](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[azure-cli-install]: /cli/azure/install-azure-cli
[az-container-export]: /cli/azure/container#az_container_export

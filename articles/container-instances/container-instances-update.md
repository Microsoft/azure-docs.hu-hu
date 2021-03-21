---
title: Tároló csoportjának frissítése
description: Megtudhatja, hogyan frissítheti a futó tárolókat a Azure Container Instances-tároló csoportjaiban.
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: fb31eeda83532c408a303e879439006bcd7d4e45
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102200648"
---
# <a name="update-containers-in-azure-container-instances"></a>Tárolók frissítése az Azure Container Instancesben

A tároló példányainak normál működése során előfordulhat, hogy frissítenie kell a futó tárolókat egy [tároló csoportba](./container-instances-container-groups.md). Előfordulhat például, hogy frissíteni kíván egy tulajdonságot, például egy rendszerkép verzióját, egy DNS-nevet vagy egy környezeti változót, vagy frissítenie kell egy tulajdonságot egy olyan tárolóban, amelynek az alkalmazása összeomlott.

Frissítse a tárolókat egy futó tároló csoportban egy meglévő csoport legalább egy módosított tulajdonsággal való újbóli üzembe helyezésével. Amikor frissít egy tároló csoportot, a csoportban lévő összes futó tárolót a rendszer helyben indítja újra, általában ugyanarra az alapul szolgáló tároló-gazdagépre.

> [!NOTE]
> A leállított vagy törölt tároló-csoportok nem frissíthetők. Ha a tároló csoport leállt (vagy sikeres vagy sikertelen állapotban van), vagy törölve lett, a csoportot újként kell telepíteni. További [korlátozások](#limitations).

## <a name="update-a-container-group"></a>Tároló csoport frissítése

Meglévő tároló csoport frissítése:

* Adja ki a Create parancsot (vagy használja a Azure Portal), és adja meg egy meglévő csoport nevét. 
* Módosítsa vagy adja hozzá a csoport legalább egy olyan tulajdonságát, amely támogatja a frissítést az újbóli üzembe helyezéskor. Bizonyos tulajdonságok [nem támogatják a frissítéseket](#properties-that-require-container-delete).
* Adja meg a korábban megadott értékekkel rendelkező egyéb tulajdonságokat. Ha nem állít be értéket a tulajdonsághoz, az visszaáll az alapértelmezett értékére.

> [!TIP]
> A [YAML-fájlok](./container-instances-container-groups.md#deployment) segítenek fenntartani egy tároló csoport telepítési konfigurációját, és kiindulási pontot biztosítanak a frissített csoportok telepítéséhez. Ha más módszert használt a csoport létrehozásához, exportálhatja a konfigurációt YAML az [az Container export][az-container-export]paranccsal. 

### <a name="example"></a>Példa

A következő Azure CLI-példa egy új DNS-név címkével rendelkező tároló-csoportot frissít. Mivel a csoport DNS-név felirat tulajdonsága frissíthető, a tároló csoport újratelepítése történik, és a tárolók újraindulnak.

Kezdeti üzembe helyezés DNS-névvel feliratú *myapplication – átmeneti*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Frissítse a tároló csoportot egy új DNS-név címkével, *myapplication*, és állítsa be a fennmaradó tulajdonságokat a korábban használt értékekkel:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Frissítési előnyök

Egy meglévő tároló csoport frissítésének elsődleges előnye a gyorsabb üzembe helyezés. Egy meglévő tároló-csoport újratelepítésekor a tároló képrétegei a korábbi üzemelő példány által gyorsítótárazott adatokból lesznek leképezve. Ahelyett, hogy az összes képréteget a beállításjegyzékből frissen, az új központi telepítésekkel végzett, csak a módosított rétegeket (ha vannak) húzta.

A nagyobb méretű tárolók rendszerképein alapuló alkalmazások, például a Windows Server Core, jelentősen javítják az üzembe helyezés sebességét, ha a törlés és az új telepítése helyett a frissítésre kerül.

## <a name="limitations"></a>Korlátozások

* A Container Group egyik tulajdonsága sem támogatja a frissítéseket. A tárolók egyes tulajdonságainak módosításához először törölnie kell, majd újra kell telepítenie a csoportot. Tekintse meg [a tároló törlését igénylő tulajdonságokat](#properties-that-require-container-delete).
* A tároló csoport összes tárolója újraindul a tároló csoport frissítésekor. Egy többtárolós csoporton belül nem végezheti el a megadott tároló frissítését vagy helyben történő újraindítását.
* A tárolók csoportjának IP-címét általában a frissítések között őrzi meg a rendszer, de nem garantált, hogy változatlan marad. Ha a tároló csoport ugyanarra a mögöttes gazdagépre van telepítve, a tároló csoport megőrzi az IP-címét. Bár ritka, vannak olyan Azure-belső események, amelyek egy másik gazdagépre való újratelepítést okozhatnak. A probléma megoldásához javasolt a DNS-név címkét használni a tároló példányaihoz.
* A leállított vagy törölt tároló-csoportok nem frissíthetők. Miután leállította a tároló csoportot (leállt *állapotban van* ) vagy törölve lett, a csoport új néven lesz telepítve.

## <a name="properties-that-require-container-delete"></a>Tároló törlését igénylő tulajdonságok

Nem lehet frissíteni a Container Group összes tulajdonságát. Ha például egy tároló újraindítási szabályzatát szeretné módosítani, először törölnie kell a tároló csoportot, majd újra létre kell hoznia.

Ezeknek a tulajdonságoknak a módosítása az újratelepítést megelőzően a tároló csoportjának törlését igényli:

* Operációs rendszer típusa
* CPU-, memória-vagy GPU-erőforrások
* Újraindítási szabályzat
* Hálózati profil

Amikor töröl egy tároló csoportot, és újból létrehozza, azt nem "újból üzembe helyezi", hanem új létrehozása szükséges. Az összes képréteget a beállításjegyzékből frissen kell húzni, nem pedig a korábbi üzemelő példány által gyorsítótárazott adatokból. Előfordulhat, hogy a tároló IP-címe is változhat, mert egy másik mögöttes gazdagépre van telepítve.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben többször is említettük a **Container csoportot**. Azure Container Instances összes tárolója egy tároló csoportba van telepítve, és a tárolók csoportjai több tárolót is tartalmazhatnak.

[Tárolócsoportok az Azure Container Instancesben](./container-instances-container-groups.md)

[Többtárolós csoport üzembe helyezése](container-instances-multi-container-group.md)

[Tárolók manuális leállítása vagy indítása az Azure Container Instancesben](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
[az-container-export]: /cli/azure/container#az-container-export

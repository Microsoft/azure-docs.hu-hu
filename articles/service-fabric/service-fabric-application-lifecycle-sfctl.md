---
title: Azure Service Fabric-alkalmazások kezelése a sfctl használatával
description: Ismerje meg, hogyan helyezhet üzembe és távolíthat el alkalmazásokat egy Azure Service Fabric-fürtből az Azure Service Fabric parancssori felület használatával
author: Christina-Kang
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 7d361d44c349bc7a6e3c041f78d00ad66182fa15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84711034"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Azure Service Fabric-alkalmazás kezelése az Azure Service Fabric parancssori felület (sfctl) használatával

Megtudhatja, hogyan hozhat létre és törölhet olyan alkalmazásokat, amelyek Azure Service Fabric-fürtön futnak.

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a Service Fabric CLI-t. Ezután válassza ki a Service Fabric-fürtöt. További információ: Ismerkedés [a Service FABRIC CLI-vel](service-fabric-cli.md).

* Service Fabric alkalmazáscsomag készen áll a telepítésre. További információ az alkalmazások létrehozásáról és becsomagolásáról: tudnivalók a [Service Fabric alkalmazás modelljéről](service-fabric-application-model.md).

## <a name="overview"></a>Áttekintés

Új alkalmazás üzembe helyezéséhez hajtsa végre a következő lépéseket:

1. Alkalmazáscsomag feltöltése a Service Fabric rendszerkép-tárolóba.
2. Alkalmazás típusának kiépítése.
3. Törölje a rendszerkép-tároló tartalmát.
4. Alkalmazás megadásához és létrehozásához.
5. Szolgáltatások meghatározása és létrehozása.

Egy meglévő alkalmazás eltávolításához hajtsa végre a következő lépéseket:

1. Törölje az alkalmazást.
2. A társított alkalmazás típusának kiépítése.

## <a name="deploy-a-new-application"></a>Új alkalmazás üzembe helyezése

Új alkalmazás telepítéséhez hajtsa végre a következő feladatokat:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Új alkalmazáscsomag feltöltése a rendszerkép-tárolóba

Az alkalmazás létrehozása előtt töltse fel az alkalmazáscsomag a Service Fabric rendszerkép-tárolójába.

Ha például az alkalmazáscsomag a `app_package_dir` címtárban található, akkor a következő parancsokkal töltheti fel a könyvtárat:

```shell
sfctl application upload --path ~/app_package_dir
```

Nagyméretű alkalmazáscsomag esetén megadhatja a `--show-progress` feltöltés folyamatának állapotát.

### <a name="provision-the-application-type"></a>Az alkalmazás típusának kiépítése

A feltöltés befejezésekor kiépítheti az alkalmazást. Az alkalmazás üzembe helyezéséhez használja a következő parancsot:

```shell
sfctl application provision --application-type-build-path app_package_dir
```

A értéke annak `application-type-build-path` a könyvtárnak a neve, ahová az alkalmazáscsomag fel lett töltve.

### <a name="delete-the-application-package"></a>Alkalmazáscsomag törlése

Javasoljuk, hogy az alkalmazás sikeres regisztrálása után távolítsa el az alkalmazáscsomag-csomagot.  A rendszerkép-tárolóban lévő alkalmazáscsomag törlése a rendszererőforrásokat felszabadítja.  A nem használt alkalmazáscsomag megőrzése a lemezes tárolást és az alkalmazások teljesítményével kapcsolatos problémákat eredményez. 

Az alkalmazáscsomag rendszerkép-tárolóból való törléséhez használja a következő parancsot:

```shell
sfctl store delete --content-path app_package_dir
```

`content-path` annak a könyvtárnak a neve, amelyet az alkalmazás létrehozásakor töltött fel.

### <a name="create-an-application-from-an-application-type"></a>Alkalmazás létrehozása alkalmazás típusától

Az alkalmazás üzembe helyezése után az alábbi parancs használatával nevezze el és hozza létre az alkalmazást:

```shell
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` az alkalmazás példányához használni kívánt név. További paramétereket az előzőleg kiosztott alkalmazási jegyzékből kaphat.

Az alkalmazás nevének az előtaggal kell kezdődnie `fabric:/` .

### <a name="create-services-for-the-new-application"></a>Szolgáltatások létrehozása az új alkalmazáshoz

Miután létrehozott egy alkalmazást, hozzon létre szolgáltatásokat az alkalmazásból. Az alábbi példában egy új állapot nélküli szolgáltatást hozunk létre az alkalmazásból. Az alkalmazásból létrehozható szolgáltatások a korábban kiosztott alkalmazáscsomag szolgáltatásbeli jegyzékfájljában vannak meghatározva.

```shell
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Alkalmazások központi telepítésének és állapotának ellenőrzése

Annak ellenőrzéséhez, hogy minden kifogástalan állapotú-e, használja a következő állapotfigyelő parancsokat:

```shell
sfctl application list
sfctl service list --application-id TestApp
```

Annak ellenőrzéséhez, hogy a szolgáltatás kifogástalan-e, használja a hasonló parancsokat a szolgáltatás és az alkalmazás állapotának lekéréséhez:

```shell
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

A kifogástalan állapotú szolgáltatások és alkalmazások `HealthState` értéke `Ok` .

## <a name="remove-an-existing-application"></a>Meglévő alkalmazás eltávolítása

Egy alkalmazás eltávolításához hajtsa végre a következő feladatokat:

### <a name="delete-the-application"></a>Az alkalmazás törlése

Az alkalmazás törléséhez használja a következő parancsot:

```shell
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Az alkalmazás típusának kiépítése

Az alkalmazás törlése után kiépítheti az alkalmazás típusát, ha már nincs rá szüksége. Az alkalmazás típusának megszüntetéséhez használja a következő parancsot:

```shell
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

A típus nevének és típusának meg kell egyeznie a korábban kiépített alkalmazás jegyzékfájljának nevével és verziójával.

## <a name="upgrade-application"></a>Alkalmazás frissítése

Az alkalmazás létrehozása után megismételheti ugyanezeket a lépéseket az alkalmazás második verziójának kiépítéséhez. Ezután a Service Fabric alkalmazás frissítése után átválthat az alkalmazás második verziójának futtatására. További információ: [Service Fabric alkalmazás frissítéseinek](service-fabric-application-upgrade.md)dokumentációja.

A frissítés elvégzéséhez először az alábbi parancsokkal kell kiépíteni az alkalmazás következő verzióját:

```shell
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Azt javasoljuk, hogy a figyelt automatikus frissítést a következő parancs futtatásával indítsa el:

```shell
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

A frissítések felülbírálják a meglévő paramétereket, bármilyen készlet van megadva. Ha szükséges, a frissítési parancshoz argumentumként kell átadni az alkalmazás paramétereit. Az alkalmazás paramétereit JSON-objektumként kell kódolni.

A korábban megadott paraméterek lekéréséhez használhatja az `sfctl application info` parancsot.

Ha egy alkalmazás frissítése folyamatban van, az állapot a parancs használatával kérhető le `sfctl application upgrade-status` .

Végül, ha folyamatban van egy frissítés, és meg kell szakítani, a használatával `sfctl application upgrade-rollback` visszaállíthatja a frissítést.

## <a name="next-steps"></a>Következő lépések

* [A CLI alapjai Service Fabric](service-fabric-cli.md)
* [Első lépések a Service Fabric Linuxon](service-fabric-get-started-linux.md)
* [Service Fabric alkalmazás frissítésének indítása](service-fabric-application-upgrade.md)

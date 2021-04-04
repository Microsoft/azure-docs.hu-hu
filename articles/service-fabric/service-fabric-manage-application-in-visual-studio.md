---
title: Alkalmazások kezelése a Visual Studióban
description: Az Azure Service Fabric-alkalmazások és-szolgáltatások létrehozásához, fejlesztéséhez, csomagolásához, üzembe helyezéséhez és hibakereséséhez használja a Visual studiót.
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: 1fd9295a0dead180767febcc5339ef0a25cb1e48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574547"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>A Visual Studio használata a Service Fabric-alkalmazások írásának és kezelésének egyszerűbbé tételéhez
Azure Service Fabric-alkalmazásait és szolgáltatásait a Visual Studióban kezelheti. [A fejlesztési környezet beállítása](service-fabric-get-started.md)után a Visual Studio használatával Service Fabric alkalmazásokat hozhat létre, szolgáltatásokat vehet fel, illetve csomagokat telepíthet, regisztrálhat és telepíthet alkalmazásokat a helyi fejlesztési fürtben.

## <a name="deploy-your-service-fabric-application"></a>A Service Fabric alkalmazás üzembe helyezése
Alapértelmezés szerint az alkalmazások központi telepítése egyetlen egyszerű művelettel ötvözi a következő lépéseket:

1. Az alkalmazáscsomag létrehozása
2. Alkalmazáscsomag feltöltése a rendszerkép-tárolóba
3. Az alkalmazás típusának regisztrálása
4. Futó alkalmazások példányainak eltávolítása
5. Alkalmazás példányának létrehozása

A Visual Studióban nyomja le az **F5** billentyűt az alkalmazás üzembe helyezéséhez, és csatolja a hibakeresőt az összes alkalmazás-példányhoz. A **CTRL + F5 billentyűkombinációval** hibakeresés nélkül telepítheti az alkalmazásokat, vagy közzéteheti a helyi vagy távoli fürtön a közzétételi profil használatával.

### <a name="application-debug-mode"></a>Alkalmazás hibakeresési módja
A Visual Studio egy **Application Debug Mode** nevű tulajdonságot biztosít, amely azt szabályozza, hogy a Visual studiók hogyan kezelhetik az alkalmazások telepítését a hibakeresés részeként.

#### <a name="to-set-the-application-debug-mode-property"></a>Az alkalmazás hibakeresési módjának tulajdonságának beállítása
1. A Service Fabric alkalmazás projekt (*. sfproj) helyi menüjében válassza a **Tulajdonságok** lehetőséget (vagy nyomja le az **F4** billentyűt).
2. A **Tulajdonságok** ablakban állítsa be az **alkalmazás hibakeresési módjának** tulajdonságát.

![Alkalmazás hibakeresési módjának beállítása tulajdonság][debugmodeproperty]

#### <a name="application-debug-modes"></a>Alkalmazás hibakeresési módjai

1. **Alkalmazás frissítése** Ez a mód lehetővé teszi a kód gyors módosítását és hibakeresését, és támogatja a statikus webfájlok hibakeresés közbeni szerkesztését. Ez a mód csak akkor működik, ha a helyi fejlesztési fürt 1 csomópontos módban van. Ez az alkalmazás alapértelmezett hibakeresési módja.
2. Az **alkalmazás eltávolítása** hatására az alkalmazás el lesz távolítva a hibakeresési munkamenet végén.
3. **Automatikus frissítés** Az alkalmazás továbbra is fut, amikor a hibakeresési munkamenet véget ér. A következő hibakeresési munkamenet az üzemelő példányt frissítésként fogja kezelni. A frissítési folyamat megőrzi az előző hibakeresési munkamenetben megadott összes adat megadását.
4. **Alkalmazás megtartása** Ha a hibakeresési munkamenet véget ér, az alkalmazás a fürtön fut. A következő hibakeresési munkamenet elején a rendszer eltávolítja az alkalmazást.

Az **automatikus verziófrissítéshez** az Service Fabric alkalmazás frissítési funkcióit alkalmazva megőrzi a rendszer. További információ az alkalmazások frissítéséről és a valós környezetben való frissítésről: [Service Fabric alkalmazás frissítése](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Szolgáltatás hozzáadása a Service Fabric alkalmazáshoz
Új szolgáltatásokat adhat hozzá az alkalmazáshoz, és kiterjesztheti a funkcióit. Annak biztosítása érdekében, hogy a szolgáltatás szerepeljen az alkalmazáscsomag részeként, adja hozzá a szolgáltatást az **új háló szolgáltatás...** menüpontban.

![Új Service Fabric szolgáltatás hozzáadása][newservice]

Válasszon ki egy Service Fabric projekttípus, amelyet hozzá szeretne adni az alkalmazáshoz, és adja meg a szolgáltatás nevét.  Lásd: [keretrendszer kiválasztása a szolgáltatáshoz](service-fabric-choose-framework.md) , amely segít eldönteni, hogy melyik szolgáltatási típust kell használni.

![Válassza ki az alkalmazáshoz hozzáadandó Service Fabric Service-projekt típusát.][addserviceproject]

Az új szolgáltatás bekerül a megoldásba és a meglévő alkalmazáscsomagba. A szolgáltatási referenciák és az alapértelmezett szolgáltatási példány hozzá lesz adva az alkalmazás-jegyzékfájlhoz, így a szolgáltatás létrehozható és elindítható az alkalmazás következő telepítésekor.

![Az új szolgáltatás bekerül az alkalmazás-jegyzékfájlba][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>A Service Fabric alkalmazás becsomagolása
Az alkalmazás és a szolgáltatásainak fürtön való üzembe helyezéséhez létre kell hoznia egy alkalmazáscsomag-csomagot.  A csomag egy adott elrendezésben rendezi az alkalmazás jegyzékfájlját, a szolgáltatási jegyzékfájlokat és az egyéb szükséges fájlokat.  A Visual Studio beállítja és kezeli a csomagot az alkalmazás projekt mappájában a "pkg" könyvtárban.  Az **alkalmazás** helyi menüjének **csomag** elemére kattintva létrehozhatja vagy frissítheti az alkalmazáscsomag.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Alkalmazások és alkalmazás-típusok eltávolítása a Cloud Explorer használatával
A Cloud Explorer használatával elvégezheti az alapszintű fürtszolgáltatási műveleteket a Visual studióból, amely a **nézet** menüből indítható el. Például törölheti az alkalmazásokat, és kiépítheti az alkalmazások típusát a helyi vagy távoli fürtökön.

![Alkalmazás eltávolítása][removeapplication]

> [!TIP]
> A fürtözést részletesebben kezelő funkciókért lásd: [a fürt megjelenítése a Service Fabric Explorersal](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Következő lépések
* [Service Fabric alkalmazás modellje](service-fabric-application-model.md)
* [Service Fabric alkalmazás központi telepítése](service-fabric-deploy-remove-applications.md)
* [Alkalmazások paramétereinek kezelése több környezethez](service-fabric-manage-multiple-environment-app-configuration.md)
* [Az Service Fabric-alkalmazás hibakeresése](service-fabric-debugging-your-application.md)
* [A fürt megjelenítése Service Fabric Explorer használatával](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png

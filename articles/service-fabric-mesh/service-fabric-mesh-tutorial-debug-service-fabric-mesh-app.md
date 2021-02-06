---
title: Helyileg futó Azure Service Fabric Mesh-webalkalmazás hibakeresése
description: Ebben az oktatóanyagban egy olyan Azure Service Fabric mesh-alkalmazásban végez hibakeresést, amely a helyi fürtön fut.
author: georgewallace
ms.topic: tutorial
ms.date: 10/31/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 9e3dc16481340c0266cd398d0970e2147648e17f
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625449"
---
# <a name="tutorial-debug-a-service-fabric-mesh-application-running-in-your-local-development-cluster"></a>Oktatóanyag: Egy helyi fejlesztési fürtben futó Service Fabric Mesh-alkalmazás hibakeresése

> [!IMPORTANT]
> Az Azure Service Fabric Mesh előzetes verziója ki lett vonva. Az új központi telepítések többé nem lesznek engedélyezve a Service Fabric Mesh API-n keresztül. A meglévő központi telepítések támogatása a 2021. április 28. után folytatódik.
> 
> Részletekért lásd: az [Azure Service Fabric Mesh előzetes verziójának nyugdíjazása](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Ez az oktatóanyag a sorozat második része, amely az Azure Service Fabric Mesh-alkalmazások helyi fejlesztői fürtön történő felépítését és hibakeresését ismerteti.

Ebben az oktatóanyagban a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Mi történik az Azure Service Fabric mash-alkalmazások fejlesztésekor
> * Hogyan állítható be egy töréspont a szolgáltatások közti hívás figyelése céljából

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Service Fabric Mesh-alkalmazás létrehozása Visual Studióban](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * Egy helyi fejlesztési fürtben futó Service Fabric Mesh-alkalmazás hibakeresése
> * [Service Fabric Mesh-alkalmazás üzembe helyezése](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Service Fabric Mesh-alkalmazás frissítése](service-fabric-mesh-tutorial-upgrade.md)
> * [A Service Fabric Mesh erőforrásainak eltávolítása](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Győződjön meg arról, hogy [beállította a fejlesztési környezetet](service-fabric-mesh-howto-setup-developer-environment-sdk.md), amely tartalmazza a Service Fabric futtatókörnyezet, az SDK, a Docker, és a Visual Studio 2017 telepítését.

## <a name="download-the-to-do-sample-application"></a>A teendőlista-mintaalkalmazás letöltése

Ha nem hozta létre a tennivaló minta alkalmazást a [jelen oktatóanyag-sorozat első részében](service-fabric-mesh-tutorial-create-dotnetcore.md), letöltheti azt. Egy parancssori ablakban futtassa a következő parancsot a mintaalkalmazás-adattár helyi számítógépre történő klónozásához.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

Az alkalmazás a `src\todolistapp` könyvtárban található.

## <a name="build-and-debug-on-your-local-cluster"></a>Fejlesztés és hibakeresés a helyi fürtön

Amint a projekt betölt, a rendszer automatikusan létrehozza és üzembe helyezi a Docker-rendszerképet a helyi fürtön. Ez a folyamat eltarthat egy ideig. Ha figyelni szeretné a folyamat előrehaladását a Visual Studio **Kimenet** ablaktábláján, állítsa be a kimeneti ablaktábla **Kimenet mutatása innen** legördülő listájában a **Service Fabric Tools** lehetőséget.

Az **F5** billentyű megnyomásával végezze el a fordítást és a szolgáltatás helyi futtatását. A projekt helyi futtatásakor és hibakeresésekor a Visual Studio az alábbiakat végzi el:

* Ellenőrzi, hogy a Windows rendszerhez készült Docker fut, és a Windowst használja tárolói operációs rendszerként.
* Letölti az összes hiányzó, alapként szolgáló Docker-rendszerképet. Ez a rész eltarthat egy ideig
* Létrehozza (vagy újraépíti) a kódprojekt üzemeltetésére szolgáló Docker-rendszerképet.
* Üzembe helyezi és futtatja a tárolót a helyi Service Fabric fejlesztési fürtön.
* Futtatja a szolgáltatásokat, és kezeli a beállított töréspontokat.

Miután a helyi üzembe helyezés befejeződött, és a Visual Studio elindította az alkalmazást, egy böngészőablakban megnyílik a mintául szolgáló alapértelmezett weboldal.

## <a name="debugging-tips"></a>Hibakeresési tippek

A [Visual Studio teljesítményének optimalizálása](service-fabric-mesh-howto-optimize-vs.md)című részben leírtak szerint végezze el az első hibakeresési műveletet (F5).

Jelenleg probléma merül fel, ami miatt a hívás `using (HttpResponseMessage response = client.GetAsync("").GetAwaiter().GetResult())` sikertelen lesz a szolgáltatáshoz való kapcsolódásra. Ez ekkor fordulhat elő, ha a gazdagép IP-címe megváltozik. A probléma megoldása:

1. Távolítsa el az alkalmazást a helyi fürtből (a Visual Studióban, **hozzon létre**  >  **tiszta megoldást**).
2. A Service Fabric Local Cluster Manager alkalmazásban válassza a **Stop Local Cluster** (Helyi fürt leállítása), majd a **Start Local Cluster** (Helyi fürt indítása) elemet.
3. Helyezze ismét üzembe az alkalmazást (a Visual Studióban nyomja le az **F5** gombot).

Ha **Nem fut helyi Service Fabric-fürt** tartalmú hibaüzenetet kap, győződjön meg arról, hogy fut a Service Fabric Local Cluster Manager (LCM), majd kattintson a tálca LCM ikonjára a jobb gombbal, és kattintson a **Start Local Cluster** (Helyi fürt indítása) elemre. A fürt elindítása után térjen vissza a Visual Studióba, és nyomja meg az **F5** billentyűt.

Ha **404-es** hibát kap az alkalmazás indításakor, elképzelhető, hogy helytelenek a **service.yaml** környezeti változói. Győződjön meg arról, hogy az `ApiHostPort` és a `ToDoServiceName` megfelelően, a [környezeti változók létrehozását](./service-fabric-mesh-tutorial-create-dotnetcore.md#create-environment-variables) ismertető részben leírtak szerint van beállítva.

Ha felépítési hibákat kap a **service.yaml** fájlban, ellenőrizze, hogy tabulátorok helyett szóközöket használ-e a sorok behúzására. Ezenkívül ebben az esetben angol területi beállításokkal kell létrehoznia az alkalmazást.

### <a name="debug-in-visual-studio"></a>Hibakeresés a Visual Studióban

Ha egy Service Fabric Mesh alkalmazást a Visual Studióban tesz elérhetővé, helyi Service Fabric fejlesztési fürtöt használ. Annak ellenőrzéséhez, hogy a rendszer miként olvassa be a teendőket a háttérszolgáltatásból, végezze el az OnGet() metódus hibakeresését.
1. A **webfrontend** projektben nyissa meg a **Pages**  >  **index. cshtml**  >  **index.cshtml.cs** , és állítson be egy töréspontot a **OnGet** metódusban (17. sor).
2. A **ToDoService** projektben nyissa meg a **TodoController.cs** , és állítson be egy töréspontot a **Get** metódusban (15. sor).
3. Térjen vissza a böngészőbe, és frissítse az oldalt. Az első töréspont a webes kezelőfelület `OnGet()` metódusában jelentkezik. A `backendUrl` változó ellenőrzésével megvizsgálhatja, hogy a rendszer hogyan vonja össze a **service.yaml** fájlban megadott környezeti változókat a háttérszolgáltatással való kapcsolatfelvételhez használt URL-ben.
4. Lépje át a `client.GetAsync(backendUrl).GetAwaiter().GetResult())` hívást (F10), és a következő töréspont a vezérlő `Get()`metódusában jelentkezik. Ezzel a módszerrel láthatja, hogy a rendszer hogyan olvassa be a teendők listáját a memóriabeli listából.
5. Ha elkészült, a **SHIFT + F5** billentyűkombináció lenyomásával állítsa le a projekt hibakeresését a Visual Studióban.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag a következőket ismertette:

> [!div class="checklist"]
> * Mi történik az Azure Service Fabric mash-alkalmazások fejlesztésekor
> * Hogyan állítható be egy töréspont a szolgáltatások közti hívás figyelése céljából

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Service Fabric Mesh-alkalmazás üzembe helyezése](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)

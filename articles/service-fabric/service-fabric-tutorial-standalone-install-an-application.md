---
title: Alkalmazás telepítése önálló fürtre
description: Ebből az oktatóanyagból megtudhatja, hogyan telepíthet alkalmazást önálló Service Fabric-fürtre.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: ae946321b34f12c816a717db4a3d07f57feefe52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96485360"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Oktatóanyag: Alkalmazás üzembe helyezése a különálló Service Fabric-fürtön

Az önálló Service Fabric-fürtök lehetővé teszik, hogy kiválassza a saját környezetét, és hogy a Service Fabric „bármely operációs rendszer, bármilyen felhő” módszerével hozzon létre egy fürtöt. Ebben az oktatóanyag-sorozatban létrehozunk egy AWS-ben üzemeltetett önálló fürtöt, és üzembe helyezünk egy alkalmazást.

Ez az oktatóanyag egy sorozat harmadik része.  Service Fabric önálló fürtök lehetővé teszi, hogy kiválassza a saját környezetét, és hozzon létre egy fürtöt a "bármely operációs rendszer, bármilyen felhő" megközelítés részeként a Service Fabric használatával. Ez az oktatóanyag bemutatja, hogyan hozhatja létre az önálló fürt futtatásához szükséges AWS-infrastruktúrát.

Ebből a cikkből megtudhatja, hogyan:

> [!div class="checklist"]
> * A mintaalkalmazás letöltése
> * Üzembe helyezés a fürtön

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* [Telepítse a Visual Studio 2019](https://www.visualstudio.com/) alkalmazást, és telepítse az **Azure fejlesztési** és **ASP.net, valamint a webes fejlesztési** feladatokat.
* [A Service Fabric SDK telepítése](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>A mintául szolgáló szavazóalkalmazás letöltése

Egy parancssori ablakban futtassa a következő parancsot a mintaalkalmazás-adattár helyi számítógépre történő klónozásához.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Az alkalmazás üzembe helyezése a Service Fabric-fürtben

Az alkalmazás letöltése után telepítheti azt a fürtön, közvetlenül a Visual Studióból.

1. A Visual Studio megnyitása

2. Válassza a **fájl**  >  **megnyitása** lehetőséget.

3. Navigáljon ahhoz a mappához, amelybe a Git-adattárat klónozta, és válassza ki a Voting.sln fájlt

4. A Megoldáskezelőben kattintson a jobb gombbal a `Voting` alkalmazásra, és válassza a **Közzététel** lehetőséget.

5. Kattintson a **Kapcsolati végpont** legördülő menüjére, és írja be a fürtben lévő egyik csomópont DNS-nevét.  Például: `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`. Az Azure-ban a teljes tartománynév (FQDN) nincs automatikusan megadva, de egyszerűen [beállítható a virtuális gép áttekintése lapon.](../virtual-machines/create-fqdn.md)

6. Nyissa meg az előnyben részesített böngészőt, és adja meg a fürt címét (a kapcsolati végpontot; ez az alkalmazás a 8080-as porton van üzembe helyezve; például: ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![A fürttől érkező API-válasz](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan helyezhet üzembe egy alkalmazást a fürtön:

> [!div class="checklist"]
> * A mintaalkalmazás letöltése
> * Üzembe helyezés a fürtön

A sorozat negyedik részében megtisztítja a fürtöt a felesleges erőforrásoktól.

> [!div class="nextstepaction"]
> [Az erőforrások törlése](service-fabric-tutorial-standalone-clean-up.md)
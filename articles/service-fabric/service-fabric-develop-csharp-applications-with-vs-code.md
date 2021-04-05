---
title: .NET Core-alkalmazások fejlesztése a Visual Studio Code használatával
description: Ez a cikk bemutatja, hogyan hozhat létre, helyezhet üzembe és kereshet .NET Core Service Fabric-alkalmazásokat a Visual Studio Code használatával.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 5fbd523a38b3c4860316e45b8b7c03a17de19499
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92678341"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>C# Service Fabric-alkalmazások fejlesztése a Visual Studio Code-ban

A [vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) -hoz készült Service Fabric Reliable Services-bővítmény megkönnyíti a .net Core Service Fabric-alkalmazások kiépítését Windows, Linux és MacOS operációs rendszereken.

Ebből a cikkből megtudhatja, hogyan hozhat létre, helyezhet üzembe és kereshet .NET Core Service Fabric-alkalmazásokat a Visual Studio Code használatával.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már telepítette a vs Code-ot, a Service Fabric Reliable Services bővítményt a VS Code-hoz, valamint a fejlesztési környezethez szükséges függőségeket. További információ: [első lépések](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>A minta letöltése
Ez a cikk a CounterService alkalmazást használja a [Service Fabric .net Core-ban – első lépések minták GitHub-tárház](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Ha a tárházat a fejlesztői gépre szeretné klónozott, futtassa a következő parancsot egy terminál-ablakból (Windowsos parancssorablak):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Az alkalmazás megnyitása a VS Code-ban

### <a name="windows"></a>Windows
Kattintson a jobb gombbal a VS Code ikonra a Start menüben, majd válassza a **Futtatás rendszergazdaként** lehetőséget. Ha a hibakeresőt a szolgáltatásaihoz szeretné csatolni, a VS Code-t rendszergazdaként kell futtatnia.

### <a name="linux"></a>Linux
A terminál használatával navigáljon ahhoz a könyvtárhoz, amelyről az alkalmazást helyileg klónozott/service-fabric-dotnet-core-getting-started/Services/CounterService.

Futtassa a következő parancsot a VS Code root felhasználóként való megnyitásához, hogy a hibakereső csatolható legyen a szolgáltatásokhoz.
```
sudo code . --user-data-dir='.'
```

Az alkalmazásnak ekkor meg kell jelennie a VS Code munkaterületen.

![Számláló szolgáltatás alkalmazása a munkaterületen](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Az alkalmazás létrehozása
1. Nyomja meg a (CTRL + SHIFT + p) billentyűkombinációt a **Command paletta** vs Code-ban való megnyitásához.
2. Keresse meg és válassza ki a **Service Fabric: build Application (alkalmazás létrehozása** ) parancsot. A rendszer a Build kimenetét az integrált terminálnak küldi el.

   ![Alkalmazás létrehozása parancs a VS Code-ban](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Az alkalmazás központi telepítése a helyi fürtre
Az alkalmazás létrehozása után üzembe helyezheti azt a helyi fürtön. 

1. A **parancs palettáján** válassza ki a **Service Fabric: Deploy Application (localhost) parancsot**. A telepítési folyamat kimenetét az integrált terminálba küldi a rendszer.

   ![Alkalmazás üzembe helyezése parancs a VS Code-ban](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Ha a telepítés befejeződött, indítson el egy böngészőt, és nyissa meg Service Fabric Explorer: http: \/ /localhost: 19080/Explorer. Látnia kell, hogy az alkalmazás fut. Ez eltarthat egy ideig, így türelmesnek kell lennie. 

   ![Counter Service-alkalmazás a Service Fabric Explorer](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Miután ellenőrizte, hogy az alkalmazás fut, indítson el egy böngészőt, és nyissa meg a következő oldalt: http: \/ /localhost: 31002. Ez az alkalmazás webes kezelőfelülete. Frissítse az oldalt, hogy megtekintse a számláló aktuális értékét, ahogy az növekszik.

   ![Counter Service-alkalmazás a böngészőben](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="publish-the-application-to-an-azure-service-fabric-cluster"></a>Az alkalmazás közzététele Azure Service Fabric-fürtön
Az alkalmazás helyi fürtön való üzembe helyezése mellett közzéteheti az alkalmazást egy távoli Azure Service Fabric-fürtön is. 

1. Győződjön meg arról, hogy az alkalmazást a fenti utasítások alapján építették. Frissítse a létrehozott konfigurációs fájlt a `Cloud.json` közzétenni kívánt távoli fürt részleteivel.

2. A **parancs palettáján** válassza ki a **Service Fabric: Application publish (alkalmazás közzététele) parancsot**. A telepítési folyamat kimenetét az integrált terminálba küldi a rendszer.

   ![Alkalmazás közzététele parancs a VS Code-ban](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-publish-application.png)

3. Ha a telepítés befejeződött, indítson el egy böngészőt, és nyissa meg Service Fabric Explorer: `https:<clusterurl>:19080/Explorer` . Látnia kell, hogy az alkalmazás fut. Ez eltarthat egy ideig, így türelmesnek kell lennie. 

## <a name="debug-the-application"></a>Az alkalmazás hibakeresése
Ha a VS Code alkalmazásban hibakeresést végez, az alkalmazásnak helyi fürtön kell futnia. A töréspontok ezután hozzáadhatók a kódhoz.

Töréspont és hibakeresés beállításához hajtsa végre a következő lépéseket:
1. Az Intézőben nyissa meg a */src/CounterServiceApplication/CounterService/CounterService.cs* fájlt, és állítson be egy töréspontot a 62. sorban a `RunAsync` metóduson belül.
3. A VS Code-ban a hibakereső nézet megnyitásához kattintson a **tevékenység sávján** található hibakeresés ikonra. Kattintson a fogaskerék ikonra a hibakereső nézet tetején, és válassza a **.net Core** elemet a legördülő menüből. Megnyílik a launch.jsfájl. Bezárhatja ezt a fájlt. Most meg kell jelennie a konfigurációs beállításoknak a Futtatás gomb melletti hibakeresési konfigurációs menüben (zöld nyíl).

   ![Hibakeresés ikon a VS Code munkaterületen](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Válassza a **.net Core csatolás** lehetőséget a hibakeresési konfiguráció menüjében.

   ![Képernyőkép, amely a hibakeresési konfiguráció menüjében kijelölt .NET Core csatolást jeleníti meg.](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Service Fabric Explorer megnyitása böngészőben: http: \/ /localhost: 19080/Explorer. Kattintson az **alkalmazások** elemre, és válassza ki azt az elsődleges csomópontot, amelyen a CounterService fut. Az alábbi képen a CounterService elsődleges csomópontja a 0. csomópont.

   ![CounterService elsődleges csomópontja](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. A VS Code-ban kattintson a Futtatás ikonra (zöld nyíl) a **.net Core csatolási** hibakeresési konfiguráció mellett. A folyamat kiválasztása párbeszédpanelen válassza ki a 4. lépésben azonosított elsődleges csomóponton futó CounterService folyamatot.

   ![Elsődleges folyamat](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. A *CounterService. cs* fájl töréspontja nagyon gyorsan fog megjelenni. Ezután megismerheti a helyi változók értékeit. A VS Code tetején található hibakeresés eszköztár használatával folytathatja a végrehajtást, átléphet a vonalakon, megkezdheti a metódusokat, vagy kiléphet az aktuális metódusból. 

   ![Hibakeresési értékek](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. A hibakeresési munkamenet befejezéséhez kattintson a VS Code oldal tetején található hibakeresés eszköztáron a dugó ikonra.
   
   ![Leválasztás a hibakeresőből](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. A hibakeresés befejezése után a **Service Fabric: Remove Application** paranccsal távolítsa el a CounterService alkalmazást a helyi fürtből. 

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [fejleszthet és kereshet Java Service Fabric-alkalmazásokat a vs Code](./service-fabric-develop-java-applications-with-vs-code.md)használatával.




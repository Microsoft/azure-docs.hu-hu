---
title: Azure Service Fabric VS Code–Első lépések
description: Ez a cikk áttekintést nyújt a Service Fabric code használatával való Visual Studio létrehozásáról.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.custom: devx-track-js
ms.openlocfilehash: 7d54b4b048632324a58708f893a4778a56137916
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876078"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric a Visual Studio Code-hez

A [Service Fabric Reliable Services VS Code-hoz](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) használható bővítmény biztosítja a Windows, Linux és macOS operációs rendszereken Service Fabric alkalmazások létrehozásához, létrehozásához és hibakereséséhez szükséges eszközöket.

Ez a cikk áttekintést nyújt a bővítmény követelményeiről és beállításáról, valamint a bővítmény által biztosított különböző parancsok használatáról. 

> [!IMPORTANT]
> Service Fabric Java-alkalmazások Windows rendszerű gépeken fejleszthatók, de csak Azure Linux-fürtökön helyezhetők üzembe. A Java-alkalmazások hibakeresése Windows rendszeren nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Az alábbi előfeltételeket minden környezetben telepíteni kell.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Service Fabric SDK](./service-fabric-get-started.md)
* Yeoman Generators – az alkalmazáshoz megfelelő generátorok telepítése

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Java-fejlesztéshez a következő előfeltételeket kell telepíteni:

* [Java SDK](/azure/developer/java/fundamentals/java-jdk-long-term-support) (1.8-as verzió)
* [Gradle](https://gradle.org/install/)
* [Hibakereső Java VS Code-bővítményhez](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Java-szolgáltatások hibakeresése szükséges. A Java-szolgáltatások hibakeresése csak Linux rendszeren támogatott. A telepítéshez kattintson a BŐVÍTMÉNYEK ikonra a VS Code tevékenységsávján, és rákeresve a bővítményre, vagy a VS Code Marketplace-en. 

A .NET Core-/C#-fejlesztéshez a következő előfeltételeket kell telepíteni:

* [.NET Core](https://dotnet.microsoft.com/download) (2.0.0-s vagy újabb verzió)
* [C# a Visual Studio Code-on (az OmniSharp által működtetve) VS Code-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) A C#-szolgáltatások hibakeresése szükséges. A telepítéshez kattintson a BŐVÍTMÉNYEK ikonra a VS Code tevékenységsávján, és rákeresve a bővítményre, vagy a VS Code Marketplace-en. 

## <a name="setup"></a>Beállítás

1. Nyissa meg a VS Code-ot.
2. Kattintson a VS Code  bal oldalán található Tevékenységsáv Bővítmények ikonjára. Keressen rá a "Service Fabric" kifejezésre. Kattintson **az Install** (Telepítés) Service Fabric Reliable Services bővítményhez.

## <a name="commands"></a>Parancsok
A Service Fabric Reliable Services VS Code-hoz készült bővítmény számos parancsot biztosít, amelyek segítségével a fejlesztők létrehozhatnak és üzembe helyezhetnek Service Fabric projekteket. A parancskatajánlából úgy hívhat meg parancsokat, hogy lenyomja a parancs nevét a beviteli sávban, majd kiválasztja a kívánt parancsot a  `(Ctrl + Shift + p)` parancssori listából. 

* Service Fabric: Alkalmazás létrehozása 
* Service Fabric: Alkalmazás közzététele 
* Service Fabric: Alkalmazás üzembe helyezése 
* Service Fabric: Alkalmazás eltávolítása  
* Service Fabric: Alkalmazás összeállítása 
* Service Fabric: Clean Application 

### <a name="service-fabric-create-application"></a>Service Fabric: Alkalmazás létrehozása

A **Service Fabric: Alkalmazás létrehozása parancs** új Service Fabric hoz létre az aktuális munkaterületen. Attól függően, hogy mely Yeoman generátorok vannak telepítve a fejlesztői gépen, számos különböző típusú Service Fabric-alkalmazást hozhat létre, például Java-, C#-, tároló- és vendégprojekteket. 

1.  Válassza a **Service Fabric: Create Application (Alkalmazás létrehozása)** parancsot
2.  Válassza ki az új Service Fabric típusát. 
3.  Adja meg a létrehozni kívánt alkalmazás nevét
3.  Válassza ki a szolgáltatástípust, amit hozzá szeretne adni a Service Fabric alkalmazáshoz. 
4.  Az utasításokat választva nevezze el a szolgáltatást. 
5.  Az új Service Fabric alkalmazás megjelenik a munkaterületen.
6.  Nyissa meg az új alkalmazásmappát, hogy az a munkaterület gyökérmappa lesz. Innen folytathatja a parancsok futtatását.

### <a name="service-fabric-add-service"></a>Service Fabric: Szolgáltatás hozzáadása
A **Service Fabric: Szolgáltatás** hozzáadása parancs új szolgáltatást ad hozzá egy meglévő Service Fabric alkalmazáshoz. Az alkalmazásnak, amelybe a szolgáltatást hozzá fogja adni, a munkaterület gyökérkönyvtárának kell lennie. 

1.  Válassza a **Service Fabric: Szolgáltatás hozzáadása** parancsot.
2.  Válassza ki az aktuálisan Service Fabric típusát. 
3.  Válassza ki a szolgáltatástípust, amit hozzá szeretne adni a Service Fabric alkalmazáshoz. 
4.  Kövesse az utasításokat a szolgáltatás elnevezéshez. 
5.  Az új szolgáltatás megjelenik a projektkönyvtárban. 

### <a name="service-fabric-publish-application"></a>Service Fabric: Alkalmazás közzététele
A **Service Fabric: Alkalmazás** közzététele parancs üzembe Service Fabric alkalmazás egy távoli fürtön. A célfürt lehet biztonságos vagy nem biztonságos fürt. Ha a paraméterek nincsenek beállítva a Cloud.js, az alkalmazás a helyi fürtön lesz üzembe állítva.

1.  Az alkalmazás első létrehozásakor létrejön Cloud.jsfájlon található fájl a projektkönyvtárban.
2.  Adja meg annak a fürtnek az értékeit, amelyekhez csatlakozni szeretne a Cloud.jsfájlban.
3.  Válassza a **Service Fabric: Alkalmazás közzététele parancsot.**
4.  Tekintse meg a célfürtöt Service Fabric Explorer, hogy az alkalmazás telepítve van-e. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: Alkalmazás üzembe helyezése (Localhost)
A **Service Fabric: Alkalmazás** üzembe helyezése parancs üzembe Service Fabric a helyi fürtön. A parancs használata előtt győződjön meg arról, hogy a helyi fürt fut. 

1. Válassza a **Service Fabric: Deploy Application (Alkalmazás üzembe helyezése)** parancsot
2. Tekintse meg a helyi fürtöt Service Fabric Explorer (http: \/ /localhost:19080/Explorer) annak megerősítéséhez, hogy az alkalmazás telepítve van. Ez némi időt is vegyen el, úgyhogy legyen beteg.
3. Használhatja a **Service Fabric:** Publish Application (Alkalmazás közzététele) parancsot is, ha nincs beállítva paraméter a Cloud.jsa fájlban a helyi fürtön való üzembe helyezéshez.

> [!NOTE]
> A Java-alkalmazások helyi fürtön való üzembe helyezése Windows rendszerű gépeken nem támogatott.

### <a name="service-fabric-remove-application"></a>Service Fabric: Alkalmazás eltávolítása
A **Service Fabric:** Alkalmazás eltávolítása parancs eltávolít egy Service Fabric-alkalmazást a fürtről, amelyet korábban a VS Code-bővítmény használatával üzembe helyezett. 

1.  Válassza a **Service Fabric: Alkalmazás eltávolítása** parancsot.
2.  Tekintse meg a fürtöt a Service Fabric Explorer, és ellenőrizze, hogy az alkalmazás el lett-e távolítva. Ez némi időt is vegyen el, úgyhogy legyen beteg.

### <a name="service-fabric-build-application"></a>Service Fabric: Alkalmazás összeállítása
A **Service Fabric: Build Application parancs** Java- vagy C#-alkalmazásokat Service Fabric létre. 

1.  A parancs végrehajtása előtt győződjön meg arról, hogy az alkalmazás gyökérmappában van. A parancs azonosítja az alkalmazás típusát (C# vagy Java), és ennek megfelelően buildeli az alkalmazást.
2.  Válassza a **Service Fabric: Build Application (Alkalmazás összeállítása)** parancsot.
3.  A buildfolyamat kimenete az integrált terminálba van írva.

### <a name="service-fabric-clean-application"></a>Service Fabric: Clean Application
A **Service Fabric: Clean Application parancs** törli a build által létrehozott összes JAR-fájlt és natív kódtárat. Csak Java-alkalmazásokhoz érvényes. 

1.  A parancs végrehajtása előtt győződjön meg arról, hogy az alkalmazás gyökérmappában van. 
2.  Válassza a **Service Fabric: Clean Application (Alkalmazás tisztítása)** parancsot.
3.  A tiszta folyamat kimenete az integrált terminálba lesz írva.

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [fejleszthet C#-alkalmazásokat és Service Fabric a VS Code-kóddal.](./service-fabric-develop-csharp-applications-with-vs-code.md)
* Útmutató Java-alkalmazások fejlesztéséhez és [hibakereséséhez Service Fabric VS Code használatával.](./service-fabric-develop-java-applications-with-vs-code.md)
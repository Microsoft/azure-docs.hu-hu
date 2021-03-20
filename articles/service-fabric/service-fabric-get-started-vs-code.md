---
title: Azure Service Fabric VS Code-Első lépések
description: Ez a cikk áttekintést nyújt Service Fabric alkalmazások Visual Studio Code használatával történő létrehozásáról.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.custom: devx-track-js
ms.openlocfilehash: a655199d854462285ca98a2d8f454c483c061f99
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92316671"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric a Visual Studio Code-hoz

A [vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) -hoz készült Service Fabric Reliable Services-bővítmény biztosítja azokat az eszközöket, amelyek Service Fabric alkalmazások Windows, Linux és MacOS operációs rendszereken való létrehozásához, elkészítéséhez és hibakereséséhez szükségesek.

Ez a cikk áttekintést nyújt a bővítmény követelményeiről és beállításáról, valamint a bővítmény által biztosított különböző parancsok használatáról. 

> [!IMPORTANT]
> Service Fabric Java-alkalmazások fejleszthetők Windows rendszerű gépeken, de csak az Azure Linux-fürtökön helyezhetők üzembe. A Java-alkalmazások hibakeresése Windows rendszeren nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételeket minden környezetben telepíteni kell.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Service Fabric SDK](./service-fabric-get-started.md)
* Yeoman-generátorok – az alkalmazáshoz tartozó megfelelő generátorok telepítése

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

A Java-fejlesztéshez a következő előfeltételeket kell telepíteni:

* [Java SDK](/azure/developer/java/fundamentals/java-jdk-long-term-support) (1,8-es verzió)
* [Gradle](https://gradle.org/install/)
* [Hibakereső a Java vs Code bővítményhez](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) A Java-szolgáltatások hibakereséséhez szükséges. A Java-szolgáltatások hibakeresése csak Linux rendszeren támogatott. A-t a VS Code **tevékenység sávján** található Extensions (bővítmények) ikonra kattintva telepítheti, és megkeresheti a bővítményt, vagy a vs Code piactéren is.

A .NET Core/C# fejlesztéshez a következő előfeltételeket kell telepíteni:

* [.Net Core](https://www.microsoft.com/net/learn/get-started) (2.0.0 vagy újabb verzió)
* [C# for Visual Studio Code (OmniSharp-alapú) vs Code-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) A C#-szolgáltatások hibakereséséhez szükséges. A-t a VS Code **tevékenység sávján** található Extensions (bővítmények) ikonra kattintva telepítheti, és megkeresheti a bővítményt, vagy a vs Code piactéren is.

## <a name="setup"></a>Beállítás

1. Nyissa meg a VS Code-ot.
2. Kattintson a VS Code bal oldalán található **Activity sávban** a bővítmények ikonra. Keressen rá a "Service Fabric" kifejezésre. Kattintson a **telepítés** elemre a Service Fabric Reliable Services bővítményhez.

## <a name="commands"></a>Parancsok
A VS Code Service Fabric Reliable Services bővítménye számos parancsot biztosít a fejlesztők számára Service Fabric projektek létrehozásához és üzembe helyezéséhez. A parancsok a **parancssorból** való meghívásához nyomja le `(Ctrl + Shift + p)` a parancsot, írja be a parancs nevét a bemeneti sávra, majd válassza ki a kívánt parancsot a prompt listából. 

* Service Fabric: alkalmazás létrehozása 
* Service Fabric: alkalmazás közzététele 
* Service Fabric: alkalmazás üzembe helyezése 
* Service Fabric: alkalmazás eltávolítása  
* Service Fabric: alkalmazás összeállítása 
* Service Fabric: tiszta alkalmazás 

### <a name="service-fabric-create-application"></a>Service Fabric: alkalmazás létrehozása

A **Service Fabric: Create Application** parancs létrehoz egy új Service Fabric alkalmazást az aktuális munkaterületen. Attól függően, hogy mely Yeoman-generátorok vannak telepítve a fejlesztői gépen, többféle Service Fabric alkalmazást hozhat létre, beleértve a Java, a C#, a Container és a Guest projekteket is. 

1.  Válassza ki a **Service Fabric: Create Application** parancs
2.  Válassza ki az új Service Fabric alkalmazás típusát. 
3.  Adja meg a létrehozni kívánt alkalmazás nevét
3.  Válassza ki a Service Fabric alkalmazáshoz hozzáadni kívánt szolgáltatás típusát. 
4.  A szolgáltatás nevének megadásához kövesse az utasításokat. 
5.  Az új Service Fabric alkalmazás megjelenik a munkaterületen.
6.  Nyissa meg az új alkalmazás mappáját, hogy az a munkaterület legfelső mappájába kerüljön. Innen továbbra is végrehajthat parancsokat.

### <a name="service-fabric-add-service"></a>Service Fabric: szolgáltatás hozzáadása
A **Service Fabric: Add Service** parancs új szolgáltatást ad hozzá egy meglévő Service Fabric alkalmazáshoz. A szolgáltatáshoz hozzáadni kívánt alkalmazásnak a munkaterület gyökérkönyvtárának kell lennie. 

1.  Válassza ki a **Service Fabric: Add Service** parancsot.
2.  Válassza ki a jelenlegi Service Fabric alkalmazás típusát. 
3.  Válassza ki a Service Fabric alkalmazáshoz hozzáadni kívánt szolgáltatás típusát. 
4.  A szolgáltatás nevének megadásához kövesse az utasításokat. 
5.  Az új szolgáltatás megjelenik a projekt címtárában. 

### <a name="service-fabric-publish-application"></a>Service Fabric: alkalmazás közzététele
A **Service Fabric: Application publish** parancs üzembe helyezi a Service Fabric alkalmazást egy távoli fürtön. A célként megadott fürt biztonságos vagy nem biztonságos fürt lehet. Ha a paraméterek nincsenek megadva a Cloud.json beállításban, az alkalmazás a helyi fürtre lesz telepítve.

1.  Az alkalmazás első felépítésekor a rendszer létrehoz egy Cloud.jsa fájlban a projekt könyvtárában.
2.  Adja meg annak a fürtnek az értékeit, amelyhez csatlakozni szeretne a fájl Cloud.jsjában.
3.  Válassza ki a **Service Fabric: Application publish (alkalmazás közzététele** ) parancsot.
4.  Service Fabric Explorer megtekintheti a célként megadott fürtöt, és ellenőrizheti, hogy az alkalmazás telepítve van-e. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: alkalmazás üzembe helyezése (localhost)
A **Service Fabric: alkalmazás telepítése** parancs üzembe helyezi a Service Fabric alkalmazást a helyi fürtön. A parancs használata előtt győződjön meg arról, hogy a helyi fürt fut. 

1. Válassza ki a **Service Fabric: alkalmazás telepítése** parancs
2. A helyi fürtöt Service Fabric Explorer (http: \/ /localhost: 19080/Explorer) megtekintve ellenőrizheti, hogy telepítve van-e az alkalmazás. Ez eltarthat egy ideig, így türelmesnek kell lennie.
3. A **Service Fabric: alkalmazás közzététele** parancs nem állítható be paraméterek nélkül a fájl Cloud.jsa helyi fürtre történő központi telepítéshez.

> [!NOTE]
> A Java-alkalmazások helyi fürtön történő üzembe helyezése Windows rendszerű gépeken nem támogatott.

### <a name="service-fabric-remove-application"></a>Service Fabric: alkalmazás eltávolítása
A **Service Fabric: Remove Application** parancs eltávolít egy Service Fabric alkalmazást a fürtből, amelyet korábban a vs Code bővítmény használatára telepített. 

1.  Válassza ki a **Service Fabric: alkalmazás eltávolítása** parancsot.
2.  Tekintse meg a fürtöt a Service Fabric Explorer segítségével annak ellenőrzéséhez, hogy az alkalmazás el lett-e távolítva. Ez eltarthat egy ideig, így türelmesnek kell lennie.

### <a name="service-fabric-build-application"></a>Service Fabric: alkalmazás összeállítása
A **Service Fabric: build Application** parancs Java vagy C# Service Fabric alkalmazásokat hozhat létre. 

1.  A parancs végrehajtása előtt győződjön meg arról, hogy az alkalmazás gyökérkönyvtárában van. A parancs azonosítja az alkalmazás típusát (C# vagy Java), és ennek megfelelően építi fel az alkalmazást.
2.  Válassza ki a **Service Fabric: build Application (alkalmazás létrehozása** ) parancsot.
3.  Az összeállítási folyamat kimenete az integrált terminálba íródik.

### <a name="service-fabric-clean-application"></a>Service Fabric: tiszta alkalmazás
A **Service Fabric: Clean Application** parancs törli a Build által generált összes jar-fájlt és natív kódtárat. Csak Java-alkalmazásokhoz használható. 

1.  A parancs végrehajtása előtt győződjön meg arról, hogy az alkalmazás gyökérkönyvtárában van. 
2.  Válassza a **Service Fabric: Clean Application (alkalmazás tisztítása** ) parancsot.
3.  A tiszta folyamat kimenete az integrált terminálba íródik.

## <a name="next-steps"></a>Következő lépések

* Útmutató [C# Service Fabric alkalmazások fejlesztéséhez és hibakereséséhez a vs Code](./service-fabric-develop-csharp-applications-with-vs-code.md)használatával.
* Ismerje meg, hogyan [fejleszthet és kereshet Java Service Fabric-alkalmazásokat a vs Code](./service-fabric-develop-java-applications-with-vs-code.md)használatával.
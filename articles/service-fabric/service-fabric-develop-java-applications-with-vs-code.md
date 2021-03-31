---
title: Java-alkalmazások fejlesztése a Visual Studio Code-ban
description: Ez a cikk bemutatja, hogyan hozhat létre, helyezhet üzembe és kereshet Java Service Fabric-alkalmazásokat a Visual Studio Code használatával.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.custom: devx-track-java
ms.author: pepogors
ms.openlocfilehash: cc65deb924a9f3367c2ea1d7c71544743ccf2697
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87327361"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Java Service Fabric-alkalmazások fejlesztése a Visual Studio Code-ban

A [vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) -hoz készült Service Fabric Reliable Services-bővítmény megkönnyíti a Java-Service Fabric alkalmazások Windows, Linux és MacOS operációs rendszereken való összeállítását.

Ez a cikk bemutatja, hogyan hozhat létre, helyezhet üzembe és kereshet Java Service Fabric-alkalmazásokat a Visual Studio Code használatával.

> [!IMPORTANT]
> Service Fabric Java-alkalmazások fejleszthetők Windows rendszerű gépeken, de csak az Azure Linux-fürtökön helyezhetők üzembe. A Java-alkalmazások hibakeresése Windows rendszeren nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már telepítette a vs Code-ot, a Service Fabric Reliable Services bővítményt a VS Code-hoz, valamint a fejlesztési környezethez szükséges függőségeket. További információ: [első lépések](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>A minta letöltése
Ez a cikk a szavazási alkalmazást használja a Service Fabric Java-alkalmazás rövid útmutatójában a [GitHub-tárházban](https://github.com/Azure-Samples/service-fabric-java-quickstart). 

Ha a tárházat a fejlesztői gépre szeretné klónozott, futtassa a következő parancsot egy terminál-ablakból (Windowsos parancssorablak):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Az alkalmazás megnyitása a VS Code-ban

Nyissa meg a VS Code-ot.  Kattintson az Intéző ikonra a **tevékenység sávján** , majd kattintson a **mappa megnyitása** lehetőségre, vagy kattintson a  **fájl > Megnyitás mappa** elemre. Keresse meg a *./Service-Fabric-Java-Quickstart/voting* könyvtárat abban a mappában, ahová a tárházat klónozotta, majd kattintson az **OK** gombra. A munkaterületnek az alábbi képernyőképen látható fájlokat kell tartalmaznia.

![Java-szavazati alkalmazás a munkaterületen](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Az alkalmazás létrehozása

1. Nyomja meg a (CTRL + SHIFT + p) billentyűkombinációt a **Command paletta** vs Code-ban való megnyitásához.
2. Keresse meg és válassza ki a **Service Fabric: build Application (alkalmazás létrehozása** ) parancsot. A rendszer a Build kimenetét az integrált terminálnak küldi el.

   ![Alkalmazás létrehozása parancs a VS Code-ban](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Az alkalmazás központi telepítése a helyi fürtre
Az alkalmazás létrehozása után üzembe helyezheti azt a helyi fürtön. 

> [!IMPORTANT]
> A Java-alkalmazások helyi fürtön történő üzembe helyezése Windows rendszerű gépeken nem támogatott.

1. A **parancs palettáján** válassza ki a **Service Fabric: Deploy Application (localhost) parancsot**. A telepítési folyamat kimenetét az integrált terminálba küldi a rendszer.

   ![Alkalmazás üzembe helyezése parancs a VS Code-ban](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Ha a telepítés befejeződött, indítson el egy böngészőt, és nyissa meg Service Fabric Explorer: `http://localhost:19080/Explorer` . Látnia kell, hogy az alkalmazás fut. Ez eltarthat egy ideig, így türelmesnek kell lennie. 

   ![Szavazási alkalmazás Service Fabric Explorer](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Miután ellenőrizte, hogy fut-e az alkalmazás, indítson el egy böngészőt, és nyissa meg a következő oldalt: `http://localhost:8080` . Ez az alkalmazás webes kezelőfelülete. Hozzáadhat elemeket, és kattintson rájuk a szavazáshoz.

   ![Szavazási alkalmazás a böngészőben](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Ha el szeretné távolítani az alkalmazást a fürtből, válassza a **Service Fabric: alkalmazás eltávolítása** parancsot a **parancssorból**. Az eltávolítási folyamat kimenetét az integrált terminálra küldi a rendszer. A Service Fabric Explorer segítségével ellenőrizheti, hogy az alkalmazás el lett-e távolítva a helyi fürtből.

## <a name="debug-the-application"></a>Az alkalmazás hibakeresése
Ha a VS Code alkalmazásban hibakeresést végez, az alkalmazásnak helyi fürtön kell futnia. A töréspontok ezután hozzáadhatók a kódhoz.

> [!IMPORTANT]
> A Java-alkalmazások hibakeresése Windows rendszerű gépeken nem támogatott.

A VotingDataService és a szavazó alkalmazás hibakereséshez való előkészítéséhez végezze el a következő lépéseket:

1. Frissítse a *szavazó/VotingApplication/VotingDataServicePkg/Code/BelépésiPont. sh* fájlt.
Tegye megjegyzésbe a parancsot a 6. sorban (használja a "#" utasítást), és adja hozzá a következő parancsot a fájl aljához:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. A *szavazási/VotingApplication/ApplicationManifest.xml* fájl frissítése. Állítsa a **MinReplicaSetSize** és a **TargetReplicaSetSize** attribútumot "1" értékre a **StatefulService** elemben:
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. A VS Code-ban a hibakereső nézet megnyitásához kattintson a **tevékenység sávján** található hibakeresés ikonra. Kattintson a fogaskerék ikonra a hibakereső nézet tetején, és válassza a **Java** elemet a legördülő menüből. Megnyílik a launch.jsfájl. 

   ![Hibakeresés ikon a VS Code munkaterületen](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. A launch.jsfájlon állítsa be a port értékét a **Debug (csatolás)** nevű konfigurációban a **8001** értékre. Mentse a fájlt.

   ![Hibakeresési konfiguráció a launch.json](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Telepítse az alkalmazást a helyi fürtre a **Service Fabric: Deploy Application (localhost)** parancs használatával. Ellenőrizze, hogy az alkalmazás fut-e Service Fabric Explorer. Az alkalmazás most már készen áll a hibakeresésre.

Töréspont beállításához hajtsa végre a következő lépéseket:

1. Az Explorerben nyissa meg a */voting/VotingDataService/src/statefulservice/VotingDataService.Java* fájlt. Állítson be egy töréspontot a kód első sorában a `try` metódusban található blokkban `addItem` (80. sor).
   
   ![Töréspont beállítása a szavazó adatszolgáltatásban](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Ügyeljen arra, hogy töréspontokat állítson be a kód végrehajtható sorain. `try` `catch` A Debugger nem fogja kihagyni például a metódus deklarációinak, utasításait vagy utasításait használó töréspontokat.
2. A hibakeresés megkezdéséhez kattintson a hibakeresés ikonra a **tevékenység sávján**, válassza a hibakeresés **(csatolás)** konfigurációt a hibakeresés menüben, majd kattintson a Futtatás gombra (zöld nyíl).

   ![Hibakeresés (csatolás) konfigurálása](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. Egy böngészőben nyissa meg a következőt: `http://localhost:8080`. Írjon be egy új tételt a szövegmezőbe, majd kattintson a **+ Hozzáadás** gombra. A töréspontot meg kell érinteni. A VS Code tetején található hibakeresés eszköztár használatával folytathatja a végrehajtást, átadhatja a vonalakat, beléphet a metódusokra, vagy kiléphet az aktuális metódusból. 
   
   ![Találati töréspont](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. A hibakeresési munkamenet befejezéséhez kattintson a VS Code felső részén található hibakeresés eszköztáron a dugó ikonra.
   
   ![Leválasztás a hibakeresőből](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. A hibakeresés befejezésekor a **Service Fabric: Remove Application** paranccsal távolíthatja el a szavazati alkalmazást a helyi fürtből. 

## <a name="next-steps"></a>Következő lépések

* Útmutató [C# Service Fabric alkalmazások fejlesztéséhez és hibakereséséhez a vs Code](./service-fabric-develop-csharp-applications-with-vs-code.md)használatával.

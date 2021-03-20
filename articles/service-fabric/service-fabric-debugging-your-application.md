---
title: Az alkalmazás hibakeresése a Visual Studióban
description: A szolgáltatások megbízhatóságának és teljesítményének javítása a Visual Studióban a helyi fejlesztési fürtön való fejlesztéssel és hibakereséssel.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 0b7d08d610c883240abedc66c55abba64a74c8e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576315"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Service Fabric-alkalmazás hibakeresése a Visual Studióval
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Helyi Service Fabric-alkalmazás hibakeresése
Időt és pénzt takaríthat meg az Azure Service Fabric-alkalmazás üzembe helyezésével és hibakeresésével egy helyi számítógép-fejlesztési fürtben. A Visual Studio 2019 vagy a 2015 képes telepíteni az alkalmazást a helyi fürtre, és automatikusan összekapcsolja a hibakeresőt az alkalmazás összes példányával. A hibakeresőhöz a Visual studiót rendszergazdaként kell futtatni.

1. Indítsa el a helyi fejlesztési fürtöt a [Service Fabric fejlesztői környezet beállítása](service-fabric-get-started.md)című témakör lépéseit követve.
2. Nyomja le az **F5** billentyűt, **vagy kattintson a hibakeresés**  >  **indítása** gombra.
   
    ![A hibakeresés menüt megjelenítő képernyőkép.][startdebugging]
3. A **hibakeresés** menüben a parancsok elemre kattintva adja meg a töréspontokat a kódban, és lépjen az alkalmazásba.
   
   > [!NOTE]
   > A Visual Studio az alkalmazás összes példányához csatlakozik. A kód megismétlése közben a töréspontokat több folyamat is elérheti, ami az egyidejű munkameneteket eredményezi. Próbálja meg letiltani a töréspontokat a találatok után, hogy az egyes töréspontok feltételesek legyenek a szál azonosítójával vagy diagnosztikai események használatával.
   > 
   > 
4. A **diagnosztikai események** ablak automatikusan megnyílik, így valós időben megtekintheti a diagnosztikai eseményeket.
   
    ![Diagnosztikai események megtekintése valós időben][diagnosticevents]
5. A **diagnosztikai események** ablak a Cloud Explorerben is megnyitható.  A **Service Fabric** alatt kattintson a jobb gombbal bármelyik csomópontra, és válassza a **folyamatos átviteli Nyomkövetések megtekintése** lehetőséget.
   
    ![A diagnosztikai események ablak megnyitása][viewdiagnosticevents]
   
    Ha egy adott szolgáltatásra vagy alkalmazásra kívánja szűrni a nyomkövetést, engedélyezze a folyamatos átviteli nyomkövetést az adott szolgáltatáson vagy alkalmazáson.
6. A diagnosztikai események az automatikusan generált **ServiceEventSource. cs** fájlban láthatók, és az alkalmazás kódjából lesznek meghívva.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. A **diagnosztikai események** ablak valós időben támogatja az események szűrését, szüneteltetését és vizsgálatát.  A szűrő egy egyszerű karakterlánc-keresés az esemény üzenetében, beleértve annak tartalmát is.
   
    ![Események szűrése, szüneteltetése és folytatása, illetve valós idejű vizsgálat][diagnosticeventsactions]
8. A hibakeresési szolgáltatások olyanok, mint bármely más alkalmazás hibakeresése. Az egyszerű hibakereséshez általában a Visual studión keresztül kell beállítani a töréspontokat. Annak ellenére, hogy a megbízható gyűjtemények több csomóponton replikálódnak, továbbra is implementálják a IEnumerable. Ez a megvalósítás azt jelenti, hogy a Visual Studióban a Results nézetet használhatja, miközben a hibakeresés során megtekintheti, hogy mit tárol a szolgáltatáson belül. Ehhez állítson be egy töréspontot bárhol a kódban.
   
    ![Alkalmazás hibakeresésének megkezdése][breakpoint]


### <a name="running-a-script-as-part-of-debugging"></a>Parancsfájl futtatása hibakeresés részeként
Bizonyos esetekben előfordulhat, hogy parancsfájlt kell futtatnia egy hibakeresési munkamenet elindításának részeként (például ha nem használja az alapértelmezett szolgáltatásokat).

A Visual Studióban hozzáadhat egy **Start-Service.ps1** nevű fájlt a Service Fabric alkalmazás projekt **szkriptek** mappájában (. sfproj). Ezt a szkriptet az alkalmazás helyi fürtben való létrehozása után hívja meg a rendszer.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Távoli Service Fabric-alkalmazás hibakeresése
Ha a Service Fabric-alkalmazások egy Azure-beli Service Fabric-fürtön futnak, ezeket az alkalmazásokat távolról, közvetlenül a Visual studióból is felhasználhatja.

> [!NOTE]
> A szolgáltatáshoz [Service FABRIC SDK 2,0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) és [Az Azure sdk for .NET 2,9](https://azure.microsoft.com/downloads/)szükséges.    

<!-- -->
> [!WARNING]
> A távoli hibakeresés fejlesztési és tesztelési forgatókönyvekhez készült, és nem használható éles környezetben, a futó alkalmazásokra gyakorolt hatás miatt.

1. Navigáljon a fürthöz a **Cloud Explorerben**. Kattintson a jobb gombbal, és válassza a **hibakeresés engedélyezése** lehetőséget.
   
    ![Távoli hibakeresés engedélyezése][enableremotedebugging]
   
    Ez a művelet lekéri a Távoli hibakeresési bővítmény engedélyezésének folyamatát a fürtcsomópontok és a szükséges hálózati konfigurációk számára.
2. Kattintson a jobb gombbal a fürt csomópontjára a **Cloud Explorerben**, és válassza a **hibakereső csatolása** elemet.
   
    ![Hibakereső csatolása][attachdebugger]
3. A **csatolás folyamathoz** párbeszédpanelen válassza ki a hibakereséshez használni kívánt folyamatot, majd kattintson a **csatolás** gombra.
   
    ![Folyamat kiválasztása][chooseprocess]
   
    Annak a folyamatnak a neve, amelyhez csatolni szeretné, megegyezik a szolgáltatási projekt szerelvényének nevével.
   
    A hibakereső a folyamatot futtató összes csomóponthoz csatolva lesz.
   
   * Ha állapot nélküli szolgáltatást futtat, a szolgáltatás minden példánya a hibakeresési munkamenet részét képezi.
   * Ha állapot-nyilvántartó szolgáltatást használ, akkor csak a partíciók elsődleges replikája lesz aktív, ezért a hibakereső fogja észlelni. Ha az elsődleges replika a hibakeresési munkamenet során mozog, akkor a replika feldolgozása továbbra is a hibakeresési munkamenet részét képezi.
   * Ha csak az adott szolgáltatáshoz tartozó partíciókat vagy példányokat szeretné kifogni, a feltételes töréspontok segítségével csak egy adott partíciót vagy példányt lehet megszüntetni.
     
     ![Feltételes töréspont][conditionalbreakpoint]
     
     > [!NOTE]
     > Jelenleg nem támogatott egy Service Fabric-fürt hibakeresése ugyanazzal a szolgáltatás-végrehajtható névvel rendelkező több példánykal.
     > 
     > 
4. Miután befejezte az alkalmazás hibakeresését, letilthatja a Távoli hibakeresési bővítményt, ha a jobb gombbal a fürtre kattint a **Cloud Explorerben** , és kiválasztja a **hibakeresés letiltása** lehetőséget.
   
    ![Távoli hibakeresés letiltása][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Adatfolyam-nyomkövetés távoli fürtcsomópont-csomópontból
A nyomkövetéseket közvetlenül egy távoli fürtcsomóponton is képes továbbítani a Visual studióba. Ez a funkció lehetővé teszi, hogy egy Service Fabric fürtcsomóponton előállított ETW nyomkövetési eseményeket továbbítsa.

> [!NOTE]
> Ehhez a szolgáltatáshoz [Service FABRIC SDK 2,0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) és [Az Azure sdk for .NET 2,9](https://azure.microsoft.com/downloads/)szükséges.
> Ez a funkció csak az Azure-ban futó fürtöket támogatja.
> 
> 

<!-- -->
> [!WARNING]
> A folyamatos átviteli Nyomkövetések fejlesztési és tesztelési célokra szolgálnak, és nem használhatók éles környezetekben a futó alkalmazásokra gyakorolt hatás miatt.
> Éles környezetben érdemes a Azure Diagnostics használatával továbbítani az eseményeket.

1. Navigáljon a fürthöz a **Cloud Explorerben**. Kattintson a jobb gombbal, és válassza a **folyamatos átviteli nyomkövetés engedélyezése** lehetőséget.
   
    ![Távoli adatfolyam-nyomkövetés engedélyezése][enablestreamingtraces]
   
    Ez a művelet lekéri a folyamatos átviteli nyomkövetési bővítmény engedélyezésének folyamatát a fürtcsomópontokon, valamint a szükséges hálózati konfigurációkat.
2. Bontsa ki a **csomópontok** elemet a **Cloud Explorerben**, kattintson a jobb gombbal arra a csomópontra, amelyen nyomon szeretné követni a nyomkövetéseket, és válassza a **stream-Nyomkövetések megtekintése** lehetőséget.
   
    ![Távoli adatfolyam-Nyomkövetések megtekintése][viewremotestreamingtraces]
   
    Ismételje meg a 2. lépést annyi csomópont esetében, mint a nyomkövetést. Az egyes csomópontok stream egy dedikált ablakban jelenik meg.
   
    Mostantól megtekintheti Service Fabric és a szolgáltatásai által kibocsátott nyomkövetéseket. Ha úgy szeretné szűrni az eseményeket, hogy csak egy adott alkalmazást mutassanak, egyszerűen írja be az alkalmazás nevét a szűrőbe.
   
    ![Adatfolyam-Nyomkövetések megtekintése][viewingstreamingtraces]
3. Ha befejezte a folyamatos átvitel nyomkövetését a fürtről, letilthatja a távoli adatfolyam-nyomkövetést, ha a jobb gombbal a fürtre kattint a **Cloud Explorerben** , és kiválasztja a **streaming nyomkövetés letiltása** lehetőséget.
   
    ![Távoli folyamatos átvitel nyomkövetésének letiltása][disablestreamingtraces]

## <a name="next-steps"></a>Következő lépések
* [Service Fabric szolgáltatás tesztelése](service-fabric-testability-overview.md).
* [Service Fabric-alkalmazások kezelése a Visual Studióban](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png

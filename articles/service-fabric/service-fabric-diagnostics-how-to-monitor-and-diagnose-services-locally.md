---
title: Azure Service Fabric-alkalmazások hibakeresése Windows rendszeren
description: Megtudhatja, hogyan figyelheti és diagnosztizálhatja a Microsoft Azure Service Fabric használatával írt szolgáltatásokat egy helyi fejlesztési gépen.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 58319b47c78a85b4f06c2c834db20f6c42cc1939
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86247421"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Szolgáltatások monitorozása és diagnosztizálása egy helyi gép fejlesztési beállításánál
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

A szolgáltatások figyelése, észlelése, diagnosztizálása és hibaelhárítása lehetővé teszi, hogy a szolgáltatások továbbra is minimálisan megzavarják a felhasználói élményt. Habár a monitorozás és a diagnosztika kritikus fontosságú a tényleges üzembe helyezett éles környezetben, a hatékonyság a szolgáltatások fejlesztése során egy hasonló modell bevezetését fogja eredményezni, hogy a valós telepítéskor is működjön. A Service Fabric megkönnyíti a szolgáltatás-fejlesztők számára a diagnosztika megvalósítását, amely zökkenőmentesen képes együttműködni mind az egyszámítógépes helyi fejlesztési környezetekben, mind a valós üzemi fürtökön.

## <a name="event-tracing-for-windows"></a>Windows esemény-nyomkövetés
A [Windows esemény-nyomkövetés](/windows/win32/etw/event-tracing-portal) (ETW) az ajánlott technológia a Service Fabricban lévő üzenetek nyomon követéséhez. A ETW használatának néhány előnye:

* **A ETW gyors.** Olyan nyomkövetési technológiaként készült, amely minimális hatással van a kód végrehajtásának idejére.
* **A ETW-nyomkövetés zökkenőmentesen működik a helyi fejlesztési környezetekben és a valós fürtökön is.** Ez azt jelenti, hogy nem kell újraírnia a nyomkövetési kódot, ha készen áll a kód valós fürtön való üzembe helyezésére.
* **Service Fabric rendszerkód a belső nyomkövetés ETW is használja.** Ez lehetővé teszi, hogy megtekintse a Service Fabric rendszerkövetésekkel összekapcsolt alkalmazás-nyomkövetéseket. Emellett könnyebben megismerheti az alkalmazás kódjának és eseményeinek az alapul szolgáló rendszeren való összejátszását.
* **A ETW-események megtekintéséhez beépített támogatás érhető el Service Fabric Visual Studio-eszközökben.** A Visual Studio diagnosztikai események nézetében megjelennek a ETW események, ha a Visual Studio megfelelően van konfigurálva a Service Fabric. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Rendszeresemények megtekintése a Visual Studióban Service Fabric
Service Fabric ETW eseményeket bocsát ki, hogy az alkalmazások fejlesztői megértsék, mi történik a platformon. Ha még nem tette meg, folytassa a lépéseket, és kövesse az [első alkalmazás létrehozása a Visual Studióban](service-fabric-tutorial-create-dotnet-app.md)című témakör lépéseit. Ezek az információk segítséget nyújtanak az alkalmazásnak a nyomkövetési üzeneteket megjelenítő diagnosztikai eseménynaplókban való futtatásához.

1. Ha a diagnosztikai események ablak nem jelenik meg automatikusan, lépjen a **View (nézet** ) lapra a Visual Studióban, majd válassza a **más Windows** , majd a **diagnosztikai események megjelenítője** elemet.
2. Minden esemény szabványos metaadat-információkkal rendelkezik, amelyekkel megtudhatja, hogy az esemény melyik csomóponton, alkalmazáson és szolgáltatáson származik. Az események listáját az események ablak felső részén található **szűrési események** mező használatával is szűrheti. Szűrheti például a **csomópont nevét** vagy a **szolgáltatás nevét.** Ha pedig az esemény részleteit keresi, az események ablak tetején lévő **szüneteltetés** gombra kattintva is szüneteltetheti az eseményeket, és később is folytathatja az események elvesztése nélkül.
   
   ![Visual Studio diagnosztikai Eseménynapló](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Saját egyéni nyomkövetés hozzáadása az alkalmazás kódjához
A Service Fabric Visual Studio Project-sablonok mintakód-kódot tartalmaznak. A kód bemutatja, hogyan adhat hozzá a Visual Studio ETW-megjelenítőben megjelenített egyéni ETW-nyomkövetéseket a rendszernyomok Service Fabric. Ennek a módszernek az az előnye, hogy a rendszer automatikusan hozzáadja a metaadatokat a nyomkövetésekhez, és a Visual Studio diagnosztikai eseményeinek megjelenítője már be van állítva a megjelenítésre.

A **szolgáltatási sablonokból** létrehozott projektek esetében (állapot nélküli vagy állapot-nyilvántartó) csak keressen rá a `RunAsync` megvalósításra:

1. A metódusban megjelenő hívás egy `ServiceEventSource.Current.ServiceMessage` `RunAsync` példát mutat be az alkalmazás kódjából származó egyéni ETW-nyomkövetésre.
2. A **ServiceEventSource. cs** fájlban a `ServiceEventSource.ServiceMessage` nagy gyakoriságú események teljesítményének okai miatt felhasználható túlterhelést talál a metódushoz.

A **Actor-sablonokból** létrehozott projektek esetében (állapot nélküli vagy állapot-nyilvántartó):

1. Nyissa meg a **"ProjectName". cs** fájlt, ahol a *projektnév* a Visual Studio-projekthez választott név.  
2. Keresse meg a kódot a `ActorEventSource.Current.ActorMessage(this, "Doing Work");` *DoWorkAsync* metódusban.  Ez egy példa az alkalmazás kódjából írt egyéni ETW-nyomkövetésre.  
3. A **ActorEventSource. cs** fájlban megtalálhatja azt a `ActorEventSource.ActorMessage` módszert, amelyet a nagy gyakoriságú eseményekhez a teljesítménnyel kapcsolatos okok miatt érdemes használni.

Miután hozzáadta az egyéni ETW-nyomkövetést a szolgáltatás kódjához, felépítheti, telepítheti és futtathatja az alkalmazást, hogy megtekintse az esemény (eke) t a diagnosztikai események megjelenítőben. Ha az **F5 billentyűvel** végzi az alkalmazás hibakeresését, a rendszer automatikusan megnyitja a diagnosztikai események megjelenítőjét.

## <a name="next-steps"></a>Következő lépések
Ugyanaz a nyomkövetési kód, amelyet a fenti alkalmazáshoz adott a helyi diagnosztika esetében, az alkalmazás Azure-fürtön való futtatásakor használható eszközökkel fog működni. Tekintse át ezeket a cikkeket, amelyek az eszközök különböző lehetőségeit tárgyalják, és leírják, hogyan állíthatja be őket.

* [Naplók összegyűjtése a Azure Diagnostics](./service-fabric-diagnostics-event-aggregation-wad.md)
* [Események összesítése és gyűjtése a EventFlow segítségével használatával](service-fabric-diagnostics-event-aggregation-eventflow.md)

---
title: Állapot jelentése és állapota az Azure Service Fabric
description: Ismerje meg, hogyan küldhet állapot-jelentéseket a szolgáltatás kódjából, és hogyan ellenőrizhető a szolgáltatás állapota az Azure Service Fabric által biztosított állapotfigyelő eszköz használatával.
ms.topic: conceptual
ms.date: 02/25/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: dc15ea7f062d3912c297050ffac755990fdd3b7a
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625942"
---
# <a name="report-and-check-service-health"></a>Szolgáltatásállapot jelentése és ellenőrzése
Ha a szolgáltatásai problémákba ütköznek, az incidensek és az kimaradások kijavításának lehetősége a problémák gyors észlelésére is függ. Ha az Azure Service Fabric Health Managerrel kapcsolatos problémákat és hibákat jelent a szolgáltatás kódjában, a Service Fabric által biztosított szabványos állapot-figyelési eszközöket használhatja az állapot ellenőrzéséhez.

Háromféle módon jelenthet jelentést az állapotról a szolgáltatásból:

* Használjon [partíciós](/dotnet/api/system.fabric.istatefulservicepartition) vagy [CodePackageActivationContext](/dotnet/api/system.fabric.codepackageactivationcontext) objektumokat.  
  A `Partition` és az objektumok segítségével `CodePackageActivationContext` jelentést készíthet az aktuális környezet részét képező elemek állapotáról. A replika részeként futó kód például csak az adott replikán, a hozzá tartozó partíción, valamint az alkalmazás részeként jelentheti az állapotot.
* Használja az `FabricClient` parancsot.   
  `FabricClient`Ha a fürt nem [biztonságos](service-fabric-cluster-security.md) , vagy ha a szolgáltatás rendszergazdai jogosultságokkal fut, akkor az állapotot a szolgáltatás kódjából lehet jelenteni. A legtöbb valós forgatókönyv nem használ nem biztonságos fürtöket, vagy rendszergazdai jogosultságokat biztosít. A segítségével `FabricClient` bármilyen, a fürt részét képező entitáson jelenthet állapotot. Ideális esetben azonban a szolgáltatási kódnak csak a saját állapotával kapcsolatos jelentéseket kell elküldenie.
* Használja a REST API-kat a fürt, az alkalmazás, a központilag telepített alkalmazás, a szolgáltatás, a szervizcsomag, a partíció, a replika vagy a csomópont szintjén. Ez felhasználható a tárolóban lévő állapot jelentésére.

Ez a cikk végigvezeti egy olyan példán, amely a szolgáltatási kód állapotát jelenti. A példa azt is bemutatja, hogyan használhatók az Service Fabric által biztosított eszközök az állapot ellenőrzéséhez. Ez a cikk az Service Fabric állapot-figyelési képességeinek gyors bevezetését célozza. Részletesebb információkért olvassa el a cikk végén található hivatkozásra kattintva megtekintheti a részletes cikkek sorozatát.

## <a name="prerequisites"></a>Előfeltételek
A következőknek kell telepítve lennie:

* Visual Studio 2015 vagy Visual Studio 2019
* Service Fabric SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>Helyi Secure dev-fürt létrehozása
* Nyissa meg a PowerShellt rendszergazdai jogosultságokkal, és futtassa a következő parancsokat:

![Parancsok, amelyek bemutatják, hogyan hozhat létre biztonságos fejlesztői fürtöt](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Alkalmazás üzembe helyezése és állapotának ellenõrzése
1. Nyissa meg a Visual studiót rendszergazdaként.
1. Hozzon létre egy projektet az **állapot-nyilvántartó** sablon használatával.
   
    ![Service Fabric alkalmazás létrehozása állapot-nyilvántartó szolgáltatással](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Az alkalmazás hibakeresési módban való futtatásához nyomja le az **F5** billentyűt. Az alkalmazás központi telepítése a helyi fürtre történik.
1. Az alkalmazás futása után kattintson a jobb gombbal a helyi Fürtfelügyelő ikonra az értesítési területen, majd válassza a **helyi fürt kezelése** lehetőséget a helyi menüben a Service Fabric Explorer megnyitásához.
   
    ![Service Fabric Explorer megnyitása az értesítési körzetből](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. Az alkalmazás állapotát ebben a képen kell megjeleníteni. Jelenleg az alkalmazásnak Kifogástalan állapotba kell esnie, és nem kell hibát megadnia.
   
    ![Kifogástalan állapotú alkalmazás a Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. Az állapotot a PowerShell használatával is megtekintheti. A használatával ```Get-ServiceFabricApplicationHealth``` ellenőrizhető az alkalmazás állapota, és a segítségével ```Get-ServiceFabricServiceHealth``` ellenőrizhető a szolgáltatás állapota. A PowerShellben ugyanarra az alkalmazásra vonatkozó állapotjelentés ebben a képen található.
   
    ![Kifogástalan állapotú alkalmazás a PowerShellben](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Egyéni állapotú események hozzáadása a szolgáltatás kódjához
A Visual Studióban Service Fabric Project-sablonok tartalmaznak mintakód-kódot. Az alábbi lépések bemutatják, hogyan jelentheti be a szolgáltatás kódjában az egyéni állapotadatok jelentéseit. Ezek a jelentések automatikusan megjelennek a szabványos eszközök az állapot-figyeléshez, amelyeket Service Fabric biztosít, például Service Fabric Explorer, Azure Portal Health View és PowerShell.

1. Nyissa meg újra a korábban a Visual Studióban létrehozott alkalmazást, vagy hozzon létre egy új alkalmazást az **állapot-nyilvántartó szolgáltatás** Visual Studio-sablonjának használatával.
1. Nyissa meg a Stateful1. cs fájlt, és keresse meg a `myDictionary.TryGetValueAsync` hívást a `RunAsync` metódusban. Láthatja, hogy ez a metódus egy olyan értéket ad vissza `result` , amely a számláló aktuális értékét tárolja, mert az alkalmazásban lévő kulcs logikája egy futó szám megtartása. Ha az alkalmazás valódi alkalmazás volt, és ha az eredmény hiánya hibát jelzett, akkor meg kell jelenítenie az eseményt.
1. Ha az eredmény hiánya hibát jelez, adja meg a következő lépéseket az állapot jelentéséhez.
   
    a. Adja hozzá a `System.Fabric.Health` névteret a Stateful1. cs fájlhoz.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Adja hozzá a következő kódot a `myDictionary.TryGetValueAsync` hívás után
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    A replika állapotát jelenteni fogjuk, mert egy állapot-nyilvántartó szolgáltatásból jelent meg. A `HealthInformation` paraméter a jelentett egészségügyi probléma adatait tárolja.
   
    Ha már létrehozott egy állapot nélküli szolgáltatást, használja a következő kódot
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Ha a szolgáltatás rendszergazdai jogosultságokkal fut, vagy ha a fürt nem [biztonságos](service-fabric-cluster-security.md), a `FabricClient` jelentés állapotát az alábbi lépésekben látható módon is használhatja.  
   
    a. Hozza létre a `FabricClient` példányt a `var myDictionary` deklaráció után.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Adja hozzá a következő kódot a `myDictionary.TryGetValueAsync` hívás után.
   
    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.Context.PartitionId,
            this.Context.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```
1. Szimuláljuk ezt a hibát, és megjelenik az állapot-figyelési eszközök között. A hiba szimulálása érdekében a korábban hozzáadott állapotjelentés-kód első sorát adja hozzá. Az első sor megjegyzése után a kód a következő példához hasonlóan fog kinézni.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Ez a kód minden alkalommal elindítja az állapotjelentést `RunAsync` . Miután elvégezte a módosítást, nyomja le az **F5** billentyűt az alkalmazás futtatásához.
1. Az alkalmazás futása után nyissa meg Service Fabric Explorer az alkalmazás állapotának vizsgálatához. Ez az idő Service Fabric Explorer azt mutatja, hogy az alkalmazás állapota nem megfelelő. Az alkalmazás nem megfelelő állapotú, mert a korábban hozzáadott kódból származó hibát észlelt.
   
    ![Sérült alkalmazás a Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Ha kijelöli az elsődleges replikát Service Fabric Explorer fanézetében, látni fogja, hogy az **állapot** hibát jelez. A Service Fabric Explorer megjeleníti az állapotjelentés részleteit is, amelyek a kódban szereplő paraméterhez lettek adva `HealthInformation` . Ugyanazokat az állapotjelentést a PowerShellben és a Azure Portal is láthatja.
   
    ![Replika állapota Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Ez a jelentés a Health Managerben marad, amíg egy másik jelentés nem váltja fel, vagy amíg nem törli a replikát. Mivel nem állítottunk be `TimeToLive` Ehhez az állapotjelentés-jelentéshez az `HealthInformation` objektumban, a jelentés soha nem jár le.

Azt javasoljuk, hogy az állapotot a legrészletesebbebb szinten jelentse, ami ebben az esetben a replika. Az állapotot is jelentheti `Partition` .

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

A, a és a állapotának jelentéséhez használja a következőt: `Application` `DeployedApplication` `DeployedServicePackage`  `CodePackageActivationContext` .

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Következő lépések
* [Service Fabric állapotának részletes bemutatása](service-fabric-health-introduction.md)
* [REST API a jelentési szolgáltatás állapotához](/rest/api/servicefabric/report-the-health-of-a-service)
* [REST API a jelentéskészítési alkalmazás állapota](/rest/api/servicefabric/report-the-health-of-an-application)

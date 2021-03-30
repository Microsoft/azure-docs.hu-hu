---
title: Service Fabric fürterőforrás-kezelő – alkalmazáscsoport
description: Az alkalmazáscsoport funkcióinak áttekintése a Service Fabric fürterőforrás-kezelőben
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 988c7ce52125800c16aa785d5b1458604a927ecd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75452149"
---
# <a name="introduction-to-application-groups"></a>Alkalmazás-csoportok bemutatása
Service Fabric fürterőforrás-kezelője jellemzően a fürt erőforrásait kezeli úgy, hogy a terhelést (a [metrikák](service-fabric-cluster-resource-manager-metrics.md)használatával) egyenletesen terjeszti a fürtön belül. Service Fabric a fürtben lévő csomópontok kapacitását és a fürtöt a teljes [kapacitással](service-fabric-cluster-resource-manager-cluster-description.md)kezeli. A metrikák és a kapacitások nagy mennyiségű számítási feladathoz használhatók, de a különböző Service Fabric alkalmazás-példányok nagy mennyiségű használatát lehetővé teszik a további követelmények. Például a következőket teheti:

- A fürt csomópontjain foglalt bizonyos kapacitások lefoglalása az elnevezett alkalmazás-példányon belüli szolgáltatásokra
- Korlátozza az elnevezett alkalmazás-példányon belüli szolgáltatások által futtatott csomópontok teljes számát (ahelyett, hogy a teljes fürtre kellene terjeszteni őket)
- Az elnevezett alkalmazási példány kapacitásának meghatározása a szolgáltatások számának vagy a benne található szolgáltatások teljes erőforrás-felhasználásának korlátozására

A követelmények teljesítése érdekében a Service Fabric fürterőforrás-kezelő támogatja az Application groups nevű szolgáltatást.

## <a name="limiting-the-maximum-number-of-nodes"></a>A csomópontok maximális számának korlátozása
Az alkalmazás kapacitásának legegyszerűbb használati esete az, amikor egy alkalmazás-példányt egy adott maximális számú csomópontra kell korlátozni. Ez összevonja az alkalmazás példányán belüli összes szolgáltatást egy meghatározott számú gépre. A konszolidáció akkor lehet hasznos, ha a szolgáltatás a megnevezett alkalmazás-példányon belüli szolgáltatások által használt fizikai erőforrás-használatot próbálja meg előre jelezni vagy leválasztani. 

Az alábbi képen egy olyan alkalmazás-példány látható, amelyben a és a csomópontok száma nem engedélyezett:

<center>

![A csomópontok maximális számát meghatározó alkalmazás-példány][Image1]
</center>

A bal oldali példában az alkalmazás nem rendelkezik a definiált csomópontok maximális számával, és három szolgáltatással rendelkezik. A fürterőforrás-kezelő kihasználta az összes replikát a hat elérhető csomópont között a fürt legjobb egyensúlyának eléréséhez (az alapértelmezett viselkedés). A jobb oldalon láthatjuk, hogy ugyanazt az alkalmazást három csomópontra korlátozzák.

Az ezt a viselkedést vezérlő paramétert MaximumNodes nevezzük. Ez a paraméter beállítható az alkalmazás létrehozásakor, vagy frissíthető egy már futó alkalmazás-példányra.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –ApplicationName fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
await fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
await fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

```

A csomópontok készletén belül a fürterőforrás-kezelő nem garantálja, hogy mely szolgáltatási objektumok legyenek elhelyezve, vagy mely csomópontok vannak használatban.

## <a name="application-metrics-load-and-capacity"></a>Alkalmazás-metrikák, betöltés és kapacitás
Az alkalmazáscsoport lehetővé teszi az adott névvel ellátott alkalmazás-példányhoz társított mérőszámok meghatározását, valamint az alkalmazás példányának kapacitását a metrikák számára. Az alkalmazás metrikái lehetővé teszik az alkalmazás-példányon belüli szolgáltatások erőforrás-felhasználásának nyomon követését, lefoglalását és korlátozását.

Minden egyes alkalmazás metrikája esetében két érték állítható be:

- **Összes alkalmazás kapacitása** – ez a beállítás az alkalmazás teljes kapacitását jelöli egy adott metrika esetében. A fürterőforrás-kezelő nem engedélyezi az alkalmazás-példányon belüli új szolgáltatások létrehozását, ami a teljes terhelést az érték túllépéseként fogja okozni. Tegyük fel például, hogy az alkalmazás példányának kapacitása 10 volt, és már a terhelése öt. Egy teljes alapértelmezett 10-es terhelésű szolgáltatás létrehozása nem engedélyezett.
- **Maximális csomópont kapacitása** – ez a beállítás határozza meg az alkalmazás maximális teljes terhelését egyetlen csomóponton. Ha a terhelés meghaladja ezt a kapacitást, a fürterőforrás-kezelő áthelyezi a replikákat más csomópontokra, így a terhelés csökken.


PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -Metrics @("MetricName:Metric1,MaximumNodeCapacity:100,MaximumApplicationCapacity:1000")
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;
appMetric.MaximumNodeCapacity = 100;
ad.Metrics.Add(appMetric);
await fc.ApplicationManager.CreateApplicationAsync(ad);
```

## <a name="reserving-capacity"></a>Kapacitás megőrzése
Az alkalmazáscsoport egy másik gyakori használata annak biztosítása, hogy a fürtben lévő erőforrások egy adott alkalmazás-példány számára legyenek fenntartva. A terület az alkalmazás példányának létrehozásakor mindig le van foglalva.

A fürtben lévő tárterület az alkalmazáshoz való lefoglalása azonnal megtörténik, még akkor is, ha:
- az alkalmazás példánya létrejött, de még nem rendelkezik szolgáltatásokkal
- az alkalmazás példányán belüli szolgáltatások száma minden alkalommal megváltozik 
- a szolgáltatások léteznek, de nem használják az erőforrásokat 

Egy alkalmazás-példány erőforrásainak lefoglalásához két további paramétert kell megadnia: *MinimumNodes* és *NodeReservationCapacity*

- **MinimumNodes** – meghatározza azon csomópontok minimális számát, amelyeken az alkalmazás példányának futnia kell.  
- **NodeReservationCapacity** – ez a beállítás az alkalmazás metrikája. Az érték az alkalmazás számára fenntartott metrika azon csomópontjának mennyisége, amelyen az adott alkalmazásban található szolgáltatások futnak.

A **MinimumNodes** és a **NodeReservationCapacity** kombinálása garantálja az alkalmazás minimális terhelését a fürtön belül. Ha a fürtben kevesebb kapacitás áll rendelkezésre a teljes foglaláshoz, akkor az alkalmazás létrehozása meghiúsul. 

Nézzük meg a kapacitás foglalásának példáját:

<center>

![Fenntartott kapacitást meghatározó alkalmazás-példányok][Image2]
</center>

A bal oldali példában az alkalmazások nem határozzák meg az alkalmazás kapacitását. A fürterőforrás-kezelő a szokásos szabályok szerint osztja el az összeset.

A jobb oldali példában tegyük fel, hogy a Application1 a következő beállításokkal lett létrehozva:

- A MinimumNodes két értékre van állítva
- Egy alkalmazás-metrika definiálva
  - 20 NodeReservationCapacity

PowerShell

 ``` posh
 New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MinimumNodes 2 -Metrics @("MetricName:Metric1,NodeReservationCapacity:20")
 ```

C#

 ``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MinimumNodes = 2;

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.NodeReservationCapacity = 20;

ad.Metrics.Add(appMetric);

await fc.ApplicationManager.CreateApplicationAsync(ad);
```

Service Fabric fenntartja a kapacitást két csomóponton a Application1, és nem engedélyezi, hogy a Application2 a kapacitást a Application1-on belüli szolgáltatások által felhasznált terhelések esetén is felhasználja. Ez a fenntartott alkalmazás kapacitása felhasználható, és az adott csomóponton és a fürtön belüli fennmaradó kapacitásnak számít.  A foglalást azonnal levonják a megmaradt fürt kapacitásáról, azonban a fenntartott felhasználást csak akkor vonja le a rendszer, ha egy adott csomópont kapacitása csak akkor történik meg, ha legalább egy szolgáltatási objektumot elhelyeznek rajta. Ez a későbbi foglalás lehetővé teszi a rugalmasságot és a jobb erőforrás-használatot, mivel az erőforrások csak szükség esetén vannak lefoglalva a csomópontokon.

## <a name="obtaining-the-application-load-information"></a>Az alkalmazás betöltési adatainak beszerzése
Minden olyan alkalmazás esetében, amely egy vagy több mérőszámhoz meghatározott alkalmazási kapacitással rendelkezik, beszerezhetheti a szolgáltatásai replikái által jelentett összesített terhelés adatait.

PowerShell

``` posh
Get-ServiceFabricApplicationLoadInformation –ApplicationName fabric:/MyApplication1
```

C#

``` csharp
var v = await fc.QueryManager.GetApplicationLoadInformationAsync("fabric:/MyApplication1");
var metrics = v.ApplicationLoadMetricInformation;
foreach (ApplicationLoadMetricInformation metric in metrics)
{
    Console.WriteLine(metric.ApplicationCapacity);  //total capacity for this metric in this application instance
    Console.WriteLine(metric.ReservationCapacity);  //reserved capacity for this metric in this application instance
    Console.WriteLine(metric.ApplicationLoad);  //current load for this metric in this application instance
}
```

A Alkalmazásterhelésben lekérdezés az alkalmazás kapacitására vonatkozóan megadott alapszintű adatokat adja vissza. Ezen információk közé tartozik a minimális csomópontok és a csomópontok maximális adatai, valamint az alkalmazás által jelenleg foglalt szám. Emellett az egyes alkalmazások terhelési metrikájának adatait is tartalmazza, beleértve a következőket:

* Metrika neve: a metrika neve.
* Foglalási kapacitás: a fürtben az alkalmazás számára fenntartott kapacitás.
* Alkalmazás terhelése: az alkalmazás alárendelt replikáinak teljes terhelése.
* Alkalmazás kapacitása: az alkalmazás terhelésének maximálisan megengedett értéke.

## <a name="removing-application-capacity"></a>Az alkalmazás kapacitásának eltávolítása
Miután beállította az alkalmazás kapacitásának paramétereit az alkalmazáshoz, a frissítési alkalmazás API-jai vagy a PowerShell-parancsmagok használatával eltávolíthatók. Például:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Ezzel a paranccsal a rendszer eltávolítja az alkalmazás-példány összes alkalmazás-kapacitási felügyeleti paraméterét. Ez magában foglalja a MinimumNodes, a MaximumNodes és az alkalmazás metrikáit, ha vannak ilyenek. A parancs hatása azonnali. A parancs végrehajtása után a fürterőforrás-kezelő az alkalmazások kezelésének alapértelmezett viselkedését használja. Az alkalmazás kapacitása paramétereit megadhatja a használatával `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()` .

### <a name="restrictions-on-application-capacity"></a>Az alkalmazás kapacitására vonatkozó korlátozások
Az alkalmazás-kapacitás paramétereinek számos korlátozása van, amelyeket figyelembe kell venni. Ha érvényesítési hibák történnek, a módosítások nem történnek meg.

- Az egész szám paraméternek nem negatív számnak kell lennie.
- A MinimumNodes soha nem lehet nagyobb, mint a MaximumNodes.
- Ha meg van adva egy terhelési metrika kapacitása, akkor a következő szabályoknak kell megfelelnie:
  - A csomópont foglalási kapacitása nem lehet nagyobb, mint a csomópontok maximális kapacitása. Nem korlátozhatja például a "CPU" mérőszám kapacitását a csomóponton két egységre, és az egyes csomópontokon három egységet próbál meg lefoglalni.
  - Ha a MaximumNodes meg van adva, akkor a MaximumNodes és a csomópontok maximális kapacitása nem lehet nagyobb, mint az összes alkalmazás kapacitása. Tegyük fel például, hogy a "CPU" betöltési metrika maximális kapacitása nyolc értékre van állítva. Azt is tegyük fel, hogy a maximális csomópontokat 10 értékre állítja be. Ebben az esetben az összes alkalmazás kapacitásának nagyobbnak kell lennie, mint 80 ennél a terhelési metrikanál.

A korlátozások az alkalmazások létrehozásakor és frissítéseikor is érvényben vannak.

## <a name="how-not-to-use-application-capacity"></a>Az alkalmazás kapacitásának használata
- Ne próbálja meg az Application Group funkcióit használni, hogy az alkalmazás a csomópontok egy _adott_ részhalmazára legyen korlátozva. Más szóval azt is megadhatja, hogy az alkalmazás legfeljebb öt csomóponton fusson, de nem a fürtben lévő öt csomópontot. Az alkalmazások adott csomópontokra való korlátozásával a szolgáltatások elhelyezési korlátozásai is elérhetők.
- Ne próbálja meg az alkalmazás kapacitását használni, hogy az azonos alkalmazásból származó két szolgáltatás ugyanahhoz a csomóponthoz legyen helyezve. Ehelyett használjon [affinitást](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) vagy [elhelyezési korlátozásokat](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Következő lépések
- A szolgáltatások konfigurálásával kapcsolatos további információkért [tekintse meg a szolgáltatások konfigurálását](service-fabric-cluster-resource-manager-configure-services.md) ismertető témakört.
- Ha szeretné megtudni, hogy a fürterőforrás-kezelő hogyan kezeli és kiegyenlíti a fürt terhelését, tekintse meg a [terhelés kiegyensúlyozásáról](service-fabric-cluster-resource-manager-balancing.md) szóló cikket.
- Kezdje a kezdetektől, és [Ismerkedjen meg a Service Fabric fürterőforrás-kezelővel](service-fabric-cluster-resource-manager-introduction.md)
- A metrikák általános működésével kapcsolatos további információkért olvassa el [Service Fabric betöltési metrikákat](service-fabric-cluster-resource-manager-metrics.md)
- A fürterőforrás-kezelő számos lehetőséget kínál a fürt leírására. Ha többet szeretne megtudni róluk, tekintse meg ezt a cikket a [Service Fabric-fürt leírását ismertető](service-fabric-cluster-resource-manager-cluster-description.md) cikkben.

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png

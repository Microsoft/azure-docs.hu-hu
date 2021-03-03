---
title: Futásidejű SDK – áttekintés
description: Ismerkedjen meg az objektum-horgonyok futásidejű SDK-val.
author: craigktreasure
manager: vriveras
services: azure-object-anchors
ms.author: crtreasu
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: azure-object-anchors
ms.openlocfilehash: 020f727674449523a57a608e8930d67e0f239cf6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746017"
---
# <a name="runtime-sdk-overview"></a>Futásidejű SDK – áttekintés

Ez a szakasz átfogó áttekintést nyújt az objektum-horgonyok futásidejű SDK-ról, amely az objektumok az objektum-horgonyok modelljével való észlelésére szolgál. Megtudhatja, hogyan képviselteti magát az objektum, és hogy a különböző összetevőket milyen összetevők használják.

Az alábbi típusok mindegyike megtalálható a **Microsoft. MixedReality. ObjectAnchors** névtérben.

## <a name="types"></a>Típusok

### <a name="objectmodel"></a>ObjectModel

A [ObjectModel](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectmodel) a fizikai objektum geometriáját jelöli, és az észleléshez és a becsléshez szükséges paramétereket kódolja. Az [objektum-horgonyok szolgáltatás](../quickstarts/get-started-model-ingestion.md)használatával kell létrehozni. Ezután az alkalmazás betöltheti a generált modellt az objektum-horgonyok API-val, és lekérdezheti a modellbe ágyazott rácsvonalat a vizualizációhoz.

### <a name="objectsearcharea"></a>ObjectSearchArea

A [ObjectSearchArea](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectsearcharea) meghatározza az egy vagy több objektum keresésének a területét. Ezt a térbeli gráf csomópont-AZONOSÍTÓja és térbeli határai határozzák meg a térbeli gráf csomópont-azonosítója által jelölt koordináta-rendszeren. Az objektum-horgonyok futtatókörnyezetének SDK négyféle típusú határt támogat, például a **mező nézet**, a **határolókeret**, a **gömb** és egy **hely** között.

### <a name="objectquery"></a>ObjectQuery

Egy [ObjectQuery](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery) azt jelzi, hogy egy **objektum megfigyelője** egy adott modell objektumainak megkeresésére. A következő hangolt paramétereket biztosítja, amelyek alapértelmezett értékeit lekérheti egy objektummodellből.

#### <a name="minsurfacecoverage"></a>MinSurfaceCoverage

A [MinSurfaceCoverage](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.minsurfacecoverage) tulajdonság azt az értéket jelzi, amelyet a rendszer észlelt.

Az egyes objektumokhoz tartozó jelöltek esetében a **megfigyelő** kiszámítja az átfedő felületek arányát az átalakított objektummodell és a jelenet között, majd csak akkor jelenti azt, ha a lefedettségi arány meghaladja az adott küszöbértéket.

#### <a name="isexpectedtobestandingongroundplane"></a>IsExpectedToBeStandingOnGroundPlane

A [IsExpectedToBeStandingOnGroundPlane](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.isexpectedtobestandingongroundplane) tulajdonság azt jelzi, hogy a célként megadott objektumnak a talajközeli síkon kell állnia.

A terepi sík a keresési terület legalacsonyabb vízszintes padlója. Ez jó korlátozást biztosít a lehetséges objektumra vonatkozóan. A jelző bekapcsolásával a **megfigyelő** megbecsüli a pózolt egy korlátozott térben, és javíthatja a pontosságot. A rendszer figyelmen kívül hagyja ezt a paramétert, ha a modell nem állhat a talajközeli síkon.

#### <a name="expectedmaxverticalorientationindegrees"></a>ExpectedMaxVerticalOrientationInDegrees

A [ExpectedMaxVerticalOrientationInDegrees](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.expectedmaxverticalorientationindegrees) tulajdonság azt jelzi, hogy az objektum-példány és a gravitáció iránya között a várt maximális szög fokban van megadva.

Ez a paraméter egy újabb korlátozást biztosít egy becsült érték felfelé irányuló irányához. Ha például egy objektum jobbra van, ez a paraméter lehet 0. Az objektum-Horgonyoknak nem kell a modelltől eltérő objektumokat felismerniük. Ha a modell jobbra van, akkor nem fogja felderíteni a leválasztott példányt. A rendszer egy új modellt használ a párhuzamos elrendezéshez. Ugyanez a szabály vonatkozik az artikulációra.

#### <a name="maxscalechange"></a>MaxScaleChange

A [MaxScaleChange](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.maxscalechange) tulajdonság azt jelzi, hogy a térbeli leképezésre vonatkozó maximális objektum-méretezési változás (0 ~ 1). A rendszer a becsült skálát alkalmazza az átalakított objektumok csúcspontokra a forrás és a tengely igazításával. A becsült skála nem lehet a CAD-modell és annak fizikai ábrázolása közötti tényleges skála, de néhány olyan érték, amely lehetővé teszi, hogy az alkalmazás egy objektummodell számára a térbeli leképezéshez a fizikai objektumon legyen megközelítve.

#### <a name="searchareas"></a>SearchAreas

A [SearchAreas](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.searchareas) tulajdonság térbeli korlátok tömbjét jelöli, ahol az objektum (ok) található.

A **megfigyelő** megkeresi az objektumokat az Unió területén a lekérdezésben megadott összes keresési terület tekintetében. Ebben a kiadásban a késés csökkentése érdekében legfeljebb egy olyan objektumot adunk vissza, amely a legmagasabb megbízhatósággal rendelkezik.

### <a name="objectinstance"></a>Objectinstance példányt használ

A [objectinstance példányt használ](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectinstance) olyan feltételezett pozíciót jelöl, amelyben egy adott modell egy példánya a HoloLens koordináta-rendszeren belül lehet. Minden példányhoz tartozik egy `SurfaceCoverage` tulajdonság, amely jelzi, hogy mennyire jó a becsült érték.

A példányok hívási `ObjectObserver.DetectAsync` metódussal jönnek létre, majd a háttérben automatikusan frissülnek, amikor életben van. Egy alkalmazás megfigyelheti az állapot változásának eseményét egy adott példányon, vagy megváltoztathatja a nyomkövetési módot a frissítés szüneteltetéséhez/folytatásához. A rendszer automatikusan eltávolítja egy példányt a **megfigyelőből** a követés elvesztésekor.

### <a name="objectobserver"></a>ObjectObserver

Az [ObjectObserver](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectobserver) betölti az objektumok modelljeit, észleli a példányaikat, és a 6 – DOF jelent jelentéseket az egyes példányok HoloLens koordináta-rendszeren.

Bár minden objektummodell vagy példány egy **megfigyelőből** jön létre, az élettartamuk független. Egy alkalmazás megtekintheti a megfigyelőt, és folytathatja az objektummodell vagy a példány használatát.

### <a name="objectdiagnosticssession"></a>ObjectDiagnosticsSession

A [ObjectDiagnosticSession](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.diagnostics.objectdiagnosticssession) rögzíti a diagnosztikát, és adatokat ír az archívumba.

A diagnosztikai Archívum magában foglalja a jelenet pont felhőjét, a megfigyelő állapotát és a modellekkel kapcsolatos információkat. Ezek az információk hasznosak lehetnek a lehetséges futásidejű problémák azonosításához. További információt a [Gyakori kérdések](../faq.md)című témakörben talál.

## <a name="runtime-sdk-usage-and-details"></a>Futásidejű SDK-használat és-részletek

Ebben a szakaszban a futásidejű SDK használatának alapjait kell megadnia. Elegendő környezetet kell biztosítania ahhoz, hogy megtekintse az objektum-horgonyok holisztikus felhasználásának módját.

### <a name="initialization"></a>Inicializálás

Az alkalmazásoknak meg kell hívniuk az `ObjectObserver.IsSupported()` API-t annak megállapításához, hogy az objektum-horgonyok támogatottak-e az eszközön a használat előtt. Ha az `ObjectObserver.IsSupported()` API visszatér `false` , ellenőrizze, hogy az alkalmazás engedélyezte-e a **spatialPerception** képesség and\or a legújabb HoloLens operációs rendszerre való frissítést.

```cs
if (!ObjectObserver.IsSupported())
{
    // Handle the error
}

// This call should grant the access we need.
var status = await ObjectObserver.RequestAccessAsync();
if(status != ObjectObserverStatus.Allowed)
{
    // Handle the error
}
```

Ezután az alkalmazás létrehoz egy objektum megfigyelőt, és betölti az objektum- [horgonyok](../quickstarts/get-started-model-ingestion.md)betöltési szolgáltatása által generált szükséges modelleket.

```cs
var observer = new ObjectObserver();

byte[] modelAsBytes; // Load a model into a byte array. Model could be a file, an embedded resource, or a network stream.
var model = await observer.LoadObjectModelAsync(modelAsBytes);

// Note that after a model is loaded, its vertices and normals are transformed into a centered coordinate system for the 
// ease of computing the object pose. The rigid transform can be retrieved through the `OriginToCenterTransform` property.
```

Az alkalmazás létrehoz egy lekérdezést, amely egy adott modell példányait fogja felderíteni egy adott helyen belül.

```cs
var coordinateSystem = default(SpatialGraphCoordinateSystem);
var searchAreaAsBox = ObjectSearchArea.FromOrientedBox(coordinateSystem, default(SpatialOrientedBox));

// Optionally change the parameters, otherwise use the default values embedded in the model.
var query = new ObjectQuery(model);
query.MinSurfaceCoverage = 0.2f;
query.ExpectedMaxVerticalOrientationInDegrees = 1.5f;
query.MaxScaleChange = 0.1f;
query.SearchAreas.Add(searchAreaAsBox);

// Detection could take long, run in a background thread.
var detectedObjects = await observer.DetectAsync(query);
```

Alapértelmezés szerint a **megfigyelő** automatikusan nyomon követi az egyes észlelt példányokat. Ezeket a példányokat tetszés szerint kezelheti a nyomkövetési módjának módosításával vagy az állapotuk változásával.

```cs
foreach (var instance in detectedObjects)
{
    // Supported modes:
    // "LowLatencyCoarsePosition" - consumes less CPU cycles thus fast to update the state.
    // "HighLatencyAccuratePosition" - (not yet implemented) consumes more CPU cycles thus potentially taking longer time to update the state.
    // "Paused" - stops to update the state until mode changed to low or high.
    instance.Mode = ObjectInstanceTrackingMode.LowLatencyCoarsePosition;

    // Listen to state changed event on this instance.
    instance.Changed += InstanceChangedHandler;

    // Optionally dispose an instance if not interested in it.
    //instance.Dispose();
}
```

Az állapot módosulása eseménynél lekérdezheti a legutóbbi állapotot, vagy megtekintheti a példányokat, ha megszakadt a követés.

```cs
var InstanceChangedHandler = new Windows.Foundation.TypedEventHandler<ObjectInstance, ObjectInstanceChangedEventArgs>((sender, args) =>
{
    // Try to query the current instance state.
    var state = sender.TryGetCurrentState();

    if (state.HasValue)
    {
        // Process latest state via state.Value.
        // An object pose includes scale, rotation and translation, applied in the same order
        // to the object model in the centered coordinate system.
    }
    else
    {
        // This object instance is lost for tracking, and will never be recovered.
        // The caller can detach the Changed event handler from this instance and dispose it.
        sender.Dispose();
    }
});
```

Emellett az alkalmazások opcionálisan rögzíthetnek egy vagy több diagnosztikai munkamenetet is az offline hibakereséshez.

```cs
string diagnosticsFolderPath = Windows.Storage.ApplicationData.Current.TemporaryFolder.Path;
const uint maxSessionSizeInMegaBytes = uint.MaxValue;

// Recording starts on the creation of a diagnostics session.
var diagnostics = new ObjectDiagnosticsSession(observer, maxSessionSizeInMegaBytes);

// Wait for the observer to do a job.

// Application can report some **pseudo ground-truth** pose for an instance acquired from other means.
diagnostics.ReportActualInstanceLocation(instance, coordinateSystem, Vector3.Zero, Quaternion.Identity);

// Close a session and write the diagnostics into an archive at specified location.
await diagnostics.CloseAsync(System.IO.Path.Combine(diagnosticsFolderPath, "diagnostics.zip"));
```

Végül Kiszabadítjuk az erőforrásokat az összes objektum ártalmatlanításával.

```cs
foreach(var instance in activeInstances)
{
    instance.Changed -= InstanceChangedHandler;
    instance.Dispose();
}

model.Dispose();
observer.Dispose();
```

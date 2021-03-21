---
title: Kiszolgálóoldali teljesítménylekérdezések
description: Kiszolgálóoldali teljesítményű lekérdezések végrehajtása API-hívások használatával
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 30b8104a9596f0b32f731c507b513b204f5d1acd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594095"
---
# <a name="server-side-performance-queries"></a>Kiszolgálóoldali teljesítménylekérdezések

A megfelelő megjelenítési teljesítmény a kiszolgálón kritikus fontosságú a stabil képarányok és a jó felhasználói élmény érdekében. Fontos, hogy körültekintően figyelje a kiszolgáló teljesítmény-jellemzőit, és szükség esetén optimalizálja azokat. A teljesítményadatokat dedikált API-függvények segítségével lehet lekérdezni.

A renderelési teljesítmény legjelentősebb hatása a modell bemeneti adatai. A bemeneti adatokat a [modell átalakításának konfigurálása](../../how-tos/conversion/configure-model-conversion.md)című témakörben leírtak szerint állíthatja be.

Az ügyféloldali alkalmazások teljesítménye is szűk keresztmetszet lehet. Az ügyféloldali teljesítmény részletes elemzéséhez azt javasoljuk, hogy tegye a következőt: [:::no-loc text="performance trace":::](../../how-tos/performance-tracing.md) .

## <a name="clientserver-timeline"></a>Ügyfél/kiszolgáló idővonala

Mielőtt részletesen bekerül a különböző késési értékekre, érdemes megtekinteni az ügyfél és a kiszolgáló közötti szinkronizálási pontokat az idővonalon:

![Folyamat idővonala](./media/server-client-timeline.png)

Az ábrán az alábbiak láthatók:

* a *becslést* az ügyfél a 60 – Hz-es kereteken (minden 16,6 MS) állandó értékre vált.
* a kiszolgáló ezután elindítja a renderelést a póz alapján
* a kiszolgáló visszaküldi a kódolt videó képét
* az ügyfél dekódolja a rendszerképet, végrehajt néhány CPU-t és GPU-t, majd megjeleníti a rendszerképet

## <a name="frame-statistics-queries"></a>Keret statisztikai lekérdezései

A keret statisztikái magas szintű információkat biztosítanak az utolsó kerethez, például a késést. A struktúrában megadott adat az `FrameStatistics` ügyfél oldalán mérhető, így az API egy szinkron hívás:

```cs
void QueryFrameData(RenderingSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
```

```cpp
void QueryFrameData(ApiHandle<RenderingSession> session)
{
    FrameStatistics frameStatistics;
    if (session->GetGraphicsBinding()->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        // do something with the result
    }
}
```

A beolvasott `FrameStatistics` objektum a következő tagokat tartalmazza:

| Tag | Magyarázat |
|:-|:-|
| LatencyPoseToReceive | A kamera késése becslést jelent az eszközön, amíg az alkalmazáshoz tartozó kiszolgálói keret teljes mértékben elérhető az ügyfélalkalmazás számára. Ez az érték magában foglalja a hálózati bemutatót, a kiszolgáló megjelenítési idejét, a videó-dekódolást és a jitter-kompenzációt. Lásd **a fenti ábrán az 1. intervallumot.**|
| LatencyReceiveToPresent | A fogadott távoli keretek rendelkezésre állásának késése, amíg az ügyfélalkalmazás nem hívja meg a PresentFrame a PROCESSZORon. Lásd **a fenti ábrán a 2. intervallumot.**|
| LatencyPresentToDisplay  | Egy keretnek a CPU-ban való megjelenítésének késése, amíg a kijelző fel nem villan. Ez az érték tartalmazza az ügyfél GPU-időpontját, az operációs rendszer által végrehajtott összes frame-pufferelést, a hardverek újravetítését és az eszköztől függő megjelenítési vizsgálat időpontját. Lásd **a fenti ábrán látható 3. intervallumot.**|
| TimeSinceLastPresent | A következő, a PROCESSZORon PresentFrame meghívások közötti idő. A megjelenítési időtartamnál nagyobb értékek (például az 16,6 MS egy 60-Hz-es ügyfélszámítógépen) jelzik, hogy az ügyfélalkalmazás által okozott problémák nem fejezik be a CPU-munkaterhelést időben.|
| VideoFramesReceived | A kiszolgálóról az elmúlt másodpercben fogadott keretek száma. |
| VideoFrameReusedCount | A fogadott képkockák száma az elmúlt másodpercben, amely többször is használatban volt az eszközön. A nullától eltérő értékek azt jelzik, hogy a rendszer újra felhasználta a képkockákat, és a hálózati Jitter vagy a kiszolgáló túlzott megjelenítési ideje miatt újra feldolgozta őket. |
| VideoFramesSkipped | Az elmúlt másodpercben a visszakapott képkockák száma, amelyek dekódolása megtörtént, de a megjelenítés nem látható, mert újabb keret érkezett. A nullától eltérő értékek azt jelzik, hogy a hálózati vibrálás több képkockát okozott, majd az eszközön együtt megérkezik a burst állapotba. |
| VideoFramesDiscarded | Nagyon hasonlít a **VideoFramesSkipped**, de az eldobásának oka, hogy egy keret olyan későn jött létre, hogy a függőben lévők már nem is korrelálnak. Ha ez az Elvetés történik, akkor a hálózati tartalom valamilyen súlyos.|
| VideoFrameMinDelta | Az elmúlt másodpercben két egymást követő keret között érkező minimális időtartam. A VideoFrameMaxDelta együtt ez a tartomány a hálózat vagy a videó kodek által okozott vibrálás jelzését adja meg. |
| VideoFrameMaxDelta | Az elmúlt másodpercben két egymást követő keret között elérkező maximális időtartam. A VideoFrameMinDelta együtt ez a tartomány a hálózat vagy a videó kodek által okozott vibrálás jelzését adja meg. |

Az összes késési érték összege általában jóval nagyobb, mint a rendelkezésre álló keret 60 Hz-nél. Ez azért van így, mert több keret van folyamatban párhuzamosan, és az új keretekre vonatkozó kérések a kívánt keretre kerülnek, ahogy az ábrán is látható. Ha azonban a késés túl nagy, az hatással van a [késői fázis újravetítésének](../../overview/features/late-stage-reprojection.md)minőségére, és veszélyeztetheti a teljes élményt.

`VideoFramesReceived`a, a `VideoFrameReusedCount` és a használatával mérhető `VideoFramesDiscarded` a hálózat és a kiszolgáló teljesítménye. Az alacsony `VideoFramesReceived` érték és a magas érték kombinációja a `VideoFrameReusedCount` hálózati torlódást vagy a kiszolgáló gyenge teljesítményét jelezheti. A magas `VideoFramesDiscarded` érték A hálózati torlódást is jelzi.

Végül,, `TimeSinceLastPresent` `VideoFrameMinDelta` és `VideoFrameMaxDelta` a beérkező képkockák és a helyi meghívások eltérésének ötlete. A nagy variancia nem stabil képarányt jelent.

A fenti értékek egyike sem teszi egyértelművé a tiszta hálózati késést (az ábrán látható piros nyilat), mert a kiszolgáló elfoglalt állapotának pontos időpontját ki kell vonni a oda-értékből `LatencyPoseToReceive` . Az általános késés kiszolgálóoldali része olyan információ, amely nem érhető el az ügyfél számára. A következő bekezdés azonban elmagyarázza, hogy ez az érték hogyan közelíthető meg a kiszolgáló további bemenetei és az `NetworkLatency` érték alapján.

## <a name="performance-assessment-queries"></a>Teljesítmény-értékelési lekérdezések

A *teljesítmény-értékelési lekérdezések* részletesebb információkat biztosítanak a kiszolgáló processzor-és GPU-munkaterheléséről. Mivel az adatok kérése a kiszolgálóról történik, a teljesítmény-pillanatfelvétel lekérdezése a szokásos aszinkron mintát követi:

```cs
async void QueryPerformanceAssessment(RenderingSession session)
{
    try
    {
        PerformanceAssessment result = await session.Connection.QueryServerPerformanceAssessmentAsync();
        // do something with result...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void QueryPerformanceAssessment(ApiHandle<RenderingSession> session)
{
    session->Connection()->QueryServerPerformanceAssessmentAsync([](Status status, PerformanceAssessment result) {
        if (status == Status::OK)
        {
            // do something with result...
        }
    });
}
```

Az objektummal ellentétben `FrameStatistics` az `PerformanceAssessment` objektum kiszolgálóoldali adatokat tartalmaz:

| Tag | Magyarázat |
|:-|:-|
| TimeCPU | Átlagos kiszolgálói CPU-idő/keret (ezredmásodperc) |
| TimeGPU | Kiszolgáló GPU-ra vonatkozó átlagos ideje (ezredmásodperc) |
| UtilizationCPU | Kiszolgáló CPU-kihasználtsága összesen (százalék) |
| UtilizationGPU | Kiszolgáló GPU-kihasználtsága összesen (százalék) |
| MemoryCPU | Kiszolgáló összes fő memóriájának kihasználtsága százalékban |
| MemoryGPU | Dedikált videomemória teljes kihasználtsága a kiszolgálói GPU százalékában |
| NetworkLatency | Az átlagos kétirányú hálózati késés ezredmásodpercben megadva. A fenti ábrán ez az érték a piros nyilak összegének felel meg. Az értéket a rendszer a tényleges kiszolgáló renderelési idejének kivonásával számítja ki `LatencyPoseToReceive` `FrameStatistics` . Habár ez a közelítés nem pontos, a hálózati késést is jelzi, amely az ügyfélen számított késési értékektől elkülönített. |
| PolygonsRendered | Az egyik keretben megjelenített háromszögek száma Ez a szám a renderelés során később kiselejtezett háromszögeket is tartalmazza. Ez azt jelenti, hogy ez a szám nem változik a különböző kamera-pozíciók között, de a teljesítmény jelentősen változhat, a háromszög-kiselejtezési aránytól függően.|

Az értékek felmérése érdekében minden részhez tartozik egy minőségi besorolás, például **nagyszerű**, **jó**, **közepes** vagy **rossz**.
Ez az értékelési metrika a kiszolgáló állapotának durva jelzését biztosítja, de nem tekinthető abszolútnak. Tegyük fel például, hogy a GPU-idő "közepes" pontszámot tartalmaz. A rendszer közepesnek tekinti, mivel az a teljes keretre vonatkozó költségkerethez tartozó korláthoz közeledik. Ebben az esetben azonban érdemes lehet jó értéket adni, mert összetett modellt tesz elérhetővé.

## <a name="statistics-debug-output"></a>Statisztikák hibakeresési kimenete

Az osztály `ServiceStatistics` egy C#-osztály, amely a keret statisztikáit és a teljesítmény-értékelési lekérdezéseket is tartalmazza, és kényelmes funkciókat biztosít a statisztikák összesített értékként való visszaküldéséhez, vagy egy előre elkészített sztringként. A következő kód a kiszolgálóoldali statisztika megjelenítésének legegyszerűbb módja az ügyfélalkalmazás számára.

```cs
ServiceStatistics _stats = null;

void OnConnect()
{
    _stats = new ServiceStatistics();
}

void OnDisconnect()
{
    _stats = null;
}

void Update()
{
    if (_stats != null)
    {
        // update once a frame to retrieve new information and build average values
        _stats.Update(Service.CurrentActiveSession);

        // retrieve a string with relevant stats information
        InfoLabel.text = _stats.GetStatsString();
    }
}
```

A fenti kód feltölti a szöveg címkéjét a következő szöveggel:

![ArrServiceStats karakterlánc kimenete](./media/arr-service-stats.png)

Az `GetStatsString` API az összes érték sztringjét formázza, de minden egyes érték programozott módon is lekérdezhető a `ServiceStatistics` példányból.

A tagoknak vannak olyan változatai is, amelyek az értékeket az idő múlásával összesítik. Tekintse meg a tagokat utótaggal `*Avg` , `*Max` vagy `*Total` . A tag `FramesUsedForAverage` azt jelzi, hogy az összesítéshez hány képkockát használtak.

## <a name="api-documentation"></a>API-dokumentáció

* [C# RenderingConnection. QueryServerPerformanceAssessmentAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.queryserverperformanceassessmentasync)
* [C++ RenderingConnection:: QueryServerPerformanceAssessmentAsync ()](/cpp/api/remote-rendering/renderingconnection#queryserverperformanceassessmentasync)

## <a name="next-steps"></a>Következő lépések

* [Teljesítmény-nyomkövetés létrehozása](../../how-tos/performance-tracing.md)
* [A modell átalakításának konfigurálása](../../how-tos/conversion/configure-model-conversion.md)
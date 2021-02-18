---
title: Telemetria folyamatos exportálása a Application Insightsból | Microsoft Docs
description: A diagnosztikai és használati adatok exportálása a Microsoft Azure tárolóba, és onnan tölthető le.
ms.topic: conceptual
ms.date: 05/26/2020
ms.openlocfilehash: 23405faeb7d2151ce0f6492c0d522e0a7f9b84a8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584232"
---
# <a name="export-telemetry-from-application-insights"></a>Telemetria exportálása az Application Insightsból
Szeretné megőrizni a telemetria a normál megőrzési időtartamnál hosszabb ideig? Vagy dolgozza fel valamilyen speciális módon? A folyamatos exportálás ideális ehhez. A Application Insights-portálon megjelenített események JSON formátumban exportálhatók Microsoft Azureba. Innen letöltheti az adatait, és bármilyen kódot írhat, amelyet fel kell dolgoznia.  

> [!NOTE]
> A folyamatos exportálás csak a klasszikus Application Insights-erőforrások esetén támogatott. A [munkaterület-alapú Application Insights-erőforrásoknak](./create-workspace-resource.md) [diagnosztikai beállításokat](./create-workspace-resource.md#export-telemetry) kell használniuk.
>

A folyamatos exportálás beállítása előtt bizonyos alternatívákat érdemes figyelembe venni:

* A metrikák vagy a Keresés lap tetején található exportálás gomb lehetővé teszi táblázatok és diagramok Excel-számolótáblába való átvitelét.

* Az [elemzés](../logs/log-query-overview.md) hatékony lekérdezési nyelvet biztosít a telemetria számára. Az eredmények exportálására is használható.
* Ha [Power BIban lévő adatait szeretné felfedezni](./export-power-bi.md), folyamatos exportálás nélkül is megteheti.
* Az [adatelérési REST API](https://dev.applicationinsights.io/) lehetővé teszi a telemetria programozott módon való elérését.
* A telepítő [folyamatos exportálását a PowerShell](/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport)használatával is elérheti.

A folyamatos exportálás után a rendszer a szokásos [megőrzési időszakra](./data-retention-privacy.md)vonatkozóan a Application Insightsban is elérhetővé teszi az adatok tárolását

## <a name="continuous-export-advanced-storage-configuration"></a>Folyamatos exportálás speciális tárolási konfiguráció

A folyamatos exportálás nem **támogatja** a következő Azure Storage-funkciókat/-konfigurációkat:

* A [VNET/Azure Storage-tűzfalak](../../storage/common/storage-network-security.md) használata az Azure Blob Storage szolgáltatással együtt.

* [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md).

## <a name="create-a-continuous-export"></a><a name="setup"></a> Folyamatos exportálás létrehozása

> [!NOTE]
> Az alkalmazások naponta nem exportálhatók több mint DEDUPLIKÁCIÓNAK. Ha a naponta több mint DEDUPLIKÁCIÓNAK exportálja, az Exportálás le lesz tiltva. A korlátozás nélküli exportáláshoz használjon [diagnosztikai beállításokon alapuló exportálást](#diagnostic-settings-based-export).

1. Az alkalmazás Application Insights erőforrásában a bal oldali konfigurálás területen nyissa meg a folyamatos exportálás elemet, és válassza a **Hozzáadás** lehetőséget:

2. Válassza ki az exportálni kívánt telemetria-adattípusokat.

3. Hozzon létre vagy válasszon ki egy [Azure Storage-fiókot](../../storage/common/storage-introduction.md) , ahol tárolni kívánja az adatkészletet. A Storage díjszabási lehetőségeivel kapcsolatos további információkért látogasson el a [hivatalos díjszabási oldalra](https://azure.microsoft.com/pricing/details/storage/).

     Kattintson a Hozzáadás, az Exportálás célhelye, a Storage-fiók lehetőségre, majd hozzon létre egy új tárolót, vagy válasszon egy meglévő tárolót.

    > [!Warning]
    > Alapértelmezés szerint a tárolási hely ugyanarra a földrajzi régióra lesz beállítva, mint a Application Insights erőforrás. Ha egy másik régióban tárolja, díjköteles lehet.

4. Hozzon létre vagy válasszon ki egy tárolót a tárolóban.

> [!NOTE]
> Miután létrehozta az exportálást, az újonnan betöltött adatforgalom az Azure Blob Storage-ba kerül. A folyamatos Exportálás csak a folyamatos Exportálás engedélyezése után létrehozott/beolvasott új telemetria továbbítja. A folyamatos exportálás engedélyezése előtt meglévő adatokat a rendszer nem exportálja, és nincs támogatott lehetőség a korábban létrehozott adatok visszamenőleges exportálására a folyamatos exportálással.

A tárolóban lévő adatmennyiség körülbelül egy órával késleltethető.

Az első exportálás befejezése után a következőhöz hasonló struktúra található az Azure Blob Storage-tárolóban: (ez a gyűjtött adatoktól függően változhat.)

|Név | Leírás |
|:----|:------|
| [Rendelkezésre állás](export-data-model.md#availability) | Jelentések [rendelkezésre állását ismertető webes tesztek](./monitor-web-app-availability.md).  |
| [Esemény](export-data-model.md#events) | A [TrackEvent ()](./api-custom-events-metrics.md#trackevent)által generált egyéni események. 
| [Kivételek](export-data-model.md#exceptions) |A kiszolgáló és a böngésző [kivételeit](./asp-net-exceptions.md) jelenti.
| [Üzenetek](export-data-model.md#trace-messages) | A [TrackTrace](./api-custom-events-metrics.md#tracktrace)és a [naplózási adapterek](./asp-net-trace-logs.md)küldik.
| [Metrikák](export-data-model.md#metrics) | Metrikai API-hívások generálása.
| [PerformanceCounters](export-data-model.md) | A Application Insights által gyűjtött teljesítményszámlálók.
| [Kérelmek](export-data-model.md#requests)| A [TrackRequest](./api-custom-events-metrics.md#trackrequest)küldte. A standard modulok ezt a kiszolgálót használják a kiszolgálón mért válaszidő megjelentéséhez.| 

### <a name="to-edit-continuous-export"></a>A folyamatos exportálás szerkesztése

Kattintson a folyamatos exportálás elemre, és válassza ki a szerkeszteni kívánt Storage-fiókot.

### <a name="to-stop-continuous-export"></a>A folyamatos exportálás leállítása

Az Exportálás leállításához kattintson a Letiltás gombra. Amikor az Engedélyezés gombra kattint, az Exportálás új adattal fog újraindulni. Az Exportálás letiltását követően nem fogja lekérni a portálon megjelenő adatkérést.

Az Exportálás végleges leállításához törölje azt. Így nem törli az adatait a tárolóból.

### <a name="cant-add-or-change-an-export"></a>Nem lehet hozzáadni vagy módosítani az exportálást?
* Az exportálások hozzáadásához vagy módosításához tulajdonosi, közreműködői vagy Application Insights-közreműködői hozzáférési jogosultságra van szüksége. [További információ a szerepkörökről][roles].

## <a name="what-events-do-you-get"></a><a name="analyze"></a> Milyen eseményekhez juthat?
Az exportált adatok az alkalmazásból kapott nyers telemetria, kivéve, ha az ügyfél IP-címéből kiszámított helyadatok hozzáadására kerül sor.

A [mintavétel](./sampling.md) által elvetett adatvesztés nem szerepel az exportált adatsorokban.

A rendszer nem tartalmazza a többi számított metrikát. Például nem exportáljuk az átlagos CPU-kihasználtságot, de exportáljuk a nyers telemetria, amelyből az átlagot számítjuk.

Az adatmennyiség magában foglalja az Ön által beállított [rendelkezésre állási webes tesztek](./monitor-web-app-availability.md) eredményeit is.

> [!NOTE]
> **Mintavételi.** Ha az alkalmazás sok adatot küld, előfordulhat, hogy a mintavételezési szolgáltatás csak a létrehozott telemetria töredékét küldi el a működése során. [További tudnivalók a mintavételezésről.](./sampling.md)
>
>

## <a name="inspect-the-data"></a><a name="get"></a> Az adatgyűjtés ellenőrzése
A tárolót közvetlenül a portálon ellenőrizheti. Kattintson a bal szélső menü Kezdőlap elemére, ahol az "Azure-szolgáltatások" lehetőséget választja a **Storage-fiókok** elemre, majd válassza ki a Storage-fiók nevét, az Áttekintés lapon a szolgáltatások területen válassza a **Blobok** lehetőséget, végül válassza ki a tároló nevét.

Az Azure Storage a Visual Studióban való vizsgálatához nyissa meg a **nézet**, **Cloud Explorer** lehetőséget. (Ha nem rendelkezik a menüparancsokkal, telepítenie kell az Azure SDK-t: Nyissa meg az **új projekt** párbeszédpanelt, bontsa ki a Visual C#/Cloud elemet, és válassza a **beolvasás Microsoft Azure SDK a .net-hez** lehetőséget.)

A blob-tároló megnyitásakor egy tárolót fog látni a blob-fájlokkal. A Application Insights-erőforrás nevéből származtatott fájlok URI-ja, a kialakítási kulcs, a telemetria-típus/dátum/idő. (Az erőforrás neve mind kisbetűs, és a kialakítási kulcs kihagyja a kötőjeleket.)

![A blob-tároló vizsgálata megfelelő eszközzel](./media/export-telemetry/04-data.png)

A dátum és az idő UTC, és az, amikor a telemetria a tárolóban helyezték el – nem a generált idő. Tehát ha kódot ír az adatletöltéshez, az az adatátvitelt lineárisan áthelyezheti.

Az elérési út formája:

```console
$"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"
```

Ahol

* `blobCreationTimeUtc` az az idő, amikor a blobot létrehozták a belső átmeneti tárolóban.
* `blobDeliveryTimeUtc` az az idő, amikor a blobot a rendszer az Exportálás célhelyére másolja

## <a name="data-format"></a><a name="format"></a> Adatformátum
* Minden blob egy szövegfájl, amely több "\n"-tagolt sort tartalmaz. Tartalmazza a feldolgozott telemetria körülbelül fél percen belül.
* Az egyes sorok egy telemetria adatpontot jelölnek, például egy kérés vagy egy oldal nézetet.
* Minden sor egy formázatlan JSON-dokumentum. Ha meg szeretné tekinteni a sorokat, nyissa meg a blobot a Visual Studióban, és válassza a   >  **speciális**  >  **formátumú fájl** szerkesztése elemet:

   ![A telemetria megtekintése megfelelő eszközzel](./media/export-telemetry/06-json.png)

Az időtartamok a kullancsokban vannak, ahol a 10 000-es osztásjelek = 1 ms. Ezek az értékek például 1 MS időpontot jelenítenek meg egy kérésnek a böngészőből való elküldéséhez, 3 MS a fogadáshoz, és 1,8 s a lap felfeldolgozásához a böngészőben:

```json
"sendRequest": {"value": 10000.0},
"receiveRequest": {"value": 30000.0},
"clientProcess": {"value": 17970000.0}
```

[Részletes adatmodell-referenciák a tulajdonságok típusaihoz és értékeihez.](export-data-model.md)

## <a name="processing-the-data"></a>Az adatfeldolgozás
Kis léptékben írhat egy kódot, amely lekéri az adatait, beolvashatja azt egy számolótáblába, és így tovább. Például:

```csharp
private IEnumerable<T> DeserializeMany<T>(string folderName)
{
   var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
   foreach (var file in files)
   {
      using (var fileReader = File.OpenText(file))
      {
         string fileContent = fileReader.ReadToEnd();
         IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
         foreach (var entity in entities)
         {
            yield return JsonConvert.DeserializeObject<T>(entity);
         }
      }
   }
}
```

Nagyobb mintakód esetén lásd: [feldolgozói szerepkör használata][exportasa].

## <a name="delete-your-old-data"></a><a name="delete"></a>Régi adatai törlése
Az Ön felelőssége, hogy kezelje a tárolókapacitást, és szükség esetén törölje a régi adatmennyiséget.

## <a name="if-you-regenerate-your-storage-key"></a>Ha újragenerálta a tárolási kulcsot...
Ha módosítja a tároló kulcsát, a folyamatos exportálás nem fog működni. Ekkor egy értesítés jelenik meg az Azure-fiókjában.

Nyissa meg a Folyamatos exportálás lapot, és módosítsa az exportálást. Módosítsa az Exportálás célhelye beállítást, de hagyja kiválasztva az adott tárolót. Kattintson az OK gombra a megerősítéshez.

A folyamatos exportálás újraindul.

## <a name="export-samples"></a>Minták exportálása

* [SQL-exportálás a Stream Analytics használatával][exportasa]
* [2. minta Stream Analytics](export-stream-analytics.md)

Nagyobb lépték esetén vegye fontolóra a [HDInsight](https://azure.microsoft.com/services/hdinsight/) Hadoop-fürtöket a felhőben. A HDInsight számos különböző technológiát biztosít a big data felügyeletéhez és elemzéséhez, és felhasználhatja az Application Insightsból exportált adatok feldolgozására.

## <a name="q--a"></a>Kérdések és válaszok
* *Azonban csak egy diagram egyszeri letöltésére van szükség.*  

    Igen, ezt megteheti. A lap tetején kattintson az **adatexportálás** elemre.
* *Egy exportálást állítottam be, de az áruházban nem találhatók adatkészletek.*

    Az Exportálás beállítása óta Application Insights kapott bármilyen telemetria az alkalmazástól? Csak az új adatgyűjtést fogja kapni.
* *Megpróbáltam beállítani az exportálást, de a hozzáférés megtagadva*

    Ha a fiók a szervezet tulajdonában van, akkor a tulajdonosok vagy közreműködők csoport tagjának kell lennie.
* *Exportálhatók közvetlenül a saját helyszíni áruházba?*

    Nem, sajnos. Az exportálási motor jelenleg csak az Azure Storage szolgáltatással működik.  
* *Van korlátozva az áruházban elhelyezett adatmennyiség?*

    Nem. Az adatmegőrzést addig tartjuk, amíg nem törli az exportálást. Ha elérjük a blob Storage külső korlátait, akkor leállnak, de ez elég nagy. Ezzel a beállítással szabályozhatja, hogy mekkora tárterületet használ.  
* *Hány blobot látok a tárolóban?*

  * Minden exportálásra kiválasztott adattípus esetében minden percben létrejön egy új blob (ha az elérhető).
  * Emellett a nagy forgalmú alkalmazások esetében további partíciós egységek is le vannak foglalva. Ebben az esetben minden egység percenként hoz létre egy blobot.
* *Újrageneráltam a kulcsot a tárhelyhez, vagy Megváltoztattam a tároló nevét, és most az exportálás nem működik.*

    Szerkessze az exportálást, és nyissa meg az Exportálás célhelye lapot. Hagyja meg ugyanazt a tárolót, mint korábban, majd a megerősítéshez kattintson az OK gombra. Az Exportálás újra fog indulni. Ha a módosítás az elmúlt néhány napon belül volt, nem veszíti el az adatvesztést.
* *Szüneteltethető az Exportálás?*

    Igen. Kattintson a Letiltás lehetőségre.

## <a name="code-samples"></a>Kódminták

* [Stream Analytics minta](export-stream-analytics.md)
* [SQL-exportálás a Stream Analytics használatával][exportasa]
* [Részletes adatmodell-referenciák a tulajdonságok típusaihoz és értékeihez.](export-data-model.md)

## <a name="diagnostic-settings-based-export"></a>Diagnosztikai beállításokon alapuló exportálás

A diagnosztikai beállításokon alapuló exportálás más sémát használ, mint a folyamatos exportálás. Emellett támogatja a folyamatos exportáláshoz hasonló funkciókat:

* Azure Storage-fiókok vnet, tűzfalakkal és privát hivatkozásokkal.
* Exportálás az Event hub-ba.

Áttelepítés diagnosztikai beállításokon alapuló exportálásra:

1. A jelenlegi folyamatos exportálás letiltása.
2. [Alkalmazás migrálása munkaterület-alapúra](convert-classic-resource.md).
3. A [diagnosztikai beállítások exportálásának engedélyezése](create-workspace-resource.md#export-telemetry). Válassza a **diagnosztikai beállítások > a diagnosztikai beállítás hozzáadása** lehetőséget a Application Insights erőforráson belül.

<!--Link references-->

[exportasa]: ./code-sample-export-sql-stream-analytics.md
[roles]: ./resources-roles-access-control.md


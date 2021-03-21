---
title: Programozott hozzáférés paradigma
description: Ismerje meg a programozott elemzések API-hívási mintájának magas szintű folyamatát. A Microsoft kereskedelmi piactéren az elemzési jelentések eléréséhez szükséges API-kat is tárgyaljuk.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8e0b94a46e96dd8ba16040e16b421520eb67de19
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583865"
---
# <a name="programmatic-access-paradigm"></a>Programozott hozzáférés paradigma

Ez az ábra az új jelentéssablon létrehozásához használt API-hívási mintát, az egyéni jelentés ütemezését és a hibák beolvasását mutatja be.

[ ![ Bemutatja az új jelentéssablon létrehozásához használt API-hívási mintát, az egyéni jelentés ütemezését és a sikertelen adatlekérdezéseket.](./media/analytics-programmatic-access/analytics-high-level-flow.png)](./media/analytics-programmatic-access/analytics-high-level-flow.png#lightbox) 
 ***1. ábra: az API-hívási minta magas szintű folyamata***

Ez a lista további részleteket tartalmaz az 1. ábrán.

1. Az ügyfélalkalmazás megadhatja az egyéni jelentési sémát vagy sablont a [jelentés lekérdezési API](#create-report-query-api)-ját meghívásával. Másik lehetőségként használhatja a jelentések sablonját ( `QueryId` ) a [rendszerlekérdezések listájáról](analytics-system-queries.md).
1. Sikeres művelet esetén a jelentés sablonjának létrehozása API a következőt adja vissza: `QueryId` .
1. Az ügyfélalkalmazás ezután meghívja a [jelentés létrehozása API](#create-report-api) - `QueryID` t a jelentés kezdő dátumával, az ismétlés intervallumával, az ismétlődéssel és egy opcionális VISSZAhívási URI-val.
1. Siker esetén a [jelentés létrehozása API](#create-report-api) a következőt adja vissza: `ReportID` .
1. Az ügyfélalkalmazás értesítést kap a visszahívási URI-n, amint a jelentéshez tartozó adatfájlok készen állnak a letöltésre.
1. Az ügyfélalkalmazás ezután a [jelentések végrehajtása végrehajtás API](#get-report-executions-api) használatával kérdezi le a jelentés állapotát a és a Dátumtartomány alapján `Report ID` .
1. Siker esetén a rendszer a jelentés letöltési hivatkozását adja vissza, és az alkalmazás elindíthatja az adatgyűjtés letöltését.

## <a name="report-query-language-specification"></a>Jelentés lekérdezési nyelvének specifikációja

Habár a jelentések létrehozásához használható [rendszerlekérdezéseket](analytics-system-queries.md) biztosítunk, az üzleti igények alapján saját lekérdezéseket is létrehozhat. További információ az egyéni lekérdezésekről: [egyéni lekérdezési specifikáció](analytics-custom-query-specification.md).

## <a name="create-report-query-api"></a>Jelentés-lekérdezési API létrehozása

Ez az API segít olyan egyéni lekérdezések létrehozásában, amelyek meghatározzák azt az adatkészletet, amelyből az oszlopokat és a metrikákat exportálni kell. Az API rugalmasságot biztosít az új jelentéskészítési sablon létrehozásához az üzleti igények alapján.

Használhatja az általunk biztosított [rendszerlekérdezéseket](analytics-system-queries.md) is. Ha nincs szükség egyéni jelentési sablonokra, a [jelentés létrehozása API](#create-report-api) -t közvetlenül az általunk megadott rendszerlekérdezések [QueryIds](analytics-system-queries.md) hívhatja meg.

Az alábbi példa bemutatja, hogyan hozhat létre egyéni lekérdezést a _normalizált használati és becsült pénzügyi költségek_ beszerzéséhez a fizetett SKU- [ISVUsage](analytics-make-your-first-api-call.md#programmatic-api-call) az elmúlt hónapra vonatkozóan.

*Kérelem szintaxisa*

| Metódus | Kérés URI-ja |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries` |
|||

*Kérelem fejléce*

| Fejléc | Típus | Description |
| ------------- | ------------- | ------------- |
| Engedélyezés | sztring | Kötelező. A Azure Active Directory (Azure AD) hozzáférési jogkivonata. A formátum `Bearer <token>`. |
| Content-Type | `string` | `application/JSON` |
||||

*Elérési út paraméter*

Nincsenek

*Lekérdezési paraméter*

Nincsenek

*Példa a kérelem hasznos adataira*

```json
{
    "Name": "ISVUsageQuery",
    "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
    "Query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"
}
```

*Szószedet*

Ez a táblázat a kérelem hasznos adatainak elemeinek legfontosabb definícióit tartalmazza.

| Paraméter | Kötelező | Leírás | Megengedett értékek |
| ------------ | ------------- | ------------- | ------------- |
| `Name` | Yes | A lekérdezés rövid neve | sztring |
| `Description` | No | A lekérdezés által visszaadott eredmény leírása | sztring |
| `Query` | Yes | Jelentés lekérdezési karakterlánca | Adattípus: karakterlánc<br>[Egyéni lekérdezés](analytics-sample-queries.md) üzleti igények alapján |
|||||

> [!NOTE]
> Egyéni lekérdezési minták esetében lásd: [példák a minták lekérdezésére](analytics-sample-queries.md).

*Minta válasz*

A válasz tartalma a következőképpen van strukturálva:

Reagálási kódok: 200, 400, 401, 403, 500

Válasz hasznos adat például:

```json
{
  "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": " ISVUsageQuery",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs”,
            "query": " SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34Z"
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

*Szószedet*

Ez a táblázat a válasz elemeinek legfontosabb definícióit tartalmazza.

| Paraméter | Leírás |
| ------------ | ------------- |
| `QueryId` | A létrehozott lekérdezés univerzálisan egyedi azonosítója (UUID) |
| `Name` | A kérelem hasznos adatainak lekérdezéséhez megadott felhasználóbarát név |
| `Description` | A lekérdezés létrehozásakor megadott leírás |
| `Query` | A lekérdezés létrehozásakor átadott lekérdezési lekérdezés |
| `Type` | Beállítás értéke `userDefined` |
| `User` | A lekérdezés létrehozásához használt felhasználói azonosító |
| `CreatedTime` | A lekérdezés a következő formátumban való létrehozásának UTC-ideje: éééé-hh-NNTóó: PP: ssZ |
| `TotalCount` | Adatkészletek száma az érték tömbben |
| `StatusCode` | Eredmény kódja<br>A lehetséges értékek a következők 200, 400, 401, 403, 500 |
| `message` | Állapotjelző üzenet az API végrehajtásáról |
|||

## <a name="create-report-api"></a>Jelentés API létrehozása

Ha sikeresen létrehoz egy egyéni jelentéssablon-sablont, és a `QueryID` [jelentés lekérdezési válaszának létrehozásakor](#create-report-query-api) befogadja a jelentést, akkor ez az API meghívható úgy, hogy rendszeres időközönként végrehajtsa a lekérdezést. Megadhatja a kézbesítés gyakoriságát és az ütemezett jelentést. Az általunk biztosított rendszerlekérdezések esetében a Create Report API [QueryId](analytics-sample-queries.md)használatával is meghívható.

*Kérelem szintaxisa*

| Metódus | Kérés URI-ja |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport` |
|||

*Kérelem fejléce*

| Fejléc | Típus | Description |
| ------ | ---- | ----------- |
| Engedélyezés | sztring | Kötelező. A Azure Active Directory (Azure AD) hozzáférési jogkivonata. A formátum `Bearer <token>`. |
| Tartalom típusa | sztring | `application/JSON` |
||||

*Elérési út paraméter*

Nincsenek

*Lekérdezési paraméter*

Nincsenek

*Példa a kérelem hasznos adataira*

```json
{
  "ReportName": "ISVUsageReport",
  "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
  "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c ",
  "StartTime": "2021-01-06T19:00:00Z ",
  "RecurrenceInterval": 48,
  "RecurrenceCount": 20,
  "Format": "csv",
  "CallbackUrl": "https://<SampleCallbackUrl>"
}
```

*Szószedet*

Ez a táblázat a kérelem hasznos adatainak elemeinek legfontosabb definícióit tartalmazza.

| Paraméter | Kötelező | Leírás | Megengedett értékek |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Yes | A jelentéshez rendelt név | sztring |
| `Description` | No | A létrehozott jelentés leírása | sztring |
| `QueryId` | Yes | Jelentés lekérdezésének azonosítója | sztring |
| `StartTime` | Yes | Az UTC-időbélyeg, amelyen a jelentés létrehozása megkezdődik.<br>A formátum a következőket kell megadni: éééé-hh-NNTóó: PP: ssZ | sztring |
| `RecurrenceInterval` | Yes | A jelentés generálásának gyakorisága órában.<br>A minimális érték 4, a maximális érték 90. | egész szám |
| `RecurrenceCount` | No | A létrehozandó jelentések száma. | egész szám |
| `Format` | No | Az exportált fájl fájlformátuma.<br>Az alapértelmezett formátum:. CSV. | CSV/TSV |
| `CallbackUrl` | No | Nyilvánosan elérhető URL-cím, amely opcionálisan konfigurálható visszahívási célként. | Karakterlánc (http URL-cím) |
| `ExecuteNow` | No | Ezt a paramétert olyan jelentés létrehozására kell használni, amely csak egyszer lesz végrehajtva. `StartTime`, a `RecurrenceInterval` és `RecurrenceCount` a figyelmen kívül lesz hagyva, ha ez a érték `true` . A jelentést a rendszer azonnal aszinkron módon hajtja végre. | Igaz/hamis |
| `QueryStartTime` | No | Opcionálisan meghatározza az adatok kinyerésének kezdési idejét. Ez a paraméter csak egyszeres végrehajtási jelentésre vonatkozik, amely a következőre van `ExecuteNow` beállítva: `true` . A formátum éééé-hh-NNTóó: PP: ssZ | Időbélyeg karakterláncként |
| `QueryEndTime` | No | Opcionálisan meghatározza a lekérdezés befejezési idejét az adatok kinyeréséhez. Ez a paraméter csak egyszeres végrehajtási jelentésre vonatkozik, amely a következőre van `ExecuteNow` beállítva: `true` . A formátum éééé-hh-NNTóó: PP: ssZ | Időbélyeg karakterláncként |
|||||

*Mintaválasz*

A válasz tartalma a következőképpen van strukturálva:

Válasz kódja: 200, 400, 401, 403, 404, 500

Válasz hasznos adatai:

```json
{
  "Value": [
    {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVUsageReport",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": "https://<SampleCallbackUrl>",
            "format": "csv"    
    }
  ],
  "TotalCount": 1,
  "Message": "Report created successfully",
  "StatusCode": 200
}
```

*Szószedet*

Ez a táblázat a válasz elemeinek legfontosabb definícióit tartalmazza.

| Paraméter | Leírás |
| ------------ | ------------- |
| `ReportId` | A létrehozott jelentés univerzálisan egyedi azonosítója (UUID) |
| `ReportName` | A jelentésnek a kérelem hasznos adataiban megadott neve |
| `Description` | A jelentés létrehozása során megadott leírás |
| `QueryId` | A jelentés létrehozásakor átadott lekérdezési azonosító |
| `Query` | A jelentéshez végrehajtandó szöveg lekérdezése |
| `User` | A jelentés létrehozásához használt felhasználói azonosító |
| `CreatedTime` | A jelentés a következő formátumban való létrehozásának UTC-ideje: éééé-hh-NNTóó: PP: ssZ |
| `ModifiedTime` | A jelentés utolsó módosításának UTC-ideje a következő formátumban: éééé-hh-NNTóó: PP: ssZ |
| `StartTime` | A jelentés végrehajtásának UTC-ideje a következő formátumban kezdődik: éééé-hh-NNTóó: PP: ssZ |
| `ReportStatus` | A jelentés végrehajtásának állapota. A lehetséges értékek: **szüneteltetve**, **aktív** és **inaktív**. |
| `RecurrenceInterval` | A jelentés létrehozásakor megadott ismétlődési időköz |
| `RecurrenceCount` | A jelentés létrehozásakor megadott ismétlődések száma |
| `CallbackUrl` | A kérelemben megadott visszahívási URL-cím |
| `Format` | A jelentés fájljainak formátuma. A lehetséges értékek a következők: CSV vagy TSV. |
| `TotalCount` | Adatkészletek száma az érték tömbben |
| `StatusCode` | Eredmény kódja<br>A lehetséges értékek a következők 200, 400, 401, 403, 500 |
| `message` | Állapotjelző üzenet az API végrehajtásáról |
|||

## <a name="get-report-executions-api"></a>Jelentések végrehajtásának beolvasása API

Ezzel a módszerrel lekérdezheti egy jelentés végrehajtásának állapotát a `ReportId` [create Report API](#create-report-api)használatával kapott jelentésből. A metódus a jelentés letöltési hivatkozását adja vissza, ha a jelentés készen áll a letöltésre. Ellenkező esetben a metódus visszaadja az állapotot. Ezt az API-t használhatja az adott jelentéshez tartozó összes végrehajtás beolvasására is.

> [!IMPORTANT]
> Az API-val alapértelmezett lekérdezési paraméterek vannak beállítva a és a számára `executionStatus=Completed`  `getLatestExecution=true` . Ezért a jelentés első sikeres végrehajtásának megkezdése előtt hívja meg az API-t 404-ra. A függőben lévő végrehajtások a beállítással szerezhetők be `executionStatus=Pending` .

*Kérelem szintaxisa*

| Metódus | Kérés URI-ja |
| ------------ | ------------- |
| Lekérés | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/{reportId}?executionId={executionId}&executionStatus={executionStatus}&getLatestExecution={getLatestExecution}` |
|||

*Kérelem fejléce*

| Fejléc | Típus | Description |
| ------ | ------ | ------ |
| Engedélyezés | sztring | Kötelező. A Azure Active Directory (Azure AD) hozzáférési jogkivonata. A formátum `Bearer <token>`. |
| Tartalomtípus | sztring | `application/json` |
||||

*Elérési út paraméter*

Nincsenek

*Lekérdezési paraméter*

| Paraméter neve | Kötelező | Típus | Leírás |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Igen | sztring | Szűréssel lekérheti az `reportId` ebben az argumentumban megadott jelentések végrehajtási részleteit. Több is megadható `reportIds` pontosvesszővel elválasztva (";"). |
| `executionId` | No | sztring | Szűréssel beolvashatja a csak a `executionId` jelen argumentumban megadott jelentések részleteit. Több is megadható `executionIds` pontosvesszővel elválasztva (";"). |
| `executionStatus` | No | karakterlánc/enumerálás | Szűréssel beolvashatja a csak a `executionStatus` jelen argumentumban megadott jelentések részleteit.<br>Az érvényes értékek a következők:,,, `Pending` `Running` `Paused` és `Completed` <br>Az alapértelmezett érték `Completed`. Több állapot is megadható pontosvesszővel (;) elválasztva. |
| `getLatestExecution` | No | boolean | Az API a jelentés legutóbbi végrehajtásának részleteit fogja visszaadni.<br>Alapértelmezés szerint ez a paraméter a következőre van beállítva: `true` . Ha úgy dönt, hogy a paraméter értékét adja át `false` , akkor az API az utolsó 90 nap végrehajtási példányát adja vissza. |
|||||

*Kérelem tartalma*

Nincsenek

*Mintaválasz*

A válasz tartalma a következőképpen van strukturálva:

Reagálási kódok: 200, 400, 401, 403, 404, 500

Válasz hasznos adat például:

```json
{
    "value": [
        {
            "executionId": "a0bd78ad-1a05-40fa-8847-8968b718d00f",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 4,
            "recurrenceCount": 10,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Completed",
            "reportAccessSecureLink": "https://<path to report for download>",
            "reportExpiryTime": null,
            "reportGeneratedTime": "2021-01-13T14:40:46Z"
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

A jelentés végrehajtásának befejeződése után a végrehajtás állapota `Completed` jelenik meg. A jelentést úgy töltheti le, hogy kiválasztja az URL-címet a alkalmazásban `reportAccessSecureLink` .

*Szószedet*

A válasz elemeinek legfontosabb definíciói.

| Paraméter | Leírás |
| ------------ | ------------- |
| `ExecutionId` | A végrehajtási példány univerzálisan egyedi azonosítója (UUID) |
| `ReportId` | A végrehajtási példánnyal társított jelentési azonosító |
| `RecurrenceInterval` | A jelentés létrehozásakor megadott ismétlődési időköz |
| `RecurrenceCount` | A jelentés létrehozásakor megadott ismétlődések száma |
| `CallbackUrl` | A végrehajtási példányhoz társított visszahívási URL-cím |
| `Format` | A létrehozott fájl formátuma a végrehajtás végén |
| `ExecutionStatus` | A jelentés végrehajtási példányának állapota.<br>Az érvényes értékek a következők:,,, `Pending` `Running` `Paused` és `Completed` |
| `ReportAccessSecureLink` | Hivatkozás, amelyen keresztül a jelentés biztonságos módon elérhető |
| `ReportExpiryTime` | UTC-idő, amely után a jelentés hivatkozása lejár a következő formátumban: éééé-hh-NNTóó: PP: ssZ |
| `ReportGeneratedTime` | A jelentés a következő formátumban való létrehozásának UTC-ideje: éééé-hh-NNTóó: PP: ssZ |
| `TotalCount` | Adatkészletek száma az érték tömbben |
| `StatusCode` | Eredmény kódja <br>A lehetséges értékek a következők 200, 400, 401, 403, 404 és 500 |
| `message` | Állapotjelző üzenet az API végrehajtásáról |
|||

## <a name="next-steps"></a>Következő lépések
- Az API-kat kipróbálhatja a [hencegő API URL-címén](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html) keresztül
- Ismerkedés az elemzési szolgáltatásokhoz való programozott hozzáféréssel

---
title: Azure Stream Analytics kimenetek hibáinak megoldása
description: Ez a cikk a Azure Stream Analytics-feladatokban lévő kimeneti kapcsolatok hibaelhárítási módszereit ismerteti.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 10/05/2020
ms.custom: seodec18
ms.openlocfilehash: 02a3a7ad73bf0434a215c5ab7a6e89c299e9518b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019856"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Azure Stream Analytics kimenetek hibáinak megoldása

Ez a cikk a Azure Stream Analytics kimeneti kapcsolataival és a kimeneti problémák elhárításával kapcsolatos gyakori problémákat ismerteti. Számos hibaelhárítási lépéshez szükség van erőforrásra és más diagnosztikai naplókra a Stream Analytics feladatokhoz. Ha nincs engedélyezve az erőforrás-naplók, tekintse meg [a Azure stream Analytics az erőforrás-naplók használatával történő hibakeresését](stream-analytics-job-diagnostic-logs.md)ismertető témakört.

## <a name="the-job-doesnt-produce-output"></a>A művelet nem hoz létre kimenetet

1. Ellenőrizze a kimenetek kapcsolatát az egyes kimenetek **kapcsolat tesztelése** gombjának használatával.
1. Tekintse meg a [figyelési metrikákat](stream-analytics-monitoring.md) a **figyelés** lapon. Mivel az értékek összesítve vannak, a metrikák néhány perc késéssel lesznek késleltetve.

   * Ha a **bemeneti események** értékének értéke nullánál nagyobb, a feladatnak a bemeneti adatokat is el kell olvasnia. Ha a **bemeneti események** értéke nem nagyobb nullánál, akkor probléma van a feladathoz megadott bemenettel. További információért lásd a [bemeneti kapcsolatok hibakeresését](stream-analytics-troubleshoot-input.md) ismertető témakört. Ha a feladatra hivatkozó adatok szerepelnek, a **bemeneti események** metrikájának megtekintésekor alkalmazza a logikai név szerinti felosztást. Ha a hivatkozási adatokból nincsenek bemeneti események, akkor ez valószínűleg azt jelenti, hogy a bemeneti forrás nem megfelelően van konfigurálva a megfelelő hivatkozási adatkészlet beolvasásához.
   * Ha az **Adatátalakítási hibák** értéke nagyobb, mint a nulla és a hegymászás, tekintse meg az Adatátalakítási hibákkal kapcsolatos részletes információkat a [Azure stream Analytics adathibák](data-errors.md) című részben.
   * Ha a **futásidejű hibák** értéke nullánál nagyobb, akkor a feladatsor adatokhoz jut, de hibákat generál a lekérdezés feldolgozásakor. A hibák megkereséséhez nyissa meg a [naplókat](../azure-resource-manager/management/view-activity-logs.md), majd szűrje a **hibás** állapotot.
   * Ha a **bemeneti események** értékének nullánál nagyobbnak kell lennie, és a **kimeneti események** értéke nulla, a következő utasítások egyike igaz:
      * A lekérdezés feldolgozása nulla kimeneti eseményt eredményezett.
      * Lehet, hogy az események vagy a mezők helytelenek, ami nulla kimenetet eredményez a lekérdezés feldolgozását követően.
      * A feladatsor nem tudott adatokat leküldeni a kimeneti fogadónak kapcsolati vagy hitelesítési okokból.

   Az Operations log-üzenetek további részleteket adnak meg, beleértve a mi történik, kivéve azokat az eseteket, amikor a lekérdezési logika kiszűri az összes eseményt. Ha több esemény feldolgozásával hibát generál, a hibák 10 percenként összesítve jelennek meg.

## <a name="the-first-output-is-delayed"></a>Az első kimenet késleltetve

Stream Analytics feladatok indításakor a bemeneti események beolvasása történik. A kimenetben azonban bizonyos esetekben késés is lehet.

Az időbeli lekérdezési elemek nagy időbeli értékei hozzájárulhatnak a kimeneti késleltetéshez. A megfelelő kimenet nagy idő alatt történő létrehozásához a folyamatos átviteli feladatok a lehető legkésőbb beolvassák az adatokat az időablak kitöltéséhez. Az adattartalom legfeljebb hét nappal korábbi lehet. A rendszer nem hoz létre kimenetet a függőben lévő bemeneti események beolvasása előtt. Ez a probléma akkor lehet felszíni, ha a rendszer frissíti a folyamatos átviteli feladatokat. A frissítés megkezdése után a rendszer újraindítja a feladatot. Az ilyen verziófrissítések általában néhány havonta egyszer történnek.

A Stream Analytics-lekérdezés tervezésekor saját mérlegelést kell alkalmaznia. Ha nagy időintervallumot használ a feladatok lekérdezési szintaxisában lévő időbeli elemek számára, akkor az az első kimenet késését eredményezheti a feladatok indításakor vagy újraindításakor. Több mint több óra, akár hét napig is nagy időintervallumnak számít.

Az ilyen típusú első kimeneti késések esetében az egyik megoldás a lekérdezési párhuzamos technikák használata, például az adatparticionálás. Emellett további folyamatos átviteli egységeket is hozzáadhat az átviteli sebesség növeléséhez, amíg a feladatok fel nem zárkóznak.  További információ: [stream Analytics feladatok létrehozásakor megfontolandó szempontok](stream-analytics-concepts-checkpoint-replay.md).

Ezek a tényezők az első kimenet időszerűségét érintik:

* Ablakos összesítések, például a kieséses, átugrásos és csúszó ablakok szerinti GROUP BY záradék használata:

  * Az ablakos összesítések kiesése vagy átugrása esetén az eredmények az ablak időkeretének végén kerülnek elő.
  * Egy csúszó ablak esetében az eredmények akkor kerülnek elő, amikor egy esemény bekerül a csúszó ablakba, vagy kilép onnan.
  * Ha nagy méretű ablakméret (például több mint egy óra) használatát tervezi, a legjobb megoldás a beugró vagy a csúszó ablak kiválasztása. Ezek az ablakkeretek lehetővé teszik a kimenet gyakoribb megjelenítését.

* Időbeli illesztések használata, például csatlakozás a DATEDIFF-hez:
  * A csak akkor jön elő, ha a megegyező események mindkét oldalán érkeznek.
  * A egyezést nem tartalmazó adat, például a bal oldali külső illesztés a DATEDIFF ablak végén jön létre, a bal oldalon lévő összes eseménynél.

* Az időbeli elemzési függvények, például a ISFIRST, a LAST és a LAG használata a korlát IDŐTARTAMával:
  * Az analitikai függvények esetében minden eseménynél létrejön a kimenet. Nincs késés.

## <a name="the-output-falls-behind"></a>A kimenet mögött esik

A feladatok normál működése során a kimenet hosszabb és hosszabb késéssel is rendelkezhet. Ha a kimenet így van, a kiváltó okokat a következő tényezők vizsgálatával határozhatja meg:

* Az alsóbb rétegbeli fogadó szabályozása
* Azt jelzi, hogy a felsőbb rétegbeli forrás szabályozva van-e
* Azt jelzi, hogy a lekérdezés feldolgozási logikája nagy számítási igényű-e

A kimenet részleteinek megtekintéséhez válassza ki a folyamatos átviteli feladatot a Azure Portal, majd válassza a **feladatütemezés** lehetőséget. Minden egyes bemenethez partíción várakozó esemény-metrika található. Ha a metrika továbbra is növekszik, a rendszer azt jelzi, hogy a rendszererőforrások korlátozottak. A növekedés a kimeneti fogadó szabályozása vagy a magas CPU-használat miatt lehetséges. További információ: [adatvezérelt hibakeresés a feladatütemezés használatával](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Kulcs megsértése figyelmeztetés Azure SQL Database kimenettel

Ha egy Azure SQL Database-adatbázist kimenetként konfigurál egy Stream Analytics feladatokhoz, a rekordok tömeges beszúrása a céltáblaba. Általánosságban Azure Stream Analytics garantálja a [legalább egyszeri kézbesítést](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) a kimeneti fogadónak. Továbbra is [pontosan egyszeri kézbesítést érhet]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) el egy SQL-kimenetben, ha egy SQL-táblához egyedi korlátozás van meghatározva.

Amikor egyedi kulcsokra vonatkozó korlátozásokat állít be az SQL-táblán, Azure Stream Analytics eltávolítja az ismétlődő rekordokat. Feldarabolja az adatok kötegekre való felosztását, és rekurzív módon beszúrja a kötegeket, amíg egyetlen duplikált rekord nem található. A felosztási és beillesztési folyamat egyszerre csak egy másodpéldányt hagy el. A sok ismétlődő sorból álló folyamatos átviteli feladatok esetében a folyamat nem hatékony és időigényes. Ha az előző órában több kulcs megsértését jelző figyelmeztető üzenet jelenik meg a tevékenység naplójában, akkor valószínű, hogy az SQL-kimenet lelassítja a teljes feladatot.

A probléma megoldásához konfigurálja a kulcs megsértését okozó [indexet]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) a IGNORE_DUP_KEY beállítás engedélyezésével. Ez a beállítás lehetővé teszi, hogy az SQL figyelmen kívül hagyja az ismétlődő értékeket a tömeges beszúrások során. Azure SQL Database egyszerűen figyelmeztető üzenetet állít elő hiba helyett. Ennek eredményeképpen Azure Stream Analytics már nem hoz létre elsődleges kulcs megsértésével kapcsolatos hibákat.

A IGNORE_DUP_KEY különböző típusú indexekhez való konfigurálásakor vegye figyelembe a következő észrevételeket:

* A IGNORE_DUP_KEY nem állítható be egy elsődleges kulcson vagy egy olyan egyedi korlátozáson, amely az ALTER INDEXet használja. El kell dobnia az indexet, majd újra létre kell hoznia.  
* Megadhatja IGNORE_DUP_KEY az ALTER INDEX használatával egy egyedi indexhez. Ez a példány különbözik az elsődleges kulcs/egyedi korlátozástól, és egy CREATE INDEX vagy INDEX definíció használatával jön létre.  
* Az IGNORE_DUP_KEY lehetőség nem vonatkozik az oszlopok tárolására szolgáló indexekre, mert nem kényszerítheti ki rajtuk egyediségét.

## <a name="sql-output-retry-logic"></a>SQL-kimenet újrapróbálkozási logikája

Amikor az SQL kimenettel rendelkező Stream Analytics-feladatok megkapják az első köteget, a következő lépések történnek:

1. A feladatok megpróbálnak csatlakozni az SQL-hez.
2. A művelet beolvassa a céltábla sémáját.
3. A feladattípus ellenőrzi az oszlopnevek és a típusokat a céltábla sémáján.
4. A feladatsor felkészíti a memóriában tárolt adattáblázatot a köteg kimeneti rekordjaiból.
5. A feladatsor a bulkcopy objektum céloszlopa [API](/dotnet/api/system.data.sqlclient.sqlbulkcopy.writetoserver)használatával írja az adattáblát az SQL-re.

A fenti lépések során az SQL-kimenet a következő típusú hibákat képes megtapasztalni:

* Az exponenciális leállítási újrapróbálkozási stratégiájának használatával újrapróbált átmeneti [hibák](../azure-sql/database/troubleshoot-common-errors-issues.md#transient-fault-error-messages-40197-40613-and-others) . A minimális újrapróbálkozási időköz az egyes hibakódtól függ, de az intervallumok jellemzően kisebbek, mint 60 másodperc. A felső korlát legfeljebb öt perc lehet. 

   A [bejelentkezési hibák](../azure-sql/database/troubleshoot-common-errors-issues.md#unable-to-log-in-to-the-server-errors-18456-40531) és a [tűzfal problémái](../azure-sql/database/troubleshoot-common-errors-issues.md#cannot-connect-to-server-due-to-firewall-issues) legalább 5 perccel az előző próbálkozás után újrapróbálkoznak, és a rendszer újrapróbálkozik, amíg azok nem sikerül.

* Az adathibák, például a hibák és a séma megkötésének megsértése a kimeneti hibák házirendjével kezelhetők. Ezeket a hibákat a bináris felosztott kötegek újrapróbálkozásával kezeli a rendszer, amíg a hibát okozó egyedi rekordot kihagyja vagy újra nem kezeli. Az elsődleges egyedi kulcs korlátozásának megsértését [mindig kezeli](./stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output)a rendszer.

* A nem átmeneti hibák akkor fordulhatnak elő, ha SQL-szolgáltatással kapcsolatos problémák vagy belső hibakódok vannak. Ha például a (1132 rugalmas készlet-es kódú) hibák megkeresik a tárolási korlátját, az újrapróbálkozások nem oldják meg a hibát. Ezekben a forgatókönyvekben a Stream Analytics a feladatok [romlását](job-states.md)tapasztalják.
* `BulkCopy` az időtúllépések `BulkCopy` az 5. lépésben történnek. `BulkCopy` időnként a művelet időtúllépéseit is megtapasztalhatja. Az alapértelmezett minimálisan konfigurált időkorlát öt perc, és a rendszer kétszer is megduplázódik, ha egymás után következik be.
Ha az időkorlát 15 percet vesz igénybe, a rendszer a Batch maximális méretre mutató tippet `BulkCopy` 100-ig csökkenti, amíg a kötegek száma lejár.

## <a name="column-names-are-lowercase-in-azure-stream-analytics-10"></a>Az oszlopnevek kisbetűsek Azure Stream Analytics (1,0)

Az eredeti kompatibilitási szint (1,0) használatakor Azure Stream Analytics az oszlopnevek kisbetűsre változnak. Ez a viselkedés a későbbi kompatibilitási szinteken lett kijavítva. Az eset megőrzése érdekében váltson a 1,1-es vagy újabb kompatibilitási szintre. További információ: [stream Analytics feladatok kompatibilitási szintje](./stream-analytics-compatibility-level.md).

## <a name="get-help"></a>Segítség kérése

További segítségért próbálja ki a [Microsoft Q&a Azure stream Analytics kérdéseit](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Következő lépések

* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics lekérdezés nyelvi referenciája](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics felügyeleti REST API referenciája](/rest/api/streamanalytics/)
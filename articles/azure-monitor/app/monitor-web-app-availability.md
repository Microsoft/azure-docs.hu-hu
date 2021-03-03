---
title: Webhelyek rendelkezésre állásának és válaszkészségének megfigyelése | Microsoft Docs
description: Webes teszteket állíthat be az Application Insightsban. Riasztásokat kaphat, ha egy webhely elérhetetlenné válik vagy lassan válaszol.
ms.topic: conceptual
ms.date: 02/14/2021
ms.reviewer: sdash
ms.openlocfilehash: 0ea44aad43a3dd6f11656a45dc7d0e619187fc22
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729028"
---
# <a name="monitor-the-availability-of-any-website"></a>Bármely webhely rendelkezésre állásának monitorozása

A webalkalmazás/webhely üzembe helyezése után ismétlődő teszteket állíthat be a rendelkezésre állás és a válaszadás figyeléséhez. Az [Azure Application Insights](./app-insights-overview.md) rendszeres időközönként, világszerte különböző helyekről webes kéréseket küld az alkalmazására. Riasztást küld, ha az alkalmazás nem válaszol, vagy ha túl lassan válaszol.

Rendelkezésre állási teszteket állíthat be bármely olyan HTTP- vagy HTTPS-végponthoz, amely a nyilvános internetről érhető el. Nem kell módosítania a tesztelni kívánt webhelyet. Valójában még nem kell a saját webhelynek lennie. A szolgáltatástól függő REST API rendelkezésre állását ellenőrizheti.

### <a name="types-of-availability-tests"></a>A rendelkezésre állási tesztek típusai:

A rendelkezésre állási tesztek három típusa létezik:

* [URL-ping teszt](#create-a-url-ping-test): egyszerű teszt, amelyet az Azure Portalon hozhat létre.
* [Többlépéses webes teszt](availability-multistep.md): a webes kérések sorozatának rögzítése, amely az összetettebb forgatókönyvek tesztelésére használható. A többlépéses webes tesztek a Visual Studio Enterprise-ban jönnek létre, és a portálra lesznek feltöltve a végrehajtáshoz.
* [Egyéni nyomon követési rendelkezésre állási tesztek](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability): Ha úgy dönt, hogy egyéni alkalmazást hoz létre a rendelkezésre állási tesztek futtatásához, a `TrackAvailability()` metódus segítségével küldheti el az eredményeket Application Insights.

**Application Insights erőforráson akár 100 rendelkezésre állási tesztet is létrehozhat.**

> [!IMPORTANT]
> Az [URL ping teszt](#create-a-url-ping-test) és a [többlépéses webes teszt](availability-multistep.md) is a nyilvános internetes DNS-infrastruktúrára támaszkodik a tesztelt végpontok tartománynevének feloldásához. Ez azt jelenti, hogy ha saját DNS használ, meg kell győződnie arról, hogy a teszt minden tartománynevét a nyilvános tartománynév-kiszolgálók is feloldhatók, vagy ha ez nem lehetséges, az [Egyéni nyomkövetési rendelkezésre állási teszteket](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) is használhatja helyette.

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

A rendelkezésre állási teszt létrehozásához először létre kell hoznia egy Application Insights erőforrást. Ha már létrehozott egy erőforrást, folytassa a következő szakasszal [egy URL ping teszt létrehozásához](#create-a-url-ping-test).

A Azure Portal válassza az **erőforrás létrehozása**  >  **fejlesztői eszközök**  >  **Application Insights** lehetőséget, és [hozzon létre egy Application Insights erőforrást](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>URL-ping teszt létrehozása

Az "URL ping test" név egy kis helytelen felhasználóneve. Ez a teszt nem teszi elérhetővé az ICMP (Internet Control Message Protocol) használatát a hely rendelkezésre állásának ellenőrzéséhez. Ehelyett több speciális HTTP-kérés funkciót használ annak ellenőrzéséhez, hogy a végpont válaszol-e. Emellett méri az adott válaszhoz kapcsolódó teljesítményt, és lehetővé teszi az egyéni sikerességi feltételek megadását, amely fejlettebb funkciókkal, például a függő kérelmek elemzésével és az újrapróbálkozások engedélyezésével párosul.

Az első rendelkezésre állási kérelem létrehozásához nyissa meg a rendelkezésre állás panelt, és válassza a **teszt létrehozása** lehetőséget.

![Töltse ki legalább a webhelye URL-címét](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Teszt létrehozása

|Beállítás| Magyarázat
|----|----|----|
|**URL-cím** |  Az URL-cím bármilyen tesztelni kívánt weblap lehet, de láthatónak kell lennie a nyilvános internetről. Az URL-cím tartalmazhat lekérdezési sztringet. Tehát például kísérletezhet egy kicsit az adatbázissal. Ha az URL feloldása egy átirányítást eredményez, legfeljebb 10 átirányításig követjük.|
|**Függő kérelmek elemzése**| Tesztelje a weboldalhoz tartozó képeket, parancsfájlokat, stílusú fájlokat és egyéb fájlokat, amelyek a tesztelés alatt állnak. A rögzített válaszidőbe a fájlok lekérése is beleszámít. A teszt sikertelen lesz, ha a teljes teszt időkorlátján belül nem tölthetők le ezek az erőforrások. Ha a beállítás nincs bejelölve, a teszt csak a fájlt és a megadott URL-címet kéri le. Ha engedélyezi ezt a beállítást, a rendszer szigorúbb ellenőrzését eredményez. Előfordulhat, hogy a teszt sikertelen lehet, ami nem észlelhető a hely manuális böngészése során.
|**Újrapróbálkozások engedélyezése**|Ha a teszt sikertelen, a rendszer rövid idő után újrapróbálkozik. Csak akkor jelent hibát, ha három egymást követő kísérlet meghiúsul. Ezután a rendszer a teszteket a szokásos tesztelési gyakorisággal végzi el. Az újrapróbálkozás ideiglenesen fel van függesztve a következő sikeres műveletig. Ez a szabály függetlenül van alkalmazva minden egyes teszthelyen. **Ezt a beállítást javasoljuk**. Átlagosan körülbelül a hibák 80%-a megszűnik az újrapróbálkozáskor.|
|**Tesztelési gyakoriság**| Beállítja, hogy a teszt milyen gyakran fusson az egyes tesztelési helyekről. Öt perces alapértelmezett gyakorisággal és öt teszthellyel a helyén átlagosan percenként egy teszt történik.|
|**Tesztelési helyszínek**| Azok a helyek, ahonnan a kiszolgálók webes kéréseket küldenek az URL-címre. Az **ajánlott tesztelési helyszínek minimális száma öt** annak biztosítására, hogy a hálózati problémák alapján megkülönböztetni tudja a webhelyén felmerülő problémákat. Legfeljebb 16 hely választható ki.

**Ha az URL-cím nem látható a nyilvános internetről, dönthet úgy, hogy szelektíven megnyitja a tűzfalat, hogy csak a tesztelési tranzakciókat engedélyezze**. Ha többet szeretne megtudni a rendelkezésre állási tesztelési ügynökökkel kapcsolatos tűzfal-kivételekről, tekintse meg az [IP-cím útmutatóját](./ip-addresses.md#availability-tests).

> [!NOTE]
> Javasoljuk, hogy több helyről, **legalább öt hellyel** tesztelje a tesztelést. Ennek célja, hogy megakadályozza a hamis riasztásokat, amelyek egy adott hellyel kapcsolatos átmeneti problémákhoz vezethetnek. Emellett azt is találtuk, hogy az optimális konfiguráció a **teszt helyeinek száma a riasztási hely küszöbértéke + 2**.

### <a name="success-criteria"></a>Sikerességi feltételek

|Beállítás| Magyarázat
|----|----|----|
| **Teszt időtúllépése** |Csökkentse ezt az értéket, hogy figyelmeztessen a lassú válaszokra. A teszt akkor sikertelen, ha nem érkeznek meg a webhely válaszai ezen az időtartamon belül. Ha bejelölte a **Függő kérelmek elemzése** lehetőséget, akkor az összes képnek, stílusfájlnak, szkriptnek és más függő erőforrásnak meg kell érkeznie ezen az időn belül.|
| **HTTP-válasz** | A visszaadott állapotkód, amely sikeresnek számít. A 200-as kód jelzi, hogy normál weblap lett visszaküldve.|
| **Tartalom egyezése** | Egy karakterlánc, például "Welcome!" Teszteljük, hogy minden válaszban előfordul-e a kis- és nagybetűket figyelembe véve is pontos egyezés. Egyszerű sztringnek kell lennie helyettesítő karakterek nélkül. Ne feledje, hogy ha a laptartalom megváltozik, lehet, hogy ezt is frissíteni kell. **A tartalmi egyezés csak az angol karaktereket támogatja** |

### <a name="alerts"></a>Riasztások

|Beállítás| Magyarázat
|----|----|----|
|**Közel valós idejű (előzetes verzió)** | Javasoljuk, hogy a közel valós idejű riasztásokat használja. Az ilyen típusú riasztások konfigurálása a rendelkezésre állási teszt létrehozása után történik.  |
|**Riasztási hely küszöbértéke**|Legalább 3/5 helyet ajánlunk. A riasztási hely küszöbértéke és a tesztelési helyek száma közötti optimális kapcsolat a **riasztási hely küszöbértéke**  =  **– 2, legalább öt tesztelési hely.**|

### <a name="location-population-tags"></a>Hely populációjának címkéi

A következő populációs címkék használhatók a Geo-Location attribútumhoz a rendelkezésre állási URL pingelési tesztek Azure Resource Manager használatával történő telepítésekor.

#### <a name="azure-gov"></a>Azure gov

| Megjelenítendő név   | Populáció neve     |
|----------------|---------------------|
| USGov Virginia | USA Korm.-va-azr        |
| USGov Arizona  | USA Korm.-PHX-azr       |
| USGov Texas    | USA Korm.-TX-azr        |
| USA keleti régiója     | USA Korm.-ddeast-azr    |
| USA középső régiója  | USA Korm.-ddcentral-azr |

#### <a name="azure"></a>Azure

| Megjelenítendő név                           | Populáció neve   |
|----------------------------------------|-------------------|
| Kelet-Ausztrália                         | EMEA-au-Syd-Edge  |
| Dél-Brazília                           | LATAM-br-GRU-Edge |
| Az USA középső régiója                             | US-FL-Mia-Edge    |
| Kelet-Ázsia                              | APAC-HK-hkn-azr   |
| USA keleti régiója                                | USA-VA-Ash-azr     |
| Dél-Franciaország (korábban Franciaország középső régiója) | EMEA – CH-ZRH – Edge  |
| Közép-Franciaország                         | EMEA-fr-PRA-Edge  |
| Kelet-Japán                             | APAC-JP-KAW-Edge  |
| Észak-Európa                           | EMEA-GB-db3-azr   |
| USA északi középső régiója                       | US-Il-CH1-azr     |
| USA déli középső régiója                       | USA-TX-SN1-azr     |
| Délkelet-Ázsia                         | APAC-SG-Sin-azr   |
| Az Egyesült Királyság nyugati régiója                                | EMEA-se-Sto-Edge  |
| Nyugat-Európa                            | EMEA-nl-AMS-azr   |
| USA nyugati régiója                                | USA – CA-SJC – azr     |
| Az Egyesült Királyság déli régiója                               | EMEA-ru-MSA-Edge  |

## <a name="see-your-availability-test-results"></a>A rendelkezésre állási teszt eredményeinek megtekintése

A rendelkezésre állási teszt eredményei a line és a scatter ábrázolási nézetekkel is megjeleníthetők.

Néhány perc elteltével kattintson a **frissítés** gombra a tesztek eredményeinek megtekintéséhez.

![Képernyőfelvétel: a rendelkezésre állási oldal, amelyben a frissítés gomb ki van emelve.](./media/monitor-web-app-availability/availability-refresh-002.png)

A scatterplot nézet a tesztelési eredmények olyan mintáit jeleníti meg, amelyek diagnosztikai tesztelési lépésekkel kapcsolatos részletességgel rendelkeznek. A tesztmotor tárolja a hibákat tartalmazó tesztek diagnosztikai adatait. A sikeres tesztek esetében a végrehajtások részhalmazainak diagnosztikai adatait is tárolja. A teszt, a teszt neve és a hely megjelenítéséhez vigye a kurzort a zöld/piros pontok bármelyikére.

![Vonal nézet](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Ha további adatokat szeretne látni egy konkrét időszakról, válassza ki a megfelelő tesztet vagy helyet, illetve állítsa rövidebbre az időszakot. A Keresési ablak használatával megtekintheti az összes végrehajtás eredményét, vagy az Analytics lekérdezéseinek használatával egyéni jelentéseket hozhat létre ezen adatok alapján.

## <a name="inspect-and-edit-tests"></a> Tesztek megtekintése és szerkesztése

A tesztek szerkesztéséhez, ideiglenes letiltásához vagy törléséhez kattintson a teszt neve melletti három pontra. A változtatások végrehajtása után akár 20 percet is igénybe vehet, amíg a konfigurációs módosítások propagálják az összes tesztelési ügynököt.

![A teszt részleteinek megtekintése. Webes tesztek szerkesztése és letiltása](./media/monitor-web-app-availability/edit.png)

Amikor karbantartást végez a szolgáltatáson, célszerű kikapcsolni a rendelkezésre állási teszteket és az azokhoz kapcsolódó riasztási szabályokat.

## <a name="if-you-see-failures"></a>Ha hibákat lát

Kattintson egy piros pontra.

![Kattintson egy piros pontra](./media/monitor-web-app-availability/open-instance-3.png)

A rendelkezésre állási teszt eredményét követve megtekintheti a tranzakció részleteit az összes összetevőben. Itt a következőket teheti:

* Megvizsgálhatja a kiszolgálótól érkezett választ.
* A sikertelen rendelkezésre állási teszt feldolgozása során gyűjtött, korrelált kiszolgálóoldali telemetria hibáinak diagnosztizálása.
* A probléma nyomon követéséhez naplózhat egy problémát vagy munkaelemet a git-vagy az Azure-táblákban. A hiba tartalmazni fog egy hivatkozást erre az eseményre.
* Megnyithatja a webes teszt eredményét a Visual Studióban.

További információ a végpontok közötti tranzakciós [diagnosztikai élményről](./transaction-diagnostics.md).

Kattintson a kivétel sorra, és tekintse meg a szintetikus rendelkezésre állási teszt sikertelenségét okozó kiszolgálóoldali kivétel részleteit. Emellett a [hibakeresési pillanatképet](./snapshot-debugger.md) is megtekintheti a gazdagabb kódok szintjének diagnosztizálásához.

![Kiszolgálóoldali diagnosztika](./media/monitor-web-app-availability/open-instance-4.png)

A nyers eredmények mellett két fő rendelkezésre állási metrikát is megtekintheti [Metrikaböngészőban](../essentials/metrics-getting-started.md):

1. Rendelkezésre állás: Az összes végrehajtott teszt közül a sikeresen végrehajtott tesztek százalékos aránya.
2. Tesztek időtartama: A tesztek átlagos időtartama az összes végrehajtás alapján.

## <a name="automation"></a>Automation

* [Használjon PowerShell-szkripteket a rendelkezésre állási teszt automatikus beállításához](./powershell.md#add-an-availability-test).
* Állítson be egy [webhookot](../alerts/alerts-webhooks.md), amelyet a rendszer riasztás esetén hív meg.

## <a name="troubleshooting"></a>Hibaelhárítás

Dedikált [hibaelhárítási cikk](troubleshoot-availability.md).

## <a name="next-steps"></a>Következő lépések

* [Rendelkezésre állási riasztások](availability-alerts.md)
* [Többlépéses webes tesztek](availability-multistep.md)


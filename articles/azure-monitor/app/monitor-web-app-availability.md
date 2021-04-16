---
title: Bármely webhely rendelkezésre állásának és válaszkészségének figyelése – Azure Monitor
description: Állítson be pingelési teszteket a Application Insights. Riasztásokat kaphat, ha egy webhely elérhetetlenné válik vagy lassan válaszol.
ms.topic: conceptual
ms.date: 04/15/2021
ms.reviewer: sdash
ms.openlocfilehash: ecfd4ffee3582ff37411e59c75d8be8fca5e945f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516622"
---
# <a name="monitor-the-availability-of-any-website"></a>Webhelyek rendelkezésre állásának monitorozása

Az "URL ping teszt" név kissé téves. Hogy egyértelmű legyen, ezek a tesztek nem használják az ICMP-t (Internet Control Message Protocol) a webhely rendelkezésre állásának ellenőrzésére. Ehelyett fejlettebb HTTP-kérési funkciókat használnak annak ellenőrzéséhez, hogy egy végpont válaszol-e. Emellett mérik a válaszhoz társított teljesítményt, és lehetővé teszik egyéni sikeresség feltételeinek beállítását olyan speciális funkciókkal együtt, mint a függő kérések elemezését és az újrakérések engedélyezését.

Kétféle URL-pingtesztet hozhat létre: alapszintű és szabványos ping teszteket.

> [!NOTE]
> Az alapszintű és a standard ping tesztek jelenleg nyilvános előzetes verzióban állnak rendelkezésre. Ezek az előzetes verziók szolgáltatói szerződés nélkül biztosítanak. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.

Alapszintű és Standard:

- Az Alapszintű tesztenként öt helyre van korlátozva.
- A standard tesztek egyéni fejlécekkel vagy kérelemtitkokkal is létrehozhatnak.
- A standard tesztek bármilyen HTTP-kérési módszert használhatnak, míg az alapszintűek csak a `GET` metódust.
- Az SSL-tanúsítvány élettartam-ellenőrzése riasztást küld a tanúsítvány lejárata előtti meghatározott időtartamról.
- A standard tesztek fizetős funkciók.

> [!NOTE]
> A standard ping tesztek előzetes verziójú funkciója jelenleg nem számít fel további díjakat. Az előzetes verzióban elérhető funkciók díjszabását a jövőben, a számlázás megkezdése előtt pedig egy értesítésben fogjuk jelenteni. Ha úgy dönt, hogy a figyelmeztetési időszak után is folytatja a standard ping tesztek használatának folytatását, a megfelelő díjakat kell számláznunk.

## <a name="create-a-url-ping-test"></a>URL-ping teszt létrehozása

Rendelkezésre állási teszt létrehozásához egy meglévő Application Insight-erőforrást kell használnia, vagy létre kell hoznia [egy Application Insights erőforrást.](create-new-resource.md)

Az első rendelkezésre állási kérés létrehozásához nyissa meg a Rendelkezésre állás panelt, és válassza a Teszt létrehozása & a teszt termékváltozatot.

:::image type="content" source="./media/monitor-web-app-availability/create-basic-test.png" alt-text="Képernyőkép egy alapszintű URL-ping teszt létrehozásáról az Azure Portalon":::

|Beállítás | Magyarázat |
|--------|-------------|
|**URL-cím** |  Az URL-cím bármilyen tesztelni kívánt weblap lehet, de láthatónak kell lennie a nyilvános internetről. Az URL-cím tartalmazhat lekérdezési sztringet. Tehát például kísérletezhet egy kicsit az adatbázissal. Ha az URL feloldása egy átirányítást eredményez, legfeljebb 10 átirányításig követjük.|
|**Függő kérések elemezve**| Tesztelés: lekért képek, szkriptek, stílusfájlok és egyéb fájlok, amelyek a weblap tesztelése alatt állnak. A rögzített válaszidőbe a fájlok lekérése is beleszámít. A teszt meghiúsul, ha ezen erőforrások bármelyike nem tölthető le sikeresen a teljes teszt időkorlátján belül. Ha a beállítás nincs bejelölve, a teszt csak a fájlt és a megadott URL-címet kéri le. Ennek a beállításnak az engedélyezése szigorúbb ellenőrzést ad vissza. A teszt sikertelen lehet olyan esetekben, amelyek a webhely manuális böngészése során nem észrevehetők. |
|**Újrakikerek engedélyezése**| Ha a teszt sikertelen, a rendszer rövid idő után újraküldi. Csak akkor jelent hibát, ha három egymást követő kísérlet meghiúsul. Ezután a rendszer a teszteket a szokásos tesztelési gyakorisággal végzi el. Az újrapróbálkozás ideiglenesen fel van függesztve a következő sikeres műveletig. Ez a szabály függetlenül van alkalmazva minden egyes teszthelyen. **Ezt a lehetőséget javasoljuk.** Átlagosan körülbelül a hibák 80%-a megszűnik az újrapróbálkozáskor.|
| **SSL-tanúsítványérvényesítési teszt** | A webhelyén ellenőrizheti az SSL-tanúsítványt, hogy meggyőződjön arról, hogy az megfelelően van telepítve, érvényes és megbízható, és nem ad hibaüzenetet egyik felhasználónak sem. |
| **Proaktív élettartam-ellenőrzés** | Ez lehetővé teszi, hogy meghatározza az SSL-tanúsítvány lejárata előtti időszakot. A lejárat után a teszt sikertelen lesz. |
|**Teszt gyakorisága**| Beállítja, hogy milyen gyakran fusson a teszt az egyes teszthelyről. Öt perces alapértelmezett gyakorisággal és öt teszthellyel a helyén átlagosan percenként egy teszt történik.|
|**Tesztelési helyek**| Ezek azok a helyek, ahonnan a kiszolgálóink webes kéréseket küldenek az Ön URL-címére. **A teszthelyek minimális** száma öt, így biztos lehet abban, hogy a webhely problémáit meg tudja különböztetni a hálózati problémáktól. Ötnél több, standard teszteléssel és legfeljebb 16 helyről is kiválaszthat helyeket.|

**Ha az URL-cím nem látható a** nyilvános internetről, dönthet úgy, hogy szelektíven megnyitja a tűzfalat, hogy csak a teszttranzakciók legyenek átvéve. Ha többet szeretne megtudni a rendelkezésre állási tesztügynökök tűzfal-kivételeiről, tekintse meg az [IP-cím útmutatóját.](./ip-addresses.md#availability-tests)

> [!NOTE]
> Határozottan javasoljuk, hogy a tesztelést több helyről, **legalább öt helyről javasoljuk.** Ez megakadályozza az olyan téves riasztásokat, amelyek egy adott helyről eredő átmeneti problémákat okozhatnak. Emellett azt is kiderült, hogy az optimális konfiguráció szerint a teszthelyek száma megegyezik a riasztás helyének **küszöbértékével + 2.**

## <a name="standard-test"></a>Standard teszt

:::image type="content" source="./media/monitor-web-app-availability/standard-test-post.png" alt-text="Képernyőkép a standard tesztadatok lapról." border="false":::

|Beállítás | Magyarázat |
|--------|-------------|
| **Egyéni fejlécek** | Az üzemeltetési paramétereket meghatározó kulcs-érték párok. |
| **HTTP-kérési művelet** | Jelezze, hogy milyen műveletet szeretne a kéréssel együtt. Ha a választott ige nem érhető el a felhasználói felületen, az Azure erőforrás-figyelő a kívánt módon üzembe helyezhet egy standard tesztet. |
| **Kérelem törzse** | A HTTP-kéréshez társított egyéni adatok. Feltölthet saját típusú fájlokat a tartalomba, vagy letilthatja ezt a funkciót. A nyers törzstartalmakhoz a TEXT, A JSON, a HTML, az XML és a JavaScript támogatott. |

## <a name="success-criteria"></a>Sikeresség feltételei

|Beállítás| Magyarázat
|----|----|----|
| **Teszt időkorlátja** |Ha csökkenti ezt az értéket, a rendszer riasztást küld a lassú válaszokról. A teszt akkor sikertelen, ha nem érkeznek meg a webhely válaszai ezen az időtartamon belül. Ha bejelölte a **Függő kérelmek elemzése** lehetőséget, akkor az összes képnek, stílusfájlnak, szkriptnek és más függő erőforrásnak meg kell érkeznie ezen az időn belül.|
| **HTTP-válasz** | A visszaadott állapotkód, amely sikeresnek számít. A 200-as kód jelzi, hogy normál weblap lett visszaküldve.|
| **Tartalom egyezése** | Egy sztring, például "Üdvözöljük!" Teszteljük, hogy minden válaszban előfordul-e a kis- és nagybetűket figyelembe véve is pontos egyezés. Egyszerű sztringnek kell lennie helyettesítő karakterek nélkül. Ne feledje, hogy ha a laptartalom megváltozik, lehet, hogy ezt is frissíteni kell. **A tartalom egyezése csak az angol karaktereket támogatja** |

## <a name="alerts"></a>Riasztások

|Beállítás| Magyarázat
|----|----|----|
|**Közel valós idejű (előzetes verzió)** | Javasoljuk a közel valós idejű riasztások használatát. Az ilyen típusú riasztások konfigurálása a rendelkezésre állási teszt létrehozása után történik.  |
|**Riasztás helyének küszöbértéke**|Javasoljuk, hogy legalább 3/5 helyet javasoljon. A riasztási hely küszöbértéke és a teszthelyek száma közötti optimális kapcsolat a riasztás helyének küszöbértéke  =  **– 2, legalább öt teszthely esetén.**|

## <a name="location-population-tags"></a>Hely népességcímkéi

Az alábbi populációcímkék használhatók a földrajzihely-attribútumhoz, amikor rendelkezésre állási URL-ping tesztet helyez üzembe a Azure Resource Manager.

#### <a name="azure-gov"></a>Azure Gov

| Megjelenítendő név   | Népesség neve     |
|----------------|---------------------|
| USGov Virginia | usgov-va-azr        |
| USGov Arizona  | usgov-phx-azr       |
| USGov Texas    | usgov-tx-azr        |
| USDoD – keleti régió     | usgov-ddeast-azr    |
| USDoD – középső  | usgov-ddcentral-azr |

#### <a name="azure"></a>Azure

| Megjelenítendő név                           | Népesség neve   |
|----------------------------------------|-------------------|
| Kelet-Ausztrália                         | emea-au-syd-edge  |
| Dél-Brazília                           | latam-br-gru-edge |
| Az USA középső régiója                             | us-fl-mia-edge    |
| Kelet-Ázsia                              | apac-hk-hkn-azr   |
| USA keleti régiója                                | us-va-ox-azr     |
| Dél-Franciaország (korábban Közép-Franciaország) | emea-ch-zrh-edge  |
| Közép-Franciaország                         | emea-fr-pra-edge  |
| Kelet-Japán                             | apac-jp-kaw-edge  |
| Észak-Európa                           | emea-gb-db3-azr   |
| USA északi középső régiója                       | us-il-ch1-azr     |
| USA déli középső régiója                       | us-tx-sn1-azr     |
| Délkelet-Ázsia                         | apac-sg-sin-azr   |
| Az Egyesült Királyság nyugati régiója                                | emea-se-sto-edge  |
| Nyugat-Európa                            | emea-nl-ams-azr   |
| USA nyugati régiója                                | us-ca-sjc-azr     |
| Az Egyesült Királyság déli régiója                               | emea-ru-msa-edge  |

## <a name="see-your-availability-test-results"></a>A rendelkezésre állási teszt eredményeinek megtekintése

A rendelkezésre állási teszt eredményei vonal- és pontdiagramnézetekkel is ábrázolhatóak.

Néhány perc múlva kattintson a Frissítés **gombra** a teszteredmények eléréséhez.

:::image type="content" source="./media/monitor-web-app-availability/availability-refresh-002.png" alt-text="Képernyőkép a Rendelkezésre állás lapról, kiemelt Frissítés gomb gombbal.":::

A pontdiagram nézet azon teszteredmények mintáit jeleníti meg, amelyekben a diagnosztikai tesztlépés részletei vannak. A tesztmotor tárolja a hibákat tartalmazó tesztek diagnosztikai adatait. A sikeres tesztek esetében a végrehajtások részhalmazainak diagnosztikai adatait is tárolja. Vigye az egérmutatót a zöld/piros pont fölé a teszt, a teszt neve és a hely megjelenítéséhez.

:::image type="content" source="./media/monitor-web-app-availability/availability-scatter-plot-003.png" alt-text="Sor nézet." border="false":::

Ha további adatokat szeretne látni egy konkrét időszakról, válassza ki a megfelelő tesztet vagy helyet, illetve állítsa rövidebbre az időszakot. A Keresési ablak használatával megtekintheti az összes végrehajtás eredményét, vagy az Analytics lekérdezéseinek használatával egyéni jelentéseket hozhat létre ezen adatok alapján.

## <a name="inspect-and-edit-tests"></a> Tesztek megtekintése és szerkesztése

A teszt szerkesztéséhez, ideiglenes letiltásához vagy törléséhez kattintson a teszt neve melletti három pontra. Akár 20 percig is eltarthat, hogy a konfigurációs módosítások a módosítás után az összes tesztügynökre propagálva legyen.

:::image type="content" source="./media/monitor-web-app-availability/edit.png" alt-text="Tekintse meg a teszt részleteit. Webes teszt szerkesztése és letiltása." border="false":::

Amikor karbantartást végez a szolgáltatáson, célszerű kikapcsolni a rendelkezésre állási teszteket és az azokhoz kapcsolódó riasztási szabályokat.

## <a name="if-you-see-failures"></a>Ha hibákat lát

Válasszon egy piros gombot.

:::image type="content" source="./media/monitor-web-app-availability/end-to-end.png" alt-text="Képernyőkép a végpontok között lekért tranzakció részletei lapról." border="false":::

A rendelkezésre állási teszt eredményeiből láthatja az összes összetevő tranzakciós adatait. Itt a következőt használhatja:

* Tekintse át a hibaelhárítási jelentést, és állapítsa meg, mi okozhatta a teszt sikertelen volt, de az alkalmazás továbbra is elérhető.
* Megvizsgálhatja a kiszolgálótól érkezett választ.
* Diagnosztizálja a hibát a meghiúsult rendelkezésre állási teszt feldolgozása során gyűjtött, korrelált kiszolgálóoldali telemetriával.
* A probléma nyomon követéséhez naplóz egy problémát vagy munkaelemet a Gitben vagy az Azure Boardsban. A hiba tartalmazni fog egy hivatkozást erre az eseményre.
* Megnyithatja a webes teszt eredményét a Visual Studióban.

A teljes tranzakciódiagnosztikai élményről a tranzakciódiagnosztika dokumentációjában [talál további információt.](./transaction-diagnostics.md)

Kattintson a kivétel sorára annak a kiszolgálóoldali kivételnek a részleteiért, amely a szintetikus rendelkezésre állási teszt sikertelenségét okozta. A hibakeresési [pillanatképet gazdagabb](./snapshot-debugger.md) kódszintű diagnosztikákhoz is lekérte.

:::image type="content" source="./media/monitor-web-app-availability/open-instance-4.png" alt-text="Kiszolgálóoldali diagnosztika.":::

A nyers eredmények mellett két fő rendelkezésre állási metrikát is megtekinthet a [Metrikaböngésző:](../essentials/metrics-getting-started.md)

1. Rendelkezésre állás: Az összes végrehajtott teszt közül a sikeresen végrehajtott tesztek százalékos aránya.
2. Tesztek időtartama: A tesztek átlagos időtartama az összes végrehajtás alapján.

## <a name="automation"></a>Automation

* [Használjon PowerShell-szkripteket a rendelkezésre állási teszt automatikus beállításához](./powershell.md#add-an-availability-test).
* Állítson be egy [webhookot](../alerts/alerts-webhooks.md), amelyet a rendszer riasztás esetén hív meg.


## <a name="next-steps"></a>Következő lépések

* [Rendelkezésre állási riasztások](availability-alerts.md)
* [Többlépéses webes tesztek](availability-multistep.md)
* [Hibaelhárítás](troubleshoot-availability.md)

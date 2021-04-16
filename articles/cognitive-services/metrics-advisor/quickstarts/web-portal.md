---
title: 'Rövid útmutató: Metrics Advisor portál használata'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan kezdheti el használni Metrics Advisor webes portált.
services: cognitive-services
author: mrbullwinkle
ms.author: mbullwin
manager: nitinme
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.custom:
- mode-portal
ms.openlocfilehash: aaeaf92df140019db32c326199c9614fd8f5ae37
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531810"
---
# <a name="quickstart-monitor-your-first-metric-using-the-web-portal"></a>Rövid útmutató: Az első metrika figyelése a webportál használatával

Egy új Metrics Advisor kiépítésekor az API-k és a webalapú munkaterület használatával dolgozhat a szolgáltatással. A webalapú munkaterület egyszerűen használható a szolgáltatás gyors elkezdése érdekében. Vizuálisan is lehetővé teszi a beállítások konfigurálását, a modell testreszabását és a kiváltó ok elemzését. 

* A metrikaadatokboard
* Metrikák és vizualizációk megtekintése
* Észlelési konfigurációk finomhangolása
* Ismerkedés a diagnosztikai elemzésekkel
* Anomáliariasztás létrehozása és feliratkozás a riasztásra

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services)
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy Metrics Advisor-erőforrást, és hozzon létre egy Metrics Advisor-erőforrást a Azure Portal a <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" "  target="_blank"> Metrics Advisor üzembe </a> helyezéséhez.  

    
> [!TIP]
> * Az erőforrás üzembe helyezése 10–30 Metrics Advisor is eltarthat. A **sikeres üzembe helyezés után kattintson** az Erőforrás ugrás gombra.
> * Ha a szolgáltatáshoz a REST API szeretné használni, szüksége lesz a létrehozott erőforrás kulcsára és végpontjára. Ezeket a létrehozott erőforrás Kulcsok  **és végpontok** lapján találhatja meg.

Ez a dokumentum SQL Database az első figyelő létrehozásához.

## <a name="sign-in-to-your-workspace"></a>Bejelentkezés a munkaterületre

Az erőforrás létrehozása után jelentkezzen be a Metrics Advisor [portálra.](https://go.microsoft.com/fwlink/?linkid=2143774) Válassza ki a munkaterületet a metrikák monitorozásának elkezdése érdekében. 
 
Jelenleg minden elérhető régióban Metrics Advisor egy erőforrást. A portálon bármikor válthat Metrics Advisor munkaterületek között.


## <a name="onboard-time-series-data"></a>Idősorozat-adatok be- és berekesze

Metrics Advisor összekötőket biztosít a különböző adatforrások, például a SQL Database, Azure Data Explorer és az Azure Table Storage. Az adatok csatlakoztatásának lépései hasonlóak a különböző összekötőkhöz, bár egyes konfigurációs paraméterek eltérőek lehetnek. Az [adatok különböző forrásokból való csatlakoztatását](../data-feeds-from-different-sources.md) és az egyes adatforrások kötelező paramétereit lásd: Csatlakozás különböző forrásokból.

Ez a rövid útmutató SQL Database egy példát. Saját adatokat is be is tudni, ugyanezeket a lépéseket kell követnie.

Első lépésekként jelentkezzen be Metrics Advisor munkaterületére a Active Directory fiókjával. A kezdőlapon válassza ki az  előbb létrehozott **címtárat,**  előfizetést és munkaterületet, majd kattintson az Első lépések **elemre.** A számítási feladat főoldalának betöltése után válassza a bal oldali menü **Adatcsatorna** hozzáadása parancsát.

### <a name="data-schema-requirements-and-configuration"></a>Adatsémára vonatkozó követelmények és konfiguráció

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings"></a>Kapcsolati beállítások konfigurálása

> [!TIP]
> Az [elérhető paraméterekkel kapcsolatos](../how-tos/onboard-your-data.md) részletekért tekintse meg, hogyan adhat hozzá adatcsatornákat.

Adja hozzá az adatcsatornát az idősorozat-adatforráshoz való csatlakozással. Először válassza ki a következő paramétereket:

* **Forrás típusa:** Az idősorozat-adatokat tároló adatforrás típusa.
* **Részletesség:** Az idősorozat-adatok egymást követő adatpontok közötti intervallum, például Éves, Havi, Napi. A legalacsonyabb támogatott időköz 60 másodperc.
* **Adatok beszámítása (UTC) óta:** Az első időbélyeg beszámításának kezdési ideje. 


Ezután adja meg a **Kapcsolati sztringet** az adatforrás hitelesítő adataival, valamint egy egyéni **lekérdezést.** A lekérdezés a betöltött adatok megadására, majd a szükséges sémává konvertálására használható.

[!INCLUDE [query requirements](../includes/query-requirements.md)]

:::image type="content" source="../media/connection-settings.png" alt-text="Kapcsolati beállítások" lightbox="../media/connection-settings.png":::


### <a name="verify-the-connection-and-load-the-data-schema"></a>A kapcsolat ellenőrzése és az adatséma betöltése

A kapcsolati sztring és a  lekérdezési sztring létrehozása után válassza az Ellenőrzés és séma lekérdezve lehetőséget a kapcsolat ellenőrzéséhez, majd futtassa a lekérdezést az adatsémát az adatforrásból való lekért lekérdezéshez. Általában néhány másodpercet vesz igénybe az adatforrás-kapcsolattól függően. Ha ebben a lépésben hiba lép fel, ellenőrizze a következőt:

1. A kapcsolati sztring és a lekérdezés helyes.
2. A Metrics Advisor-példány képes csatlakozni az adatforráshoz, ha vannak tűzfalbeállítások.

### <a name="schema-configuration"></a>Sémakonfiguráció

Miután az adatsémát betöltötte és az alábbihoz hasonló módon jelenik meg, válassza ki a megfelelő mezőket.


|Kiválasztás  |Leírás  |Jegyzetek  |
|---------|---------|---------|
|**Időbélyeg**     | Az adatpont időbélyege. Ha nincs megadva, Metrics Advisor a rendszer az időbélyeget használja, amikor az adatpont be van adva. Minden adatcsatornához megadhat legalább egy oszlopot időbélyegként.        | Választható. A értéknek legalább egy oszlopot kell megadnia.       |
|**Measure**     |  Az adatcsatorna numerikus értékei. Minden adatcsatornához több mértéket is megadhat, de legalább egy oszlopot ki kell választani mértékként.        | Legalább egy oszlopot meg kell adni.        |
|**dimenzió**     | Kategorikus értékek. A különböző értékek kombinációja egy adott egydimenziós idősort azonosít, például: ország, nyelv, bérlő. Dimenziókként egyetlen oszlopot vagy tetszőleges számú oszlopot is kiválaszthat. Megjegyzés: ha nem sztringoszlopot választ dimenzióként, legyen óvatos a dimenziórobbanással. | Választható.        |
|**Figyelmen kívül hagyja**     | Hagyja figyelmen kívül a kijelölt oszlopot.        |         |


:::image type="content" source="../media/schema-configuration.png" alt-text="Sémakonfiguráció" lightbox="../media/schema-configuration.png":::

### <a name="automatic-roll-up-settings"></a>Automatikus összesítési beállítások

> [!IMPORTANT]
> Ha engedélyezni szeretné a  kiváltó okokat elemző és egyéb diagnosztikai képességeket, konfigurálnia kell az automatikus összesítési beállítást. Ha engedélyezve van, az automatikus összesítési beállítások nem módosíthatók.

Metrics Advisor a betekintés során automatikusan összesítést (SUM/MAX/MIN...) hajthat végre az egyes dimenziókon, majd felépíthet egy hierarchiát, amelyet a rendszer a gyökéreset-elemzésekben és más diagnosztikai funkciókban fog használni. További [részletekért lásd:](../how-tos/onboard-your-data.md#automatic-roll-up-settings) Automatikus összesítési beállítások.

Adjon egy egyéni nevet az adatcsatorna számára, amely megjelenik a munkaterületen. Kattintson a **Submit (Küldés) gombra.** 

## <a name="tune-detection-configuration"></a>Észlelési konfiguráció hangolása

Az adatcsatorna hozzáadása után a Metrics Advisor megkíséreli a megadott kezdő dátumból származó metrikaadatokat. Az adatok teljes betáplált állapotának megtekintéséhez némi időt kell el kell végeznie, és az adatcsatorna oldalának tetején található **Ingestion progress** (Adatbesorolási folyamat) gombra kattintva tekintheti meg a betáplált adatok állapotát. Ha az adatok be lesznek edzve, Metrics Advisor észlelést alkalmaznak, és továbbra is figyelik az új adatok forrását.

Az észlelés alkalmazásakor kattintson az adatcsatornában felsorolt metrikák egyikére a Metrika **részletei lap megkeresése érdekében, hogy:** 
- A metrika alatt található összes idősorozat-szelet vizualizációinak megtekintése
- Frissítés észlelése a konfigurációban a várt eredményeknek megfelelően
- Értesítés beállítása az észlelt anomáliákhoz

:::image type="content" source="../media/metric-details.png" alt-text="Metrika részletei" lightbox="../media/metric-details.png":::

## <a name="view-the-diagnostic-insights"></a>A diagnosztikai elemzések megtekintése

Az észlelési konfiguráció finomhangolása után a talált anomáliáknak tükrözniük kell az adatok tényleges anomáliákat. Metrics Advisor többdimenziós metrikákon, például anomáliafürtözésen, incidenskorreláción és a kiváltó ok elemzésen végez elemzést. Ezekkel a funkciókkal elemezheti és diagnosztizálhatja az adatokban található incidenseket.

A diagnosztikai elemzések megtekintéséhez kattintson az észlelt anomáliákat ábrázoló idősorozat-vizualizációk piros pontjaira. Megjelenik egy ablak, amely egy incidenselemzési oldalra mutató hivatkozást is ad. 

:::image type="content" source="../media/incident-link.png" alt-text="Incidens hivatkozása" lightbox="../media/incident-link.png":::

Miután a hivatkozásra kattintott, a rendszer az incidenselemzési oldalra fordítja, amely a megfelelő anomáliákat elemzi, és diagnosztikai elemzéseket is biztosít. Felül lesznek az incidensre vonatkozó statisztikák, például a súlyosság, az  érintett anomáliák, az érintett kezdési idő és **a záró időpont.**   

Ezután látni fogja az incidens anomáliákat és az automatizált kiváltó okokat kiváltó tanácsokat. Ez az automatizált kiváltó okra vonatkozó tanács az összes kapcsolódó rendellenesség, például az eltérések, az eloszlás és a szülőanomáliákhoz való hozzájárulás elemzésével jön létre. 

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Incidensdiagnosztika" lightbox="../media/incident-diagnostic.png":::

Ezek alapján már most is egyszerűen megtekintheti, hogy mi történik, milyen hatása van az incidensnek, valamint hogy mi a lehetséges kiváltó ok. Így azonnal meg lehet tenni a szükséges lépéseket az incidens minél hamarabbi megoldása érdekében. 

De további diagnosztikai elemzéseket is eloszthat, amelyek további funkciókat nyújtanak a rendellenességek dimenziónkénti részletezéséhez, a hasonló anomáliák megtekintéséhez és a metrikák összehasonlításához. További információ: How to: diagnose an incident ( Incidens [diagnosztizálása).](../how-tos/diagnose-incident.md) 

## <a name="get-notified-when-new-anomalies-are-found"></a>Értesítést kap, ha új anomáliákat talál

Ha riasztást szeretne kapni, amikor rendellenességet észlel az adatokban, létrehozhat egy előfizetést egy vagy több metrikához. Metrics Advisor a riasztások küldését hookokkal. Háromféle hook támogatott: e-mail hook, web hook Azure DevOps. Példaként a web hook használjuk. 

### <a name="create-a-web-hook"></a>Új web hook

A web hook az anomália belépési pontja, amelyet programozott módon észlel a Metrics Advisor szolgáltatás, amely egy felhasználó által megadott API-t hív meg riasztás esetén. A hookok létrehozásáról az [How-to: Configure alerts and get notifications using a](../how-tos/alerts.md#create-a-hook) **hook** (Hogyan lehet: Riasztások konfigurálása és értesítések leküldése egy hook használatával) című szakaszban talál további információt. 

### <a name="configure-alert-settings"></a>Riasztási beállítások konfigurálása

A hook létrehozása után egy riasztási beállítás határozza meg, hogyan és milyen riasztási értesítéseket kell küldeni. Az egyes metrikákhoz több riasztási beállítást is meg lehet állítani. Két fontos beállítás a **Riasztás,** amely meghatározza a  rendellenességeket, és a Szűrési anomáliabeállítások, amelyek meghatározzák, hogy mely anomáliákat kell a riasztásba foglalni. További **részleteket a** [How-to: Configure alerts and get notifications using a hook (How-to: Configure alerts and get notifications using a hook](../how-tos/alerts.md#add-or-edit-alert-settings) using a hook ) (Hogyan lehet konfigurálni a riasztásokat és értesítéseket egy hook használatával) című szakaszban talál.


## <a name="next-steps"></a>Következő lépések

- [Adatcsatornákba való be- és berekesz](../how-tos/onboard-your-data.md)
    - [Adatcsatornák kezelése](../how-tos/manage-data-feeds.md)
    - [Konfigurációk különböző adatforrások számára](../data-feeds-from-different-sources.md)
- [A REST API vagy ügyfélkódtárak használata](./rest-api-and-client-library.md)
- [Metrikák konfigurálása és a konfiguráció észlelésének finomhangolása](../how-tos/configure-metrics.md)

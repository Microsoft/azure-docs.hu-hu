---
title: 'Rövid útmutató: mérőszámok Advisor webes portál'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan kezdheti el a metrikai tanácsadó webes portál használatát.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/30/2020
ms.author: mbullwin
ms.openlocfilehash: f853dca12b6a5855dc0603259ab534fa9933cd5b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102432298"
---
# <a name="quickstart-monitor-your-first-metric-using-the-web-portal"></a>Rövid útmutató: az első metrika monitorozása a webes portál használatával

Metrikai Advisor-példány kiépítésekor az API-k és a webalapú munkaterület használatával dolgozhat a szolgáltatással. A webalapú munkaterület egyszerűen használható a szolgáltatás használatának gyors megkezdéséhez. Emellett vizualizációs módszert is biztosít a beállítások konfigurálásához, a modell testreszabásához és a kiváltó okok elemzéséhez. 

* A metrikus adatok előkészítése
* A metrikák és a vizualizációk megtekintése
* Észlelési konfigurációk finomhangolása
* A diagnosztikai ismeretek megismerése
* Anomália-riasztások létrehozása és előfizetése

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" hozzon létre egy mérőszámok Advisor "  target="_blank"> -erőforrást </a> a Azure Portal a metrikai tanácsadó példány üzembe helyezéséhez.  

    
> [!TIP]
> * A metrikai tanácsadói erőforrás üzembe helyezése 10 – 30 percet is igénybe vehet. A sikeres üzembe helyezése után kattintson **az erőforrás keresése** gombra.
> * Ha a REST APIt szeretné használni a szolgáltatással való kommunikációhoz, szüksége lesz a létrehozott erőforrás kulcsára és végpontra. Ezeket a létrehozott erőforrás  **kulcsok és végpontok** lapján tekintheti meg.

Ez a dokumentum egy SQL Database példát használ az első figyelő létrehozásához.

## <a name="sign-in-to-your-workspace"></a>Bejelentkezés a munkaterületre

Miután létrehozta az erőforrást, jelentkezzen be a [metrika Advisor portálra](https://go.microsoft.com/fwlink/?linkid=2143774). Válassza ki a munkaterületet a metrikák figyelésének megkezdéséhez. 
 
Jelenleg egy metrikai Advisor-erőforrást is létrehozhat az egyes elérhető régiókban. A metrikák tanácsadó portálon bármikor válthat munkaterületeket.


## <a name="onboard-time-series-data"></a>Előkészítési idősoros adatsorok

A metrikák tanácsadója különböző adatforrások, például a SQL Database, az Azure Adatkezelő és az Azure Table Storage számára biztosít összekötőket. Az adatok összekapcsolásának lépései hasonlóak a különböző összekötők esetében, bár egyes konfigurációs paraméterek eltérőek lehetnek. Az egyes adatforrásokhoz szükséges paraméterekhez a [különböző forrásokból származó adatok összekapcsolását](../data-feeds-from-different-sources.md) ismertető témakörben talál további információt.

Ez a rövid útmutató egy SQL Database példát használ. Saját adatait is betöltheti, ugyanezeket a lépéseket követve.

Első lépésként jelentkezzen be a metrikák tanácsadó munkaterületére a Active Directory-fiókjával. A kezdőlapon válassza ki az imént létrehozott **címtárat**, **előfizetést** és **munkaterületet** , majd kattintson az első **lépések** elemre. A munkaterhelés betöltésének főoldalát követően válassza az **adatcsatorna hozzáadása** lehetőséget a bal oldali menüben.

### <a name="data-schema-requirements-and-configuration"></a>Az Adatséma követelményei és konfigurációja

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings"></a>Kapcsolatbeállítások konfigurálása

> [!TIP]
> A rendelkezésre álló paraméterek részleteiért lásd: [adatcsatornák hozzáadása](../how-tos/onboard-your-data.md) .

Az adatcsatorna hozzáadásához csatlakozzon az idősoros adatforráshoz. Először válassza ki a következő paramétereket:

* **Forrás típusa**: az adatforrások típusa, ahol az idősorozat-adatait tárolják.
* **Részletesség**: az idősoros adatokat követő adatpontok közötti intervallum, például évente, havonta, naponta. A legalacsonyabb intervallum testreszabása 60 másodperc.
* Az **adatgyűjtés óta (UTC)**: az első időbélyeg betöltésének kezdési időpontja. 


Ezután adja **meg az adatforrás** hitelesítő adatait, valamint egy egyéni **lekérdezést**. A lekérdezés segítségével megadhatja a betöltendő és a szükséges sémába konvertált adatot.

[!INCLUDE [query requirements](../includes/query-requirements.md)]

:::image type="content" source="../media/connection-settings.png" alt-text="Kapcsolati beállítások" lightbox="../media/connection-settings.png":::


### <a name="verify-the-connection-and-load-the-data-schema"></a>A kapcsolatok ellenőrzése és az Adatséma betöltése

A kapcsolódási karakterlánc és a lekérdezési karakterlánc létrehozása után válassza a **hitelesítés és a séma lekérése** lehetőséget a kapcsolódás ellenőrzéséhez és a lekérdezés futtatásához, hogy az adatforrásból beolvassa az adatsémát. Általában eltarthat néhány másodpercig az adatforrás-kapcsolatoktól függően. Ha hiba lép fel ebben a lépésben, győződjön meg arról, hogy:

1. A kapcsolatok karakterlánca és a lekérdezés helyes.
2. A metrikai tanácsadó példánya képes csatlakozni az adatforráshoz, ha vannak tűzfalbeállítások.

### <a name="schema-configuration"></a>Séma konfigurációja

Ha az Adatséma betöltődik, és az alábbi módon jelenik meg, válassza ki a megfelelő mezőket.


|Kiválasztás  |Leírás  |Jegyzetek  |
|---------|---------|---------|
|**Időbélyeg**     | Egy adatpont időbélyege Ha nincs megadva, a metrikák tanácsadója az adatpont betöltésének időbélyegét fogja használni. Minden adatcsatorna esetében legfeljebb egy oszlopot adhat meg timestamp típusúként.        | Választható. Legfeljebb egy oszlopnak kell megadnia.       |
|**Measure**     |  Az adatcsatorna numerikus értékei. Minden adatcsatorna esetében több mértéket is megadhat, de legalább egy oszlopot ki kell jelölni mértékként.        | Legalább egy oszloppal kell megadni.        |
|**dimenzió**     | Kategorikus értékek. A különböző értékek kombinációja egy adott egydimenziós idősorozatot azonosít, például: ország, nyelv, bérlő. A none vagy tetszőleges számú oszlopot kiválaszthatja dimenzióként. Megjegyzés: Ha nem karakterlánc típusú oszlopot választ dimenzióként, a dimenzió alábontása legyen óvatos. | Választható.        |
|**Figyelmen kívül**     | A kijelölt oszlop figyelmen kívül hagyása.        |         |


:::image type="content" source="../media/schema-configuration.png" alt-text="Séma konfigurációja" lightbox="../media/schema-configuration.png":::

### <a name="automatic-roll-up-settings"></a>Automatikus összesítő beállítások

> [!IMPORTANT]
> Ha engedélyezni szeretné a **kiváltó okok elemzését** és az egyéb diagnosztikai képességeket, konfigurálnia kell az "automatikus összesítő beállítás" beállítást. Ha engedélyezve van, az automatikus összesítő beállítások nem módosíthatók.

A metrikai tanácsadó automatikusan végrehajtja az összesítést (SUM/MAX/MIN...) az egyes dimenziókban a betöltés során, majd létrehoz egy hierarchiát, amelyet a rendszer a legfelső szintű elemzéshez és más diagnosztikai funkciókhoz fog használni. További részletekért tekintse meg az [automatikus összesítő beállítások](../how-tos/onboard-your-data.md#automatic-roll-up-settings) című témakört.

Adja meg az adatcsatorna egyéni nevét, amely a munkaterületen fog megjelenni. Kattintson a **Submit (elküldés**) gombra. 

## <a name="tune-detection-configuration"></a>Észlelési konfiguráció hangolása

Az adatcsatorna hozzáadása után a metrikai tanácsadó megkísérli bevenni a megadott kezdő dátumból származó metrikai adatokat. Időbe telik az adatmennyiség teljes betöltése, és megtekintheti a betöltési állapotot az adatcsatorna oldal tetején lévő betöltési **folyamat** elemre kattintva. Az adatok betöltését követően a metrikák Advisor észleli az észlelést, és folytatja az új adatok forrásának figyelését.

Az észlelés alkalmazása esetén kattintson az adatcsatorna listájában felsorolt mérőszámok egyikére, hogy megkeresse a **metrika részletes oldalát** : 
- A jelen metrika alatt lévő összes idősorozat-szelet vizualizációinak megtekintése
- A konfiguráció észlelésének frissítése a várt eredmények kielégítése érdekében
- Értesítés beállítása az észlelt rendellenességekről

:::image type="content" source="../media/metric-details.png" alt-text="Metrika részletei" lightbox="../media/metric-details.png":::

## <a name="view-the-diagnostic-insights"></a>A diagnosztikai ismeretek megtekintése

Az észlelési konfiguráció finomhangolása után az észlelt rendellenességeknek tükröznie kell az adatok tényleges rendellenességeit. A metrikai tanácsadó a többdimenziós metrikák elemzését végzi el, például a anomália-fürtözést, az incidensek korrelációját és a kiváltó okok elemzését. Ezekkel a szolgáltatásokkal elemezheti és diagnosztizálhatja az adatincidenseket az adataiban.

A diagnosztikai megállapítások megtekintéséhez kattintson az idősorozat-vizualizációk piros pontokra, amelyek az észlelt rendellenességeket jelölik. Ekkor megjelenik egy ablak az incidensek elemzése oldalra mutató hivatkozással. 

:::image type="content" source="../media/incident-link.png" alt-text="Incidens hivatkozása" lightbox="../media/incident-link.png":::

Miután rákattintott a hivatkozásra, a rendszer az incidensek elemzése oldalra mutat, amely a megfelelő anomálián alapul, és a diagnosztikai elemzések egy rakásával foglalkozik. A fentiekben az incidensre vonatkozó statisztikai adatok, például a **Súlyosság**, a rendellenességek és az **érintett** **kezdési** és **befejezési idő** szerepel. 

Ezután látni fogja az incidens őse anomáliát, és automatizálja a kiváltó okokat. Ez az automatizált kiváltó okok az összes kapcsolódó rendellenesség esetében az incidensek fájának elemzésével jönnek létre, többek között a következőkkel: szórás, eloszlás és a szülő anomáliák való hozzájárulás. 

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Incidens-diagnosztika" lightbox="../media/incident-diagnostic.png":::

Ezek alapján már könnyedén megtekintheti, hogy mi történik, és milyen hatással van az incidensre, valamint a legvalószínűbb kiváltó ok. Annak érdekében, hogy a lehető legrövidebb időn belül azonnali művelettel lehessen megoldani az incidenst. 

Emellett a további funkciókat kihasználó diagnosztikai elemzések is felhasználhatók a dimenziók által észlelt rendellenességek részletezésére, a hasonló anomáliák megtekintésére és a metrikák összehasonlítására. További információ [: incidensek diagnosztizálása](../how-tos/diagnose-incident.md). 

## <a name="get-notified-when-new-anomalies-are-found"></a>Értesítést kaphat, ha új anomáliák találhatók

Ha riasztást szeretne kapni, ha az adatokban anomáliát észlel, létrehozhat egy előfizetést egy vagy több mérőszámhoz. A metrikai tanácsadó horgokat használ a riasztások küldéséhez. A hookok háromféle típusa támogatott: e-mail Hook, webhook és Azure DevOps. A web hookot példaként fogjuk használni. 

### <a name="create-a-web-hook"></a>Webes Hook létrehozása

A web Hook az a belépési pont, amellyel a rendszer a metrikai tanácsadó szolgáltatástól egy programozott módon észlelte a anomáliát, amely egy felhasználó által megadott API-t hív meg riasztás indításakor. A hookok létrehozásával kapcsolatos részletekért tekintse meg a **hookok létrehozása** című szakaszt a következő [útmutatóban: riasztások konfigurálása és értesítések beszerzése Hook használatával](../how-tos/alerts.md#create-a-hook). 

### <a name="configure-alert-settings"></a>Riasztási beállítások konfigurálása

A Hook létrehozása után a riasztási beállítások határozzák meg, hogy a rendszer milyen riasztási értesítéseket küldjön. Az egyes mérőszámokhoz több riasztási beállítást is beállíthat. két fontos beállítás a **riasztás** , amely meghatározza a befoglalni kívánt rendellenességeket, és **szűri az anomáliák beállításait** , amelyek meghatározzák, hogy mely rendellenességek szerepeljenek a riasztásban. További részletekért tekintse meg a **riasztási beállítások hozzáadása vagy szerkesztése** című szakaszt a következő témakörben [: útmutató: riasztások konfigurálása és értesítések beszerzése Hook használatával](../how-tos/alerts.md#add-or-edit-alert-settings) .


## <a name="next-steps"></a>Következő lépések

- [Adatcsatornák előkészítése](../how-tos/onboard-your-data.md)
    - [Adatcsatornák kezelése](../how-tos/manage-data-feeds.md)
    - [Különböző adatforrások beállításai](../data-feeds-from-different-sources.md)
- [A REST API vagy az ügyféloldali kódtárak használata](./rest-api-and-client-library.md)
- [Metrikák konfigurálása és a konfiguráció észlelésének finomhangolása](../how-tos/configure-metrics.md)
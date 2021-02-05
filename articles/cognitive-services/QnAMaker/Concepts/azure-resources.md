---
title: Azure-erőforrások – QnA Maker
description: QnA Maker számos Azure-forrást használ, amelyek mindegyike más célra szolgál. A használatuk módjának megismerése lehetővé teszi, hogy megtervezze és kiválassza a megfelelő árképzési szintet, vagy tudja, mikor kell módosítania az árképzési szintet. Annak megismerése, hogy a rendszer hogyan használja a kombinációkat, így megkeresheti és elháríthatja a problémákat, amikor azok bekövetkeznek.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 0864db8a653ff1d6f89ed0b1c857e51053ff50ff
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592603"
---
# <a name="azure-resources-for-qna-maker"></a>Azure-erőforrások a QnA Makerhoz

QnA Maker számos Azure-forrást használ, amelyek mindegyike más célra szolgál. A használatuk módjának megismerése lehetővé teszi, hogy megtervezze és kiválassza a megfelelő árképzési szintet, vagy tudja, mikor kell módosítania az árképzési szintet. Annak megismerése, hogy a rendszer hogyan használja _a kombinációkat_ , így megkeresheti és elháríthatja a problémákat, amikor azok bekövetkeznek.

## <a name="resource-planning"></a>Erőforrás-tervezés

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

Amikor először fejleszt egy QnA Maker tudásbázist, a prototípus fázisban gyakori, hogy egyetlen QnA Maker erőforrással rendelkezzen mind a teszteléshez, mind a gyártáshoz.

A projekt fejlesztési szakaszába való áttéréskor érdemes megfontolnia a következőket:

* Hány nyelvet fog tartani a Tudásbázis-rendszer?
* Hány régióban kell elérhetővé tennie a tudásbázist?
* Hány dokumentumot fog tárolni a rendszer az egyes tartományokban?

Tervezze meg, hogy egyetlen QnA Maker erőforrással rendelkezzen minden olyan tudásbázissal, amelynek a nyelve, ugyanazon a régiója és a tárgy tartományának kombinációja.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

A QnA Maker felügyelt Tudásbázis első fejlesztésekor a prototípus fázisban gyakori, hogy egyetlen QnA Maker felügyelt erőforrással rendelkezzen mind a tesztelési, mind a éles környezetben.

A projekt fejlesztési szakaszába való áttéréskor érdemes megfontolnia a következőket:

* Hány nyelvet fog tartani a Tudásbázis-rendszer?
* Hány régióban kell elérhetővé tennie a tudásbázist?
* Hány dokumentumot fog tárolni a rendszer az egyes tartományokban?

---

## <a name="pricing-tier-considerations"></a>Árképzési szintek szempontjai

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

Általában három paramétert kell figyelembe vennie:

* **A szolgáltatás által igényelt átviteli sebesség**:
    * Az igényeinek megfelelően válassza ki az App Service-hez megfelelő alkalmazáscsomag- [csomagot](https://azure.microsoft.com/pricing/details/app-service/plans/) . Az alkalmazás vertikális fel-és [leskálázásra](../../../app-service/manage-scale-up.md) is használható.
    * Ez az Azure **Cognitive Search** SKU kiválasztását is befolyásolhatja, további részletek [itt](../../../search/search-sku-tier.md)találhatók. Emellett előfordulhat, hogy módosítania kell a Cognitive Search [kapacitást](../../../search/search-capacity-planning.md) a replikákkal.

* A **Tudásbázis mérete és száma**: válassza ki a megfelelő [Azure Search SKU](https://azure.microsoft.com/pricing/details/search/) -t a forgatókönyvhöz. Általában a különböző tulajdonosi tartományok száma alapján döntheti el, hogy hány tudásbázisra van szüksége. Ha a tárgy tartománya (egyetlen nyelv esetében) egy Tudásbázisban kell lennie.

    Az N-1 tudásbázist egy adott rétegben teheti közzé, ahol N a rétegben engedélyezett maximális indexek. Tekintse meg a maximális méretet és a másodpercenként engedélyezett dokumentumok számát is.

    Ha például a réteg 15 engedélyezett indextel rendelkezik, a közzétett Tudásbázisban 14 tudásbázist (1 indexet) tehet közzé. A tizenötödik index a szerzői műveletek és a tesztelés összes tudásbázisához használatos.

* **Dokumentumok száma forrásként**: a QnA Maker felügyeleti szolgáltatás ingyenes SKU-jának a Portálon és az API-kon keresztül kezelhető dokumentumok számát korlátozza (egyenként 1 MB méretűre). A standard SKU nem korlátozza a felügyelhető dokumentumok számát. További részletek [itt](https://aka.ms/qnamaker-pricing)találhatók.

A következő táblázat néhány magas szintű útmutatást nyújt.

|                            | QnA Maker-kezelés | App Service | Azure Cognitive Search | Korlátozások                      |
| -------------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| **Experimentation**        | Ingyenes SKU             | Ingyenes szintű   | Ingyenes szintű    | Legfeljebb 2 Tudásbázis közzététele, 50 MB méretű  |
| **Fejlesztési/tesztelési környezet**   | Standard termékváltozat         | Megosztott      | Alapszintű        | Legfeljebb 14 Tudásbázis közzététele, 2 GB méretű méret    |
| **Éles környezet** | Standard termékváltozat         | Alapszintű       | Standard     | Közzététel akár 49 Tudásbázis, 25 GB méretű |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

Általában három paramétert kell figyelembe vennie:

* **A szolgáltatás által igényelt átviteli sebesség**:
    * QnA Maker felügyelt (előzetes verzió) egy ingyenes szolgáltatás, és az átviteli sebesség jelenleg a felügyeleti API-k és az előrejelzési API-k 10 TPS van korlátozva.
    * Ez az Azure **Cognitive Search** SKU kiválasztását is befolyásolhatja, további részletek [itt](../../../search/search-sku-tier.md)találhatók. Emellett előfordulhat, hogy módosítania kell a Cognitive Search [kapacitást](../../../search/search-capacity-planning.md) a replikákkal.

* A **Tudásbázis mérete és száma**: válassza ki a megfelelő [Azure Search SKU](https://azure.microsoft.com/pricing/details/search/) -t a forgatókönyvhöz. Általában a különböző tulajdonosi tartományok száma alapján döntheti el, hogy hány tudásbázisra van szüksége. Ha a tárgy tartománya (egyetlen nyelv esetében) egy Tudásbázisban kell lennie.

    A QnA Maker felügyelt (előzetes verzió) lehetőséggel beállíthatja, hogy a Tudásbázis egyetlen nyelven vagy több nyelven állítsa be a QnA Maker szolgáltatást. Ezt a lehetőséget akkor teheti meg, ha az első tudásbázist hozza létre a QnA Maker felügyelt (előzetes verzió) szolgáltatásban.

    ![QnA Maker felügyelt (előzetes verzió) – többnyelvű Tudásbázis kiválasztása](../media/concept-plan-your-knowledge-base/qnamaker-v2-select-multilanguage-knowledge-base.png)

    Ha n-1 tudásbázist tesz közzé egy adott szinten, vagy N/2 különböző nyelveken alapuló tudásbázist szeretne közzétenni egy adott szinten, ahol N a rétegben engedélyezett maximális indexek száma. Tekintse meg a maximális méretet és a másodpercenként engedélyezett dokumentumok számát is.

    Ha például a réteg 15 engedélyezett indextel rendelkezik, akkor az azonos nyelvű 14 tudásbázist közzéteheti (egy közzétett Tudásbázis 1 indexe). A tizenötödik index a szerzői műveletek és a tesztelés összes tudásbázisához használatos. Ha úgy dönt, hogy különböző nyelveken rendelkezik tudásbázisokkal, akkor csak 7 tudásbázist tehet közzé.

* A **dokumentumok száma forrásként**: QnA Maker felügyelt (előzetes verzió) egy ingyenes szolgáltatás, amely nem korlátozza a forrásként felvehető dokumentumok számát. További részletek [itt](https://aka.ms/qnamaker-pricing)találhatók.

A következő táblázat néhány magas szintű útmutatást nyújt.

|                            |Azure Cognitive Search | Korlátozások                      |
| -------------------------- |------------ | -------------------------------- |
| **Experimentation**        |Ingyenes szintű    | Legfeljebb 2 Tudásbázis közzététele, 50 MB méretű  |
| **Fejlesztési/tesztelési környezet**   |Alapszintű        | Legfeljebb 14 Tudásbázis közzététele, 2 GB méretű méret    |
| **Éles környezet** |Standard     | Közzététel akár 49 Tudásbázis, 25 GB méretű |

---

## <a name="recommended-settings"></a>Ajánlott beállítások

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

|Cél QPS | App Service | Azure Cognitive Search |
| -------------------- | ----------- | ------------ |
| 3             | S1, 1 replika   | S1, 1 replika    |
| 50         | S3, 10 replika       | S1, 12 replika         |
| 80         | S3, 10 replika      |  S3, 12 replika  |
| 100         | P3V2, 10 replika  | S3, 12 replika, 3 partíció   |
| 200 – 250         | P3V2, 20 replika | S3, 12 replika, 3 partíció    |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

QnA Maker felügyelt egy ingyenes szolgáltatás, és az átviteli sebesség jelenleg 10 tranzakció/másodperc értékre van korlátozva a felügyeleti API-k és az előrejelzési API-k esetében is. Ha a szolgáltatáshoz másodpercenként 10 tranzakciót szeretne megcélozni, az Azure Cognitive Search S1 (1 példány) SKU-jának használatát javasoljuk.

---

## <a name="when-to-change-a-pricing-tier"></a>Mikor kell módosítani a díjszabási szintet

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

|Frissítés|Ok|
|--|--|
|[Frissítés](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) QnA Maker felügyeleti SKU|Szeretné, hogy a Tudásbázisban több QnA-párt vagy dokumentum-forrás legyen.|
|[Frissítés](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) App Service SKU-t, és Cognitive Searchi szintet, és [hozzon létre Cognitive Search replikákat](../../../search/search-capacity-planning.md)|A Tudásbázisnak több kérést kell kiszolgálnia az ügyfélalkalmazástól, például egy csevegési robottól.|
|[Frissítés](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Azure Cognitive Search szolgáltatás|Számos tudásbázist tervez.|

Szerezze be a legújabb futtatókörnyezeti frissítéseket a [app Service frissítésével a Azure Portal](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

[Frissítés](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Azure Cognitive Search szolgáltatás, ha sok tudásbázist tervez.

---

## <a name="keys-in-qna-maker"></a>Kulcsok a QnA Makerban

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

A QnA Maker szolgáltatás két típusú kulccsal foglalkozik: az App Service-ben üzemeltetett futtatókörnyezettel használt kulcsok és **lekérdezési végponti kulcsok** **létrehozásával** .

Ezeket a kulcsokat akkor használja, ha az API-kon keresztül kéri a szolgáltatást a szolgáltatásnak.

![Kulcskezelés](../media/qnamaker-how-to-key-management/key-management.png)

|Név|Hely|Cél|
|--|--|--|
|Szerzői műveletek/előfizetési kulcs|[Azure Portal](https://azure.microsoft.com/free/cognitive-services/)|Ezek a kulcsok a [QnA Maker Management szolgáltatás API-jai](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)elérésére szolgálnak. Ezek az API-k lehetővé teszik a tudásbázisban található kérdések és válaszok szerkesztését, valamint a Tudásbázis közzétételét. Ezek a kulcsok akkor jönnek létre, amikor új QnA Maker szolgáltatást hoz létre.<br><br>Keresse meg ezeket a kulcsokat a **kulcsok** oldalon található **Cognitive Services** erőforrásban.|
|Lekérdezési végpont kulcsa|[QnA Maker portál](https://www.qnamaker.ai)|Ezekkel a kulcsokkal lekérdezheti a közzétett Tudásbázis-végpontot, hogy választ kapjon a felhasználó kérdéseire. Ezt a lekérdezési végpontot általában a csevegési robotjában vagy a QnA Maker szolgáltatáshoz csatlakozó ügyfélalkalmazás kódjában használja. Ezek a kulcsok akkor jönnek létre, amikor közzéteszi a QnA Maker tudásbázist.<br><br>Keresse meg ezeket a kulcsokat a **szolgáltatás beállításai** lapon. Keresse meg ezt a lapot a legördülő menüben található oldal jobb felső sarkában található felhasználó menüjében.|

### <a name="find-authoring-keys-in-the-azure-portal"></a>Szerzői kulcsok keresése a Azure Portal

A szerzői kulcsok megtekinthetők és alaphelyzetbe állíthatók a Azure Portal, ahol létrehozta a QnA Maker erőforrást. Ezeket a kulcsokat előfizetési kulcsoknak is nevezzük.

1. Nyissa meg a Azure Portal QnA Maker erőforrását, és válassza ki a _Cognitive Services_ típust tartalmazó erőforrást:

    ![QnA Maker erőforráslista](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Ugrás a **kulcsok és a végpontra**:

    ![QnA Maker felügyelt (előzetes verzió) előfizetési kulcs](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="find-query-endpoint-keys-in-the-qna-maker-portal"></a>Lekérdezési végpont kulcsainak keresése a QnA Maker portálon

A végpont ugyanabban a régióban található, mint az erőforrás, mert a végponti kulcsok használatával hívható meg a Tudásbázis.

A végponti kulcsok kezelhetők a [QnA Maker portálról](https://qnamaker.ai).

1. Jelentkezzen be a [QnA Maker portálra](https://qnamaker.ai), nyissa meg a profilt, majd válassza a **szolgáltatás beállításai**:

    ![Végpont kulcsa](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. A kulcsok megtekintése és alaphelyzetbe állítása:

    > [!div class="mx-imgBorder"]
    > ![Endpoint Key Manager](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Frissítse a kulcsokat, ha úgy gondolja, hogy sérült a biztonsága. Ehhez szükség lehet az ügyfélalkalmazás vagy a robot kódjának megfelelő módosítására.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

A QnA Maker felügyelt (előzetes verzió) szolgáltatás két típusú kulccsal foglalkozik: a **szerzői kulcsok** és az **Azure Cognitive Search kulcsaival** , amelyek az ügyfél előfizetésében a szolgáltatás eléréséhez használatosak.

Ezeket a kulcsokat akkor használja, ha az API-kon keresztül kéri a szolgáltatást a szolgáltatásnak.

![Kulcskezelő által felügyelt előzetes verzió](../media/qnamaker-how-to-key-management/qnamaker-v2-key-management.png)

|Név|Hely|Cél|
|--|--|--|
|Szerzői műveletek/előfizetési kulcs|[Azure Portal](https://azure.microsoft.com/free/cognitive-services/)|Ezek a kulcsok a [QnA Maker Management szolgáltatás API-jai](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)elérésére szolgálnak. Ezek az API-k lehetővé teszik a tudásbázisban található kérdések és válaszok szerkesztését, valamint a Tudásbázis közzétételét. Ezek a kulcsok akkor jönnek létre, amikor új QnA Maker szolgáltatást hoz létre.<br><br>Keresse meg ezeket a kulcsokat a **kulcsok** oldalon található **Cognitive Services** erőforrásban.|
|Azure Cognitive Search rendszergazdai kulcs|[Azure Portal](../../../search/search-security-api-keys.md)|Ezek a kulcsok a felhasználó Azure-előfizetésében telepített Azure kognitív keresési szolgáltatással való kommunikációra szolgálnak. Ha egy Azure-beli kognitív keresést társít a QnA Maker felügyelt (előzetes verzió) szolgáltatáshoz, a rendszer automatikusan továbbítja a rendszergazdai kulcsot a QnA Maker szolgáltatásnak. <br><br>Ezeket a kulcsokat a **kulcsok** oldalon található **Azure Cognitive Search** erőforrásban találja.|

### <a name="find-authoring-keys-in-the-azure-portal"></a>Szerzői kulcsok keresése a Azure Portal

A szerzői kulcsok megtekinthetők és alaphelyzetbe állíthatók a Azure Portal, ahol létrehozta a QnA Maker felügyelt (előzetes verzió) erőforrást. Ezeket a kulcsokat előfizetési kulcsoknak is nevezzük.

1. Nyissa meg a QnA Maker felügyelt (előzetes verzió) erőforrást a Azure Portalban, és válassza ki a *Cognitive Services* típusú erőforrást:

    ![QnA Maker felügyelt (előzetes verzió) erőforrás-lista](../media/qnamaker-how-to-key-management/qnamaker-v2-resource-list.png)

2. Ugrás a **kulcsok és a végpontra**:

    ![QnA Maker felügyelt (előzetes verzió) előfizetési kulcs](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="update-the-resources"></a>Az erőforrások frissítése

Ismerje meg, hogyan frissítheti a Tudásbázis által használt erőforrásokat. A QnA Maker felügyelt (előzetes verzió) előzetes verzióban **ingyenes** . 

---

## <a name="management-service-region"></a>Felügyeleti szolgáltatási régió

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

QnA Maker felügyeleti szolgáltatása csak a QnA Maker portál és a kezdeti adatfeldolgozás esetében használatos. Ez a szolgáltatás csak az **USA nyugati** régiójában érhető el. Ebben a Nyugat-amerikai szolgáltatásban nem tárolunk ügyféladatokat.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

QnA Maker felügyelt (előzetes verzió) a felügyelet és az előrejelzési szolgáltatások is ugyanabban a régióban találhatók. Jelenleg QnA Maker felügyelt (előzetes verzió) az **USA déli középső régiójában, Észak-Európában és Kelet-Ausztrália** érhető el.

---

## <a name="resource-naming-considerations"></a>Erőforrás-elnevezési megfontolások

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

A QnA Maker erőforrás neve (például `qna-westus-f0-b` ) a többi erőforrás elnevezésére is használatos.

A Azure Portal létrehozási ablakban létrehozhat egy QnA Maker-erőforrást, és kiválaszthatja a többi erőforrás díjszabási szintjeit.

> [!div class="mx-imgBorder"]
> ![Azure Portal képernyőképe QnA Maker erőforrás-létrehozásról](../media/concept-azure-resource/create-blade.png)

Az erőforrások létrehozása után ugyanaz a neve, kivéve a nem kötelező Application Insights erőforrást, amely a postpends karaktert adja meg.

> [!div class="mx-imgBorder"]
> ![Képernyőkép Azure Portal erőforrás-lista](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Hozzon létre egy új erőforráscsoportot QnA Maker erőforrás létrehozásakor. Ez lehetővé teszi a QnA Maker erőforráshoz társított összes erőforrás megtekintését az erőforráscsoport keresésekor.

> [!TIP]
> Az erőforrás vagy az erőforráscsoport neve alapján megadhatja az árképzési szinteket a névadási konvenció alapján. Ha új Tudásbázis létrehozásával vagy új dokumentumok hozzáadásával kapcsolatos hibákat kap, akkor a Cognitive Search díjszabási szint korlátja gyakori probléma.

### <a name="resource-purposes"></a>Erőforrás-felhasználási célok

A QnA Makerrel létrehozott minden egyes Azure-erőforrásnak konkrét célja van:

* Erőforrás QnA Maker
* Erőforrás Cognitive Search
* App Service
* App Plan szolgáltatás
* Application Insights szolgáltatás


### <a name="cognitive-search-resource"></a>Erőforrás Cognitive Search

A [Cognitive Search](../../../search/index.yml) erőforrás a következőket használja:

* A QnA párok tárolása
* A QnA párok kezdeti rangsorának (ranker #1) megadása futásidőben

#### <a name="index-usage"></a>Indexelés használata

Az erőforrás megtartja az egyik indexet, hogy tesztelje az indexet, és a fennmaradó indexek egy közzétett tudásbázishoz tartozzanak.

A 15 indexek tárolására szolgáló erőforrás 14 közzétett tudásbázist tart fenn, és az összes Tudásbázis teszteléséhez egy indexet kell használni. Ezt a tesztelési indexet a Tudásbázis particionálja, így az interaktív tesztelési panelt használó lekérdezések a tesztelési indexet fogják használni, de csak az adott tudásbázishoz társított partíció eredményeit adják vissza.

#### <a name="language-usage"></a>Nyelvi használat

A QnA Maker erőforrásban létrehozott első Tudásbázis a Cognitive Search erőforráshoz és annak összes indexéhez beállított _egységes_ nyelv meghatározására szolgál. Egy QnA Maker szolgáltatáshoz csak _egy nyelvi beállítás_ tartozhat.

### <a name="qna-maker-resource"></a>Erőforrás QnA Maker

A QnA Maker erőforrás hozzáférést biztosít a szerzői és közzétételi API-khoz, valamint a természetes nyelvi feldolgozó (NLP) alapú második rangsorolási réteghez (ranker #2) a QnA-párokhoz futásidőben.

A második rangsor olyan intelligens szűrőket alkalmaz, amelyek metaadatokat és követő utasításokat is tartalmazhatnak.

#### <a name="qna-maker-resource-configuration-settings"></a>Erőforrás-konfigurációs beállítások QnA Maker

Amikor új tudásbázist hoz létre a QnA Maker- [portálon](https://qnamaker.ai), a **nyelvi** beállítás az egyetlen beállítás, amely az erőforrás szintjén lesz alkalmazva. Az erőforrás első tudásbázisának létrehozásakor ki kell választania a nyelvet.

### <a name="app-service-and-app-service-plan"></a>App Service-és app Service-csomag

Az [ügyfélalkalmazás a közzétett](../../../app-service/index.yml) tudásbázisokat a futásidejű végponton keresztül éri el.

A közzétett Tudásbázis lekérdezéséhez az összes közzétett Tudásbázis ugyanazt az URL-végpontot használja, de az útvonalon belül adja meg a **TUDÁSBÁZIS azonosítóját** .

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

A [Application Insights](../../../azure-monitor/app/app-insights-overview.md) a csevegési naplók és a telemetria összegyűjtésére szolgál. Tekintse át a szolgáltatással kapcsolatos általános [Kusto-lekérdezéseket](../how-to/get-analytics-knowledge-base.md) .

## <a name="share-services-with-qna-maker"></a>Szolgáltatások megosztása QnA Maker

QnA Maker több Azure-erőforrást hoz létre. A felügyelet és a költségmegosztás előnyeinek csökkentése érdekében az alábbi táblázat segítségével megismerheti, hogy mit tehet és nem oszthat meg:

|Szolgáltatás|Megosztás|Ok|
|--|--|--|
|Cognitive Services|X|Nem lehetséges a kialakítás|
|App Service-csomag|✔|App Service csomag számára lefoglalt rögzített lemezterület. Ha az azonos App Service-csomaggal rendelkező más alkalmazások jelentős lemezterületet használnak, akkor a QnAMaker App Service példánya problémákba ütközik.|
|App Service|X|Nem lehetséges a kialakítás|
|Application Insights|✔|Megosztható|
|Keresési szolgáltatás|✔|1. a `testkb` QnAMaker szolgáltatás számára fenntartott név, amelyet mások nem használhatnak.<br>2. az `synonym-map` QnAMaker szolgáltatás számára fenntartott szinonimák hozzárendelése a név alapján történik.<br>3. a közzétett tudásbázisok számát a Search szolgáltatási szintje korlátozza. Ha ingyenes indexek állnak rendelkezésre, más szolgáltatások is használhatják őket.|

### <a name="using-a-single-cognitive-search-service"></a>Egyetlen Cognitive Search szolgáltatás használata

Ha létrehoz egy QnA szolgáltatást és annak függőségeit (például a keresést) a portálon keresztül, akkor létrejön egy keresési szolgáltatás, amely a QnA Maker szolgáltatáshoz van csatolva. Az erőforrások létrehozása után frissítheti az App Service beállítást egy korábban meglévő keresési szolgáltatás használatára, és eltávolíthatja az imént létrehozott szolgáltatást.

Megtudhatja, [hogyan konfigurálhatja](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) a QnA Makert úgy, hogy az QnA Maker erőforrás-létrehozási folyamat részeként létrehozott egy másik kognitív szolgáltatási erőforrást használjon.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

A QnA Maker felügyelt (előzetes verzió) erőforrás neve (például) a `qna-westus-f0-b` többi erőforrás neveként is használatos.

A Azure Portal létrehozási ablak lehetővé teszi QnA Maker felügyelt (előzetes verzió) erőforrás létrehozását, és a többi erőforrás díjszabási szintjeinek kiválasztását.

> [!div class="mx-imgBorder"]
> ![Képernyőkép a QnA Maker felügyelt (előzetes verzió) erőforrás-létrehozási Azure Portalról az ](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png) erőforrások létrehozása után, azonos névvel rendelkeznek.

> [!div class="mx-imgBorder"]
> ![A felügyelt QnA Maker (előzetes verzió) Azure Portal erőforrás-listájának képernyőképe](../media/qnamaker-how-to-setup-service/resources-created-v2.png)

> [!TIP]
> Hozzon létre egy új erőforráscsoportot QnA Maker erőforrás létrehozásakor. Ez lehetővé teszi az QnA Maker felügyelt (előzetes verzió) erőforráshoz társított összes erőforrás megtekintését az erőforráscsoport keresésekor.

> [!TIP]
> Az erőforrás vagy az erőforráscsoport neve alapján megadhatja az árképzési szinteket a névadási konvenció alapján. Ha új Tudásbázis létrehozásával vagy új dokumentumok hozzáadásával kapcsolatos hibákat kap, akkor a Cognitive Search díjszabási szint korlátja gyakori probléma.

### <a name="resource-purposes"></a>Erőforrás-felhasználási célok

A QnA Maker felügyelt (előzetes verzió) szolgáltatással létrehozott összes Azure-erőforrásnak konkrét célja van:

* Erőforrás QnA Maker
* Erőforrás Cognitive Search

### <a name="azure-cognitive-search-resource"></a>Azure Cognitive Search erőforrás

A [Cognitive Search](../../../search/index.yml) erőforrás a következőket használja:

* A QnA párok tárolása
* A QnA párok kezdeti rangsorának (ranker #1) megadása futásidőben

#### <a name="index-usage"></a>Indexelés használata

Egy adott szinten n-1 tudásbázist tehet közzé a különböző nyelveken található n/2 Tudásbázisban, ahol N az Azure Cognitive Search szinten engedélyezett maximális indexek. Tekintse meg a maximális méretet és a másodpercenként engedélyezett dokumentumok számát is.

Ha például a réteg 15 engedélyezett indextel rendelkezik, akkor az azonos nyelvű 14 tudásbázist közzéteheti (egy közzétett Tudásbázis 1 indexe). A tizenötödik index a szerzői műveletek és a tesztelés összes tudásbázisához használatos. Ha úgy dönt, hogy különböző nyelveken rendelkezik tudásbázisokkal, akkor csak 7 tudásbázist tehet közzé.

#### <a name="language-usage"></a>Nyelvi használat

A QnA Maker felügyelt (előzetes verzió) lehetőséggel megadhatja, hogy a QnA Maker szolgáltatás a tudásbázisok számára egyetlen nyelven vagy több nyelven legyen beállítva. Ezt a lehetőséget a QnA Maker szolgáltatás első tudásbázisának létrehozása során teheti meg. [Itt](#pricing-tier-considerations) megtudhatja, hogyan engedélyezheti a nyelvi beállítást a Tudásbázisban.

### <a name="qna-maker-resource"></a>Erőforrás QnA Maker

A QnA Maker felügyelt (előzetes verzió) erőforrás hozzáférést biztosít a szerzői és közzétételi API-khoz, üzemelteti a rangsorolási futtatókörnyezetet, valamint biztosítja a telemetria.

---

## <a name="next-steps"></a>Következő lépések

* Tudnivalók a QnA Maker [Tudásbázisról](../index.yml)
* A [Tudásbázis életciklusának](development-lifecycle-knowledge-base.md) megismerése
* A szolgáltatás és az Tudásbázis [korlátainak](../limits.md) áttekintése

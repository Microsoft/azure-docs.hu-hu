---
title: Gyakori kérdések (GYIK) – LUIS
description: Ez a cikk a Language Understanding (LUIS) kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: troubleshooting
ms.date: 05/06/2020
ms.openlocfilehash: b5e25e9ed25ced96d38994928bcb6275ce79420f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612796"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Language Understanding – Gyakori kérdések (GYIK)

Ez a cikk a Language Understanding (LUIS) kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

## <a name="whats-new"></a>Újdonságok

[További](whats-new.md) információ a Language Understanding újdonságáról (Luis).

<a name="luis-authoring"></a>

## <a name="authoring"></a>Tartalomkészítés

### <a name="what-are-the-luis-best-practices"></a>Mik a LUIS ajánlott eljárásai?
Kezdje a [szerzői ciklussal](luis-concept-app-iteration.md), majd olvassa el az [ajánlott eljárásokat](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Mi a legjobb módszer az alkalmazás létrehozásához a LUIS-ben?

Az alkalmazás kiépítésének legjobb módja egy [növekményes folyamat](luis-concept-app-iteration.md).

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Mi a bevált gyakorlat az alkalmazás céljainak modellezéséhez? Érdemes-e több konkrét vagy több általános szándékot létrehozni?

Válassza ki azokat a leképezéseket, amelyek nem annyira általánosak, hogy átfedésben legyenek, de nem annyira, hogy a LUIS megnehezíti a hasonló szándékok megkülönböztetését. A megkülönböztethető specifikus leképezések létrehozása a LUIS modellezés egyik ajánlott eljárása.

### <a name="is-it-important-to-train-the-none-intent"></a>Fontos a nincs szándék betanítása?

Igen, érdemes betanítani a **nincs** szándékot több hosszúságú kimondott szöveg, mivel további címkéket ad hozzá más szándékokhoz. A megfelelő arány 1 vagy 2 címke, amelyet a rendszer az egyik szándékhoz hozzáadott 10 címkéhez **sem** ad hozzá. Ez az arány fokozza a LUIS megkülönböztető erejét.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Hogyan lehet helyesírási hibákat kijavítani a hosszúságú kimondott szöveg-ben?

Lásd a [Bing Spell Check API v7](luis-tutorial-bing-spellcheck.md) oktatóanyagot. LUIS kikényszeríti a Bing Spell Check API v7 által kiszabott korlátokat.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Hogyan a LUIS-alkalmazás programozással való szerkesztését?
Ha programozott módon szeretné szerkeszteni a LUIS alkalmazást, használja az [authoring API](https://go.microsoft.com/fwlink/?linkid=2092087)-t. Lásd: a [Luis authoring API meghívása](./get-started-get-model-rest-apis.md) és [a Luis-alkalmazás programozott módon történő létrehozása Node.jshasználatával ](./luis-tutorial-node-import-utterances-csv.md) , példákkal a szerzői API meghívására. A szerzői API használatához a létrehozási [kulcsot](luis-how-to-azure-subscription.md#azure-resources-for-luis) kell használnia a végponti kulcs helyett. A programozott szerzői műveletek másodpercenként legfeljebb 1 000 000 hívást tesznek lehetővé, és másodpercenként öt tranzakciót biztosítanak. A LUIS használatával használt kulcsokról további információt a [kulcsok kezelése](./luis-how-to-azure-subscription.md)című témakörben talál.

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Hol található a reguláris kifejezésnek megfelelő minta funkció?
Az előző **minta funkció** jelenleg elavult, **[mintázatok](luis-concept-patterns.md)** helyett.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Hogyan használ egy entitást a megfelelő adatok lekéréséhez?
Tekintse meg az [entitások](luis-concept-entity-types.md) és [az adatkiemelés](luis-concept-data-extraction.md)témakört.

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Egy példa kifejezés változatának tartalmaznia kell a központozást?
Használja az alábbi megoldások egyikét:
* [Írásjelek](luis-reference-application-settings.md#punctuation-normalization) figyelmen kívül hagyása
* Adja hozzá a különböző változatokat példaként hosszúságú kimondott szöveg a szándékhoz
* Adja hozzá a példa kifejezésének mintázatát a [szintaxissal, hogy figyelmen kívül hagyja](luis-concept-patterns.md#pattern-syntax) a központozást.

### <a name="does-luis-currently-support-cortana"></a>A LUIS jelenleg támogatja a Cortana-t?

A Cortana előre elkészített alkalmazások 2017-ben elavultak. Már nem támogatottak.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Hogyan LUIS-alkalmazás tulajdonjogának átruházása?
Ha egy LUIS-alkalmazást másik Azure-előfizetésre szeretne továbbítani, exportálja a LUIS alkalmazást, és importálja egy új fiókkal. Frissítse a LUIS-alkalmazás AZONOSÍTÓját az ügyfélalkalmazás, amely meghívja az alkalmazást. Előfordulhat, hogy az új alkalmazás némileg eltérő LUIS-pontszámot ad vissza az eredeti alkalmazásból.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Egy előre összeépített entitás egy példaként való kiírással van megjelölve az egyéni entitás helyett. Hogyan javítsa ezt?

A LUIS-portálon megcímkézheti a pontos entitáshoz tartozó szöveget, amelyet szeretne kinyerni. Ha a LUIS-portál nem jeleníti meg a megfelelő entitások előrejelzését, előfordulhat, hogy további hosszúságú kimondott szöveg kell hozzáadnia, és fel kell címkéznie az entitást a szövegben, vagy hozzá kell adnia egy szolgáltatást.

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Megpróbáltam importálni egy alkalmazást vagy verziót, de hiba történt?

További információ a [verzió importálásával kapcsolatos hibákról](luis-how-to-manage-versions.md#import-errors).

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Együttműködés és közreműködés

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-azure-role-based-access-control-azure-rbac"></a>Hogyan a közreműködők hozzáférhetnek a LUIShoz Azure Active Directory (Azure AD) vagy az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC)?

A közreműködők hozzáférésének megismeréséhez tekintse meg [Azure Active Directory erőforrásokat](luis-how-to-collaborate.md#azure-active-directory-resources)  és [Azure Active Directory bérlői felhasználót](luis-how-to-collaborate.md#azure-active-directory-tenant-user) .

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Végpont

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>HTTP 403-es hiba-állapotkód érkezett. Hogyan lehet kijavítani a hibát?

A 403-es és a 429-es hibakód akkor jelenik meg, ha az árképzési szinten a másodpercenkénti tranzakciók száma vagy a havi tranzakció. Növelje az árképzési szintet, vagy használjon Language Understanding [tárolókat](luis-container-howto.md).

Ha az összes ingyenes 1000-végpontot lekérdezi, vagy túllépi a díjszabási csomag havi tranzakciós kvótáját, a rendszer HTTP 403 hibakódot kap.

Ennek a hibának a kijavításához [módosítania kell az árképzési szintet](luis-how-to-azure-subscription.md#change-the-pricing-tier) egy magasabb szintű csomagra, vagy [létre kell hoznia egy új erőforrást](get-started-portal-deploy-app.md#create-the-endpoint-resource) , és [hozzá kell rendelnie az alkalmazáshoz](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

A hiba megoldásai a következők:

* A [Azure Portal](https://portal.azure.com)a Language Understanding erőforráson az **Erőforrás-kezelés – > díjszabási** szinten módosítsa az árképzési szintet magasabb TPS szintjére. Ha az erőforrás már hozzá van rendelve a Language Understanding alkalmazáshoz, semmit nem kell tennie a Language Understanding portálon.
*  Ha a használat meghaladja a legmagasabb szintű díjszabást, vegyen fel további Language Understanding erőforrásokat egy terheléselosztó elé. A Kubernetes vagy Docker-összeállítással rendelkező [Language Understanding-tároló](luis-container-howto.md) segíthet ennek elvégzésében.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>HTTP 429-es hiba-állapotkód érkezett. Hogyan lehet kijavítani a hibát?

A 403-es és a 429-es hibakód akkor jelenik meg, ha az árképzési szinten a másodpercenkénti tranzakciók száma vagy a havi tranzakció. Növelje az árképzési szintet, vagy használjon Language Understanding [tárolókat](luis-container-howto.md).

Ezt az állapotkódot akkor adja vissza a rendszer, ha a másodpercenkénti tranzakciók száma meghaladja a díjszabási szintet.

A megoldások a következők:

* [Megnövelheti az árképzési szintet](luis-how-to-azure-subscription.md#change-the-pricing-tier), ha nem a legmagasabb szintű szinten van.
* Ha a használat meghaladja a legmagasabb szintű díjszabást, vegyen fel további Language Understanding erőforrásokat egy terheléselosztó elé. A Kubernetes vagy Docker-összeállítással rendelkező [Language Understanding-tároló](luis-container-howto.md) segíthet ennek elvégzésében.
* Az ügyfélalkalmazás kérelmeit megadhatja az [újrapróbálkozási szabályzattal](/azure/architecture/best-practices/transient-faults#general-guidelines) , amelyet Ön saját maga is végrehajthat, amikor megkapja ezt az állapotkódot.

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>A végponti lekérdezés nem várt eredményt adott vissza. Mit tegyek?

A lekérdezési előrejelzések váratlan eredményei a közzétett modell állapotától függenek. A modell kijavítani lehet, hogy módosítania kell a modellt, a betanítást és a közzétételt.

A modell javítása az [aktív tanulással](luis-how-to-review-endpoint-utterances.md)kezdődik.

A nem determinisztikus-képzések eltávolításához frissítse az [Application Version Settings API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) -t az összes betanítási érték használatához.

További tippekért tekintse át az [ajánlott eljárásokat](luis-concept-best-practices.md) .

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Miért adja meg a LUIS szóközt a lekérdezéshez a szavak közepén?
LUIS [tokenizes](luis-glossary.md#token) a [kultúrán](luis-language-support.md#tokenization)alapuló Kimondás. Az eredeti érték és a jogkivonat-érték is elérhető az [kinyeréshez](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Hogyan LUIS Endpoint-kulcsot létrehozni és hozzárendelni?
[Hozza létre a végponti kulcsot](luis-how-to-azure-subscription.md) az Azure-ban a [szolgáltatási](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) szinthez. [Rendelje hozzá a kulcsot](luis-how-to-azure-subscription.md) az **[Azure-erőforrások](luis-how-to-azure-subscription.md)** lapon. Ehhez a művelethez nem tartozik megfelelő API. Ezt követően módosítania kell a HTTP-kérést a végpontra [az új Endpoint kulcs használatához](luis-how-to-azure-subscription.md).

### <a name="how-do-i-interpret-luis-scores"></a>Hogyan a LUIS pontszámok értelmezését?
A rendszeren a legmagasabb pontozási szándékot kell használnia, függetlenül annak értékétől. Például az alábbi pontszám 0,5 (kevesebb, mint 50%) nem feltétlenül jelenti azt, hogy LUIS alacsony megbízhatósággal rendelkezik. A további betanítási [adatmennyiséggel növelheti a](luis-concept-prediction-score.md) legvalószínűbb szándékot.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Miért nem látom a végpontok találatait az alkalmazás irányítópultján?
Az alkalmazás irányítópultján a végpontok teljes látogatottsága rendszeresen frissül, de a Azure Portal LUIS-végpont kulcsához társított metrikák gyakrabban frissülnek.

Ha nem látja a végpontok frissített találatait az irányítópulton, jelentkezzen be a Azure Portalba, és keresse meg a LUIS Endpoint kulcshoz társított erőforrást, és nyissa meg a **metrikákat** a **hívások teljes** metrikájának kiválasztásához. Ha a végponti kulcsot több LUIS-alkalmazáshoz is használják, a Azure Portal metrika az azt használó LUIS-alkalmazásoktól érkező hívások összesített számát jeleníti meg.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Van egy PowerShell-parancs a végponti kvóta eléréséhez?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A végponti kvóta megtekintéséhez használhatja a PowerShell-parancsot:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>A LUIS-alkalmazásom tegnap dolgoztam, de ma 403-es hiba történt. Nem módosítottam az alkalmazást. Hogyan lehet kijavítani a hibát?
Az alábbi [útmutatást](#how-do-i-create-and-assign-a-luis-endpoint-key) követve hozzon létre egy Luis-végponti kulcsot, és rendelje hozzá az alkalmazáshoz. Ezután az [új Endpoint kulcs használatához](luis-how-to-azure-subscription.md)módosítania kell az ügyfélalkalmazás http-kérelmét a végpontra. Ha egy másik régióban hozott létre egy új erőforrást, módosítsa a HTTP-ügyfél kérelmének régióját is.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Hogyan biztonságos My LUIS-végpontom?
Lásd: [a végpont biztonságossá tétele](luis-how-to-azure-subscription.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>A LUIS-korlátokon belül működik

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>A LUIS-alkalmazás által támogatott leképezések és entitások maximális száma
Lásd a [határok](luis-limits.md) referenciát.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>El szeretnék készíteni egy LUIS-alkalmazást, amely több mint a leképezések maximális száma. Mit tegyek?

Lásd: [ajánlott eljárások a szándékokhoz](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Egy olyan alkalmazást szeretnék létrehozni a LUIS-ben, amely több, mint az entitások maximális száma. Mit tegyek?

Lásd: [ajánlott eljárások az entitásokhoz](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Milyen korlátozások vonatkoznak a kifejezések számának és méretének korlátozására?
A [kifejezések listájának](./luis-concept-feature.md)maximális hosszát lásd: a [határok](luis-limits.md) hivatkozása.

### <a name="what-are-the-limits-on-example-utterances"></a>Milyen korlátozások vonatkoznak például a hosszúságú kimondott szöveg?
Lásd a [határok](luis-limits.md) referenciát.

## <a name="testing-and-training"></a>Tesztelés és képzés

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Néhány hibát látok a Batch tesztelési paneljén az alkalmazás egyes modelljeinél. Hogyan lehet kezelni ezt a problémát?

A hibák arra utalnak, hogy a címkék és a modellek előrejelzései között némi eltérés tapasztalható. A probléma megoldásához hajtsa végre az alábbi műveletek egyikét vagy mindkettőt:
* Ha segíteni szeretne a LUIS számára a szándékok közötti diszkrimináció növelésében, adjon hozzá további címkéket.
* Ha segítségre van a LUIS-ról, adja hozzá a tartomány-specifikus szókincset bevezető kifejezés-lista funkciókat.

Lásd a [Batch-tesztelési](./luis-how-to-batch-test.md) oktatóanyagot.

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Egy alkalmazás exportálásakor a rendszer újraimportál egy új alkalmazásba (új alkalmazás-AZONOSÍTÓval), a LUIS-előrejelzési pontszámok eltérőek. Miért történik ez?

Tekintse meg [az azonos alkalmazás példányai között megjelenő előrejelzési különbségeket](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Néhány hosszúságú kimondott szöveg az alkalmazás módosítása után nem megfelelő szándékot mutat. Úgy tűnik, hogy a probléma véletlenszerűen eltűnik. Hogyan lehet kijavítani a hibát?

Tekintse meg [a vonatot az összes adattal](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Alkalmazások közzététele

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Mi a bérlő azonosítója a "kulcs hozzáadása az alkalmazáshoz" ablakban?
Az Azure-ban a bérlő a szolgáltatáshoz társított ügyfelet vagy szervezetet jelöli. A **címtár-azonosító** mezőben keresse meg a bérlő azonosítóját a Azure Portal **Azure Active Directory** a  >    >  **Tulajdonságok** kezelése lehetőség kiválasztásával.

![Bérlő azonosítója a Azure Portal](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Miért van több Endpoint Key rendelve az alkalmazáshoz, mint amennyit hozzárendeltem?
Az egyes LUIS-alkalmazások kényelme érdekében a végpontok listájában a szerzői és kezdő kulcs szerepel. Ez a kulcs csak néhány végponti találatot tesz lehetővé, így kipróbálhatja a LUIS-t.

Ha az alkalmazás már a LUIS általánosan elérhetővé vált (GA), az előfizetés LUIS Endpoint kulcsait a rendszer automatikusan hozzárendeli. Ez azért történt, hogy a GA-áttelepítés egyszerűbb legyen. A Azure Portalban található új LUIS Endpoint kulcsok _nem_ lesznek automatikusan kiosztva a Luis-hoz.

## <a name="key-management"></a>Kulcskezelés

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Hogyan tudja, milyen kulcsra van szükségem, hol kapok, és mit csinálok?

További információ a szerzői műveletek és az előrejelzési futtatókörnyezet kulcsa közötti különbségekről: [szerzői és lekérdezési előrejelzési végpont kulcsai a Luis-ben](luis-how-to-azure-subscription.md) .

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Hiba történt a kvóta lejártakor. Hogyan lehet kijavítani a hibát?

További információért lásd: a [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) -es és a [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) -es http-állapotkód javítása.

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>További végponti lekérdezéseket kell kezelnie. Mit kell tennem ehhez?

További információért lásd: a [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) -es és a [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) -es http-állapotkód javítása.

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Létrehoztam egy szerzői kulcsot, de nem jelenik meg a LUIS-portálon. Mi történt?

A szerzői kulcsok a [szerzői műveletek elvégzése](luis-migration-authoring.md)után érhetők el a Luis-portálon.

## <a name="app-management"></a>Alkalmazáskezelés

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Hogyan letölti a felhasználói hosszúságú kimondott szöveg naplóját?
Alapértelmezés szerint a LUIS-alkalmazás a felhasználóktól hosszúságú kimondott szöveg naplózza. A felhasználók által a LUIS-alkalmazásba küldött hosszúságú kimondott szöveg letöltéséhez lépjen a **saját alkalmazások** elemre, és válassza ki az alkalmazást. A környezetfüggő eszköztáron válassza a **végponti naplók exportálása** lehetőséget. A napló vesszővel tagolt (CSV) fájlként van formázva.

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Hogyan lehet letiltani a hosszúságú kimondott szöveg naplózását?
Kikapcsolhatja a felhasználói hosszúságú kimondott szöveg naplózását `log=false` a végpont URL-címének beállításával, amelyet az ügyfélalkalmazás a Luis lekérdezésére használ. A naplózás kikapcsolása azonban letiltja a LUIS-alkalmazás hosszúságú kimondott szöveg, vagy javíthatja az [aktív tanuláson](luis-concept-review-endpoint-utterances.md#what-is-active-learning)alapuló teljesítményt. Ha adatvédelmi okokból állítja be az `log=false` adatokat, nem töltheti le a felhasználói hosszúságú kimondott szöveg származó rekordokat a Luis-ból, vagy a hosszúságú kimondott szöveg használatával javíthatja az alkalmazását.

A naplózás a hosszúságú kimondott szöveg egyetlen tárolója.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Miért nem szeretném naplózni az összes végponti hosszúságú kimondott szöveg?
Ha a naplót előrejelzési elemzésre használja, ne rögzítsen teszt hosszúságú kimondott szöveg a naplóban.

## <a name="data-management"></a>Adatkezelés

### <a name="can-i-delete-data-from-luis"></a>Törölhetők az adatok a LUIS-ból?

* Bármikor törölheti például a LUIS-képzéshez használt hosszúságú kimondott szöveg. Ha töröl egy példát a LUIS-alkalmazásból, az el lesz távolítva a LUIS webszolgáltatásból, és nem érhető el exportálásra.
* A hosszúságú kimondott szöveg törölheti a felhasználói hosszúságú kimondott szöveg listájáról, amelyet a LUIS az **Endpoint hosszúságú kimondott szöveg áttekintése** lapon javasol. Ha törli a hosszúságú kimondott szöveg a listából, azzal megakadályozza, hogy a rendszer ne törölje őket a naplókból.
* Ha töröl egy fiókot, a rendszer törli az összes alkalmazást, valamint a hosszúságú kimondott szöveg és a naplókat. Az adat megőrzése a kiszolgálókon 60 nappal a végleges törlés előtt történik.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Hogyan kezeli a Microsoft a LUIS-nak küldött adatvédelmet?

Az adatvédelmi [központ](https://www.microsoft.com/trustcenter) ismerteti a kötelezettségvállalásokat, valamint az adatkezelési és-hozzáférési lehetőségeket az Azure-szolgáltatásokban.

## <a name="language-and-translation-support"></a>Nyelvi és fordítási támogatás

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Van egy alkalmazásom egy nyelven, és egy másik nyelven szeretnék létrehozni egy párhuzamos alkalmazást. Mi a legegyszerűbb módszer?
1. Exportálja az alkalmazást.
2. Fordítsa le a címkével ellátott hosszúságú kimondott szöveg az exportált alkalmazás JSON-fájljából a célként megadott nyelvre.
3. Előfordulhat, hogy módosítania kell a leképezések és entitások nevét, vagy hagyja őket.
4. Végül importálja az alkalmazást, hogy a megcélzott nyelven rendelkezzen LUIS-alkalmazással.

## <a name="app-notification"></a>Alkalmazás-értesítés

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Miért kaptam egy e-mailt, mondván, hogy már majdnem a kvóta?
Az authoring/Starter-kulcs csak havonta 1000-es végpontot kérdez le. Hozzon létre egy LUIS Endpoint Key (ingyenes vagy fizetős) kulcsot, és használja az adott kulcsot a végponti lekérdezések végrehajtásakor. Ha egy robot vagy egy másik ügyfélalkalmazás végponti lekérdezéseit teszi elérhetővé, akkor ott kell módosítania a LUIS Endpoint kulcsot.

## <a name="bots"></a>Robotok

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>A LUIS-bot nem működik. Mit tegyek?

Az első probléma az, hogy elkülöníti a problémát, ha a probléma LUIS-hez kapcsolódik, vagy a LUIS middleware-n kívül történik.

#### <a name="resolve-issue-in-luis"></a>Probléma megoldása a LUIS-ben
A Luis- [végponttal](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint)azonos Kimondás a Luis-nek. Ha hibaüzenetet kap, oldja meg a problémát a LUIS-ben, amíg a hibát már nem adja vissza. Gyakori hibák a következők:

* `Out of call volume quota. Quota will be replenished in <time>.` – Ez a probléma azt jelzi, hogy egy szerzői kulcsból egy [végponti kulcsra](luis-how-to-azure-subscription.md) kell váltania, vagy módosítania kell a [szolgáltatási szinteket](luis-how-to-azure-subscription.md#change-the-pricing-tier).

#### <a name="resolve-issue-in-azure-bot-service"></a>Probléma megoldása Azure Bot Service

Ha a Azure Bot Service használja, és a probléma az, hogy a **webes csevegésben a teszt** visszaadja `Sorry, my bot code is having an issue` , ellenőrizze a naplókat:

1. A robot Azure Portal a robot **kezelése** szakaszban válassza a **Létrehozás** lehetőséget.
1. Nyissa meg az online Kódszerkesztő alkalmazást.
1. A felső, kék navigációs sávban válassza ki a robot nevét (a második elem jobbra).
1. Az eredményül kapott legördülő listában válassza a **kudu-konzol megnyitása** lehetőséget.
1. Válassza ki a **LogFiles** elemet, majd válassza az **alkalmazás** lehetőséget. Tekintse át az összes naplófájlt. Ha nem látja a hibát az alkalmazás mappájában, tekintse át az összes naplófájlt a **LogFiles (naplófájlok**) területen.
1. Ne felejtse el újraépíteni a projektet, ha olyan lefordított nyelvet használ, mint például a C#.

> [!Tip]
> A konzol emellett csomagokat is telepíthet.

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Probléma megoldása a helyi gépen a bot Framework-mel való hibakeresés során.

Ha többet szeretne megtudni a robot helyi hibakereséséről, olvassa el [a robot hibakeresése](/azure/bot-service/bot-service-debug-bot)című témakört.

## <a name="integrating-luis"></a>A LUIS integrálása

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Hol jött létre a LUIS-alkalmazásom az Azure-webalkalmazás bot-előfizetési folyamata során?
Ha kijelöl egy LUIS-sablont, majd kiválasztja a **kiválasztás** gombot a sablon ablaktáblán, a bal oldali ablaktábla a sablon típusát is megváltoztatja, és megkérdezi, hogy milyen régióban hozza létre a Luis-sablont. A webalkalmazás-robot folyamata azonban nem hoz létre LUIS-előfizetést.

![LUIS-sablon webalkalmazásának bot-régiója](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Milyen LUIS-régiók támogatják a bot Framework Speech alapozót?
A [beszédfelismerési](/bot-framework/bot-service-manage-speech-priming) alapszolgáltatások csak a Central (US) példányban található Luis-alkalmazásokhoz támogatottak.

## <a name="api-programming-strategies"></a>API programozási stratégiák

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Hogyan programozott módon beszerezhet egy erőforrás LUIS régióját?

A LUIS minta használatával programozott módon [keresheti meg a régiót](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) C# vagy Node.Js használatával.

## <a name="luis-service"></a>LUIS szolgáltatás

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Elérhető-e a Language Understanding (LUIS) a helyszínen vagy a privát felhőben?

Igen, használhatja a LUIS- [tárolót](luis-container-howto.md) ezekben a forgatókönyvekben, ha rendelkezik a szükséges kapcsolattal a méréshez.

## <a name="migrating-to-the-next-version"></a>Migrálás a következő verzióra

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Hogyan Migrálás az előnézet V3 API-ra?

Lásd: [API v2 – v3 áttelepítési útmutató Luis-alkalmazásokhoz](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>Build 2019 konferencia hirdetményei

A Build 2019 konferencián a következő funkciók jelentek meg:

* [A V3 API áttelepítési útmutatójának előzetes verziója](luis-migration-api-v3.md)
* [Továbbfejlesztett elemzési irányítópult](luis-how-to-use-dashboard.md)
* [Továbbfejlesztett előre összeépített tartományok](luis-reference-prebuilt-domains.md)
* [Dinamikus lista entitásai](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Külső entitások](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Videók:

* [Az Azure társalgási AI használata az üzlet méretezésére a következő generáció számára](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Következő lépések

A LUIS-vel kapcsolatos további tudnivalókért tekintse meg a következő forrásokat:
* [A LUIS-mel kapcsolatos kérdések Stack Overflow](https://stackoverflow.com/questions/tagged/luis)
* [Microsoft Q&az MSDN Language Understanding intelligens szolgáltatások (LUIS) kérdésének oldalát](/answers/topics/azure-language-understanding.html)
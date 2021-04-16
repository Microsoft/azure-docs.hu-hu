---
title: Gyakori kérdések (GYIK) – LUIS
description: Ez a cikk az alkalmazásokkal (LUIS Language Understanding kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: troubleshooting
ms.date: 04/13/2021
ms.openlocfilehash: 97b7c02a418a87a0700414e19bc939bda899d073
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503813"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Language Understanding – Gyakori kérdések (GYIK)

Ez a cikk a szolgáltatással (LUIS Language Understanding kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

## <a name="whats-new"></a>Újdonságok

[További információ](whats-new.md) a Language Understanding (LUIS) újdonságról.

<a name="luis-authoring"></a>

## <a name="authoring"></a>Tartalomkészítés

### <a name="what-are-the-luis-best-practices"></a>Mik a LUIS ajánlott eljárásai?
Kezdje a [szerzői ciklussal,](luis-concept-app-iteration.md)majd olvassa el az [ajánlott eljárásokat.](luis-concept-best-practices.md)

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Mi a legjobb módszer az alkalmazás luis-ban való építéséhez?

Az alkalmazás felépítésének legjobb módja egy [növekményes folyamat.](luis-concept-app-iteration.md)

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Mi a jó gyakorlat az alkalmazás szándékának modellezéséhez? Pontosabb vagy általánosabb szándékokat kell létrehoznom?

Olyan szándékokat válasszon, amelyek nem annyira általánosak, hogy átfedőek, de nem annyira specifikusak, hogy a LUIS nehezen tudja megkülönböztetni a hasonló szándékokat. A LUIS modellezésének egyik ajánlott eljárása a diszkriminatív specifikus szándékok létrehozása.

### <a name="is-it-important-to-train-the-none-intent"></a>Fontos, hogy betanítsa a None szándékot?

Igen, jó, ha a **None** szándékot több kimondott szövegkel betanítja, miközben további címkéket ad hozzá más szándékhoz. A jó arány 1 vagy 2 címke hozzáadása a **None** címkéhez minden egyes szándékhoz hozzáadott 10 címkéhez. Ez az arány növeli a LUIS diszkriminatív erejét.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Hogyan javíthatja ki a helyesírási hibákat a kimondott szövegekben?

Lásd a [Bing Spell Check API 7-es verzió oktatóanyagát.](luis-tutorial-bing-spellcheck.md) A LUIS kikényszeríteni a Bing Spell Check API 7-es verzió által előírt korlátokat.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Hogyan a LUIS-alkalmazást?
A LUIS-alkalmazás programozott szerkesztéséhez használja a [Szerzői API-t.](https://go.microsoft.com/fwlink/?linkid=2092087) A [szerzői API hívásával](./get-started-get-model-rest-apis.md) kapcsolatos példákért lásd: A LUIS szerzői API hívása és [LUIS-alkalmazás ](./luis-tutorial-node-import-utterances-csv.md) Node.jshasználatával történő összeállítása a Node.jshasználatával. A Szerzői API-hoz nem [](luis-how-to-azure-subscription.md#azure-resources-for-luis) végpontkulcsot, hanem szerzői kulcsot kell használnia. A programozott írás havonta legfeljebb 1 000 000 hívást és másodpercenként öt tranzakciót tesz lehetővé. A LUIS-sal használt kulcsokkal kapcsolatos további információkért lásd: [Kulcsok kezelése.](./luis-how-to-azure-subscription.md)

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Hol található a reguláriskifejezés-egyeztetést lehetővé tő Pattern funkció?
Az előző **Minta funkció** jelenleg elavult, és a Patterns (Minták) szöveg **[váltja fel.](luis-concept-patterns.md)**

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Hogyan a megfelelő adatokat egy entitás használatával?
Lásd: [Entitások és](luis-concept-entity-types.md) [adatkinyerés.](luis-concept-data-extraction.md)

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>A példaként kimondott szöveg változatának tartalmaznia kell írásjeleket?
Használja az alábbi megoldások egyikét:
* Írásjelek [figyelmen kívül hagyása](luis-reference-application-settings.md#punctuation-normalization)
* A különböző változatok hozzáadása kimondott példaként a szándékhoz
* Adja hozzá a példa kimondott szöveg mintázatát a [szintaxissal, hogy](luis-concept-patterns.md#pattern-syntax) figyelmen kívül hagyja az írásjeleket.

### <a name="does-luis-currently-support-cortana"></a>A LUIS jelenleg támogatja Cortanát?

A Cortana előre összeállított alkalmazásai 2017-ben elavultak voltak. Ezek a továbbiakban nem támogatottak.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Hogyan LUIS-alkalmazás tulajdonjogának átadása?
Egy LUIS-alkalmazás másik Azure-előfizetésbe való átviteléhez exportálja a LUIS-alkalmazást, és importálja egy új fiókkal. Frissítse a LUIS-alkalmazás azonosítóját az azt megindító ügyfélalkalmazásban. Az új alkalmazás némileg eltérő LUIS-pontszámokat ad vissza az eredeti alkalmazásból.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Az előre összeállított entitások az egyéni entitásom helyett egy példa kimondott szövegben vannak megcímkézve. Hogyan ezt kijavítani?

A LUIS-portálon megcímkézheti pontosan a kinyerni szeretne entitás szövegét. Ha a LUIS-portál nem a megfelelő entitás-előrejelzést mutatja, előfordulhat, hogy további kimondott szövegeket kell hozzáadnia, és fel kell címkéznie az entitást a szövegben, vagy hozzá kell adni egy funkciót.

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Alkalmazás- vagy verziófájlt próbáltam importálni, de hibaüzenetet kaptam. Mi történt?

További információ a verzió [importálási hibáiról.](luis-how-to-manage-versions.md#import-errors)

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Együttműködés és közreműködés

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-azure-role-based-access-control-azure-rbac"></a>Hogyan luis-hoz való hozzáférést Azure Active Directory (Azure AD) vagy azure-beli szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával?

A [Azure Active Directory a közreműködők](luis-how-to-collaborate.md#azure-active-directory-resources)  hozzáférésének Azure Active Directory [információkért](luis-how-to-collaborate.md#azure-active-directory-tenant-user) tekintse meg a Azure Active Directory és bérlői felhasználóval való hozzáférést.

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Végpont

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>HTTP 403-as hibaállapotkódot kaptam. Hogyan lehet kijavítani a hibát?

403-as és 429-es hibaállapotkódokat kap, ha túllépi a másodpercenkénti tranzakciókat vagy a havi tranzakciókat a tarifacsomaghoz. Növelje a tarifacsomagot, vagy használjon Language Understanding [tárolókat.](luis-container-howto.md)

Ha az összes ingyenes 1000 végponti lekérdezést használja, vagy túllépi a tarifacsomag havi tranzakciókvótáját, HTTP 403-as hibaállapotkódot kap.

A hiba kijavítása esetén [](luis-how-to-azure-subscription.md#change-the-pricing-tier) módosítania kell a tarifacsomagot egy magasabb szintre, vagy létre kell hoznia egy új erőforrást, és hozzá kell rendelnie [az alkalmazáshoz.](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal) [](get-started-portal-deploy-app.md#create-the-endpoint-resource)

A hiba megoldásai a következők:

* A [Azure Portal](https://portal.azure.com)erőforrásán Language Understanding Resource **Management ->** tarifacsomagban módosítsa a tarifacsomagot magasabb TPS-szintre. Semmit nem kell megtennie a Language Understanding portálon, ha az erőforrás már hozzá van rendelve az Language Understanding alkalmazáshoz.
*  Ha a használat meghaladja a legmagasabb tarifacsomagot, adjon hozzá további Language Understanding terheléselosztási erőforrásokkal. A [Language Understanding](luis-container-howto.md) Kubernetes vagy a Docker Compose segítségével.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>HTTP 429-es hibaállapotkódot kaptam. Hogyan lehet kijavítani a hibát?

403-as és 429-es hibaállapotkódokat kap, ha túllépi a másodpercenkénti tranzakciókat vagy a havi tranzakciókat a tarifacsomaghoz. Növelje a tarifacsomagot, vagy használjon Language Understanding [tárolókat.](luis-container-howto.md)

Ez az állapotkód akkor lesz visszaadva, ha a tranzakciók másodpercenkénti száma meghaladja a tarifacsomagot.

A megoldások a következők:

* Ha nem [a legmagasabb szinten](luis-how-to-azure-subscription.md#change-the-pricing-tier)van, növelheti a tarifacsomagját.
* Ha a használat meghaladja a legmagasabb tarifacsomagot, adjon hozzá további Language Understanding terheléselosztási erőforrásokkal. A [Language Understanding](luis-container-howto.md) Kubernetes vagy a Docker Compose segítségével.
* Az ügyfélalkalmazások kéréseit egy [](/azure/architecture/best-practices/transient-faults#general-guidelines) olyan újrapróbálkozási szabályzattal kaphatja meg, amely az állapotkód lekérésekor lép érvénybe.

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>A végpontlekérdezés váratlan eredményeket adott vissza. Mit tegyek?

A lekérdezés váratlan előrejelzési eredményei a közzétett modell állapotán alapulnak. A modell kijavításhoz előfordulhat, hogy módosítania kell a modellt, be kell betanítani és újra közzé kell tennie.

A modell kijavítása az aktív [tanulással kezdődik.](luis-how-to-review-endpoint-utterances.md)

A nem determinisztikus betanítás eltávolításához frissítse az alkalmazás verzióbeállítási [API-ját](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) az összes betanításadat használata érdekében.

További [tippekért tekintse](luis-concept-best-practices.md) át az ajánlott eljárásokat.

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Miért ad a LUIS szóközöket a lekérdezéshez szavak körül vagy közben?
A LUIS [a kulturális](luis-glossary.md#token) környezet alapján jogkivonatot hoz a [kimondott szöveghez.](luis-language-support.md#tokenization) Az adatok kinyerése az eredeti és a tokenreizált [értékkel is elérhető.](luis-concept-data-extraction.md#tokenized-entity-returned)

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Hogyan LUIS-végpontkulcsot szeretne létrehozni és hozzárendelni?
[Hozza létre a szolgáltatásszinthez szükséges](luis-how-to-azure-subscription.md) végpontkulcsot az [Azure-ban.](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) [Rendelje hozzá a kulcsot](luis-how-to-azure-subscription.md) az **[Azure-erőforrások lapon.](luis-how-to-azure-subscription.md)** Ehhez a művelethez nincs megfelelő API. Ezután módosítania kell a HTTP-kérést a végpontra, hogy [az új végpontkulcsot használja.](luis-how-to-azure-subscription.md)

### <a name="how-do-i-interpret-luis-scores"></a>Hogyan LUIS-pontszámokat?
A rendszernek a legmagasabb pontszámú szándékot kell használnia, függetlenül annak értékétől. Például egy 0,5 (kevesebb, mint 50%) alatti pontszám A nem feltétlenül jelenti azt, hogy a LUIS alacsony megbízhatósággal rendelkezik. Több betanításadat biztosítása segíthet növelni a [legvalószínűbb](luis-concept-prediction-score.md) szándék pontszámát.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Miért nem látom a végpontom találatát az alkalmazás irányítópultján?
Az alkalmazás irányítópultján található végpontok összesített találatai rendszeresen frissülnek, de a LUIS-végpontkulcshoz társított metrikák a Azure Portal gyakrabban frissülnek.

Ha nem látja a frissített végponti találatokat az irányítópulton, jelentkezzen be a Azure Portal-be, keresse  meg a LUIS-végpont kulcsához társított erőforrást, és nyissa meg a Metrikák gombra a Hívások összesen metrika **kiválasztásához.** Ha a végpontkulcsot egynél több LUIS-alkalmazáshoz használja, a Azure Portal metrika megjeleníti az összes olyan LUIS-alkalmazás hívásának összesített számát, amely azt használja.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Elérhető PowerShell-parancs a végpontkvóta kvótához?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A végpontkvóta PowerShell-paranccsal is látható:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>A LUIS-alkalmazásom tegnap működött, de ma 403-as hibákat kapok. Nem módosíttam az alkalmazást. Hogyan lehet kijavítani a hibát?
Kövesse ezeket [az utasításokat](#how-do-i-create-and-assign-a-luis-endpoint-key) egy LUIS-végpontkulcs létrehozásához és az alkalmazáshoz való hozzárendeléséhez. Ezután módosítania kell az ügyfélalkalmazás végpontra vonatkozó HTTP-kérését az [új végpontkulcs használatára.](luis-how-to-azure-subscription.md) Ha egy másik régióban hozott létre új erőforrást, módosítsa a HTTP-ügyfélkérés régióját is.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Hogyan LUIS-végpontot?
Lásd: [A végpont biztonságossá tétele.](luis-how-to-azure-subscription.md#securing-the-endpoint)

## <a name="working-within-luis-limits"></a>Munkavégzés a LUIS korlátain belül

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Legfeljebb hány szándékot és entitást támogathat egy LUIS-alkalmazás?
Lásd a [határokra vonatkozó](luis-limits.md) referenciát.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Olyan LUIS-alkalmazást szeretnék felépíteni, amely a szándékok maximális számán kívül van. Mit tegyek?

Tekintse meg [a szándékokkal kapcsolatos ajánlott eljárásokat.](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Milyen korlátok vonatkoznak a kifejezéslisták számára és méretére?
A kifejezéslista maximális hosszát [a](./luis-concept-feature.md)határokat [referenciában láthatja.](luis-limits.md)

### <a name="what-are-the-limits-on-example-utterances"></a>Milyen korlátok vonatkoznak a kimondott példaként való kimondott szövegre?
Lásd a [határokra vonatkozó](luis-limits.md) referenciát.

## <a name="testing-and-training"></a>Tesztelés és betanítás

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Az alkalmazás egyes modelljeinél hibákat látok a kötegelt tesztelési panelen. Hogyan lehet ezt a problémát megoldanom?

A hibák azt jelzik, hogy eltérés van a címkék és a modellek előrejelzései között. A probléma megoldásához tegye a következők egyikét vagy mindkettőt:
* Ha segíteni szeretne a LUIS-nak a szándékok közötti elsiklottság javításában, adjon hozzá további címkéket.
* A LUIS gyorsabb elsajátítása érdekében adjon hozzá kifejezéslista-funkciókat, amelyek tartományspecifikus szókincset mutatnak be.

Lásd a [Batch-tesztelési](./luis-how-to-batch-test.md) oktatóanyagot.

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Amikor egy alkalmazást exportál, majd újraimportál egy új alkalmazásba (új alkalmazásazonosítóval), a LUIS előrejelzési pontszámai eltérőek. Miért történik ez?

Lásd: [Előrejelzési különbségek ugyanazon alkalmazás másolatai között.](luis-concept-prediction-score.md#review-intents-with-similar-scores)

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Néhány kimondott szöveg nem a megfelelő szándékra fog vonatkozni, miután módosíttam az alkalmazásomat. Úgy tűnik, hogy a probléma véletlenszerűen eltűnik. Hogyan lehet kijavítani a hibát?

Lásd: [Betanítás az összes adattal.](luis-how-to-train.md#train-with-all-data)

## <a name="app-publishing"></a>Alkalmazás közzététele

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Mi a bérlőazonosító az "Add a key to your app" (Kulcs hozzáadása az alkalmazáshoz) ablakban?
Az Azure-ban a bérlő a szolgáltatáshoz társított ügyfelet vagy szervezetet jelöli. Keresse meg a bérlőazonosítót a Azure Portal **a Címtár-azonosító mezőben** a **Tulajdonságok kezelése Azure Active Directory**  >    >  **kiválasztásával.**

![Bérlőazonosító a Azure Portal](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Miért van több végpontkulcs rendelve az alkalmazáshoz, mint amit hozzárendeltem?
Az egyszerűség kedvéért minden LUIS-alkalmazás rendelkezik a szerzői/kezdőkulcsokkal a végpontok listájában. Ez a kulcs csak néhány végponttalálatot tesz lehetővé, így kipróbálhatja a LUIS-t.

Ha az alkalmazás már létezett a LUIS általánosan elérhetővé (GA) megelőzően, a rendszer automatikusan hozzárendeli az előfizetés LUIS-végponti kulcsait. Ez a GA-migrálás egyszerűbbé vált. A szolgáltatásban található új LUIS-végpontkulcsok Azure Portal _automatikusan hozzá_ vannak rendelve a LUIS-hoz.

## <a name="key-management"></a>Kulcskezelés

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Hogyan tudja, milyen kulcsra van szükségem, honnan kapom, és mit tegyek vele?

A [szerzői kulcs és](luis-how-to-azure-subscription.md) az előrejelzési futásidejű kulcs közötti különbségekről lásd: Előrejelzési végponti kulcsok létrehozása és lekérdezése a LUIS-ban.

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Hibaüzenetet kaptam arról, hogy nem tudok kvótát túllépni. Hogyan lehet kijavítani a hibát?

További információ: Fix HTTP status code 403 and 429 (A [403-as](#i-received-an-http-403-error-status-code-how-do-i-fix-it) és [a 429-es HTTP-állapotkód](#i-received-an-http-429-error-status-code-how-do-i-fix-it) kijavítva).

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Több végpontlekérdezésekre van szükségem. Mit kell tennem ehhez?

További információ: Fix HTTP status code 403 and 429 (A [403-as](#i-received-an-http-403-error-status-code-how-do-i-fix-it) és [a 429-es HTTP-állapotkód](#i-received-an-http-429-error-status-code-how-do-i-fix-it) kijavítva).

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Létrehoztam egy szerzői kulcsot, de az nem jelenik meg a LUIS portálon. Mi történt?

A szerzői kulcsok a szerzői kulcs felhasználói élményére való áttelepítés után érhetők el a [LUIS-portálon.](luis-migration-authoring.md)

## <a name="app-management"></a>Alkalmazáskezelés

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Hogyan a felhasználói kimondott szövegeket?
Alapértelmezés szerint a LUIS-alkalmazás a felhasználóktól származó kimondott szövegeket naplózza. A felhasználók által a LUIS-alkalmazásba küldött kimondott szövegeket napló letöltéséhez lépjen a **Saját alkalmazások,** és válassza ki az alkalmazást. A környezetfüggő eszköztáron válassza a **Végpontnaplók exportálása lehetőséget.** A napló vesszővel elválasztott értékfájlként (CSV) van formázva.

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Hogyan tiltható le a kimondott szöveg naplózása?
A felhasználói kimondott szöveg naplózását az ügyfélalkalmazás által a LUIS lekérdezéséhez használt végponti URL-cím beállításával `log=false` kapcsolhatja ki. A naplózás kikapcsolása azonban letiltja a LUIS-alkalmazás azon képességét, hogy kimondott szövegeket javasoljon, vagy javítsa az aktív [tanuláson alapuló teljesítményt.](luis-concept-review-endpoint-utterances.md#what-is-active-learning) Ha adatvédelmi megfontolások miatt be van állítva, nem töltheti le a felhasználói kimondott szövegeket a LUIS-ból, és nem használhatja ezeket a kimondott szövegeket az alkalmazás `log=false` javítására.

A naplózás a kimondott szöveg egyetlen tárolója.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Miért nem szeretném az összes végponti kimondott szövegem naplózását?
Ha a naplót előrejelzési elemzéshez használja, ne rögzítse a teszt kimondott szövegeket a naplóban.

## <a name="data-management"></a>Adatkezelés

### <a name="can-i-delete-data-from-luis"></a>Törölhetek adatokat a LUIS-ból?

* A LUIS betanításhoz használt példa kimondott szövegeket mindig törölheti. Ha töröl egy példa kimondott szövegfájlt a LUIS-alkalmazásból, az el lesz távolítva a LUIS webszolgáltatásból, és nem érhető el exportálásra.
* A LUIS által javasolt felhasználói kimondott szövegeket a Végponti  kimondott szöveg áttekintése lapon törölheti. Ha töröl kimondott szövegeket erről a listáról, nem javasolják őket, de nem törli őket a naplókból.
* Ha töröl egy fiókot, az összes alkalmazás törlődik a kimondott példaként használt szövegekkel és naplóval együtt. Az adatok 60 napig lesznek megőrizve a kiszolgálókon, mielőtt véglegesen törölve lesznek.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Hogyan kezeli a Microsoft a LUIS-nak küldött adatokat?

Az [Adatvédelmi központ](https://www.microsoft.com/trustcenter) ismerteti a kötelezettségvállalásainkat, valamint az Azure-szolgáltatásokban elérhető adatkezelési és hozzáférési lehetőségeket.

## <a name="language-and-translation-support"></a>Nyelvi és fordítási támogatás

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Van egy alkalmazásom egy nyelven, és egy párhuzamos alkalmazást szeretnék létrehozni egy másik nyelven. Mi ennek a legegyszerűbb módja?
1. Exportálja az alkalmazást.
2. Fordítsa le az exportált alkalmazás JSON-fájljában található címkével jelölt kimondott szövegeket a célnyelvre.
3. Előfordulhat, hogy módosítania kell a szándékok és entitások nevét, vagy meg kell hagynia őket.
4. Végül importálja az alkalmazást, hogy a LUIS-alkalmazás a célnyelven legyen.

## <a name="app-notification"></a>Alkalmazásértesítés

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Miért kapok olyan e-mailt, amely szerint majdnem túllépem a kvótát?
A szerzői/kezdőkulcs havonta csak 1000 végpontlekérdezés engedélyezett. Hozzon létre egy LUIS-végpontkulcsot (ingyenes vagy fizetős), és használja azt a végpontlekérdezésekhez. Ha egy robotból vagy más ügyfélalkalmazásból készít végpontlekérdezeket, ott módosítania kell a LUIS-végpontkulcsot.

## <a name="bots"></a>Robotok

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>A LUIS-robot nem működik. Mit tegyek?

Az első probléma annak elkülönítése, hogy a probléma a LUIS-hoz kapcsolódik-e, vagy a LUIS középső szoftveren kívül következik-e be.

#### <a name="resolve-issue-in-luis"></a>Probléma megoldása a LUIS-ban
Adja át ugyanazt a kimondott szövegeket a [LUIS-végpontnak.](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) Ha hibaüzenetet kap, oldja meg a problémát a LUIS-ban, amíg a hiba már nem lesz visszaadva. Gyakori hibák többek között a következők:

* `Out of call volume quota. Quota will be replenished in <time>.`– Ez a probléma azt jelzi, hogy módosítania [](luis-how-to-azure-subscription.md) kell a szerzői kulcsról egy végpontkulcsra, vagy módosítania kell a [szolgáltatási szinteket.](luis-how-to-azure-subscription.md#change-the-pricing-tier)

#### <a name="resolve-issue-in-azure-bot-service"></a>A probléma megoldása a Azure Bot Service

Ha a függvényt használja Azure Bot Service a probléma  az, hogy a teszt a webchat a értéket adja `Sorry, my bot code is having an issue` vissza, ellenőrizze a naplókat:

1. A Azure Portal a robothoz válassza a **Robotkezelés** szakaszban a **Build lehetőséget.**
1. Nyissa meg az online kódszerkesztőt.
1. A felső, kék navigációs sávon válassza ki a robot nevét (a jobb oldalon található második elemet).
1. Az eredményül kapott legördülő listában válassza a **Kudu-konzol megnyitása lehetőséget.**
1. Válassza **a LogFiles** lehetőséget, majd az **Application (Alkalmazás) lehetőséget.** Tekintse át az összes naplófájlt. Ha nem látja a hibát az alkalmazás mappájában, tekintse át az összes naplófájlt a **LogFiles alatt.**
1. Ne felejtse el újraépíteni a projektet, ha lefordított nyelvet, például C#-t használ.

> [!Tip]
> A konzol csomagokat is telepíthet.

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Hárítsa el a helyi gépen a hibakeresés közben Bot Framework.

A robotok helyi hibakeresésének további információért lásd: [Robot hibakeresése.](/azure/bot-service/bot-service-debug-bot)

## <a name="integrating-luis"></a>A LUIS integrálása

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Hol jön létre a LUIS-alkalmazásom az Azure webalkalmazás-robot előfizetési folyamata során?
Ha kiválaszt egy LUIS-sablont, és a Sablon panelen a Kijelölés gombot választja, a bal oldali panel a sablon típusának megfelelőre változik, és megkérdezi, hogy melyik régióban hozza létre a LUIS-sablont.  A webalkalmazás-robot folyamata azonban nem hoz létre LUIS-előfizetést.

![LUIS-sablon webalkalmazás-robot régiója](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Milyen LUIS-régiók támogatják a Bot Framework a beszédberesziálást?
[A beszédfelirasztás](/bot-framework/bot-service-manage-speech-priming) csak a középső (USA) példányban található LUIS-alkalmazások esetében támogatott.

## <a name="api-programming-strategies"></a>API programozási stratégiák

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Hogyan be az erőforrás LUIS-régióját?

A LUIS-mintával [programozott módon](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) találhatja meg a régiót a C# vagy a Node.Js.

## <a name="luis-service"></a>LUIS szolgáltatás

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Elérhető Language Understanding (LUIS) a helyszínen vagy magánfelhőben?

Igen, használhatja a [](luis-container-howto.md) LUIS-tárolót ezekhez a forgatókönyvekhez, ha rendelkezik a fogyasztásmérőhöz szükséges kapcsolattal.

## <a name="migrating-to-the-next-version"></a>Áttelepítés a következő verzióra

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Hogyan 3-as verziójú API előzetes verzióra?

Lásd: [API v2–v3 migrálási útmutató LUIS-alkalmazásokhoz](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>A 2019-es konferencia bejelentései

A 2019-es buildkonferencia a következő funkciókat tartalmazza:

* [A V3 API migrálási útmutatójának előzetes verziója](luis-migration-api-v3.md)
* [Továbbfejlesztett elemzési irányítópult](luis-how-to-use-dashboard.md)
* [Továbbfejlesztett előre összeállított tartományok](luis-reference-prebuilt-domains.md)
* [Dinamikus listaentitások](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Külső entitások](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Videók:

* [Az Azure Conversational AI használata az üzlet méretezése a következő generációra](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Következő lépések

A LUIS-ról az alábbi forrásokban olvashat bővebben:
* [Stack Overflow LUIS címkével ellátott kérdések](https://stackoverflow.com/questions/tagged/luis)
* [Microsoft Q&Kérdésoldal az MSDN Language Understanding Intelligent Services (LUIS) szolgáltatáshoz](/answers/topics/azure-language-understanding.html)
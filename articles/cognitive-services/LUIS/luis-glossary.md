---
title: Szószedet – LUIS
description: A Szószedet a LUIS API szolgáltatással való munka során felmerülő feltételeket ismerteti.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/08/2020
ms.openlocfilehash: 7c65c8272172cab9f5361d16141bf7b229037480
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98786944"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Nyelvi ismeretek a gyakori szószedetekről és fogalmakról
A Language Understanding (LUIS) Szószedet a LUIS szolgáltatással való munka során felmerülő feltételeket ismerteti.

## <a name="active-version"></a>Aktív verzió

Az aktív verzió az alkalmazás [verziója](luis-how-to-manage-versions.md) , amely akkor frissül, amikor a Luis portál használatával módosítja a modellt. Ha a LUIS-portálon olyan verziót szeretne módosítani, amely nem az aktív verzió, először be kell állítania az adott verziót aktívként.

## <a name="active-learning"></a>Aktív tanulás

Az aktív tanulás a gépi tanulás olyan technikája, amelyben a Machine megtanult modell használatával azonosíthatók a címkére vonatkozó, informatív új példák. A LUIS-ben az aktív tanulás arra utal, hogy hosszúságú kimondott szöveg ad hozzá a végponti forgalomból, amelynek a jelenlegi előrejelzései nem egyértelműek a modell tökéletesítése érdekében. Kattintson a "végpont hosszúságú kimondott szöveg áttekintése" elemre a hosszúságú kimondott szöveg a címkéhez való megtekintéséhez.

Lásd még:
* [Elméleti információk](luis-concept-review-endpoint-utterances.md)
* [Oktatóanyag a végpontok hosszúságú kimondott szöveg áttekintéséhez](luis-tutorial-review-endpoint-utterances.md)
* A LUIS-alkalmazás fejlesztése a [végpontok hosszúságú kimondott szöveg áttekintésével](luis-how-to-review-endpoint-utterances.md)

## <a name="application-app"></a>Alkalmazás (alkalmazás)

A LUIS, az alkalmazása vagy az alkalmazás a gépi megtanult modellek gyűjteménye, amely ugyanazon az adathalmazon alapul, és a szándékok és entitások előrejelzésére szolgál egy adott forgatókönyv esetében. Minden alkalmazás külön előrejelzési végponttal rendelkezik.

Ha egy HR-robotot készít, lehet, hogy van egy szándéka, például "a szabadság ideje", "az előnyök lekérdezése" és a "személyes adatok frissítése", valamint az egyes, egyetlen alkalmazásba csoportosított célokhoz tartozó entitások.

## <a name="authoring"></a>Tartalomkészítés

A szerző a Luis-portál vagy a szerzői API-k használatával hozhat létre, kezelhet és helyezhet üzembe egy LUIS-alkalmazást.

### <a name="authoring-key"></a>Szerzői kulcs

A szerzői [kulcs](luis-how-to-azure-subscription.md) az alkalmazás létrehozásához használatos. Üzemi szintű végponti lekérdezésekhez nem használatos. További információ: [Key Limits](luis-limits.md#key-limits).

### <a name="authoring-resource"></a>Erőforrás létrehozása

A LUIS [authoring Resource](luis-how-to-azure-subscription.md#azure-resources-for-luis) egy felügyelhető tétel, amely az Azure-on keresztül érhető el. Az erőforrás az Azure-szolgáltatáshoz kapcsolódó szerzői műveletek, képzések és közzétételi képességek elérését teszi elérhetővé. Az erőforrás a kapcsolódó Azure-szolgáltatás eléréséhez szükséges hitelesítési, engedélyezési és biztonsági adatokat tartalmaz.

Az authoring erőforrás rendelkezik egy Azure "Kind" típussal `LUIS-Authoring` .

## <a name="batch-test"></a>Batch-teszt

A Batch-tesztelés lehetővé teszi, hogy a jelenlegi LUIS alkalmazás modelljeit a felhasználói hosszúságú kimondott szöveg konzisztens és ismert tesztelési készletével érvényesítse. A Batch-teszt egy JSON- [formátumú fájlban](./luis-how-to-batch-test.md#batch-test-file)van definiálva.


Lásd még:
* [Alapelvek](./luis-how-to-batch-test.md)
* A Batch [-](luis-how-to-batch-test.md) teszt futtatása
* [Oktatóanyag](./luis-how-to-batch-test.md) – batch-teszt létrehozása és futtatása

### <a name="f-measure"></a>F-mérték

A Batch-tesztelés során a teszt pontosságának mértékét.

### <a name="false-negative-fn"></a>Hamis negatív (FN)

A Batch-tesztelés során az adatpontok olyan hosszúságú kimondott szöveg jelentenek, amelyekben az alkalmazás helytelenül jelezte a célként megadott szándék/entitás hiányát.

### <a name="false-positive-fp"></a>Hamis pozitív (FP)

A Batch-tesztelés során az adatpontok olyan hosszúságú kimondott szöveg jelentenek, amelyekben az alkalmazás helytelenül jósolta meg a cél szándék/entitás létezését.

### <a name="precision"></a>Pontosság
A kötegelt tesztelés során a pontosság (más néven pozitív prediktív érték) a megfelelő hosszúságú kimondott szöveg töredéke a beolvasott hosszúságú kimondott szöveg között.

Egy Animal batch-tesztre példa az előre jelzett juhok száma az állatok összesített száma (juh és nem juh) szerint elosztva.

### <a name="recall"></a>Recall

A Batch-tesztelés során a visszahívás (más néven érzékenység) lehetőség a LUIS általánosítása.

Egy Animal batch-tesztre példa a megjósolt juhok száma az elérhető juhok teljes száma alapján.

### <a name="true-negative-tn"></a>Igaz negatív (TN)

Igaz negatív érték, ha az alkalmazás megfelelően előre nem egyezik. A Batch-tesztelésben igaz negatív esemény következik be, ha az alkalmazás egy szándékot vagy entitást jelez egy olyan példához, amely nem lett felcímkézve az adott szándékkal vagy entitással.

### <a name="true-positive-tp"></a>Valódi pozitív (TP)

Igaz pozitív (TP) – igaz pozitív érték, amikor az alkalmazás megfelelően megjósol egy egyezést. A Batch-tesztelés során igaz pozitív esemény történik, ha az alkalmazás egy szándékot vagy entitást jelez egy olyan példához, amelyet az adott szándékkal vagy entitással címkéztek.

## <a name="classifier"></a>Osztályozó

Az osztályozó olyan gépi megtanult modell, amely azt jelzi, hogy milyen kategóriába vagy osztályba illeszkedik a bemenet.

Egy [példa egy](#intent) osztályozó példára.

## <a name="collaborator"></a>Közreműködő

Egy közreműködő fogalmilag ugyanaz, mint a [közreműködő](#contributor). A közreműködő hozzáférést kap, ha a tulajdonos hozzáadja a közreműködő e-mail-címét egy olyan alkalmazáshoz, amely nem az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC) van vezérelve. Ha továbbra is használja a közreműködőket, telepítse át a LUIS-fiókját, és a LUIS authoring Resources használatával kezelhesse a közreműködőket az Azure RBAC.

## <a name="contributor"></a>Közreműködő

A közreműködő nem [tulajdonosa](#owner) az alkalmazásnak, de ugyanazokkal az engedélyekkel rendelkezik a szándékok, az entitások és a hosszúságú kimondott szöveg hozzáadásához, szerkesztéséhez és törléséhez. A közreműködők Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) biztosítanak egy LUIS-alkalmazáshoz.

Lásd még:
* [Közreműködők hozzáadása](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource)

## <a name="descriptor"></a>Leíró

A leíró a Machine learning [szolgáltatáshoz](#features)korábban használt kifejezés.

## <a name="domain"></a>Tartomány

A LUIS kontextusban a tartomány az ismeretek területe. A tartomány a forgatókönyvre jellemző. A különböző tartományok adott nyelvet és terminológiát használnak, amelyek a tartomány kontextusában jelentenek. Ha például a zene lejátszásához készít egy alkalmazást, az alkalmazás a zenére jellemző használati feltételekkel és nyelvvel fog rendelkezni: "Song, Track, album, dalszöveg, b-Side, Artist". Tartományokra vonatkozó példák: [előre összeépített tartományok](#prebuilt-domain).

## <a name="endpoint"></a>Végpont

### <a name="authoring-endpoint"></a>Szerzői végpont

A LUIS authoring Endpoint URL-címe az alkalmazás szerzője, betanítása és közzététele. A végpont URL-címe tartalmazza a közzétett alkalmazás régióját vagy egyéni altartományát, valamint az alkalmazás AZONOSÍTÓját.

További információ az alkalmazás programozás útján történő készítéséről a [fejlesztői segédletből](developer-reference-resource.md#rest-endpoints)

### <a name="prediction-endpoint"></a>Előrejelzési végpont

A LUIS-előrejelzési végpont URL-címe a Luis-lekérdezések elküldése a [Luis-alkalmazás](#application-app) létrehozása és közzététele után. A végpont URL-címe tartalmazza a közzétett alkalmazás régióját vagy egyéni altartományát, valamint az alkalmazás AZONOSÍTÓját. A végpontot az alkalmazás Azure- **[erőforrások](luis-how-to-azure-subscription.md)** lapján találja, vagy lekérheti a végpont URL-címét az [app info](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API-ból.

Az előrejelzési végponthoz való hozzáférés engedélyezve van a LUIS-előrejelzési kulccsal.

## <a name="entity"></a>Entitás

Az [entitások](luis-concept-entity-types.md) olyan szavak a hosszúságú kimondott szöveg, amelyek a szándék teljesítésére vagy azonosítására szolgáló információkat írják le. Ha az entitás összetett, és szeretné, hogy a modell azonosítani tudja az egyes részeket, a modellt alentitásokra lehet bontani. Tegyük fel például, hogy a modellt előre jelezni szeretné egy címnek, valamint az utca, város, állam és irányítószám alentitásait is. Az entitások modellként is használhatók. A LUIS-alkalmazás válasza magában foglalja a várható szándékokat és az összes entitást is.

### <a name="entity-extractor"></a>Entitás kivonója

A kivonó entitások néha csak a kivonóként ismertek, mint a Machine known Model, amelyet a LUIS az entitások előrejelzésére használ.

### <a name="entity-schema"></a>Entitás sémája

Az entitás séma az a struktúra, amelyet a gép megtanult entitásai alentitásokkal határoz meg. Az előrejelzési végpont a sémában definiált összes kinyert entitást és alentitást adja vissza.

### <a name="entitys-subentity"></a>Entitás alentitása

Az alentitások egy gépi tanulási entitás alárendelt entitásai.

### <a name="non-machine-learning-entity"></a>Nem gépi tanulási entitás

Az adatok kinyeréséhez megfelelő szöveget használó entitás:
* Listaentitás
* Reguláriskifejezés-entitás

### <a name="list-entity"></a>Listaentitás

A [lista entitás](reference-entity-list.md) a kapcsolódó szavak rögzített, lezárt készletét jelöli a szinonimákkal együtt. Az entitások listázása pontos egyezést mutat a megmunkált megtanult entitásokkal szemben.

Az entitás akkor fog megjelenni, ha a lista entitásban szó szerepel a listában. Ha például egy "size" nevű listával rendelkezik, és a listában a "kis, közepes, nagy" szöveg szerepel, akkor a rendszer az összes olyan hosszúságú kimondott szöveg előre megjósolja a méret entitást, amelyben a környezettől függetlenül a "kis", a "közepes" vagy a "nagy" szavakat használják.

### <a name="regular-expression"></a>Reguláris kifejezés

A [reguláris kifejezés entitás](reference-entity-regular-expression.md) reguláris kifejezést jelöl. A reguláris kifejezések entitásai pontos egyezések, a megmunkált megtanult entitásokkal ellentétben.
### <a name="prebuilt-entity"></a>Előre elkészített entitás

Előre összeépített [entitáshoz](#prebuilt-entity) tartozó előre felépített modell bejegyzésének megjelenítése

## <a name="features"></a>Funkciók

A gépi tanulásban a funkció olyan jellemző, amely segít a modellnek egy adott fogalom felismerésében. Ez egy olyan tipp, amelyet a LUIS használhat, de nem kemény szabályt.

Ezt a kifejezést **[gépi tanulási szolgáltatásnak](luis-concept-feature.md)** is nevezzük.

Ezeket a mutatókat a címkékkel együtt használva megtudhatja, hogyan jósolhatja meg az új információkat. A LUIS támogatja a kifejezések listáját és más modellek használatát is.

### <a name="required-feature"></a>Szükséges funkció

Egy kötelező funkció a LUIS-modell kimenetének korlátozására szolgál. Ha egy entitás egy funkciója kötelezőként van megjelölve, akkor a funkciónak jelen kell lennie az előre jelzett entitáshoz tartozó példában, függetlenül attól, hogy mit tanult a gép a modell előrejelzésében.

Vegyünk egy példát arra, hogy egy előre elkészített számú funkcióval rendelkezzen, amelyet kötelezőként jelölt ki a mennyiségi entitásban egy menü rendezéséhez Ha a robot látja `I want a bajillion large pizzas?` , a bajillion nem lesz előre jelezve, függetlenül attól, hogy milyen kontextusban jelenik meg. A bajillion nem érvényes szám, és az előre elkészített entitások száma nem várható.

## <a name="intent"></a>Szándék

A [szándék](luis-concept-intent.md) azt a feladatot vagy műveletet jelöli, amelyet a felhasználó végre szeretne hajtani. Ez a felhasználó bemenetében kifejezett cél vagy cél, például egy járat foglalása vagy számla kifizetése. A LUIS-ben a teljes teljesség a szándék szerint van besorolva, de a lemondás részei entitásként vannak kibontva

## <a name="labeling-examples"></a>Példák címkézésére

A címkézés vagy a jelölés a pozitív vagy negatív példa modellhez való társításának folyamata.

### <a name="labeling-for-intents"></a>Szándékok címkézése
LUIS-ben az alkalmazáson belüli szándékok kölcsönösen kizárják egymást. Ez azt jelenti, hogy ha egy _kizáró_ szándékot ad hozzá, akkor az adott szándéknak _pozitív_ példája, és az összes többi szándék negatív példája. A negatív példák nem tévesztendő össze a "None" szándékkal, ami az alkalmazás hatókörén kívüli hosszúságú kimondott szöveg jelöli.

### <a name="labeling-for-entities"></a>Entitások címkézése
LUIS-ban egy szót vagy kifejezést kell [megcímkézni](label-entity-example-utterance.md) egy olyan példával, amely _pozitív_ példaként jelöli meg az entitást. A címkézés azt a szándékot mutatja, hogy mit kell előre jelezni. A címkével ellátott hosszúságú kimondott szöveg a szándék betanítására szolgál.

## <a name="luis-app"></a>LUIS-alkalmazás

Tekintse meg az [alkalmazás definícióját (alkalmazás)](#application-app).

## <a name="model"></a>Modellezés

A (gépi megtanult) modell egy olyan függvény, amely előrejelzést készít a bemeneti adatokról. A LUIS-ben a szándék-osztályozók és az entitás-kiállítók általános módon "modellként" szerepelnek, és az "alkalmazásként" együtt betanított, közzétett és lekérdezett modellek gyűjteményét nevezzük.

## <a name="normalized-value"></a>Normalizált érték

Értékeket adhat hozzá a [listához](#list-entity) tartozó entitásokhoz. Ezeknek az értékeknek a listája egy vagy több szinonimát tartalmazhat. A válaszban csak a normalizált érték lesz visszaadva.

## <a name="overfitting"></a>Túlillesztés

Ha a modell rögzítése az adott példákon történik, és nem képes jól általánosítani.

## <a name="owner"></a>Tulajdonos

Minden alkalmazás rendelkezik egy tulajdonossal, aki az alkalmazást létrehozó személy. A tulajdonos kezeli az alkalmazásra vonatkozó engedélyeket a Azure Portalban.

## <a name="phrase-list"></a>Kifejezések listája

A [kifejezések listája](luis-concept-feature.md) a gépi tanulási funkció adott típusa, amely az ugyanahhoz az osztályhoz tartozó értékek (szavak vagy kifejezések) egy csoportját tartalmazza, és hasonló módon kell kezelni (például városok vagy termékek neve).

## <a name="prebuilt-model"></a>Előre elkészített modell

Egy [előre összeépített modell](luis-concept-prebuilt-model.md) egy cél, entitás vagy gyűjtemény, amely a címkével ellátott példákkal együtt is szerepel. Ezek a közös előre összeépített modellek hozzáadhatók az alkalmazáshoz az alkalmazáshoz szükséges modell-fejlesztési munka csökkentése érdekében.

### <a name="prebuilt-domain"></a>Előre elkészített tartomány

Az előre elkészített tartomány egy adott tartományhoz, például a Home automationhez (HomeAutomation) vagy az éttermi foglalásokhoz (RestaurantReservation) konfigurált LUIS-alkalmazás. A szándékok, a hosszúságú kimondott szöveg és az entitások konfigurálva vannak ehhez a tartományhoz.

### <a name="prebuilt-entity"></a>Előre elkészített entitás

Az előre elkészített entitások a LUIS entitások olyan általános típusú információkat biztosítanak, mint például a szám, az URL-cím és az e-mail. Ezek a nyilvános adatszolgáltatások alapján hozhatók létre. Dönthet úgy is, hogy létrehoz egy előre összeépített entitást önálló entitásként vagy entitásként való szolgáltatásként

### <a name="prebuilt-intent"></a>Előre összeépített leképezés

Az előre összeépített szándék egy olyan szándék, hogy a LUIS általános típusú információkat biztosít, és a saját címkével ellátott példa hosszúságú kimondott szöveg.

## <a name="prediction"></a>Előrejelzés

A jóslat egy REST-kérelem az Azure LUIS előrejelzési szolgáltatásba, amely új adatokkal (felhasználói Kimondás) veszi fel a betanított és közzétett alkalmazást, és meghatározza, hogy a rendszer milyen szándékokat és entitásokat talál.

### <a name="prediction-key"></a>Előrejelzési kulcs

Az [előrejelzési kulcs](luis-how-to-azure-subscription.md) (korábbi nevén előfizetési kulcs) az Azure-ban létrehozott Luis szolgáltatáshoz társított kulcs, amely engedélyezi az előrejelzési végpont használatát.

Ez a kulcs nem a szerzői kulcs. Ha van egy előrejelzési végpont kulcsa, azt a szerzői kulcs helyett a végponti kérelmekhez kell használni. A jelenlegi előrejelzési kulcsot a végpont URL-címében tekintheti meg a LUIS webhely Azure-erőforrások lapjának alján. Ez az előfizetés-kulcs neve/érték párok értéke.

### <a name="prediction-resource"></a>Előrejelzési erőforrás

A LUIS-előrejelzési erőforrás egy felügyelhető tétel, amely az Azure-on keresztül érhető el. Az erőforrás az Azure-szolgáltatáshoz kapcsolódó előrejelzéshez való hozzáférés. Az erőforrás előrejelzéseket tartalmaz.

Az előrejelzési erőforrás rendelkezik egy Azure "Kind" típussal `LUIS` .

### <a name="prediction-score"></a>Előrejelzési pontszám

A [pontszám](luis-concept-prediction-score.md) egy 0 és 1 közötti szám, amely azt méri, hogy a rendszer mennyire bízik meg abban, hogy egy adott bemenet kimondása egy adott szándéknak felel meg. Az 1. számú pontszám azt jelenti, hogy a rendszer nagyon magabiztos a kimenetével kapcsolatban, és a pontszám közelebb esik a 0 értékhez, és a rendszer biztos abban, hogy a bemenet nem felel meg egy adott kimenetnek. A közepes pontszámok azt jelentik, hogy a rendszer nagyon nem biztos benne, hogyan kell dönteni.

Tegyük fel például, hogy egy olyan modellt használ, amely meghatározza, hogy egy adott ügyfél-szöveg tartalmaz-e élelmiszer-rendelést. Előfordulhat, hogy az "1. egy kávét szeretnék rendelni" (a rendszer nagyon biztos benne, hogy ez egy megrendelés), és a "csapatom a csapat nyerte el a játékot az elmúlt éjszakában" (a rendszer nagyon biztos abban, hogy ez nem megrendelés). És lehet, hogy 0,5-es pontszáma van a "Let 's have a some tea" (nem biztos abban, hogy ez egy megrendelés vagy nem).

## <a name="programmatic-key"></a>Programozott kulcs

Átnevezve [szerzői kulcsra](#authoring-key).

## <a name="publish"></a>Közzététel

A [Közzététel](luis-how-to-publish-app.md) azt jelenti, hogy a Luis aktív verziója elérhető az átmeneti vagy a termelési [végponton](#endpoint).

## <a name="quota"></a>Kvóta

A LUIS kvóta az Azure-előfizetési szintek korlátozása. A LUIS-kvótát a másodpercenkénti kérések (429-es HTTP-állapot) és a havi kérelmek teljes száma korlátozhatja (HTTP-állapot: 403).

## <a name="schema"></a>Séma

A séma tartalmazza a szándékait és entitásait, valamint az alentitásokat. A sémát kezdetben az idő múlásával megismétlik. A séma nem tartalmaz Alkalmazásbeállítások, funkciók vagy példa hosszúságú kimondott szöveg.

## <a name="sentiment-analysis"></a>Hangulatelemzés
Az érzelmek elemzése a [text Analytics](../text-analytics/overview.md)által biztosított hosszúságú kimondott szöveg pozitív vagy negatív értékeit biztosítja.

## <a name="speech-priming"></a>Beszéd alapozó

A beszédfelismerési [alapszolgáltatások](../speech-service/overview.md)javítják az adott forgatókönyvben leggyakrabban használt szóbeli szavak és kifejezések felismerését a Speech Services használatával. A beszédfelismerést engedélyező alkalmazások esetében az összes LUIS címkével ellátott példa a beszédfelismerés pontosságának növelésére szolgál egy testreszabott beszédfelismerési modell létrehozásával ehhez az adott alkalmazáshoz. Ha például egy sakk-játékban szeretné meggyőződni arról, hogy ha a felhasználó a "Knight Move" kifejezést állítja be, a rendszer nem az "áthelyezés éjszaka" kifejezést fogja használni. A LUIS alkalmazásnak tartalmaznia kell olyan példákat, amelyekben a "Knight" entitásként van megjelölve.

## <a name="starter-key"></a>Alapszintű kulcs

A LUIS használatával való első indításkor használandó ingyenes kulcs.

## <a name="synonyms"></a>Szinonimák

A LUIS- [lista entitásokban](reference-entity-list.md)normalizált értéket hozhat létre, amely a szinonimák listáját is tartalmazhatja. Ha például olyan méretű entitást hoz létre, amely kis, közepes, nagy és extra méretű normalizált értékekkel rendelkezik. A következőhöz hasonló értékek szinonimáit hozhatja létre:

|Nomalized érték| Szinonimák|
|--|--|
|Kicsi| a kis egy, 8 uncia|
|Közepes| normál, 12 uncia|
|Nagy| nagy, 16 uncia|
|Xtra – nagy| a legnagyobb, 24 uncia|

A modell az entitás normalizált értékét adja vissza, ha a bemenet valamelyik szinonimája látható.

## <a name="test"></a>Tesztelés

A LUIS-alkalmazás [tesztelése](luis-concept-test.md) a modell-előrejelzések megtekintését jelenti.

## <a name="timezone-offset"></a>Időzóna eltolása

A végpont tartalmazza a [timezoneOffset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). A datetimeV2 előre összeépített entitásból felvenni vagy eltávolítani kívánt percek száma. Ha például a Kimondás a "mi idő most?", a visszaadott datetimeV2 az ügyfél-kérelem aktuális ideje. Ha az ügyfél kérése egy olyan robotból vagy más alkalmazásból származik, amely nem egyezik meg a robot felhasználójának, akkor a bot és a felhasználó közötti eltolást kell átadnia.

Lásd: [előre elkészített datetimeV2 entitás időzóna módosítása](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Jogkivonat
A [token](luis-language-support.md#tokenization) a szöveg legkisebb egysége, amelyet a Luis képes felismerni. Ez némileg eltér a különböző nyelveken.

Az **angol nyelv** esetén a token folytonos span (szóközök vagy írásjelek nélkül) a betűkből és számokból. A szóköz nem token.

|Kifejezés|Tokenek száma|Magyarázat|
|--|--|--|
|`Dog`|1|Egyetlen szó, írásjelek és szóközök nélkül.|
|`RMT33W`|1|A rekord lokátorának száma. Előfordulhat, hogy számok és betűk is lehetnek, de nincs írásjele.|
|`425-555-5555`|5|Egy telefonszám. Minden írásjel egyetlen jogkivonat, ezért  `425-555-5555` 5 token lenne:<br>`425`<br>`-`<br>`555`<br>`-`<br>`5555` |
|`https://luis.ai`|7|`https`<br>`:`<br>`/`<br>`/`<br>`luis`<br>`.`<br>`ai`<br>|

## <a name="train"></a>Betanítás

A [képzés](luis-how-to-train.md) során a Luis tanítási folyamata az utolsó betanítás óta az aktív verzió változásairól szól.

### <a name="training-data"></a>Betanítási adatok

A betanítási adatok a modellek betanításához szükséges információk. Ebbe beletartozik a séma, a hosszúságú kimondott szöveg, a funkciók és az Alkalmazásbeállítások.

### <a name="training-errors"></a>Betanítási hibák

A betanítási hibák olyan előrejelzések a betanítási adataiban, amelyek nem felelnek meg a címkének.

## <a name="utterance"></a>Beszédelem

A [Kimondás](luis-concept-utterance.md) olyan felhasználói bevitel, amely egy adott mondat rövid szöveges képviselője egy beszélgetésben. Ez egy természetes nyelv kifejezése, mint például a "2. könyv jegyek a Seattle következő kedden". Példa hosszúságú kimondott szöveg a modell betanításához, és a modell új Kimondás esetén várható

## <a name="version"></a>Verzió

A LUIS- [verzió](luis-how-to-manage-versions.md) a Luis-alkalmazás azonosítójával és a közzétett végponttal társított Luis-alkalmazás adott példánya. Minden LUIS-alkalmazásnak legalább egy verziója van.

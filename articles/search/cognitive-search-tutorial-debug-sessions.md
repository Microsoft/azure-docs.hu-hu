---
title: 'Oktatóanyag: hibakeresési munkamenetek használata a szakértelmével javításához'
titleSuffix: Azure Cognitive Search
description: A hibakeresési munkamenetek (előzetes verzió) egy Azure Portal eszköz, amely a készségkészlet kapcsolatos problémák kereséséhez, diagnosztizálásához és javításához használatos.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/02/2021
ms.openlocfilehash: ed988baec46152d55cf63aec09fce7a298157212
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509150"
---
# <a name="tutorial-debug-a-skillset-using-debug-sessions"></a>Oktatóanyag: hibakeresési munkameneteket használó készségkészlet hibakeresése

A szakértelmével összehangolja azokat a műveleteket, amelyek a tartalom elemzését vagy átalakítását végzik, ahol egy adott képzettség kimenete lesz egy másik bemenet. Ha a bemenetek a kimenettől függenek, a készségkészlet-definíciókban és a mezők társításában szereplő hibák kimaradó műveleteket és adatokat eredményezhetnek.

A Azure Portal **hibakeresési munkamenetei** átfogó vizualizációt biztosítanak a készségkészlet. Ezzel az eszközzel részletesen megtudhatja, hogy milyen lépésekkel lehet leesni egy adott művelet.

Ebben a cikkben a **hibakeresési munkamenetek** segítségével megkeresheti és kijavíthatja a hiányzó adatokat, és 2) a kimeneti mezők leképezéseit. Az oktatóanyag teljes körű. Információkat biztosít az index (klinikai vizsgálatok adatai), a Poster-gyűjtemények számára, amely objektumokat hoz létre, valamint útmutatást nyújt a **hibakeresési munkamenetek** használatához a készségkészlet kapcsolatos problémák megkereséséhez és megoldásához.

> [!Important]
> A hibakeresési munkamenetek az előzetes verziójú szolgáltatások, amelyek szolgáltatói szerződés nélkül érhetők el, és nem ajánlottak éles számítási feladatokhoz. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt a következő előfeltételek vonatkoznak:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/).

+ Egy Azure Cognitive Search szolgáltatás. [Hozzon létre egy szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat. 

+ Egy Azure Storage-fiók [blob Storage](../storage/blobs/index.yml)szolgáltatással, amely a mintaadatok üzemeltetésére szolgál, valamint a hibakeresési munkamenet során létrehozott ideiglenes adatmennyiség megőrzése.

+ A [Poster Desktop alkalmazás](https://www.getpostman.com/) és egy [Poster-gyűjtemény](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) , amely a REST API-k használatával hoz létre objektumokat.

+ [Mintaadatok (klinikai vizsgálatok)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19).

> [!NOTE]
> Ez a rövid útmutató az AI-hoz készült [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) is használja. Mivel a számítási feladatok olyan kicsik, Cognitive Services a jelenetek mögött, akár 20 tranzakcióra is felhasználható. Ez azt jelenti, hogy ezt a gyakorlatot anélkül végezheti el, hogy további Cognitive Services erőforrást kellene létrehoznia.

## <a name="set-up-your-data"></a>Az adatai beállítása

Ez a szakasz létrehozza a minta adatkészletet az Azure Blob Storage-ban, hogy az indexelő és a készségkészlet rendelkezzen a megfelelő tartalommal.

1. Töltse le a 19 fájlból álló [mintaadatok (klinikai vizsgálatok – PDF-19)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19).

1. [Hozzon létre egy Azure Storage-fiókot](../storage/common/storage-account-create.md?tabs=azure-portal) , vagy [keressen egy meglévő fiókot](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   + Válassza ki ugyanazt a régiót, mint az Azure Cognitive Search a sávszélességgel kapcsolatos költségek elkerülése érdekében.

   + Válassza ki a StorageV2 (általános célú v2) fiók típusát.

1. Navigáljon az Azure Storage Services oldalaira a portálon, és hozzon létre egy BLOB-tárolót. Az ajánlott eljárás a "Private" hozzáférési szint megadására szolgál. Nevezze el a tárolót `clinicaltrialdataset` .

1. A tárolóban kattintson a **feltöltés** gombra, és töltse fel az első lépésben a letöltött és kibontott fájlokat.

1. A portálon lekérdezheti és mentheti az Azure Storage-hoz tartozó kapcsolódási karakterláncot. Szüksége lesz rá az adatindexelést REST API hívásokhoz. A kapcsolati karakterláncot a portál **Beállítások**  >  **hozzáférési kulcsaiból** kérheti le.

## <a name="get-a-key-and-url"></a>Kulcs és URL-cím lekérése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. A Search szolgáltatás mindkettővel jön létre, így ha az előfizetéshez hozzáadta az Azure Cognitive Searcht, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások**  >  **kulcsaiban** kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="HTTP-végpont és elérési kulcs beszerzése" border="false":::

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="create-data-source-skillset-index-and-indexer"></a>Adatforrás, készségkészlet, index és indexelő létrehozása

Ebben a szakaszban a Poster és egy megadott Gyűjtemény segítségével hozza létre a Cognitive Search adatforrást, a készségkészlet, az indexet és az indexelő. Ha még nem ismeri a Poster-t, tekintse meg [ezt a](search-get-started-rest.md)rövid útmutatót.

A feladat elvégzéséhez szüksége lesz az oktatóanyaghoz létrehozott [Poster-gyűjteményre](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) . 

1. Indítsa el a Poster-t, és importálja a gyűjteményt. A **fájlok**  >  **új** területen válassza ki az importálni kívánt gyűjteményt.
1. A gyűjtemény importálása után bontsa ki a műveletek listáját (...).
1. Kattintson a **Szerkesztés** gombra.
1. Az aktuális érték mezőben adja meg a nevét `searchService` (például ha a végpont `https://mydemo.search.windows.net` , akkor a szolgáltatás neve: " `mydemo` ").
1. Adja meg a `apiKey` keresési szolgáltatás elsődleges vagy másodlagos kulcsát.
1. Adja meg az `storageConnectionString` Azure Storage-fiók kulcsok lapját.
1. Adja meg a `containerName` Storage-fiókban létrehozott tárolót, majd kattintson a **frissítés** gombra.

   :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="a Poster változóinak szerkesztése":::

A gyűjtemény négy különböző REST-hívást tartalmaz, amelyek az oktatóanyagban használt objektumok létrehozásához használatosak.

Az első hívás létrehozza az adatforrást. `clinical-trials-ds`. A második hívás létrehozza a készségkészlet `clinical-trials-ss` . A harmadik hívás létrehozza az indexet `clinical-trials` . A negyedik és a végső hívás létrehozza az indexelő `clinical-trials-idxr` .

+ Nyissa meg az egyes kéréseket, majd a **Küldés** gombra kattintva küldje el az egyes kérelmeket a keresési szolgáltatásnak. 

Miután a gyűjtemény összes hívása befejeződött, zárjuk be a postát, és térjen vissza a Azure Portal.

## <a name="check-results-in-the-portal"></a>Eredmények keresése a portálon

A mintakód szándékosan létrehoz egy hibás indexet a készségkészlet végrehajtása során felmerülő problémák következményeként. A probléma nem tartalmaz adatvesztést. 

1. Azure Portal a keresési szolgáltatás áttekintése lapon válassza az **indexek** lapot. 
1. Válassza ki az `clinical-trials` indexet.
1. Adja meg ezt a lekérdezési karakterláncot: `$select=metadata_storage_path, organizations, locations&$count=true` az adott dokumentumok mezőinek visszaadásához (az egyedi `metadata_storage_path` mezővel azonosítva).
1. A lekérdezés futtatásához kattintson a **Keresés** gombra, és adja vissza az összes 19 dokumentumot, és jelenítse meg a "szervezetek" és a "Locations" üres értékeit.

Ezeket a mezőket a készségkészlet [entitás-felismerési képességeivel](cognitive-search-skill-entity-recognition.md)kell feltölteni, amely a blob tartalmán belül bárhol megtalálja a szervezeteket és a helyszíneket. A következő gyakorlat során a hibakeresési munkamenettel határozza meg, hogy mi volt a hiba.

A hibák és figyelmeztetések vizsgálatának másik módja a Azure Portalon keresztül történik.

1. Nyissa meg az **Indexelő** fület, és válassza a elemet `clinical-trials-idxr` .
1. Figyelje meg, hogy az indexelő feladata összességében sikeres volt, 57 figyelmeztetés történt.
1. Kattintson a **siker** gombra a figyelmeztetések megtekintéséhez (ha többnyire hibák léptek fel, a részletek hivatkozása **sikertelen** lesz). Az indexelő által kibocsátott összes figyelmeztetés hosszú listáját fogja látni.

   :::image type="content" source="media/cognitive-search-debug/portal-indexer-errors-warnings.png" alt-text="figyelmeztetések megtekintése":::

## <a name="start-your-debug-session"></a>A hibakeresési munkamenet elindítása

:::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="új hibakeresési munkamenet elindítása":::

1. A keresés áttekintése lapon kattintson a **hibakeresési munkamenetek** fülre.
1. Válassza az **+ új hibakeresési munkamenet** lehetőséget.
1. Adja meg a munkamenet nevét. 
1. Kapcsolja össze a munkamenetet a Storage-fiókkal. 
1. Az indexelő sablonban adja meg az indexelő nevét. Az indexelő az adatforrásra, a készségkészlet és az indexre mutató hivatkozásokat tartalmaz.
1. Fogadja el a gyűjtemény első dokumentumának alapértelmezett választható dokumentumát. 
1. **Mentse** a munkamenetet. A munkamenet mentése a készségkészlet által meghatározott mesterséges intelligencia-bővítési folyamat elindítását végzi.

> [!Important]
> Egy hibakeresési munkamenet csak egyetlen dokumentummal működik. Kiválaszthatja, hogy melyik dokumentumot kell hibakeresésre használni, vagy csak az elsőt használja.

<!-- > :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="New debug session started"::: -->

Ha a hibakeresési munkamenet befejezte az inicializálást, a munkamenet alapértelmezett értéke a **mesterséges intelligencia-dúsítások** lap, amely kiemeli a **skill Graphot**. A skill Graph a készségkészlet vizuális hierarchiáját, valamint a végrehajtás sorrendjét egymás után és párhuzamosan biztosítja.

## <a name="find-issues-with-the-skillset"></a>Problémák keresése a készségkészlet

Az indexelő által jelentett problémák a szomszédos **hibák/figyelmeztetések** lapon találhatók. 

Figyelje meg, hogy a **hibák/figyelmeztetések** lap sokkal kisebb listát ad meg, mint a korábban megjelenő lista, mert ez a lista csak egyetlen dokumentum hibáit részletezi. Az indexelő által megjelenített listához hasonlóan a figyelmeztető üzenetre is kattinthat, és megtekintheti a figyelmeztetés részleteit.

Az értesítések áttekintéséhez válassza a **hibák/figyelmeztetések** lehetőséget. Háromat kell látnia:

   + "Nem lehet leképezni a kimeneti mező helyét a keresési indexhez. Keresse meg az indexelő "outputFieldMappings" tulajdonságát.
Hiányzó érték: "/Document/merged_content/Locations". "

   + "A" szervezetek "kimeneti mező nem képezhető le a keresési indexre. Keresse meg az indexelő "outputFieldMappings" tulajdonságát.
Hiányzó érték: "/Document/merged_content/Organizations". "

   + "A szaktudás végrehajtása megtörtént, de nem várt eredménnyel járhat, mert egy vagy több képzettségi bemenet érvénytelen volt.
Hiányzik a választható szakértelem-bevitel. Név: "languageCode", forrás: "/document/languageCode". Kifejezés nyelvi elemzési problémái: hiányzó "/document/languageCode" érték. "

   Számos szaktudáshoz tartozik egy "languageCode" paraméter. A művelet vizsgálatával láthatja, hogy a nyelvi kód bemenete hiányzik a (z) rendszerből `Enrichment.NerSkillV2.#1` , amely ugyanaz az entitás-felismerési képesség, amely a "Locations" és a "Organizations" kimenettel kapcsolatos problémákkal is rendelkezik. 

Mivel mindhárom értesítés erre a képességre vonatkozik, a következő lépés a szakértelem hibakeresése. Ha lehetséges, először a outputFieldMapping problémákra való áttérés előtt oldja meg a bemeneti problémákat.

 :::image type="content" source="media/cognitive-search-debug/debug-session-errors-warnings.png" alt-text="Az új hibakeresési munkamenet elindult":::

<!-- + The Skill Graph provides a visual hierarchy of the skillset and its order of execution from top to bottom. Skills that are side by side in the graph are executed in parallel. Color coding of skills in the graph indicate the types of skills that are being executed in the skillset. In the example, the green skills are text and the red skill is vision. Clicking on an individual skill in the graph will display the details of that instance of the skill in the right pane of the session window. The skill settings, a JSON editor, execution details, and errors/warnings are all available for review and editing.

+ The Enriched Data Structure details the nodes in the enrichment tree generated by the skills from the source document's contents. -->

## <a name="fix-missing-skill-input-value"></a>Hiányzó szaktudás bemeneti értékének javítása

A **hibák/figyelmeztetések** lapon hiba található a címkével ellátott műveletnél `Enrichment.NerSkillV2.#1` . A hiba részletei azt ismertetik, hogy probléma merült fel a (z) "/document/languageCode" képzettségi értékkel. 

1. Térjen vissza az **AI-dúsítások** lapra.
1. Kattintson a **skill gráfra**.
1. A jobb oldali ablaktáblán kattintson a **#1ra** jelölt képességre, hogy megjelenjen a részletei.
1. Keresse meg a "languageCode" bemenetét.
1. Válassza ki a **</>** vonal elején található szimbólumot, és nyissa meg a kifejezés kiértékelését.
1. A **kiértékelés** gombra kattintva erősítse meg, hogy a kifejezés hibát eredményez. A rendszer megerősíti, hogy a "languageCode" tulajdonság nem érvényes bemenet.

   :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="Kifejezés kiértékelése":::

Ezt a hibát kétféleképpen lehet megkeresni a munkamenetben. Az első az a hely, ahol a bemenet származik – milyen képességgel kellene előállítani ezt az eredményt? A szakértelem részleteit tartalmazó ablaktábla végrehajtások lapján meg kell jelennie a bemenet forrásának. Ha nincs forrás, ez egy mező-hozzárendelési hibát jelez.

1. Kattintson a **végrehajtások** lapra.
1. Tekintse meg a BEMENETeket, és keresse meg a "languageCode" kifejezést. A megadott bemenethez nem található forrás. 
1. Váltson a bal oldali panelre a dúsított adatstruktúra megjelenítéséhez. Nincs a "languageCode" tulajdonságnak megfelelő leképezési útvonal.

   :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="Dúsított adatstruktúra":::

Van egy leképezett elérési út a "Language" kifejezéshez. Szóval, van egy elírás a képességek beállításaiban. A kifejezésnek a #1 "/Document/Language" kifejezéssel való kijavításához frissítenie kell a kifejezést.

1. Nyissa meg a kifejezés kiértékelését **</>** a "Language" elérési útra.
1. Másolja a kifejezést. Zárja be az ablakot.
1. Nyissa meg az #1 szakértelmet, és nyissa meg a kifejezés kiértékelését **</>** a "languageCode" bemenethez.
1. Illessze be a "/Document/Language" nevű új értéket a kifejezés mezőbe, és kattintson az **értékelés** gombra.
1. Meg kell jelennie az "en" helyes bemenetnek. A kifejezés frissítéséhez kattintson az Apply (alkalmaz) gombra.
1. Kattintson a **Save (Mentés** ) gombra a jobb oldalon, a szakértelem részletei panelen.
1. A munkamenet ablak menüjében kattintson a **Futtatás** elemre. Ez elindít egy másik végrehajtást a készségkészlet a dokumentum használatával. 

A hibakeresési munkamenet végrehajtásának befejeződése után kattintson a hibák/figyelmeztetések lapra, és megjeleníti, hogy a "dúsítás. NerSkillV2. #1" címkével ellátott hiba megszűnt. Vannak azonban még két figyelmeztetés arról, hogy a szolgáltatás nem tudja leképezni a szervezetekhez és helyekhez tartozó kimeneti mezőket a keresési indexhez. Hiányoznak értékek: "/Document/merged_content/Organizations" és "/Document/merged_content/Locations".

## <a name="fix-missing-skill-output-values"></a>Hiányzó szaktudás-kimeneti értékek javítása

:::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="Hibák és figyelmeztetések":::

A szakértelemből hiányzó kimeneti értékek találhatók. Ha azonosítani szeretné a hibát, ugorjon a dúsított adatstruktúrára, keresse meg az érték nevét, és tekintse meg az eredeti forrását. A hiányzó szervezetek és helyszínek értéke esetén a rendszer a skill #1ból származó kimeneteket jelenít meg. Az egyes elérési utakhoz tartozó Expression értékelő </> megnyitásakor a "/Document/Content/Organizations" és a "/Document/Content/Locations" kifejezés jelenik meg.

:::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="Expression értékelő szervezetek entitása":::

Az entitások kimenete üres, és nem lehet üres. Milyen bemenetek jönnek létre ez az eredmény?

1. Nyissa meg a **skill Graphot** , és válassza a skill #1 elemet.
1. Válassza a **végrehajtások** fület a jobb képességek részletei panelen.
1. Nyissa meg a kifejezés kiértékelését **</>** a "text" szövegben.

   :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="Szöveg-képzettség bevitele":::

A bemenet megjelenített eredménye nem hasonlít a szövegbeviteli adatokhoz. Úgy néz ki, mint egy új vonallal körülvett rendszerkép. A szöveg hiánya azt jelenti, hogy egyetlen entitás sem azonosítható. A készségkészlet hierarchiájának megtekintésekor a rendszer először a #6 (OCR) által dolgozza fel a tartalmat, majd átadja a #5 (Merge) szaktudásnak. 

1. Válassza ki a #5 (egyesítés) képességet a **skill Graphban**.
1. Válassza a **végrehajtás** fület a jobb képességek részletei panelen, és nyissa meg a kifejezés kiértékelését **</>** a "mergedText" kimenetekhez.

   :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="Kimenet egyesítési képességhez":::

Itt a szöveg párosítva van a képpel. A "/Document/merged_content" kifejezésben a "szervezetek" és a "Locations" elérési utakon található hiba látható a #1 skill számára. A "/Document/Content" helyett "/Document/merged_content"-t kell használnia a "text" bemenetekhez.

1. Másolja ki a "mergedText" kimenethez tartozó kifejezést, és zárjuk be a kifejezés értékelési ablakát.
1. Válassza a szakértelem #1 lehetőséget a **skill gráfban**.
1. Válassza ki a **készségfejlesztés** lapot a megfelelő képzettség részletei panelen.
1. Nyissa meg a kifejezés kiértékelését **</>** a "text" bemenethez.
1. Illessze be az új kifejezést a mezőbe. Kattintson a **kiértékelés** lehetőségre.
1. A hozzáadott szöveggel megjelenő helyes bemenetnek szerepelnie kell. Kattintson az **alkalmaz** gombra a képzettségi beállítások frissítéséhez.
1. Kattintson a **Save (Mentés** ) gombra a jobb oldalon, a szakértelem részletei panelen.
1. A munkamenetek ablak menüjében kattintson a **Futtatás** elemre. Ez elindít egy másik végrehajtást a készségkészlet a dokumentum használatával.

Az indexelő futásának befejeződése után a hibák még mindig vannak. Lépjen vissza a skill #1 és vizsgálja meg. A képességhez megadott bemenet a tartalomból "merged_content" értékre lett javítva. Mik az entitások kimenetei a szakértelemben?

1. Válassza az **AI-gazdagítók** fület.
1. Válassza a **skill Graph** lehetőséget, majd kattintson a skill #1 lehetőségre.
1. Keresse meg a **képességek beállításait** a "kimenetek" kereséséhez.
1. Nyissa meg a **</>** "szervezetek" entitás kifejezés-értékelőjét.

   :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="A szervezetek entitásának kimenete":::

A kifejezés eredményének kiértékelése a megfelelő eredményt adja. A szakértelem feladata az entitás, a "szervezetek" helyes értékének azonosítása. Az entitás elérési útjának kimeneti leképezése azonban továbbra is hibát jelez. A szakértelem kimeneti elérési útjának és a hiba kimeneti elérési útjának összevetésével a/Document/Content csomópontban lévő kimeneteket, szervezeteket és helyeket szülő képességgel. Míg a kimeneti mező leképezése az eredmények szülővé tételét várja a/Document/merged_content csomópont alatt. Az előző lépésben a bevitel "/Document/Content" értékről "/Document/merged_content" értékre változott. A szaktudás beállításainak környezetét módosítani kell annak érdekében, hogy a kimenet a megfelelő kontextussal legyen létrehozva.

1. Válassza az **AI-gazdagítók** fület.
1. Válassza a **skill Graph** lehetőséget, majd kattintson a skill #1 lehetőségre.
1. A "kontextus" kifejezésre navigálva keresse meg a **képességek beállításait** .
1. Kattintson duplán a "Context" beállításra, és szerkessze a "/Document/merged_content" kifejezésre.
1. Kattintson a **Save (Mentés** ) gombra a jobb oldalon, a szakértelem részletei panelen.
1. A munkamenetek ablak menüjében kattintson a **Futtatás** elemre. Ez elindít egy másik végrehajtást a készségkészlet a dokumentum használatával.

   :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="Környezeti javítás a szaktudás beállításakor":::

Az összes hiba megoldódott.

## <a name="commit-changes-to-the-skillset"></a>Módosítások elvégzése a készségkészlet

A hibakeresési munkamenet elindítását követően a keresési szolgáltatás létrehozta a készségkészlet egy példányát. Ez azért történt, hogy megvédje a keresési szolgáltatás eredeti készségkészlet. Most, hogy befejezte a készségkészlet hibakeresését, a javításokat véglegesítheti (felülírhatja az eredeti készségkészlet). 

Ha nem áll készen a módosítások elvégzésére, mentheti a hibakeresési munkamenetet, és később újra megnyithatja.

1. Kattintson a **módosítások véglegesítve** gombra a fő hibakeresési munkamenetek menüben.
1. Az **OK** gombra kattintva erősítse meg, hogy szeretné frissíteni a készségkészlet.
1. Zárjuk be a hibakeresési munkamenetet, és válassza ki az **Indexelő** fület.
1. Nyissa meg a "klinikai kísérletek – idxr".
1. Kattintson az **Alaphelyzetbe állítás** gombra.
1. Kattintson a **Futtatás** elemre. A megerősítéshez kattintson **az OK** gombra.

Ha az indexelő befejezte a futást, akkor a futtatási **Előzmények** lapon a legutóbbi Futtatás időbélyegzője mellett egy zöld pipa és a szó sikeres lesz. A módosítások alkalmazása érdekében:

1. A keresés áttekintése lapon válassza a **tárgymutató** lapot.
1. Nyissa meg a "klinikai kísérletek" indexet, és a keresési Explorer lapon adja meg a következő lekérdezési karakterláncot: `$select=metadata_storage_path, organizations, locations&$count=true` adott dokumentumok mezőinek visszaadásához (az egyedi mező alapján azonosítva `metadata_storage_path` ).
1. Kattintson a **Keresés** gombra.

Az eredményeknek meg kell mutatniuk, hogy a szervezetek és a helyszínek már a várt értékekkel vannak feltöltve.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag a készségkészlet-definíciók és-feldolgozás különböző szempontjait érinti. A fogalmakkal és munkafolyamatokkal kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

+ [Készségkészlet kimeneti mezőinek leképezése a keresési index mezőire](cognitive-search-output-field-mapping.md)

+ [Szakértelmével az Azure Cognitive Search](cognitive-search-working-with-skillsets.md)

+ [A növekményes bővítés gyorsítótárazásának konfigurálása](cognitive-search-incremental-indexing-conceptual.md)
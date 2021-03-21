---
title: Példány áthelyezése másik Azure-régióba
titleSuffix: Azure Digital Twins
description: 'Lásd: Azure Digital Twins-példány áthelyezése egyik Azure-régióból a másikba.'
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: e268cca87479625af023b5970bb27c56721f6d39
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049848"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Azure digitális Twins-példány áthelyezése egy másik Azure-régióba

Ha át kell helyeznie az Azure Digital Twins-példányt az egyik régióból a másikba, a jelenlegi folyamat az új régióban újra létrehozza az erőforrásokat, majd törli az eredeti erőforrásokat. A folyamat végén egy új, az elsővel azonos Azure Digital Twins-példánnyal fog dolgozni, kivéve a frissített helyet.

Ez a cikk útmutatást nyújt arról, hogyan végezheti el a teljes áthelyezést és a másolást, amire szüksége lehet, hogy az új példány megfeleljen az eredetinek.

Ez a folyamat a következő lépéseket tartalmazza:

1. Felkészülés: töltse le az eredeti modelleket, az ikreket és a Graphot.
1. Áthelyezés: hozzon létre egy új Azure Digital Twins-példányt egy új régióban.
1. Move: feltölti az új Azure Digital Twins-példányt.
    - Töltse fel az eredeti modelleket, az ikreket és a gráfot.
    - Végpontok és útvonalak újbóli létrehozása.
    - Csatlakoztatott erőforrások újracsatolása.
1. Forrás erőforrásainak karbantartása: törölje az eredeti példányt.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt megpróbálja újból létrehozni az Azure Digital Twins-példányt, folytassa az eredeti példány összetevőivel, hogy egyértelmű képet kapjon az összes olyan darabról, amelyet újra létre kell hozni.

Célszerű megfontolni többek között a következő kérdéseket:

* Milyen *modellek* vannak feltöltve a saját példányba? Hány van?
* Milyen *ikrek* szerepelnek a saját példányban? Hány van?
* Mi a példában szereplő *gráf* általános alakja? Hány kapcsolat létezik?
* Milyen *végpontok* vannak a saját példányban?
* Milyen *útvonalakat* használhatok a saját példányban? Vannak szűrők?
* Hol kapcsolódnak a példányok *más Azure-szolgáltatásokhoz*? Néhány gyakori integrációs pont a következőkre terjed ki:

    - Azure Event Grid, Azure Event Hubs vagy Azure Service Bus
    - Azure Functions
    - Azure Logic Apps
    - Azure Time Series Insights
    - Azure Maps
    - Azure IoT Hub Device Provisioning Service
* Milyen más *személyes vagy vállalati alkalmazások* csatlakoznak a saját példányhoz?

Ezeket az információkat a [Azure Portal](https://portal.azure.com), az [Azure digitális Twins API-](how-to-use-apis-sdks.md)k, az SDK-k, az [Azure digitális Twins CLI-parancsok](how-to-use-cli.md)vagy az [Azure digitális Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) -minta használatával gyűjthetheti össze.

## <a name="prepare"></a>Előkészítés

Ebben a szakaszban elő fogja készíteni a példány újbóli létrehozását, ha az eredeti példányból letölti az eredeti modelleket, az ikreket és a gráfot. Ez a cikk az [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) -mintát használja ehhez a feladathoz.

>[!NOTE]
>Előfordulhat, hogy már rendelkezik olyan fájlokkal, amelyek tartalmazzák a modelleket vagy a diagramot a példányban. Ha így van, nem kell újra letöltenie az összes adatot – csak a hiányzó darabokat vagy olyan dolgokat, amelyek megváltoztak, mivel eredetileg feltöltötte ezeket a fájlokat. Előfordulhat például, hogy az ikrek új adattal frissültek.

### <a name="limitations-of-azure-digital-twins-explorer"></a>Az Azure Digital Twins Explorer korlátai

Az [Azure Digital Twins Explorer minta](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) egy olyan ügyfélalkalmazás-minta, amely támogatja a gráf vizuális ábrázolását, és vizuális interakciót biztosít a példánnyal. Ez a cikk bemutatja, hogyan használhatja azt a letöltéshez, majd később újrafeltöltheti a modelleket, az ikreket és a diagramokat.

Ez a minta nem egy teljes eszköz. Nem tesztelték a hangsúlyt, és nem készült nagy méretű gráfok kezelésére. Ebből kifolyólag ne feledje, hogy a következő beépített mintákra vonatkozó korlátozások érvényesek:

* A minta jelenleg csak a 1 000-es és a 2 000-es kapcsolattal rendelkező gráf méretekben lett tesztelve.
* A minta nem támogatja az újrapróbálkozást bármilyen időszakos hiba esetén.
* A minta nem feltétlenül értesíti a felhasználót, ha a feltöltött adatfeldolgozás hiányos.
* A minta nem kezeli a nagyon nagy gráfokból származó hibákat, amelyek túllépik a rendelkezésre álló erőforrásokat, például a memóriát.

Ha a minta nem tudja kezelni a gráf méretét, exportálhatja és importálhatja a gráfot más Azure Digital Twins fejlesztői eszközök használatával:

* [Azure digitális Twins CLI-parancsok](how-to-use-cli.md)
* [Azure digitális Twins API-k és SDK-k](how-to-use-apis-sdks.md)

### <a name="set-up-the-azure-digital-twins-explorer-application"></a>Az Azure digitális Twins Explorer alkalmazásának beállítása

Az Azure Digital Twins Explorer futtatásához először töltse le a minta alkalmazás kódját, és állítsa be úgy, hogy az a gépen fusson.

A minta beszerzéséhez tekintse meg a következőt: [Azure digitális Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). A **zip letöltése** gombra kattintva letöltheti a mintakód egy. zip fájlját a gépre **Azure_Digital_Twins__ADT__explorer.zip**. Bontsa ki a fájlt.

Ezután állítsa be és konfigurálja az Azure Digital Twins Explorer engedélyeit. Kövesse az Azure digitális Twins és az Azure digitális Twins [-kezelő beállítása](quickstart-adt-explorer.md#set-up-azure-digital-twins-and-azure-digital-twins-explorer) című szakasz útmutatását. Ez a szakasz végigvezeti a következő lépéseken:

1. Azure digitális Twins-példány beállítása. Ezt a részt kihagyhatja, mert már van példánya.
1. Helyi Azure-beli hitelesítő adatok beállítása a példányhoz való hozzáférés biztosításához.
1. Futtassa az Azure digitális Twins Explorer alkalmazást, és konfigurálja úgy, hogy a példányhoz kapcsolódjon. Az Ön által áthelyezett eredeti Azure Digital Twins-példány *állomásneve* lesz használatban.

Most az Azure-beli digitális Twins Explorer-alkalmazásnak kell futnia egy böngészőben a gépen. A mintát az eredeti Azure Digital Twins-példányhoz kell csatlakoztatni.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="A böngészőablakban futó alkalmazást bemutató böngészőablak: 3000. Az alkalmazás neve Azure Digital Twins Explorer, és a lekérdezési Explorer, a modell nézet, a Graph View és a Property Explorer mezőinek tartalmaz. Még nincsenek megjeleníthető adathalmazok." lightbox="media/how-to-move-regions/explorer-blank.png":::

A kapcsolat ellenőrzéséhez kattintson a **lekérdezés futtatása** gombra, és futtassa az alapértelmezett lekérdezést, amely az összes ikreket és kapcsolatot megjeleníti a Graph **Explorer** mezőben.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Az ablak jobb felső sarkában lévő lekérdezés futtatása gomb ki van emelve." lightbox="media/how-to-move-regions/run-query.png":::

Az Azure Digital Twins Explorer futtatása nem végezhető el, mert a cikk későbbi részében később újra fel fogja tölteni ezeket az elemeket az új példányba a célhelyen.

### <a name="download-models-twins-and-graph"></a>Modellek, ikrek és Graph letöltése

Ezután töltse le a modelleket, az ikreket és a gráfot a megoldásában a gépre.

Az összes elem egyszerre való letöltéséhez először ellenőrizze, hogy a teljes gráf megjelenik-e a **Graph nézet** mezőben. Ha a teljes gráf nem jelenik meg, futtassa újra a `SELECT * FROM digitaltwins` **lekérdezési Explorer** alapértelmezett lekérdezését.
 
Ezután válassza a Graph (diagram) **nézet** **Exportálás gráf** ikonját.

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="A gráf nézet mezőben egy ikon van kiemelve. Egy felhőből lefelé mutató nyilat mutat be." lightbox="media/how-to-move-regions/export-graph.png":::

Ez a művelet engedélyezi a **letöltési** hivatkozást a **Graph nézet** mezőben. Válassza ki a lekérdezési eredmény JSON-alapú megjelenítésének letöltéséhez, amely tartalmazza a modelleket, az ikreket és a kapcsolatokat. A műveletnek egy. JSON fájlt kell letöltenie a gépre.

>[!NOTE]
>Ha a letöltött fájl más kiterjesztésű, akkor próbálja meg közvetlenül szerkeszteni a bővítményt, és módosítsa a. JSON fájlba.

## <a name="move"></a>Áthelyezés

A következő lépés a példány áthelyezésének befejezése egy új példány létrehozásával a céltartományban. Ezután feltölti az adatokat és összetevőket az eredeti példányból.

### <a name="create-a-new-instance"></a>Új példány létrehozása

Először hozzon létre egy új Azure Digital Twins-példányt a megcélzott régióban. Kövesse a [útmutató: példány és hitelesítés beállítása](how-to-set-up-instance-portal.md)című témakör lépéseit. Tartsa szem előtt az alábbi mutatókat:

* Megtarthatja az új példány nevét is, *Ha* egy másik erőforráscsoporthoz van. Ha ugyanazt az erőforráscsoportot kell használnia, amely az eredeti példányt tartalmazza, akkor az új példánynak saját nevet kell adnia.
* Ha a rendszer kéri, adja meg az új célhelyet.

A lépés befejezése után szüksége lesz az új példány állomásneve, hogy továbbra is beállítsa az adatait. Ha nem jegyezte fel a gazdagép nevét a telepítés során, kövesse az [alábbi utasításokat](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) , hogy most már a Azure Portal.

### <a name="repopulate-the-old-instance"></a>A régi példány feltöltése

Ezután állítsa be az új példányt úgy, hogy az az eredeti másolata legyen.

#### <a name="upload-the-original-models-twins-and-graph-by-using-azure-digital-twins-explorer"></a>Az eredeti modellek, ikrek és gráf feltöltése az Azure Digital Twins Explorer használatával

Ebben a szakaszban újra feltöltheti a modelleket, az ikreket és a Graphot az új példányra. Ha nem rendelkezik modellekkel, ikrekkel vagy gráfokkal az eredeti példányban, vagy nem szeretné áthelyezni őket az új példányra, ugorjon a [következő szakaszra](#re-create-endpoints-and-routes).

Ellenkező esetben térjen vissza az Azure Digital Twins Explorer böngészőt futtató böngészőablakba, és kövesse az alábbi lépéseket.

##### <a name="connect-to-the-new-instance"></a>Kapcsolódás az új példányhoz

Jelenleg az Azure digitális Twins Explorer csatlakozik az eredeti Azure Digital Twins-példányhoz. Az ablak jobb felső sarkában lévő **Bejelentkezés** gombra kattintva váltson úgy, hogy az új példányra mutasson.

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="Az Azure Digital Twins Explorer kiemeli a bejelentkezési ikont az ablak jobb felső sarkában. Az ikon a kulcs sziluettjét ábrázoló személy egyszerű sziluettjét jeleníti meg." lightbox="media/how-to-move-regions/sign-in.png":::

Cserélje le a **ADT URL-címét** az új példánynak megfelelően. Módosítsa ezt az értéket úgy, hogy az beolvassa a *https://{új példány állomásneve} nevet*.

Válassza a **Kapcsolódás** lehetőséget. Előfordulhat, hogy az Azure-beli hitelesítő adataival újra be kell jelentkeznie, vagy meg kell adnia az alkalmazásnak a példányhoz való jóváhagyását.

##### <a name="upload-models-twins-and-graph"></a>Modellek, ikrek és gráfok feltöltése

Ezután töltse fel az új példányra korábban letöltött megoldás-összetevőket.

Ha fel szeretné tölteni a modelleket, az ikreket és a Graphot, jelölje be a Graph (gráf) **nézet** diagramjának **importálása** ikonját. Ezzel a beállítással egyszerre mindhárom összetevőt feltöltheti. Még a gráfban jelenleg nem használt modelleket is feltölti.

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="A gráf nézet mezőben egy ikon van kiemelve. Egy felhőbe mutató nyilat mutat be." lightbox="media/how-to-move-regions/import-graph.png":::

A fájl kiválasztása mezőben válassza a letöltött gráfot. Válassza ki a Graph **. JSON** fájlt, és kattintson a **Megnyitás** gombra.

Néhány másodperc elteltével az Azure Digital Twins Explorer egy olyan **importálási** nézetet nyit meg, amely a betöltendő gráf előnézetét jeleníti meg.

A gráf feltöltésének megerősítéséhez kattintson a **Save (Mentés** ) ikonra a **Graph nézet** jobb felső sarkában.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="A Save (Mentés) ikon kiemelése a gráf betekintő ablaktábláján." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Az Azure Digital Twins Explorer most feltölti a modelleket és a diagramot (beleértve az ikreket és a kapcsolatokat) az új Azure Digital Twins-példányra. Megjelenik egy sikeres üzenet, amely azt mutatja, hogy hány modell, ikrek és kapcsolat lett feltöltve.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="A Graph importálás sikerességét jelző párbeszédpanel. A sikeres importálást olvassa be. 2 modell importálva. 4 ikrek importálva. 2 kapcsolat importálva. &quot;" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Annak ellenőrzéséhez, hogy minden feltöltés sikeresen befejeződött-e, kattintson a **lekérdezés futtatása** gombra a **Graph Explorerben** , és futtassa az alapértelmezett lekérdezést, amely az összes ikreket és kapcsolatot megjeleníti a gráfban. Ez a művelet a **modell nézet** mezőben lévő modellek listáját is frissíti.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Az ablak jobb felső sarkában található lekérdezés futtatása gomb kiemelése." lightbox="media/how-to-move-regions/run-query.png":::

Meg kell jelennie a gráfnak a **Graph Explorerben** megjelenő összes ikrek és kapcsolat között. Emellett a **modell nézet** mezőben is megjelennek a modellek.

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Az Azure Digital Twins Explorer egyik nézete, amely két, a modell nézetében Kiemelt modellt és egy, a Graph Explorerben Kiemelt modellt mutat be." lightbox="media/how-to-move-regions/post-upload.png":::

Ezek a nézetek megerősítik, hogy a modelleket, az ikreket és a gráfot újra feltöltötték a célként megadott régióban lévő új példányra.

#### <a name="re-create-endpoints-and-routes"></a>Végpontok és útvonalak újbóli létrehozása

Ha az eredeti példányban vannak végpontok vagy útvonalak, újra létre kell hoznia őket az új példányban. Ha nem rendelkezik végpontokkal vagy útvonalakkal az eredeti példányban, vagy nem szeretné őket áthelyezni az új példányra, ugorjon a [következő szakaszra](#relink-connected-resources).

Ellenkező esetben kövesse a [útmutató: végpontok és útvonalak kezelése](how-to-manage-routes-portal.md) az új példány használatával című témakör lépéseit. Tartsa szem előtt az alábbi mutatókat:

* *Nem* kell újból létrehoznia a végponthoz használt Event Grid, Event Hubs vagy Service Bus erőforrást. További információkért lásd a végponti utasítások "Előfeltételek" című szakaszát. Most újra létre kell hoznia a végpontot az Azure Digital Twins-példányon.
* A végpontok és az útvonalak nevei újra felhasználhatók, mert egy másik példányra vannak hatókörük.
* Ne felejtse el hozzáadni a szükséges szűrőket a létrehozott útvonalakhoz.

#### <a name="relink-connected-resources"></a>Csatlakoztatott erőforrások újracsatolása

Ha más alkalmazásokkal vagy Azure-erőforrásokkal rendelkezik, amelyek az eredeti Azure Digital Twins-példányhoz csatlakoznak, akkor a kapcsolatot úgy kell szerkesztenie, hogy az új példányhoz jussanak. Ezek az erőforrások tartalmazhatnak más Azure-szolgáltatásokat, illetve személyes vagy vállalati alkalmazásokat, amelyeket az Azure digitális Twins-mel való együttműködésre állított be.

Ha nincs más erőforrás csatlakoztatva az eredeti példányhoz, vagy nem szeretné őket áthelyezni az új példányra, ugorjon a [következő szakaszra](#verify).

Ellenkező esetben vegye figyelembe a kapcsolódó erőforrásokat a forgatókönyvben. Nincs szükség a csatlakoztatott erőforrások törlésére és újbóli létrehozására. Ehelyett csak szerkesztenie kell azokat a pontokat, amelyeken egy Azure digitális Twins-példányhoz csatlakoznak az állomásnévn keresztül. Ezután frissítse ezeket a pontokat, hogy az eredeti helyett az új példány állomásneve legyen használatban.

A szerkesztéshez szükséges pontos erőforrások a forgatókönyvtől függenek, de íme néhány gyakori integrációs pont:

* Azure Functions. Ha van olyan Azure-függvénye, amelynek kódjában szerepel az eredeti példány állomásneve, frissítse ezt az értéket az új példány állomásneve, és tegye közzé újra a függvényt.
* Event Grid, Event Hubs vagy Service Bus.
* Logic Apps.
* Time Series Insights.
* Azure Maps.
* IoT Hub Device Provisioning Service.
* Az Azure-on kívüli személyes vagy vállalati alkalmazások, például az [oktatóanyagban létrehozott ügyfélalkalmazás: egy ügyfélalkalmazás](tutorial-code.md)létrehozása, amely csatlakozik a példányhoz, és meghívja az Azure Digital Twins API-kat.
* Az Azure AD-alkalmazások regisztrációit *nem* kell újból létrehozni. Ha az Azure Digital Twins API-khoz való kapcsolódáshoz [alkalmazás-regisztrációt](how-to-create-app-registration.md) használ, újra felhasználhatja ugyanazt az alkalmazást az új példánnyal.

Miután befejezte ezt a lépést, az új példánynak a célként megadott régióban kell lennie az eredeti példány másolatának.

## <a name="verify"></a>Ellenőrzés

Az új példány helyes beállításának ellenőrzéséhez használja a következő eszközöket:

* [Azure Portal](https://portal.azure.com). A portál kiválóan alkalmas arra, hogy ellenőrizze, hogy az új példány létezik-e, és hogy a megfelelő célcsoportban van-e. Emellett a végpontok és útvonalak, valamint más Azure-szolgáltatások kapcsolatainak ellenőrzésére is jó.
* [Azure digitális Twins CLI-parancsok](how-to-use-cli.md). Ezekkel a parancsokkal ellenőrizheti, hogy az új példány létezik-e, és hogy a megfelelő célcsoportban van-e. Emellett a példányok adatellenőrzéséhez is használhatók.
* [Azure digitális ikrek Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Az Azure digitális Twins Explorer kiválóan alkalmas a példányok, például modellek, ikrek és diagramok ellenőrzéséhez.
* [Azure digitális Twins API-k és SDK-](how-to-use-apis-sdks.md)k. Ezek az erőforrások kiválóan alkalmasak a példányok, például modellek, ikrek és diagramok ellenőrzéséhez. Emellett megfelelőek a végpontok és útvonalak ellenőrzéséhez.

Az eredeti példánnyal futtatott egyéni alkalmazásokat vagy végpontok közötti folyamatokat is futtathatja, hogy segítsen ellenőrizni, hogy megfelelően működnek-e az új példánnyal.

## <a name="clean-up-source-resources"></a>Forrás erőforrásainak törlése

Most, hogy az új példánya be van állítva a megcélzott régióban az eredeti példány adatai és kapcsolatainak másolatával, törölheti az eredeti példányt.

Használhatja a [Azure Portal](https://portal.azure.com), az [Azure CLI](how-to-use-cli.md)-t vagy a [vezérlési sík API-kat](how-to-use-apis-sdks.md#overview-control-plane-apis).

A példánynak a Azure Portal használatával történő törléséhez [nyissa meg a portált](https://portal.azure.com) egy böngészőablakban, és az eredeti Azure Digital Twins-példányra kattintva keresse meg a portál keresési sávjában található nevet.

Kattintson a **Törlés** gombra, és kövesse az utasításokat a törlés befejezéséhez.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Tekintse meg az Azure Digital Twins-példány részleteit a Azure Portal az Áttekintés lapon. A törlés gomb ki van emelve.":::
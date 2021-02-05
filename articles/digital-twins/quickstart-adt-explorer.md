---
title: Rövid útmutató – példa a forgatókönyvre
titleSuffix: Azure Digital Twins
description: Rövid útmutató – a ADT Explorer-minta használatával megjelenítheti és feltárhatja az előre elkészített forgatókönyvet.
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: 9d09bca246938f972a212f7ee71f03a618e16ac4
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575684"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>Gyors útmutató – a minta Azure digitális Twins-forgatókönyvek megismerése a ADT Explorer használatával

Az Azure Digital Twins segítségével valós környezetei élő modelljeit hozhat létre és kezelhet. Először *digitális Twins*-ként modellezheti az egyes elemeket. Ezután csatlakoztassuk azokat egy *olyan tudásbázishoz* , amely reagálni tud az élő eseményekre, és információkat kér le.

Ebben a rövid útmutatóban egy előre elkészített Azure Digital Twins gráfot fog felfedezni, amely az [Azure Digital Twins (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)nevű minta alkalmazás segítségével készült. A ADT Explorer használatával:

- Egy környezet digitális ábrázolásának feltöltése.
- Megtekintheti az ikrek és a gráf vizuális képeit, amelyek az Azure Digital Twins környezetének ábrázolására lettek létrehozva.
- Más felügyeleti tevékenységeket egy böngészőalapú, vizuális felülettel végezhet el.

A rövid útmutató a következő főbb lépéseket tartalmazza:

1. Hozzon létre egy Azure digitális Twins-példányt és egy ADT Explorert.
1. Előre összeépített modellek és gráf-adatok feltöltése a minta forgatókönyv létrehozásához.
1. Fedezze fel a létrehozott forgatókönyv-diagramot.
1. Módosítsa a diagramot.

A példában szereplő gráf egy két emeleten és két szobából álló épületet képvisel. A gráf a következő képhez hasonlóan fog kinézni:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Egy négy kör alakú csomópontból álló gráf nézete. A &quot;Floor1&quot; címkével ellátott kör egy &quot;Room1&quot; címkével ellátott nyíllal van összekötve. A &quot;Floor0&quot; címkével ellátott kör egy &quot;Room0&quot; címkével ellátott nyíllal van összekötve. A &quot;Floor1&quot; és a &quot;Floor0&quot; nincs csatlakoztatva.":::

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez szüksége lesz egy Azure-előfizetésre. Ha még nem rendelkezik ilyennel, [hozzon létre egyet most ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

A gépen **Node.js** is szüksége lesz. A legújabb verzió beszerzéséhez lásd: [Node.js](https://nodejs.org/).

Végezetül le kell töltenie azt a mintát, amelyet a rövid útmutatóban is használni fog. A minta alkalmazás **ADT Explorer**. Ez a minta tartalmazza a rövid útmutatóban használt alkalmazást egy Azure digitális Twins-forgatókönyv betöltéséhez és megismeréséhez. Emellett tartalmazza a minta forgatókönyvek fájljait is. A minta beszerzéséhez nyissa meg az [Azure Digital Twins (ADT) Explorert](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Kattintson a **zip letöltése** gombra a mintakód egy. zip fájljának a gépre való letöltéséhez. Bontsa ki a **Azure_Digital_Twins__ADT__explorer.zip** mappát, és bontsa ki a fájlokat.

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>Az Azure digitális Twins és a ADT Explorer beállítása

Az Azure Digital Twins használatának első lépése egy Azure digitális Twins-példány beállítása. Miután létrehozta a szolgáltatás egy példányát, és beállította a hitelesítő adatait a ADT Explorerben való hitelesítéshez, csatlakozhat a ADT Explorerben található példányhoz, és feltöltheti azt a rövid útmutatóban szereplő adatokkal.

A szakasz további részében végigvezeti a lépéseket.

### <a name="set-up-an-azure-digital-twins-instance"></a>Azure digitális Twins-példány beállítása

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>Helyi Azure-beli hitelesítő adatok beállítása

A ADT Explorer alkalmazás a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (a könyvtár részét képező `Azure.Identity` ) használatával hitelesíti a felhasználókat az Azure Digital Twins-példánnyal, amikor futtatja azt a helyi gépen. További információ az ügyfélalkalmazások az Azure digitális Twins használatával történő hitelesítésének különböző módjairól: az [alkalmazás-hitelesítési kód írása](how-to-authenticate-client.md).

Ilyen típusú hitelesítés esetén a ADT Explorer a helyi környezetben, például egy helyi [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) -ben, illetve a Visual Studióban vagy a Visual Studio Code-ban keresi a hitelesítő adatokat. Ebből kifolyólag *helyileg kell bejelentkeznie az Azure* -ba ezen mechanizmusok egyikével a ADT Explorer alkalmazás hitelesítő adatainak beállításához.

Ha már bejelentkezett az Azure-ba az egyik ilyen módon, ugorjon a [következő szakaszra](#run-and-configure-adt-explorer).

Ellenkező esetben a helyi Azure CLI-t a következő lépésekkel telepítheti:

1. Kövesse a [telepítési hivatkozás](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) folyamatát az operációs rendszerének megfelelő telepítés befejezéséhez.
1. Nyisson meg egy konzolablak ablakot a gépen.
1. Futtassa `az login` a parancsot, és kövesse a hitelesítési kéréseket, hogy bejelentkezzen az Azure-fiókjába.
1. Utolsó lépés: Ha a fiókban több Azure-előfizetést is használ, állítsa be a hitelesítési környezetet az Azure-beli digitális Twins-példányt tartalmazó Azure-előfizetésre a futtatásával `az account set --subscription "<your-subscription-name-or-ID>"` (vagy az előfizetés nevének vagy azonosítójának értéke).

A bejelentkezést követően a ADT Explorer automatikusan felveszi az Azure-beli hitelesítő adatait, amikor a következő szakaszban futtatja.

Ha szeretné, a hitelesítési konzol ablakát is lezárhatja. Másik lehetőségként megnyithatja a következő lépésben való használatát.

### <a name="run-and-configure-adt-explorer"></a>A ADT Explorer futtatása és konfigurálása

Ezután futtassa a ADT Explorer alkalmazást, és konfigurálja az Azure Digital Twins-példányhoz.

1. Nyissa meg a letöltött és kibontott **Azure_Digital_Twins__ADT__explorer** mappát.
Nyisson meg egy konzol ablakot a mappa helyére **Azure_Digital_Twins__ADT__explorer/Client/src**.

1. A futtatásával `npm install` töltse le az összes szükséges függőséget.

1. Indítsa el az alkalmazást a futtatásával `npm run start` .

   Néhány másodperc elteltével megnyílik egy böngészőablak, és megjelenik az alkalmazás a böngészőben.

   :::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="A böngészőablakban futó alkalmazást bemutató böngészőablak: 3000. Az alkalmazás neve ADT Explorer, amely a Query Explorer, a Model View, a Graph View és a Property Explorer mezőihez tartalmaz mezőket. Még nincsenek megjeleníthető adathalmazok." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

1. Az ablak jobb felső sarkában kattintson a **Bejelentkezés** gombra, ahogy az az alábbi ábrán is látható, a ADT Explorer konfigurálásával a beállított példánnyal dolgozhat.

   :::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="ADT Explorer – a bejelentkezési ikon kiemelése az ablak tetején. Az ikon a kulcs sziluettjét ábrázoló személy egyszerű sziluettjét jeleníti meg." lightbox="media/quickstart-adt-explorer/sign-in.png":::

1. Adja meg az Azure Digital Twins-példány [beállítása](#set-up-an-azure-digital-twins-instance) szakaszban korábban összegyűjtött Azure digitális Twins-példány URL-címét, a *https://{instance Host Name}* formátumban.

>[!NOTE]
> Bármikor újra megtekintheti vagy szerkesztheti ezeket az adatokat. Ehhez jelölje ki ugyanazt az ikont, ha újra megnyitja a **bejelentkezési** mezőt. A rendszer megtartja a beadott értékeket.

> [!TIP]
> Ha a `SignalRService.subscribe` csatlakozáskor hibaüzenet jelenik meg, győződjön meg arról, hogy az Azure digitális Twins URL-címe a *https://*-vel kezdődik.

Ha megjelenik a Microsoft előugró ablakát **kérő engedély** , adja meg az alkalmazás jóváhagyását, és fogadja el a folytatást.

## <a name="add-the-sample-data"></a>A mintaadatok hozzáadása

Ezután importálja a minta forgatókönyvet és a Graphot a ADT Explorerben. A minta forgatókönyv a korábban letöltött **Azure_Digital_Twins__ADT__explorer** mappában is található.

### <a name="models"></a>Modellek

Az Azure digitális ikrek megoldásának első lépéseként meg kell határozni a környezet szókincsét. Egyéni [modelleket](concepts-models.md) hozhat létre, amelyek leírják a környezetben létező entitások típusait.

Minden modell olyan nyelven íródott, mint például a JSON-LD Digital Twin Definition Language (DTDL) néven. Mindegyik modell egyetlen típusú entitást ír le *tulajdonságai*, *telemetria*, *kapcsolatai* és *összetevői* tekintetében. Később ezeket a modelleket fogja használni a digitális ikrek alapjaként, amely az ilyen típusú példányokat jelképezi.

A modell létrehozásakor általában három lépést kell végrehajtania:

1. Írja be a modell definícióját. A rövid útmutatóban ez a lépés már a minta megoldás részeként is megtörténik.
1. Ellenőrizze, hogy a szintaxis helyes-e. A rövid útmutatóban ez a lépés már a minta megoldás részeként is megtörténik.
1. Töltse fel az Azure Digital Twins-példányba.
 
Ebben a rövid útmutatóban a modell fájljait már írt és érvényesítjük Önnek. Ezek a letöltött megoldás részét képezik. Ebben a szakaszban két előre megírt modellt fog feltölteni a példányra egy építési környezet ezen összetevőinek definiálásához:

* Floor
* Szoba

#### <a name="upload-models"></a>Modellek feltöltése

Modellek feltöltéséhez kövesse az alábbi lépéseket.

1. A **modell nézet** mezőben válassza a **modell feltöltése** ikont.

   :::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="A modell nézet mezőben a középső ikon ki van emelve. Egy felhőbe mutató nyilat mutat be." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. A megjelenő fájlszűrő mezőben lépjen a letöltött adattár **Azure_Digital_Twins__ADT__explorer/Client/examples** mappájába.
1. Válassza a **Room.jsbe** és **Floor.js** be lehetőséget, majd kattintson **az OK gombra**. Ha szeretné, további modelleket is feltölthet, de ez a rövid útmutatóban nem használható.
1. Kövesse az előugró párbeszédpanelt, amely arra kéri, hogy jelentkezzen be az Azure-fiókjába.

>[!NOTE]
>Ha a következő hibaüzenet jelenik meg: :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="egy előugró ablakban a &quot;hiba: hiba történt a modellek beolvasása: ClientAuthError: hiba a felugró ablak megnyitásakor. Ez akkor fordulhat elő, ha az IE-t használja, vagy ha az előugró ablakok le vannak tiltva a böngészőben. &quot;egy Bezárás gomb alul." border="false"::: 
> Próbálja meg letiltani az előugró ablakok blokkolása vagy egy másik böngésző használatával.

A ADT Explorer most feltölti ezeket a modell-fájlokat az Azure Digital Twins-példányba. Ekkor meg kell jelennie a **modell nézet** mezőben, és a felhasználóbarát nevüket és a teljes modell azonosítóit kell megjeleníteni. Kiválaszthatja a **modell** információinak megtekintése ikont a mögöttes DTDL-kód megtekintéséhez.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="A Model View (modell nézet) mező nézete két, a padlón (dtmi: Floor; 1) és a Room (dtmi: példa: Room; 1) felsorolt modell-definícióval. A modell információinak megtekintése ikon, amely egy kör &quot;i&quot; betűjét jeleníti meg az egyes modellekhez." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Ikrek és a Twin gráf

Most, hogy egyes modellek fel lettek töltve az Azure Digital Twins-példányba, hozzáadhat [digitális ikreket](concepts-twins-graph.md) , amelyek követik a modell definícióit.

A digitális ikrek az üzleti környezetében lévő tényleges entitásokat jelölik. Olyan dolgok lehetnek, mint az érzékelők a farmon, a fények egy autóban, vagy – ebben a rövid útmutatóban – a szobák az épület emeletén. Az adott típusú típusok közül több is létrehozható, például több, a *szoba* modelljét használó szoba. A kapcsolatokat a teljes környezetet jelképező *kettős gráfhoz* kapcsolja.

Ebben a szakaszban feltölti az előlétrehozott ikreket, amelyek egy előnézeti gráfhoz csatlakoznak. A gráf két emeletet és két szobát tartalmaz, amelyek a következő elrendezésben vannak csatlakoztatva:

* Floor0
    - Room0 tartalmaz
* Floor1
    - Room1 tartalmaz

#### <a name="import-the-graph"></a>A gráf importálása

A gráf importálásához kövesse az alábbi lépéseket.

1. A **gráf nézet** mezőben válassza a **diagram importálása** ikont.

   :::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="A gráf nézet mezőben egy ikon van kiemelve. Egy felhőbe mutató nyilat mutat be." lightbox="media/quickstart-adt-explorer/import-graph.png":::

2. A fájl kiválasztása mezőben lépjen a **Azure_Digital_Twins__ADT__explorer/Client/examples** mappára, és válassza ki a **buildingScenario.xlsx** számolótábla-fájlt. Ez a fájl tartalmazza a minta gráf leírását. Válassza az **OK** lehetőséget.

   Néhány másodperc elteltével a ADT Explorer egy olyan **importálási** nézetet nyit meg, amely a betöltendő gráf előnézetét jeleníti meg.

3. A gráf feltöltésének megerősítéséhez kattintson a **Save (Mentés** ) ikonra a **Graph nézet** jobb felső sarkában.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="A Save (Mentés) ikon kiemelése a gráf betekintő ablaktábláján." lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. A ADT Explorer most a feltöltött fájllal hozza létre a kért ikreket és kapcsolatokat. Ekkor megjelenik egy párbeszédpanel, ha elkészült. Válassza a **Bezárás** gombot.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="A gráfok importálásának sikerességét jelző párbeszédpanel. A sikeres importálást olvassa be. 4 ikrek importálva. 2 kapcsolat importálva. &quot;" lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. A gráf már fel lett töltve a ADT Explorerben. A gráf megjelenítéséhez kattintson a **lekérdezés futtatása** gombra a **Graph EXPLORERBEN** , a ADT Explorer ablakának tetején.

   :::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="Az ablak jobb felső sarkában található lekérdezés futtatása gomb ki van emelve." lightbox="media/quickstart-adt-explorer/run-query.png":::

Ez a művelet futtatja az alapértelmezett lekérdezést az összes digitális ikrek kiválasztásához és megjelenítéséhez. A ADT Explorer az összes ikreket és kapcsolatot lekéri a szolgáltatásból. A **diagram nézet** mezőben az általuk definiált gráfot rajzolja meg.

## <a name="explore-the-graph"></a>A gráf megismerése

Most már láthatja a minta forgatókönyv feltöltött gráfját.

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="A Graph nézetet tartalmazó mező nézete, amely egy dupla gráfon belül található. A &quot;floor1&quot; címkével ellátott kör egy &quot;room1&quot; címkével ellátott nyíllal van összekötve. A &quot;floor0&quot; címkével ellátott kör egy &quot;room0&quot; címkével ellátott nyíllal van összekötve.":::

A körök (gráf "csomópontok") a digitális ikreket jelölik. A sorok a kapcsolatokat jelölik. A **Floor0** Twin **Room0** tartalmaz, a **Floor1** Twin pedig **Room1** tartalmaz.

Ha egeret használ, húzással áthelyezheti a gráf darabjait.

### <a name="view-twin-properties"></a>Dupla tulajdonságok megtekintése

A Twin elem kiválasztásával megtekintheti a tulajdonságok és azok értékeinek listáját a **Property Explorer** mezőben.

A Room0 tulajdonságai:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Jelölje ki a Properties (Room0) tulajdonságot, amely a következő tulajdonságokat tartalmazza: Room0 (többek között $dtId), egy 70-es hőmérsékleti mező, valamint egy 30-as páratartalom-mező." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

A Room0 70-as hőmérséklettel rendelkezik.

A Room1 tulajdonságai:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Jelölje ki a Properties (Tulajdonságok) párbeszédpanelt, amely a Room1 tulajdonságait mutatja (többek között) a Room1 $dtId mezőjét, a 80 hőmérsékleti mezőjét és a 60-es páratartalom-mezőt." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

A Room1 80-as hőmérséklettel rendelkezik.

### <a name="query-the-graph"></a>A gráf lekérdezése

Az Azure Digital Twins egyik fő funkciója, hogy könnyen és hatékonyan [lekérdezheti](concepts-query-language.md) a Twin Graphot a környezettel kapcsolatos kérdések megválaszolásához.

Az ikrek a gráfban való lekérdezésének egyik módja a *tulajdonságai*. A tulajdonságok alapján történő lekérdezés a különböző kérdések megválaszolásához nyújt segítséget. Előfordulhat például, hogy olyan kiugró értékeket talál a környezetben, amelyekre figyelmet igényel.

Ebben a szakaszban egy lekérdezést fog futtatni, amely arra a kérdésre ad választ, hogy hány ikrek a környezetében 75 fölötti hőmérséklettel rendelkezik.

A válasz megtekintéséhez futtassa a következő lekérdezést a **query Explorerben** .

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

A korábbi tulajdonságok megtekintésének visszahívása, hogy a Room0 a 70-as hőmérséklettel rendelkezik, és a Room1 hőmérséklete 80. Emiatt csak a Room1 jelennek meg az eredmények között.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="A tulajdonság-lekérdezés eredményei csak a Room1 jelenítik meg." lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> Más összehasonlító operátorok (<, >, = vagy! =) is támogatottak az előző lekérdezésben. Megpróbálhatja csatlakoztatni ezeket a kezelőket, a különböző értékeket vagy a különböző Twin tulajdonságokat a lekérdezésbe, hogy kipróbálja a saját kérdéseinek megválaszolását.

## <a name="edit-data-in-the-graph"></a>A gráfban lévő adatszerkesztés

A ADT Explorer segítségével szerkesztheti a gráfban ábrázolt ikrek tulajdonságait. Ebben a szakaszban a Room0 hőmérsékletét 76-ra emeljük.

Az indításhoz válassza a **Room0** lehetőséget a tulajdonságok listájának megjelenítéséhez a **Property Explorer** mezőben.

A lista tulajdonságai szerkeszthető. Az új érték beírásának engedélyezéséhez válassza a **70** hőmérséklet-értéket. Adja meg a **76** értéket, és válassza a **Mentés** ikont a hőmérséklet **76**-re való frissítéséhez.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="A Room0 tulajdonságait megjelenítő Property Explorer-ablak. A hőmérséklet értéke egy 76-es szerkeszthető mező, amely a mentés ikon körül látható." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Most megjelenik egy **javítási információ** ablak, ahol a javítási kód megjelenik, amely a háttérben az Azure Digital Twins [API](how-to-use-apis-sdks.md) -kkal lett használva, hogy elvégezze a frissítést. Válassza a **Bezárás** gombot.

### <a name="query-to-see-the-result"></a>Az eredmény megjelenítésének lekérdezése

Annak ellenőrzéséhez, hogy a gráf sikeresen regisztrálta-e a frissítést a Room0 hőmérsékletére, futtassa újra a lekérdezést a korábbi verzióról, hogy az összes, az 75-as hőmérsékletű, az összes ikrek a környezetben legyen.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

Most, hogy a Room0 hőmérséklete 70 – 76 értékre változott, mindkét ikreknek meg kell jelennie az eredményben.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="A Room0 és a Room1 egyaránt megjelenítő tulajdonság-lekérdezés eredményei." lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>A contextualize áttekintése és megismerése

Ebben a rövid útmutatóban létrehozott egy Azure Digital Twins-példányt, csatlakoztatta azt a ADT Explorerrel, és feltölti azt egy minta forgatókönyvvel.

Ezután megvizsgálta a Graphot:

* Lekérdezés használata a forgatókönyvre vonatkozó kérdés megválaszolásához.
* Egy tulajdonság szerkesztése egy digitális Twin-ben.
* A lekérdezés ismételt futtatásával megtekintheti, hogy a válasz Hogyan módosult a frissítés eredményeként.

Ennek a gyakorlatnak a célja, hogy bemutassa, hogyan használhatja az Azure digitális Twins gráfot a környezettel kapcsolatos kérdések megválaszolására, még akkor is, ha a környezet folyamatosan változik.

Ebben a rövid útmutatóban manuálisan hajtotta végre a hőmérséklet-frissítést. Gyakoriak az Azure Digital Twins-ben, hogy a digitális ikreket a valódi IoT-eszközökhöz csatlakoztassák, hogy a telemetria-információk alapján automatikusan megkapják a frissítéseket. Így létrehozhat egy élő gráfot, amely mindig tükrözi a környezete valódi állapotát. A lekérdezésekkel valós időben kaphat információt arról, hogy mi történik a környezetben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutatóhoz tartozó munka becsomagolásához először fejezze be a futó konzol alkalmazást. Ez a művelet leállítja a ADT Explorer alkalmazáshoz való kapcsolódást a böngészőben. Többé nem fogja tudni megtekinteni az élő adatmegjelenítést a böngészőben. A böngésző fület lezárhatja.

Ezután kiválaszthatja, hogy mely erőforrásokat szeretné eltávolítani, attól függően, hogy mit szeretne tenni.

* **Ha azt tervezi, hogy továbbra is az Azure digitális Twins-oktatóanyagokat** használja, a rövid útmutatóban szereplő példányt újra felhasználhatja a cikkekhez, és nem kell eltávolítania.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Érdemes törölni a Project mappát is a helyi gépről.

## <a name="next-steps"></a>Következő lépések

Ezután folytassa az Azure digitális Twins-oktatóanyagokkal, és készítse elő saját Azure digitális Twins-forgatókönyvét és interakciós eszközeit.

> [!div class="nextstepaction"]
> [Oktatóanyag: ügyfélalkalmazás kódolása](tutorial-code.md)
---
title: Rövid útmutató – Mintaforgatókönyv megismerés
titleSuffix: Azure Digital Twins
description: Rövid útmutató – A Azure Digital Twins Explorer minta segítségével előre összeállított forgatókönyvet vizualizálhat és vizsgálhat meg.
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: adafbdb903c9ba827e8ccf56890c69ee2ce4eaf5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495177"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-azure-digital-twins-explorer"></a>Rövid útmutató – Mintaforgatókönyv Azure Digital Twins a Azure Digital Twins Explorer

A Azure Digital Twins valós környezetek élő modelljeit hozhatja létre és használhatja. Az egyes elemeket először digital **twins-ként modelle.** Ezután összekapcsolja őket egy **tudásgráfhoz,** amely képes élő eseményekre válaszolni, és információkat lekérdezni.

Ebben a rövid útmutatóban egy előre összeállított Azure Digital Twins egy nevű mintaalkalmazással ismerkedhet meg [Azure Digital Twins Explorer.](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) A következő Azure Digital Twins Explorer használhatja:

- A környezet digitális ábrázolása feltöltése.
- Tekintse meg az ikereszközök és a gráf vizuális képeit, amelyek a környezetnek a Azure Digital Twins.
- Más felügyeleti tevékenységek végrehajtása böngészőalapú, vizuális felhasználói élményben.

A rövid útmutató a következő főbb lépéseket tartalmazza:

1. Állítson be egy Azure Digital Twins- és Azure Digital Twins Explorer.
1. Előre összeállított modellek és gráfadatok feltöltése a mintaforgatókönyv felépítéséhez.
1. Tekintse át a létrehozott forgatókönyv-diagramot.
1. Módosításokat kell eszközlni a gráfon.

A mintagráf, ahol dolgozni fog, egy kétszintes és kétszobás épületre ábrázol. A diagram az alábbi képen láthatóhoz hasonló lesz:

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="Egy négy, nyilakkal összekapcsolt kör alakú csomópontból készült gráf nézete. A &quot;Floor1&quot; (1. padló) feliratú körhöz egy &quot;contains&quot; (tartalmaz) feliratú nyíl csatlakozik a &quot;Room1&quot; (Helyiség1) címkével jelölt körhöz. A &quot;Floor0&quot; (Padló0) feliratú körhöz egy &quot;contains&quot; (tartalmaz) feliratú nyíl csatlakozik a &quot;Room0&quot; (Helyiség0) feliratú körhöz. A Floor1 és a Floor0 nincs csatlakoztatva.":::

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató befejezéséhez Szüksége lesz egy Azure-előfizetésre. Ha még nem rendelkezik ilyen fiókokkal, [hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

A gépre **Node.js** is szüksége lesz. A legújabb verzióért lásd: [Node.js. ](https://nodejs.org/)

Végül le kell töltenie a rövid útmutató során használt mintát is. A mintaalkalmazás a következő **Azure Digital Twins Explorer:**. Ez a minta tartalmazza a rövid útmutatóban használt alkalmazást egy új forgatókönyv betöltéséhez és Azure Digital Twins feltárására. A mintaforgatókönyv fájljait is tartalmazza. A minta lekért útjára [a](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)Azure Digital Twins Explorer. Válassza a **Cím alatti Kód** tallózása gombot, amellyel a minta GitHub-adattárába érhetők el. A minta **letöltéséhez** kattintson a Kód gombra, majd a **ZIP** letöltése *gombra. ZIP-fájl.* 

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/download-repo-zip.png" alt-text="Képernyőkép a digital-twins-explorer adattárról a GitHubon. A Kód gomb van kiválasztva, így egy kis párbeszédpanel jelenik meg, ahol a ZIP letöltése gomb ki van emelve." lightbox="media/quickstart-azure-digital-twins-explorer/download-repo-zip.png":::

Csomagolja ki **adigital-twins-explorer-main.zip,** és bontsa ki a fájlokat.

## <a name="set-up-azure-digital-twins-and-azure-digital-twins-explorer"></a>A Azure Digital Twins és Azure Digital Twins Explorer

A Azure Digital Twins első lépése egy Azure Digital Twins beállítása. Miután létrehozott egy szolgáltatáspéldányt, és beállította hitelesítő adatait az Azure Digital Twins Explorer-val való hitelesítéshez, csatlakozhat a példányhoz az Azure Digital Twins Explorer-ban, és feltöltheti a rövid útmutató későbbi, példaadatokkal.

A szakasz további részében végigvezetjük ezeken a lépéseken.

### <a name="set-up-an-azure-digital-twins-instance"></a>Új példány Azure Digital Twins beállítása

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>Helyi Azure-beli hitelesítő adatok beállítása

A Azure Digital Twins Explorer alkalmazás a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (a kódtár része) segítségével hitelesíti a felhasználókat a Azure Digital Twins-példányban, amikor futtatja azt a `Azure.Identity` helyi gépen. Az ügyfélalkalmazások hitelesítésének különböző módjairól az Alkalmazáshitelesítési kód írása Azure Digital Twins tartalmaz [további információt.](how-to-authenticate-client.md)

Ilyen típusú hitelesítés esetén a Azure Digital Twins Explorer a hitelesítő adatokat keresi a helyi környezetben, például egy Azure-bejelentkezést egy helyi [Azure CLI-be,](/cli/azure/install-azure-cli) vagy egy Visual Studio vagy Visual Studio Code-ban. Ezért az alábbi  mechanizmusok egyikének használatával helyileg kell bejelentkeznie az Azure-ba, hogy beállítsa a hitelesítő adatokat a Azure Digital Twins Explorer alkalmazáshoz.

Ha már be van jelentkezve az Azure-ba ezen módszerek egyikének használatával, ugorjon a [következő szakaszra.](#run-and-configure-azure-digital-twins-explorer)

Ellenkező esetben az alábbi lépésekkel telepítheti a helyi Azure CLI-t:

1. Kövesse a telepítési [hivatkozásnál található folyamatot](/cli/azure/install-azure-cli) az operációs rendszernek megfelelő telepítés befejezéséhez.
1. Nyisson meg egy konzolablakot a gépen.
1. Futtassa a parancsot, és kövesse a hitelesítési `az login` utasításokat az Azure-fiókba való bejelentkezéshez.
1. Utolsó lehetséges lépés: Ha több Azure-előfizetést használ ebben a fiókban, állítsa a hitelesítési környezetet az Azure Digital Twins-példányt tartalmazó Azure-előfizetésre a futtatásával (vagy az előfizetés neve vagy azonosítója `az account set --subscription "<your-subscription-name-or-ID>"` fog működni).

Miután bejelentkezik, Azure Digital Twins Explorer automatikusan át kell vennie az Azure-beli hitelesítő adatokat, amikor a következő szakaszban futtatja.

Ha szeretné, bezárhatja a hitelesítési konzol ablakát. Azt is meg is őrizheti, hogy a következő lépésben használni tudja.

### <a name="run-and-configure-azure-digital-twins-explorer"></a>Futtatás és konfigurálás Azure Digital Twins Explorer

Ezután futtassa a Azure Digital Twins Explorer alkalmazást, és konfigurálja a Azure Digital Twins számára.

1. Ugrás a letöltött és kicsomagolt **digital-twins-explorer-main mappára.**
Nyisson meg egy konzolablakot a **digital-twins-explorer-main/client/src mappához.**

1. Az `npm install` összes szükséges függőség letöltéséhez futtassa a következőt: .

1. Indítsa el az alkalmazást a `npm run start` futtatásával.

   Néhány másodperc elteltével megnyílik egy böngészőablak, és az alkalmazás megjelenik a böngészőben.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/explorer-blank.png" alt-text="A localhost:3000-ben futó alkalmazást megjelenítő böngészőablak. Az alkalmazás neve Azure Digital Twins Explorer, és a Lekérdezéskezelő, a Modell nézet, a Gráfnézet és a Tulajdonságkezelő mezőket tartalmazza. A képernyőn még nincsenek adatok." lightbox="media/quickstart-azure-digital-twins-explorer/explorer-blank.png":::

1. Az **ablak** jobb felső sarkában található Bejelentkezés gombra kattintva konfigurálja a Azure Digital Twins Explorer a beállított példányhoz.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/sign-in.png" alt-text="Azure Digital Twins Explorer a Bejelentkezés ikont az ablak tetején. Az ikon egy kulcssilomóval átfedő személy egyszerű silhouettját mutatja." lightbox="media/quickstart-azure-digital-twins-explorer/sign-in.png":::

1. Adja meg Azure Digital Twins példány korábban összegyűjtött URL-címét a [Azure Digital Twins-példány](#set-up-an-azure-digital-twins-instance) beállítása szakaszban *https://<instance-host-name>.*

> [!TIP]
> Ha a csatlakozáskor hibaüzenet jelenik meg, győződjön meg arról, hogy a Azure Digital Twins URL-címe a `SignalRService.subscribe` következővel *kezdődik https://.*
>
> Ha hitelesítési hiba jelenik meg, ellenőrizze  a környezeti változókat, és győződjön meg arról, hogy a megadott hitelesítő adatok érvényesek a Azure Digital Twins. A hitelesítési típusokon megadott sorrendben történő hitelesítésre tett kísérleteket és a környezeti változókat `DefaultAzureCredential` először a rendszer értékeli ki. [](/dotnet/api/overview/azure/identity-readme#defaultazurecredential)

Ha megjelenik a **MicrosoftTól** kért engedélyek előugró ablak, adja meg az alkalmazás beleegyezését, és fogadja el a folytatást.

>[!NOTE]
> Bármikor újra megnyithatja vagy szerkesztheti ezt az információt, ha ugyanazt az ikont választva újra megnyitja **a Bejelentkezés** mezőt. Megtartja a megadott értékeket.

## <a name="add-the-sample-data"></a>Mintaadatok hozzáadása

Ezután importálja a mintaforgatókönyvet és a gráfot a Azure Digital Twins Explorer. A mintaforgatókönyv a korábban letöltött **digital-twins-explorer-main** mappában is megtalálható.

### <a name="models"></a>Modellek

Egy új Azure Digital Twins első lépése a környezet szókincsének meghatározása. Egyéni modelleket [fog](concepts-models.md) létrehozni, amelyek leírják a környezetben meglévő entitástípusokat.

Minden modell egy Digital Twin Definition Language (DTDL) nevű nyelven íródott, például JSON-LD nyelven. Minden modell egyetlen entitástípust ír le a **tulajdonságaik,** telemetriák, kapcsolatok és összetevők **tekintetében.**  Később ezeket a modelleket fogja használni a digital twins alapjaként, amelyek az ilyen típusok adott példányait képviselik.

Modell létrehozásakor általában három lépést kell végrehajtania:

1. Írja meg a modell definícióját. A rövid útmutatóban ez a lépés már megtörtént a mintamegoldás részeként.
1. Ellenőrizze, hogy a szintaxis pontos-e. A rövid útmutatóban ez a lépés már megtörtént a mintamegoldás részeként.
1. Töltse fel az Azure Digital Twins-példányra.
 
Ebben a rövid útmutatóban a modellfájlokat már megírták és ellenőrizték. A letöltött megoldás tartalmazza őket. Ebben a szakaszban két előre megírt modellt fog feltölteni a példányba egy épületkörnyezet ezen összetevőinek meghatározásához:

* Floor
* Szoba

#### <a name="upload-models"></a>Modellek feltöltése

Kövesse az alábbi lépéseket a modellek feltöltéshez.

1. A **MODELLNÉZET mezőben** válassza **a Modell feltöltése ikont.**

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/upload-model.png" alt-text="A Modellnézet mezőben a középső ikon van kiemelve. Egy felhőre mutató nyilat mutat." lightbox="media/quickstart-azure-digital-twins-explorer/upload-model.png":::
 
1. A megjelenő fájlválasztó mezőben válassza a **digital-twins-explorer-main/client/examples** mappát a letöltött adattárban.
1. Válassza **Room.jsbe és** Floor.jsa **lehetőséget,** majd kattintson az **OK gombra.** Ha szeretné, további modelleket is feltölthet, de ebben a rövid útmutatóban nem használjuk őket.
1. Kövesse az előugró párbeszédpanelt, amely arra kéri, hogy jelentkezzen be az Azure-fiókjába.

>[!NOTE]
>Ha a következő hibaüzenetet látja: "Hiba: Hiba: Hiba a modellek beolvasása során: ClientAuthError: Hiba az előugró ablak :::image type="content" source="media/quickstart-azure-digital-twins-explorer/error-models-popup.png" alt-text="megnyitásakor&quot; üzenet jelenik meg. Ez akkor fordulhat elő," border="false"::: ha IE-t használ, vagy ha az előugró ablakok le vannak tiltva a böngészőben." üzenet a lap alján található Bezárás gombbal. 
> Próbálja meg letiltani az előugróablak-blokkolót, vagy egy másik böngészőt használni.

Azure Digital Twins Explorer feltölti ezeket a modellfájlokat a Azure Digital Twins példányba. Meg kell jelenni a **MODELLNÉZET** mezőben, és meg kell jelenni a rövid neveik és a teljes modell-nevük. A Modellinformációk megtekintése **ikonok** kiválasztásával megtekintheti a mögöttes DTDL-kódot.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/model-info.png" alt-text="A Modellnézet mező nézete két modelldefinícióval, a Floor (dtmi:example:Floor;1) és a Room (dtmi:example:Room;1) alatt. Az egyes modelleknél ki van emelve a Modell információinak megtekintése ikon, amely egy &quot;i&quot; betűt mutat egy körben." lightbox="media/quickstart-azure-digital-twins-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Az ikereket és az ikergráfot

Most, hogy feltöltött néhány modellt a Azure Digital Twins-példányra, [](concepts-twins-graph.md) hozzáadhat a modelldefiníciókat követő digitális ikereket.

A digitális ikereszköz az üzleti környezeten belüli tényleges entitásokat képviseli. Ezek például egy farm érzékelői, egy autó világítása, vagy – ebben a rövid útmutatóban – egy épületen található helyiségek. Számos ikereszközt hozhat létre egy adott modelltípusból, például több helyiséget, amelyek mind a Room *modellt használják.* A kapcsolatokat egy ikergráfhoz **csatlakoztathatja,** amely a teljes környezetet jelképezi.

Ebben a szakaszban előrelétrehozott ikereket fog feltölteni, amelyek egy előrelétrehozott gráfhoz vannak csatlakoztatva. A diagram két emeletet és két helyiséget tartalmaz, amelyek a következő elrendezésben vannak összekapcsolva:

* Floor0
    - A Room0 teremben található
* 1. szint
    - A Room1 helyiséget tartalmazza

#### <a name="import-the-graph"></a>A gráf importálása

Kövesse az alábbi lépéseket a gráf importáláshoz.

1. A **GRÁFNÉZET mezőben** válassza a **Gráf importálása ikont.**

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-graph.png" alt-text="A Gráfnézet mezőben egy ikon van kiemelve. Egy felhőre mutató nyíl jelenik meg." lightbox="media/quickstart-azure-digital-twins-explorer/import-graph.png":::

2. A fájlválasztó mezőben válassza ki a **digital-twins-explorer-main/client/examples**  mappát, és válassza kibuildingScenario.xlsxtáblázatfájlt. Ez a fájl tartalmazza a mintagráf leírását. Válassza az **OK** lehetőséget.

   Néhány másodperc elteltével a Azure Digital Twins Explorer megnyit egy **Importálás** nézetet, amely megjeleníti a betölthető gráf előnézetét.

3. A gráf feltöltésének megerősítéséhez válassza a **MENTÉS** ikont a GRAPH VIEW mező jobb **felső sarkában.**

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png" alt-text="A Mentés ikon kiemelése a Gráf előnézete panelen." lightbox="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. Azure Digital Twins Explorer a feltöltött fájllal hozza létre a kért ikereket és a köztük található kapcsolatokat. Ha elkészült, megjelenik egy párbeszédpanel. Válassza a **Bezárás** gombot.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-success.png" alt-text="Egy párbeszédpanel, amely azt jelzi, hogy a gráf importálása sikeres volt. A következőt olvassa: &quot;Sikeres importálás. 4 iker importálása. 2 kapcsolat importálva.&quot;" lightbox="media/quickstart-azure-digital-twins-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. A gráf most már fel van töltve a Azure Digital Twins Explorer. A gráfot úgy  láthatja, hogy a gráfablak tetején, a **GRAPH EXPLORER** mezőben a Lekérdezés Azure Digital Twins Explorer kattint.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/run-query.png" alt-text="Az ablak jobb felső sarkában lévő Lekérdezés futtatása gomb ki van emelve." lightbox="media/quickstart-azure-digital-twins-explorer/run-query.png":::

Ez a művelet futtatja az alapértelmezett lekérdezést az összes digitális iker kiválasztásához és megjelenítéséhez. Azure Digital Twins Explorer lekéri az összes ikereszközt és kapcsolatot a szolgáltatásból. A GRÁFNÉZET mezőben megrajzolja az által definiált **gráfot.**

## <a name="explore-the-graph"></a>A gráf megismerés

Most már láthatja a mintaforgatókönyv feltöltött diagramját.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="A Gráfnézet mező nézete, benne egy ikergráfgal. A &quot;floor1&quot; (1. padló) feliratú körhöz egy &quot;contains&quot; (tartalmaz) feliratú nyíl csatlakozik a &quot;room1&quot; (helyiség1) címkével jelölt körhöz. A &quot;floor0&quot; (padló) feliratú körhöz egy &quot;contains&quot; (tartalmaz) feliratú nyíl csatlakozik a &quot;room0&quot; (helyiség0) címkével jelölt körhöz.":::

A körök (gráf "csomópontok") a digitális ikereket ábrázolják. A vonalak kapcsolatokat jelképeznek. A **Floor0** ikereszköz a **Room0** és a **Floor1 ikereszközt** **tartalmazza.**

Ha egeret használ, húzással mozgathatja a gráf egy-egy darabját.

### <a name="view-twin-properties"></a>Ikertulajdonságok megtekintése

Ha kiválaszt egy ikereszközt, a tulajdonságlistát és azok értékeit a **TULAJDONSÁGKEZELŐ mezőben láthatja.**

A Room0 tulajdonságai a következőek:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room0.png" alt-text="Jelölje ki a Property Explorer (Tulajdonságkezelő) mező körüli kiemelést, amely a Room0 tulajdonságait mutatja, többek között egy $dtId Room0 mezőt, egy 70-es Hőmérséklet mezőt és egy 30-as Páratartalom mezőt." lightbox="media/quickstart-azure-digital-twins-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

A Room0 70-es hőmérsékletű.

A Room1 tulajdonságai a következőek:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room1.png" alt-text="Jelölje ki a Property Explorer (Tulajdonságkezelő) mező körüli kiemelést, amely a Room1 (1. helyiség) egy $dtId-mezőjét, egy 80-as Hőmérséklet mezőt és egy 60-as Páratartalom mezőt tartalmaz." lightbox="media/quickstart-azure-digital-twins-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Az 1. helyiség hőmérséklete 80.

### <a name="query-the-graph"></a>A gráf lekérdezése

Az ikergráf Azure Digital Twins szolgáltatása az [](concepts-query-language.md) ikergráf egyszerű és hatékony lekérdezésének képessége a környezettel kapcsolatos kérdések megválaszolása érdekében.

A gráfban található ikereket a tulajdonságaik alapján lehet **lekérdezni.** A tulajdonságokon alapuló lekérdezések számos kérdésre segíthetnek választ adni. Például megkeresheti a környezetben található olyan kitekeréseket, amelyek figyelmet igényelnek.

Ebben a szakaszban egy lekérdezést fog futtatni annak a kérdésnek a megválaszolása érdekében, hogy a környezetben hány ikereszköz hőmérséklete meghaladja a 75-öt.

A válaszhoz futtassa a következő lekérdezést a **QUERY EXPLORER (LEKÉRDEZÉSKEZELŐ)** mezőben.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

Emlékezzen vissza, hogy az ikertulajdonságok korábbi megtekintésekor a Room0 hőmérséklete 70, a Room1 hőmérséklete pedig 80. Emiatt itt csak a Room1 (1. helyiség) jelenik meg az eredmények között.
    
:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png" alt-text="A tulajdonságlekérdezés eredményei csak a Room1 teremben." lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png":::

>[!TIP]
> Más összehasonlító operátorok (<,>, =, vagy !=) szintén támogatottak az előző lekérdezésben. Ezeket az operátorokat, különböző értékeket vagy különböző ikertulajdonságokat a lekérdezésbe csatlakoztatva megpróbálhatja megválaszolni a saját kérdéseit.

## <a name="edit-data-in-the-graph"></a>Adatok szerkesztése a gráfban

A diagram Azure Digital Twins Explorer a gráfban ábrázolt ikertulajdonságok szerkesztésére is használható. Ebben a szakaszban a Room0 hőmérsékletét 76-ra emeljük.

Először válassza a **Room0 (Helyiség0)** lehetőséget a tulajdonságlista a PROPERTY EXPLORER (TULAJDONSÁGKEZELŐ) **mezőben való listához.**

A listában található tulajdonságok szerkeszthetők. Új érték bevitelének engedélyezéséhez válassza a **70-es** hőmérsékleti értéket. Írja **be a 76**-ot, majd válassza a **Mentés ikont** a hőmérséklet **76-osra frissítéséhez.**

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png" alt-text="A Property Explorer (Tulajdonságkezelő) mező a Room0 tulajdonságait mutatja. A hőmérséklet értéke egy szerkeszthető mező, amely 76-ot mutat, és a Mentés ikon körül egy kiemelés látható." lightbox="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Most megjelenik egy Javítási adatok ablak, ahol **megjelenik** a színfalak mögött a frissítéshez használt javítási kód a Azure Digital Twins API-okkal. [](how-to-use-apis-sdks.md) Válassza a **Bezárás** gombot.

### <a name="query-to-see-the-result"></a>Az eredmény lekérdezése

Annak ellenőrzéséhez, hogy a gráf sikeresen regisztrálta-e a frissítést a Room0 hőmérsékletére, futtassa újra a lekérdezést a korábbiból a környezetben 75 feletti hőmérséklettel bíró ikerpárok lekérdezése érdekében.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

Most, hogy a Room0 hőmérséklete 70-ről 76-ra változott, mindkét ikereszköznek meg kell lennie az eredményben.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png" alt-text="A Room0 és a Room1 tulajdonságlekérdezés eredményei." lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>A tanulási eredmények áttekintése és kontextusba valóezése

Ebben a rövid útmutatóban létrehozott egy Azure Digital Twins, csatlakoztatta egy Azure Digital Twins Explorer, és feltöltette egy mintaforgatókönyvvel.

Ezután a következővel ismerkedett meg a gráfokkal:

* Lekérdezés használata a forgatókönyvvel kapcsolatos kérdésre való válaszhoz.
* Digitális ikertulajdonság szerkesztése.
* Futtatjuk újra a lekérdezést, hogy lássuk, hogyan változott a válasz a frissítés eredményeként.

Ennek a gyakorlatnak az a célja, hogy bemutatja, hogyan használhatja a Azure Digital Twins-diagramot a környezettel kapcsolatos kérdésekre, még akkor is, ha a környezet folyamatosan változik.

Ebben a rövid útmutatóban manuálisan tette meg a hőmérséklet-frissítést. A digitális ikereszközöket gyakran Azure Digital Twins valós IoT-eszközökhöz csatlakoztatni, hogy a telemetriai adatok alapján automatikusan megkapják a frissítéseket. Így olyan élő gráfot építhet, amely mindig tükrözi a környezet valódi állapotát. A lekérdezésekkel valós időben kaphat információt arról, hogy mi történik a környezetében.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató munkához először a futó konzolalkalmazást kell lezárni. Ez a művelet leállítja a kapcsolatot Azure Digital Twins Explorer böngészőben az alkalmazással. A továbbiakban nem fog tudni élő adatokat megtekinteni a böngészőben. Bezárhatja a böngészőlapot.

Ezután kiválaszthatja, hogy mely erőforrásokat szeretné eltávolítani, attól függően, hogy mit szeretne tenni.

* **Ha azt tervezi,** hogy Azure Digital Twins oktatóanyagokkal folytatja, a jelen rövid útmutatóban újra felhasználhatja a példányt az ilyen cikkekhez, és nem kell eltávolítania.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

A projektmappát is törölheti a helyi gépről.

## <a name="next-steps"></a>Következő lépések

Ezután folytassa a Azure Digital Twins oktatóanyagokkal, amelyekből megtudhatja, hogyan építhet ki Azure Digital Twins forgatókönyvet és interakciós eszközöket.

> [!div class="nextstepaction"]
> [Oktatóanyag: Ügyfélalkalmazás kódja](tutorial-code.md)

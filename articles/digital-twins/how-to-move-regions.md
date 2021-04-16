---
title: Példány áthelyezése másik Azure-régióba
titleSuffix: Azure Digital Twins
description: Tekintse meg, hogyan lehet áthelyezni egy Azure Digital Twins-példányt az egyik Azure-régióból a másikba.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: 62db56ac9791cea7d6f1a40f794241ed68fa90fa
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483575"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Új Azure Digital Twins áthelyezése egy másik Azure-régióba

Ha át kell helyezze a Azure Digital Twins-példányt egy régióból egy másikba, az aktuális folyamat az, hogy újra létrehozza az erőforrásokat az új régióban, majd törli az eredeti erőforrásokat. A folyamat végén egy új Azure Digital Twins fog dolgozni, amely megegyezik az elsővel, kivéve a frissített helyet.

Ez a cikk útmutatást nyújt a teljes áthelyezéshez és minden más másoláshoz, ami ahhoz szükséges, hogy az új példány megfeleljen az eredetinek.

Ez a folyamat a következő lépéseket tartalmazza:

1. Előkészítés: Töltse le az eredeti modelleket, ikereket és gráfokat.
1. Áthelyezés: Hozzon létre egy új Azure Digital Twins új régióban.
1. Áthelyezés: Az új Azure Digital Twins újra.
    - Töltse fel az eredeti modelleket, ikereket és gráfokat.
    - Hozza létre újra a végpontokat és az útvonalakat.
    - Csatlakoztatott erőforrások újrakapcsolatba kapcsolása.
1. Forráserőforrások törlése: Törölje az eredeti példányt.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt megpróbálja újra létrehozni a Azure Digital Twins-példányt, át kell mennie az eredeti példány összetevőin, hogy egyértelműen tudja, milyen elemeket kell újra létrehozni.

Célszerű megfontolni többek között a következő kérdéseket:

* Milyen modellek vannak *feltöltve* a példányomba? Hány van?
* Mik az *ikerpéldányom?* Hány van?
* Mi a gráf általános alakja *a* példányban? Hány kapcsolat van?
* Milyen *végpontok* vannak a példányban?
* Milyen *útvonalak* vannak a példányban? Vannak szűrők?
* Hol csatlakozik a *példányom más Azure-szolgáltatásokhoz?* Néhány gyakori integrációs pont:

    - Azure Event Grid, Azure Event Hubs vagy Azure Service Bus
    - Azure Functions
    - Azure Logic Apps
    - Azure Time Series Insights
    - Azure Maps
    - Azure IoT Hub Device Provisioning Service
* Milyen egyéb *személyes vagy vállalati* alkalmazásokat csatlakoztatok a példányomhoz?

Ezt az információt a [Azure Portal](https://portal.azure.com), [Azure Digital Twins](how-to-use-apis-sdks.md)API-k és AZ [AZURE DIGITAL TWINS CLI-parancsai](how-to-use-cli.md)vagy a Azure Digital Twins Explorer használatával [gyűjtheti.](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)

## <a name="prepare"></a>Előkészítés

Ebben a szakaszban előkészítheti a példány újralétrehozására az eredeti modellek, ikerképek és gráf eredeti példányból való letöltésével. Ez a cikk a [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) mintaalkalmazást használja ehhez a feladathoz.

>[!NOTE]
>Előfordulhat, hogy már rendelkezik olyan fájlokkal, amelyek a példányban lévő modelleket vagy gráfot tartalmazzák. Ha igen, nem kell mindent újra letöltenie – csak a hiányzó adatokat, vagy a fájlok eredeti feltöltése óta esetleg módosult dolgokat. Lehetnek például olyan ikereszközei, amelyek új adatokkal frissültek.

### <a name="limitations-of-azure-digital-twins-explorer"></a>A Azure Digital Twins Explorer

A [Azure Digital Twins Explorer minta egy](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) ügyfélalkalmazás-minta, amely támogatja a gráf vizuális ábrázolásait, és vizuális interakciót biztosít a példányával. Ez a cikk bemutatja, hogyan töltheti le, majd töltheti fel újra a modelleket, ikereket és gráfokat a használatával.

Ez a minta nem teljes eszköz. Nem tesztelték a terhelést, és nem arra készültek, hogy nagy méretű gráfokat kezeljenek. Ebből kifolyólag tartsa szem előtt az alábbi használatra megfelelő mintakorlátozásokat:

* A mintát jelenleg csak 1000 csomópont és 2000 kapcsolat gráfméretén tesztelték.
* A minta nem támogatja az újrapróbálkozásokat időszakos hibák esetén.
* A minta nem feltétlenül értesíti a felhasználót, ha a feltöltött adatok hiányosak.
* A minta nem kezeli a nagyon nagy méretű gráfok által eredményező hibákat, amelyek meghaladják a rendelkezésre álló erőforrásokat, például a memóriát.

Ha a minta nem tudja kezelni a gráf méretét, exportálhatja és importálhatja a gráfot más fejlesztői Azure Digital Twins használatával:

* [Azure Digital Twins CLI-parancsok használata](how-to-use-cli.md)
* [Azure Digital Twins API-k és AZ API-k](how-to-use-apis-sdks.md)

### <a name="set-up-the-azure-digital-twins-explorer-application"></a>A Azure Digital Twins Explorer beállítása

A futtatás Azure Digital Twins Explorer először töltse le a mintaalkalmazás kódját, és állítsa be úgy, hogy a gépen fusson.

A minta lekért útjára [a](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)Azure Digital Twins Explorer. Válassza a **Cím alatti Kód** tallózása gombot, amellyel a minta GitHub-adattárába érhetők el. A minta **letöltéséhez** kattintson a Kód gombra, majd a **ZIP** letöltése *gombra. ZIP-fájl* a gépre.

:::image type="content" source="media/how-to-move-regions/download-repo-zip.png" alt-text="Képernyőkép a digital-twins-explorer adattárról a GitHubon. A Kód gomb van kiválasztva, így egy kis párbeszédpanel jelenik meg, ahol a ZIP letöltése gomb ki van emelve." lightbox="media/how-to-move-regions/download-repo-zip.png":::

Bontsa ki a fájlt.

Ezután állítsa be és konfigurálja az engedélyeket a Azure Digital Twins Explorer. Kövesse a rövid útmutató Azure Digital Twins [Azure Digital Twins Explorer](quickstart-azure-digital-twins-explorer.md#set-up-azure-digital-twins-and-azure-digital-twins-explorer) szakaszának Azure Digital Twins útmutatását. Ez a szakasz a következő lépéseken keresztülvezeti:

1. Állítson be egy Azure Digital Twins példányt. Ezt a részt kihagyhatja, mert már rendelkezik példányokkal.
1. Állítson be helyi Azure-beli hitelesítő adatokat, hogy hozzáférést biztosítson a példányhoz.
1. Futtassa Azure Digital Twins Explorer és konfigurálja úgy, hogy csatlakozzon a példányhoz. Az áthelyezéshez *használt* eredeti Azure Digital Twins gazdagépnevét fogja használni.

Most már futnia kell Azure Digital Twins Explorer mintaalkalmazásnak a gép egy böngészőjében. A mintát csatlakoztatni kell az eredeti Azure Digital Twins példányhoz.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="A localhost:3000-ben futó alkalmazást megjelenítő böngészőablak. Az alkalmazás neve Azure Digital Twins Explorer, és a Lekérdezéskezelő, a Modell nézet, a Gráfnézet és a Tulajdonságkezelő mezőket tartalmazza. A képernyőn még nincsenek adatok." lightbox="media/how-to-move-regions/explorer-blank.png":::

A kapcsolat ellenőrzéséhez kattintson a **Lekérdezés** futtatása gombra az alapértelmezett lekérdezés futtatásához, amely a GRAPH EXPLORER mezőben a gráfban lévő összes ikereszközt és kapcsolatot **megjeleníti.**

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Az ablak jobb felső sarkában lévő Lekérdezés futtatása gomb ki van emelve." lightbox="media/how-to-move-regions/run-query.png":::

Az elemeket Azure Digital Twins Explorer hagyhatja, mert a cikk egy későbbi későbbi cikkében újra fel fogja használni az elemeket az új példányra a célterületen.

### <a name="download-models-twins-and-graph"></a>Modellek, ikerképek és gráfok letöltése

Ezután töltse le a megoldásában található modelleket, ikereket és gráfokat a gépre.

Az összes elem egyszerre való letöltéséhez először győződjön meg arról, hogy a teljes gráf megjelenik a **GRAPH VIEW (GRÁFNÉZET)** mezőben. Ha a teljes gráf még nem jelenik meg, futtassa újra az alapértelmezett lekérdezését a `SELECT * FROM digitaltwins` **QUERY EXPLORER (LEKÉRDEZÉSKEZELŐ) mezőben.**
 
Ezután válassza a **Gráf exportálása** ikont a **GRÁFNÉZET mezőben.**

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="A Gráfnézet mezőben egy ikon van kiemelve. Egy felhőből kifelé mutató nyíl látható." lightbox="media/how-to-move-regions/export-graph.png":::

Ez a művelet engedélyezi a **Letöltés hivatkozást** a GRAPH **VIEW (GRÁFNÉZET)** mezőben. Válassza ki a lekérdezési eredmény JSON-alapú ábrázolásának letöltéséhez, amely tartalmazza a modelleket, ikereket és kapcsolatokat. Ennek a műveletnek egy .json-fájlt kell letöltenie a gépére.

>[!NOTE]
>Ha úgy tűnik, hogy a letöltött fájlnak más a fájlkiterjesztése, próbálja meg közvetlenül szerkeszteni a bővítményt, és .json-fájlra megváltoztatni.

## <a name="move"></a>Áthelyezés

Ezután befejezi a példány "áthelyezését", és létrehoz egy új példányt a cél régióban. Ezután feltölti az eredeti példányból származó adatokkal és összetevőkkel.

### <a name="create-a-new-instance"></a>Új példány létrehozása

Először hozzon létre egy új példányt a Azure Digital Twins a célterületen. Kövesse az [How-to: Set up an instance and authentication (A példány](how-to-set-up-instance-portal.md)és a hitelesítés beállítása) lépéseit. Tartsa szem előtt a következő mutatókat:

* Megtarthatja az új példány nevét, ha az egy másik erőforráscsoportban van.  Ha ugyanazt az erőforráscsoportot kell használnia, amely az eredeti példányt tartalmazza, az új példánynak saját különálló névre lesz szüksége.
* Amikor a rendszer kéri, adja meg az új célterületet.

A lépés befejezése után szüksége lesz az új példány gazdagépnevére, hogy az adatokkal együtt folytatja a beállítását. Ha a telepítés során nem jegyezze fel az [](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) állomásnevet, az alábbi utasításokat követve szerezze be az Azure Portal.

### <a name="repopulate-the-old-instance"></a>A régi példány újratelepítése

Ezután be kell állítania az új példányt, hogy az az eredeti másolata lesz.

#### <a name="upload-the-original-models-twins-and-graph-by-using-azure-digital-twins-explorer"></a>Töltse fel az eredeti modelleket, ikereket és gráfokat a Azure Digital Twins Explorer

Ebben a szakaszban újra betöltheti a modelleket, ikereket és gráfokat az új példányra. Ha nem található modell, ikereszköz vagy gráf az eredeti példányban, vagy nem szeretné áthelyezni őket az új példányra, ugorjon a következő [szakaszra.](#re-create-endpoints-and-routes)

Ellenkező esetben térjen vissza a böngészőablakba, Azure Digital Twins Explorer kövesse az alábbi lépéseket.

##### <a name="connect-to-the-new-instance"></a>Csatlakozás az új példányhoz

Jelenleg az Azure Digital Twins Explorer csatlakozik az eredeti Azure Digital Twins példányhoz. Váltsa úgy a kapcsolatot, hogy  az új példányra mutasson az ablak jobb felső sarkában található Bejelentkezés gombra kattintva.

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="Azure Digital Twins Explorer az ablak jobb felső sarkában található Bejelentkezés ikont. Az ikon egy kulcssilomóval átfedő személy egyszerű silhouettját mutatja." lightbox="media/how-to-move-regions/sign-in.png":::

Cserélje le **az ADT URL-címet** az új példányra. Módosítsa ezt az értéket úgy, hogy *https://{új példány gazdagépneve}*.

Válassza a **Kapcsolódás** lehetőséget. Előfordulhat, hogy a rendszer megkéri, hogy jelentkezzen be újra az Azure-beli hitelesítő adataival, vagy adja meg az alkalmazás hozzájárulását a példányhoz.

##### <a name="upload-models-twins-and-graph"></a>Modellek, ikerképek és gráfok feltöltése

Ezután töltse fel a korábban letöltött megoldás-összetevőket az új példányra.

A modellek, ikerképek és gráfok feltöltéshez válassza a **Gráf importálása** ikont a **GRAPH VIEW (GRÁFNÉZET) mezőben.** Ez a lehetőség mindhárom összetevőt egyszerre tölti fel. Még olyan modelleket is feltölt, amelyek jelenleg nincsenek használatban a gráfban.

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="A Gráfnézet mezőben egy ikon van kiemelve. Egy felhőre mutató nyilat mutat." lightbox="media/how-to-move-regions/import-graph.png":::

A fájlválasztó mezőben válassza ki a letöltött gráfot. Válassza ki a **graph .json fájlt,** majd válassza a **Megnyitás lehetőséget.**

Néhány másodperc elteltével a Azure Digital Twins Explorer megnyit egy **Importálás** nézetet, amely megjeleníti a betölthető gráf előnézetét.

A gráf feltöltésének megerősítéséhez válassza a **MENTÉS** ikont a GRAPH VIEW mező jobb **felső sarkában.**

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="A Mentés ikon kiemelése a Gráf előnézete panelen." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Azure Digital Twins Explorer feltölti a modelleket és a gráfokat (beleértve az ikereket és a kapcsolatokat) az új Azure Digital Twins példányba. A sikerességről üzenetet kell látnia, amely tudatja a feltöltött modellekkel, ikerekkel és kapcsolatokkal.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="A gráfimportozás sikerességét jelző párbeszédpanel. A következőt olvassa: &quot;Sikeres importálás. 2 modell importálva. 4 iker importálása. 2 kapcsolat importálva.&quot;" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Annak ellenőrzéséhez, hogy minden  sikeresen fel lett-e töltve, válassza a Lekérdezés futtatása gombot a **GRAPH EXPLORER** mezőben az összes ikereszközt és kapcsolatot megjelenítő alapértelmezett lekérdezés futtatásához. Ez a művelet a modellnézet mezőben található modellek listáját **is frissíti.**

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Jelölje ki az ablak jobb felső sarkában található Lekérdezés futtatása gombot." lightbox="media/how-to-move-regions/run-query.png":::

Meg kell jelenni a gráfnak, amely az összes ikereszközét és kapcsolatát megjeleníti a **GRAPH EXPLORER mezőben.** A modelleknek a MODELLNÉZET mezőben **is meg kell megjelenikuk.**

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="A nézetben Azure Digital Twins Explorer modell van kiemelve a Modellnézet mezőben, és egy gráf a Graph Explorer mezőben." lightbox="media/how-to-move-regions/post-upload.png":::

Ezek a nézetek megerősítik, hogy a modellek, ikerképek és gráfok újra fel voltak töltve a célterületen található új példányra.

#### <a name="re-create-endpoints-and-routes"></a>Végpontok és útvonalak újra létrehozása

Ha az eredeti példányban végpontok vagy útvonalak vannak, újra létre kell hoznia őket az új példányban. Ha nem található végpont vagy útvonal az eredeti példányban, vagy nem szeretné áthelyezni őket az új példányra, ugorjon a [következő szakaszra.](#relink-connected-resources)

Ellenkező esetben kövesse [](how-to-manage-routes-portal.md) a Végpontok és útvonalak kezelése az új példány használatávalcímű lépéseit. Tartsa szem előtt a következő mutatókat:

* Nem kell *újra* létrehoznia Event Grid, Event Hubs vagy Service Bus a végponthoz használt erőforrást. További információért tekintse meg a végpontokra vonatkozó utasítások "Előfeltételek" szakaszát. Csak újra létre kell hoznia a végpontot a Azure Digital Twins példányon.
* A végpont- és útvonalneveket újra felhasználhatja, mert azok hatóköre egy másik példányra terjed ki.
* Ne felejtse el hozzáadni a szükséges szűrőket a létrehozott útvonalakhoz.

#### <a name="relink-connected-resources"></a>Csatlakoztatott erőforrások újrakapcsolata

Ha más alkalmazásokkal vagy Azure-erőforrásokkal rendelkezik, amelyek az eredeti Azure Digital Twins-példányhoz csatlakoznak, akkor szerkesztenie kell a kapcsolatot, hogy azok ehelyett az új példányt érik el. Ezek az erőforrások tartalmazhatnak más Azure-szolgáltatásokat, illetve olyan személyes vagy vállalati alkalmazásokat, amelyek a Azure Digital Twins.

Ha nem rendelkezik más, az eredeti példányhoz csatlakoztatott erőforrással, vagy nem szeretné áthelyezni őket az új példányra, ugorjon a [következő szakaszra.](#verify)

Ellenkező esetben vegye figyelembe a csatlakoztatott erőforrásokat a forgatókönyvben. Nem kell törölnie és újra létrehoznia a csatlakoztatott erőforrásokat. Ehelyett csak szerkesztenie kell a pontokat, amelyeken keresztül egy Azure Digital Twins-példányhoz csatlakoznak. Ezután frissítse ezeket a pontokat, hogy az új példány gazdagépnevét használják az eredeti helyett.

A pontos szerkeszthető erőforrások a forgatókönyvtől függnek, de íme néhány gyakori integrációs pont:

* Azure Functions. Ha olyan Azure-függvénye van, amelynek a kódja tartalmazza az eredeti példány állomásnevét, frissítse ezt az értéket az új példány gazdagépnevére, és tegye közzé újra a függvényt.
* Event Grid, Event Hubs vagy Service Bus.
* Logic Apps.
* Time Series Insights.
* Azure Maps.
* IoT Hub Device Provisioning Service.
* Az Azure-n kívüli személyes vagy vállalati alkalmazások, például az oktatóanyagban létrehozott [ügyfélalkalmazás:](tutorial-code.md)A példányhoz csatlakozó és az API-kat hívó ügyfélalkalmazás Azure Digital Twins meg.
* Az Azure AD-alkalmazásregisztrációkat nem szükséges újra létrehozni.  Ha alkalmazásregisztrációt használ [a](how-to-create-app-registration.md) Azure Digital Twins API-khoz való csatlakozáshoz, újból felhasználhatja ugyanazt az alkalmazásregisztrációt az új példányban.

Miután végzett ezzel a lépéssel, a célréniszben található új példánynak az eredeti példány másolatának kell lennie.

## <a name="verify"></a>Ellenőrzés

Az új példány megfelelő beállításának ellenőrzéséhez használja a következő eszközöket:

* [Azure Portal](https://portal.azure.com). A portálon ellenőrizheti, hogy az új példány létezik-e, és a megfelelő célterületen van-e. Emellett a végpontok, útvonalak és kapcsolatok más Azure-szolgáltatásokkal való ellenőrzésére is jól szolgál.
* [Azure Digital Twins CLI-parancsokat.](how-to-use-cli.md) Ezekkel a parancsokkal ellenőrizheti, hogy az új példány létezik-e, és a megfelelő cél-régióban van-e. A példányadatok ellenőrzéséhez is használhatók.
* [Azure Digital Twins Explorer:](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Azure Digital Twins Explorer a példányadatok, például a modellek, ikerképek és gráfok ellenőrzésére használható.
* [Azure Digital Twins API-k és AZ API-k.](how-to-use-apis-sdks.md) Ezek az erőforrások a példányadatok, például a modellek, ikerképek és gráfok ellenőrzésére használhatók. Emellett a végpontok és az útvonalak ellenőrzésére is jól megfelelők.

Az eredeti példányával futtatott egyéni alkalmazásokat vagy végpontok között futó folyamatokat is kipróbálhatja, így ellenőrizheti, hogy megfelelően működnek-e az új példánysal.

## <a name="clean-up-source-resources"></a>Forráserőforrások tisztítása

Most, hogy az új példány be van állítva a célterületen az eredeti példány adatainak és kapcsolatainak másolatával, törölheti az eredeti példányt.

Használhatja a [Azure Portal,](https://portal.azure.com)az [Azure CLI-t](how-to-use-cli.md)vagy a [vezérlősík API-ját.](how-to-use-apis-sdks.md#overview-control-plane-apis)

Ha törölni szeretné a példányt a [Azure Portal,](https://portal.azure.com) nyissa meg a portált egy böngészőablakban, és nyissa meg az eredeti Azure Digital Twins-példányt a név keresését a portál keresősávjában.

Válassza a **Törlés gombot,** és kövesse az utasításokat a törlés befejezéséhez.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="A Azure Digital Twins áttekintés lapján Azure Portal meg a Azure Portal példány részleteit. A Törlés gomb ki van emelve.":::
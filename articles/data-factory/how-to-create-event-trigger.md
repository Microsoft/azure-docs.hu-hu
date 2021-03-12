---
title: Eseményvezérelt eseményindítók létrehozása a Azure Data Factoryban
description: Megtudhatja, hogyan hozhat létre eseményindítót olyan Azure Data Factoryban, amely egy adott eseményre reagálva egy folyamatot futtat.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 6474cb10cdb516bae0386b92e40ecd6f17250691
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225453"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-storage-event"></a>Egy folyamatot futtató eseményindító létrehozása tárolási eseményre válaszként

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a Data Factory folyamatokban létrehozható tárolási eseményindítókat ismerteti.

Az eseményvezérelt architektúra (EDA) olyan közös adatintegrációs minta, amely az események gyártását, észlelését, felhasználását és reagálását foglalja magában. Az adatintegrációs forgatókönyvek gyakran megkövetelik Data Factory ügyfeleknek, hogy a Storage-fiókban zajló események alapján indítsák el a folyamatokat, például egy fájl megérkezését vagy törlését az Azure Blob Storage-fiókban. Data Factory natív módon integrálható a [Azure Event Gridekkel](https://azure.microsoft.com/services/event-grid/), ami lehetővé teszi, hogy a folyamatokat ilyen eseményeken aktiválja.

A szolgáltatás tíz percen belüli bevezetéséhez és bemutatásához tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]

> [!NOTE]
> A cikkben ismertetett integráció a [Azure Event Gridtól](https://azure.microsoft.com/services/event-grid/)függ. Győződjön meg arról, hogy az előfizetés regisztrálva van a Event Grid erőforrás-szolgáltatónál. További információ: erőforrás- [szolgáltatók és típusok](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). A *Microsoft. EventGrid/eventSubscriptions/** művelet végrehajtásához képesnek kell lennie. Ez a művelet a EventGrid EventSubscription közreműködő beépített szerepkörének részét képezi.

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete

Ebből a szakaszból megtudhatja, hogyan hozhat létre tárolási esemény-eseményindítókat a Azure Data Factory felhasználói felületen belül.

1. Váltson a **Szerkesztés** lapra, amely egy ceruza szimbólummal jelenik meg.

1. Válassza az **aktiválás** lehetőséget a menüben, majd válassza az **új/szerkesztés** lehetőséget.

1. Az **Eseményindítók hozzáadása** lapon válassza az **eseményindító kiválasztása...**, majd az **+ új** lehetőséget.

1. Eseményindító típusú **tárolási esemény** kiválasztása

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image1.png" alt-text="Képernyőkép a szerző lapról új tárolási esemény eseményindítójának létrehozásához Data Factory felhasználói felületen.":::

1. Válassza ki a Storage-fiókját az Azure-előfizetés legördülő menüből, vagy manuálisan használja a Storage-fiókja erőforrás-AZONOSÍTÓját. Válassza ki, hogy melyik tárolón történjen az események betöltése. A tároló kiválasztása nem kötelező, de ne felhagyható, hogy az összes tároló kijelölése nagy számú eseményt eredményezhet.

   > [!NOTE]
   > A tárolási esemény eseményindítója jelenleg csak a Azure Data Lake Storage Gen2 és az általános célú 2-es verziójú Storage-fiókokat támogatja. Egy Azure Event Grid korlátozás miatt Azure Data Factory csak legfeljebb 500 tárolási eseményt támogat a Storage-fiókokban.

   > [!NOTE]
   > Új tárolási esemény eseményindítójának létrehozásához és módosításához a Data Factoryba való bejelentkezéshez használt Azure-fióknak és a tárolási esemény eseményindítójának közzétételéhez megfelelő szerepköralapú hozzáférés-vezérlési (Azure RBAC) engedéllyel kell rendelkeznie a Storage-fiókhoz. Nincs szükség további engedélyre: a Azure Data Factory tartozó szolgáltatásnév _nem_ igényel külön engedélyt a Storage-fiókhoz vagy a Event Gridhoz. A hozzáférés-vezérléssel kapcsolatos további információkért lásd: [szerepköralapú hozzáférés-vezérlés](#role-based-access-control) szakasz.

1. A **blob** elérési útja és a **blob elérési** útja a tulajdonságok segítségével megadhatja azokat a tárolókat, mappákat és blob-neveket, amelyekhez eseményeket szeretne kapni. A tárolási esemény eseményindítójának legalább egy ilyen tulajdonságot meg kell határoznia. Különböző mintákat használhat a **blob elérési útjához** , a **blob elérési útja** pedig tulajdonságok használatával végződik, ahogy az ebben a cikkben szereplő példákban is látható.

    * **A blob elérési útja a következőket veszi kezdettel:** A blob elérési útnak a mappa elérési útjával kell kezdődnie. Az érvényes értékek `2018/` a következők: és `2018/april/shoes.csv` . Ez a mező nem választható ki, ha nincs kiválasztva tároló.
    * **A blob elérési útja az alábbiakkal végződik:** A blob elérési útjának fájlnevet vagy kiterjesztést kell végződnie. Az érvényes értékek `shoes.csv` a következők: és `.csv` . A tároló és a mappa neve nem kötelező, de ha meg van adva, egy szegmensnek kell elválasztani őket `/blobs/` . Egy "Orders" nevű tároló például a következő értékkel rendelkezhet: `/orders/blobs/2018/april/shoes.csv` . Ha bármilyen tárolóban szeretne megadni egy mappát, hagyja ki a kezdő "/" karaktert. Például egy `april/shoes.csv` eseményt indít el bármely `shoes.csv` , a mappában található "April" nevű fájlon bármely tárolóban.
    * Vegye figyelembe, hogy a blob elérési útja a és a **végződéssel** **kezdődik** , és az egyetlen, a tárolási esemény eseményindítójában megengedett mintázat. Az trigger típusa nem támogatja más típusú helyettesítő karakterek használatát.

1. Válassza ki, hogy az eseményindító válaszol-e a **blob által létrehozott** eseményre, a **blob törölt** eseményére vagy mindkettőre. A megadott tárolási helyen minden esemény elindítja az eseményindítóhoz társított Data Factory folyamatokat.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image2.png" alt-text="A tárolási esemény eseményindító-létrehozási oldalának képernyőképe.":::

1. Válassza ki, hogy az trigger figyelmen kívül hagyja-e a nulla bájtos blobokat.

1. Az aktiválás konfigurálása után kattintson a Tovább gombra **: adatelőnézet**. Ezen a képernyőn láthatók a tárolási esemény eseményindító-konfigurációjának megfelelő meglévő Blobok. Győződjön meg arról, hogy adott szűrőket adott meg. A túl széles szűrők konfigurálása nagy számú, létrehozott/törölt fájlhoz is tartozhat, és jelentősen befolyásolhatja a költségeket. A szűrési feltételek ellenőrzése után kattintson a **Befejezés** gombra.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image3.png" alt-text="Képernyőkép – a tárolási esemény eseményindítójának előzetes lapja.":::

1. Ha csatlakoztatni szeretne egy folyamatot ehhez az triggerhez, lépjen a folyamat vászonra, és kattintson az **trigger hozzáadása** lehetőségre, és válassza az **új/szerkesztés** lehetőséget. Amikor megjelenik az oldalsó NAV, kattintson az **trigger kiválasztása...** legördülő listára, és válassza ki a létrehozott triggert. Kattintson a **Tovább gombra: az adatok előnézete** a konfiguráció megerősítéséhez, majd az adatelőnézet érvényesítése elem **melletti** helyes értékre.

1. Ha a folyamat paraméterekkel rendelkezik, akkor megadhatja őket az trigger futtatási paraméterének navigációs oldalán. A tárolási esemény eseményindítója rögzíti a blob mappájának elérési útját és fájlnevét a tulajdonságok `@triggerBody().folderPath` és `@triggerBody().fileName` . Ezen tulajdonságok értékének egy folyamaton való használatához a tulajdonságokat a folyamat paramétereinek kell képeznie. Miután a tulajdonságokat hozzárendelte a paraméterekhez, az trigger által rögzített értékeket a `@pipeline().parameters.parameterName` folyamat során a kifejezésen keresztül érheti el. Részletes magyarázatért lásd: [a folyamatokban lévő trigger-metaadatok referenciája](how-to-use-trigger-parameterization.md)

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image4.png" alt-text="Képernyőfelvétel a tárolási események eseményindítójának leképezési tulajdonságairól a folyamat paramétereinek megjelenítéséhez.":::
    
    Az előző példában az eseményindító úgy van beállítva, hogy a. csv fájlban végződő blob-elérési út a tárolóban, a Container _Sample---_ ban a mappa _esemény-tesztelés_ területén jön létre. A **folderPath** és a **filename** tulajdonság rögzíti az új blob helyét. Ha például a MoviesDB.csv bekerül az elérési út mintába – az adatelemzési/esemény-tesztelés, a `@triggerBody().folderPath` értéke `sample-data/event-testing` és a `@triggerBody().fileName` értéke `moviesDB.csv` . Ezek az értékek leképezve jelennek meg a példában a folyamat paramétereinek `sourceFolder` és `sourceFile` , amelyek az egész folyamat során használhatók `@pipeline().parameters.sourceFolder` `@pipeline().parameters.sourceFile` .

1. Ha elkészült, kattintson a **Befejezés** gombra.

## <a name="json-schema"></a>JSON-séma

A következő táblázat áttekintést nyújt a tárolási esemény-eseményindítókkal kapcsolatos séma-elemekről:

| **JSON-elem** | **Leírás** | **Típus** | **Megengedett értékek** | **Kötelező** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **hatókör** | A Storage-fiók Azure Resource Manager erőforrás-azonosítója. | Sztring | Azure Resource Manager azonosítója | Yes |
| **események** | A triggert tüzet kiváltó események típusa. | Tömb    | Microsoft. Storage. BlobCreated, Microsoft. Storage. BlobDeleted | Igen, az értékek bármely kombinációja. |
| **blobPathBeginsWith** | A blob elérési útjának a triggerhez megadott mintázattal kell kezdődnie. Például csak a `/records/blobs/december/` tárolóban lévő mappában lévő Blobok eseményindítóját kell kiváltani `december` `records` . | Sztring   | | Adja meg a következő tulajdonságok legalább egyikének értékét: `blobPathBeginsWith` vagy `blobPathEndsWith` . |
| **blobPathEndsWith** | A blob elérési útjának a triggerhez megadott mintázattal kell végződnie. Például `december/boxes.csv` csak a mappában lévő Blobok eseményindítóját kell kiváltani `boxes` `december` . | Sztring   | | Meg kell adnia egy értéket a következő tulajdonságok közül legalább egy számára: `blobPathBeginsWith` vagy `blobPathEndsWith` . |
| **ignoreEmptyBlobs** | Azt határozza meg, hogy a nulla bájtos Blobok elindítanak-e egy folyamat futtatását. Alapértelmezés szerint ez igaz értékre van állítva. | Logikai | true (igaz) vagy false (hamis) | No |

## <a name="examples-of-storage-event-triggers"></a>Példák a tárolási események eseményindítóinak tárolására

Ez a szakasz példákat tartalmaz a tárolási események eseményindítójának beállításaira.

> [!IMPORTANT]
> Az `/blobs/` elérési út szegmensét az alábbi példákban látható módon kell megadnia, amikor tárolót, mappát, tárolót, fájlt vagy tárolót, mappát és fájlt ad meg. A **blobPathBeginsWith** esetében a Data Factory felhasználói felület automatikusan hozzáadja a `/blobs/` mappa és a tároló nevét az trigger JSON-ban.

| Tulajdonság | Példa | Leírás |
|---|---|---|
| **A blob elérési útja** | `/containername/` | Eseményeket fogad a tárolóban lévő összes blobhoz. |
| **A blob elérési útja** | `/containername/blobs/foldername/` | Eseményeket fogad a tárolóban és a mappában található összes blobhoz `containername` `foldername` . |
| **A blob elérési útja** | `/containername/blobs/foldername/subfoldername/` | Egy almappára is hivatkozhat. |
| **A blob elérési útja** | `/containername/blobs/foldername/file.txt` | A `file.txt` tárolóban lévő mappában található blob eseményeinek fogadása `foldername` `containername` . |
| **A blob elérési útja véget ér** | `file.txt` | A bármely elérési úton megnevezett blob eseményeinek fogadása `file.txt` . |
| **A blob elérési útja véget ér** | `/containername/blobs/file.txt` | A tárolóban megnevezett blob eseményeinek fogadása `file.txt` `containername` . |
| **A blob elérési útja véget ér** | `foldername/file.txt` | Eseményeket fogad a `file.txt` mappában lévő Blobok `foldername` bármelyik tárolóban. |

## <a name="role-based-access-control"></a>Szerepkör alapú hozzáférés-vezérlés

Azure Data Factory az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) használja annak biztosítására, hogy a jogosulatlan hozzáférés a blob-eseményekhez kapcsolódó folyamatok figyelésére, előfizetésekre való előfizetésre, valamint a blob-eseményekhez kapcsolódó folyamatokra vonatkozó riasztások kiváltására

* Új vagy meglévő tároló-eseményindító sikeres létrehozásához a Data Factoryba bejelentkezett Azure-fióknak megfelelő hozzáférést kell biztosítania a megfelelő Storage-fiókhoz. Ellenkező esetben a művelet sikertelen lesz, mert a _hozzáférés megtagadva_.
* Data Factory nem igényel speciális engedélyt a Event Gridhoz, és _nem_ kell speciális RBAC engedélyt rendelnie a művelethez Data Factory egyszerű szolgáltatáshoz.

Az alábbi RBAC-beállítások bármelyike működik a tárolási események eseményindítója esetén:

* Tulajdonosi szerepkör a Storage-fiókhoz
* Közreműködő szerepkör a Storage-fiókhoz
* _Microsoft. EventGrid/EventSubscriptions/Write_ engedély a Storage-fiókhoz _/Subscriptions/# # # #/resourceGroups/#_ # # #/Providers/Microsoft.Storage/storageAccounts/storageAccountName

Annak megismeréséhez, hogy Azure Data Factory Hogyan biztosítja a két ígéretet, térjünk vissza egy lépést, és ismerkedjen meg a jelenet mögött. A Data Factory, a tárterület és a Event Grid közötti integráció magas szintű munkafolyamata.

### <a name="create-a-new-storage-event-trigger"></a>Új tárolási esemény eseményindítójának létrehozása

Ez a magas szintű munkafolyamat azt ismerteti, hogyan működik a Azure Data Factory a Event Grid egy tárolási esemény eseményindítójának létrehozásához

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-5-create-subscription.png" alt-text="A tárolási események eseményindítójának létrehozási munkafolyamata.":::

Két észrevehető hívás a munkafolyamatokból:

* Azure Data Factory _nem_ tesz közvetlen kapcsolatot a Storage-fiókkal. Az előfizetés létrehozásához szükséges kérelem továbbítása Event Grid történik. Ezért Data Factory nem szükséges engedély a Storage-fiókhoz ehhez a lépéshez.

* A hozzáférés-vezérlés és az engedélyek ellenőrzése Azure Data Factory oldalon történik. Mielőtt az ADF a tárolási eseményre való előfizetésre vonatkozó kérést küld, ellenőrzi a felhasználó engedélyeit. Pontosabban ellenőrzi, hogy a bejelentkezett Azure-fiók és a tárolási esemény eseményindítójának létrehozásának kísérlete megfelelő hozzáféréssel rendelkezik-e a megfelelő Storage-fiókhoz. Ha az engedély-ellenőrzés sikertelen, a trigger létrehozása is sikertelen lesz.

### <a name="storage-event-trigger-data-factory-pipeline-run"></a>Tárolási esemény eseményindítójának Data Factory folyamatának futtatása

Ez a magas szintű munkahelyi folyamatok azt írják le, hogyan futnak a tárolási események a folyamaton Event Grid

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-6-trigger-pipeline.png" alt-text="A tárolási események indítását indító folyamat futtatása folyamatban van.":::

Amikor az esemény eseményindító folyamata Data Factory, a munkafolyamatban három észrevehető hívás kimenete látható:

* A Event Grid egy leküldéses modellt használ, amely a lehető leghamarabb továbbítja az üzenetet, amikor a tároló eldobja az üzenetet a rendszerbe. Ez eltér az üzenetkezelési rendszertől, például a Kafka-től, ahol lekéréses rendszer van használatban.
* A Azure Data Factory esemény-eseményindító aktív figyelőként szolgál a bejövő üzenethez, és megfelelően elindítja a társított folyamatot.
* A tárolási esemény eseményindítója maga nem tesz közvetlen kapcsolatot a Storage-fiókkal

  * Ez azt mondta, hogy ha a folyamaton belül egy másolattal vagy más tevékenységgel dolgozza fel az adatokat a Storage-fiókban, Data Factory a társított szolgáltatásban tárolt hitelesítő adatok használatával közvetlen kapcsolatot fog létesíteni a tárolóval. Győződjön meg arról, hogy a társított szolgáltatás megfelelően van beállítva
  * Ha azonban nem hivatkozik a Storage-fiókra a folyamat során, nem kell engedélyt adnia Data Factory számára a Storage-fiók eléréséhez.

## <a name="next-steps"></a>Következő lépések

* Az eseményindítókkal kapcsolatos részletes információkért lásd: [folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md#trigger-execution).
* Megtudhatja, hogyan hivatkozhat az trigger-metaadatok a folyamat során: [hivatkozás trigger metaadatainak a folyamat futtatása során](how-to-use-trigger-parameterization.md)

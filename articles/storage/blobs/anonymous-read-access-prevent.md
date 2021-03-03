---
title: Tárolók és Blobok névtelen nyilvános olvasási hozzáférésének tiltása
titleSuffix: Azure Storage
description: Megtudhatja, hogyan elemezheti a névtelen kérelmeket egy Storage-fiókkal, és hogyan akadályozhatja meg a névtelen hozzáférést a teljes Storage-fiókhoz vagy egy adott tárolóhoz.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/09/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 53f29c2b8f7a17ac2a23cc081660e8dcb4b9f387
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701858"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>Tárolók és Blobok névtelen nyilvános olvasási hozzáférésének tiltása

Az Azure Storage szolgáltatásban tárolt tárolók és Blobok névtelen nyilvános olvasási hozzáférése kényelmes módszer az adatmegosztáshoz, de biztonsági kockázatot jelenthet. Fontos, hogy megfontoltan kezelje a névtelen hozzáférést, és Ismerje meg, hogyan értékelheti ki a névtelen hozzáférést az adataihoz. Az üzemeltetés bonyolultsága, az emberi hiba, vagy a nyilvánosan elérhető adatvédelemmel szembeni rosszindulatú támadás költséges adatvesztést eredményezhet. A Microsoft azt javasolja, hogy csak akkor engedélyezze a névtelen hozzáférést, ha az alkalmazási forgatókönyvhöz szükséges.

Alapértelmezés szerint a blob-adataihoz való nyilvános hozzáférés mindig tiltott. A Storage-fiók alapértelmezett konfigurációja azonban lehetővé teszi, hogy a felhasználó megfelelő engedélyekkel konfigurálja a tárolók és Blobok nyilvános hozzáférését egy Storage-fiókban. A fokozott biztonság érdekében megtilthatja az összes nyilvános hozzáférést a Storage-fiókhoz, függetlenül az egyes tárolók nyilvános hozzáférési beállításától. A Storage-fiókhoz való nyilvános hozzáférés letiltása megakadályozza, hogy a felhasználó engedélyezze a fiókban lévő tárolók nyilvános elérését. A Microsoft azt javasolja, hogy a Storage-fiókhoz való nyilvános hozzáférést csak akkor engedélyezze, ha a forgatókönyv megköveteli. A nyilvános hozzáférés letiltásával megakadályozható, hogy a nem kívánt névtelen hozzáférés miatt illetéktelenek legyenek az adatmegsértések.

Ha letiltja a nyilvános Blobok hozzáférését a Storage-fiókhoz, az Azure Storage elutasítja az adott fiókhoz tartozó névtelen kérelmeket. Miután a nyilvános hozzáférés nem engedélyezett egy fiók esetében, a fiókban lévő tárolók nem konfigurálhatók a nyilvános hozzáféréshez. A nyilvános hozzáférésre már konfigurált tárolók már nem fogadják el a névtelen kérelmeket. További információ: [Névtelen nyilvános olvasási hozzáférés beállítása tárolók és Blobok](anonymous-read-access-configure.md)számára.

Ez a cikk azt ismerteti, hogyan lehet a Storage-fiókok nyilvános hozzáférésének folyamatos kezeléséhez a DRAG (észlelés – szervizelés – naplózási irányítás) keretrendszert használni.

## <a name="detect-anonymous-requests-from-client-applications"></a>Ügyfélalkalmazások névtelen kérelmének észlelése

Ha nem engedélyezi a nyilvános olvasási hozzáférést egy Storage-fiókhoz, a rendszer elutasítja a hozzáférést a tárolók és a Blobok számára, amelyek jelenleg nyilvános hozzáférésre vannak konfigurálva. A Storage-fiókhoz való nyilvános hozzáférés letiltása felülbírálja a Storage-fiókban lévő egyes tárolók nyilvános hozzáférési beállításait. Ha a Storage-fiókhoz való nyilvános hozzáférés nem engedélyezett, a fiókra vonatkozó jövőbeli névtelen kérelmek sikertelenek lesznek.

Annak megismeréséhez, hogy a nyilvános hozzáférés letiltása milyen hatással lehet az ügyfélalkalmazások alkalmazására, a Microsoft javasolja, hogy engedélyezze a naplózást és a metrikákat az adott fiókhoz, és elemezze a névtelen kérések mintáit egy adott időtartamon belül. A metrikák használatával határozza meg a Storage-fiókhoz való névtelen kérések számát, és a naplók segítségével határozza meg, hogy mely tárolók névtelenül érhetők el.

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>Névtelen kérelmek figyelése Metrikaböngésző

A névtelen kérelmek Storage-fiókba való nyomon követéséhez használja az Azure Metrikaböngésző a Azure Portal. További információ a Metrikaböngészőről: az [Azure Metrikaböngésző első lépései](../../azure-monitor/essentials/metrics-getting-started.md).

Kövesse az alábbi lépéseket egy olyan metrika létrehozásához, amely nyomon követi a névtelen kérelmeket:

1. Az Azure Portalon nyissa meg a tárfiókot. A **figyelés** szakaszban válassza a **metrikák** lehetőséget.
1. Válassza a **Metrika hozzáadása** lehetőséget. A **metrika** párbeszédpanelen a következő értékeket kell megadnia:
    1. Hagyja meg a hatókör mezőt a Storage-fiók nevére.
    1. Állítsa a **metrikai névteret** a *blob* értékre. Ez a mérőszám csak a blob Storage-ra vonatkozó kérelmeket fogja jelenteni.
    1. Állítsa a **metrika** mezőt a *tranzakciók* értékre.
    1. Állítsa az **Összesítés** mezőt Sum ( *összeg*) értékre.

    Az új metrika megjeleníti a blob Storage-hoz tartozó tranzakciók számának összegét egy adott időtartamon belül. Az eredményül kapott metrika a következő képen látható módon jelenik meg:

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="A metrika a blob-tranzakciók összegére való konfigurálását bemutató képernyőkép":::

1. Ezután a **szűrő hozzáadása** gombra kattintva hozzon létre egy szűrőt a névtelen kérelmekre vonatkozó mérőszámban.
1. A **szűrő** párbeszédpanelen a következő értékeket kell megadnia:
    1. Állítsa a **tulajdonság** értékét *hitelesítésre*.
    1. Állítsa az **operátor** mezőt az egyenlőségjel (=) értékre.
    1. Állítsa a **Values (értékek** ) mezőt *Névtelen* értékre.
1. A jobb felső sarokban válassza ki azt az időintervallumot, amelyre vonatkozóan meg szeretné tekinteni a metrikát. Azt is jelezheti, hogy a kérelmek összesítésének milyen részletességgel kell megjelennie, ha az intervallumokat 1 perc és 1 hónap között adja meg.

A metrika konfigurálása után a névtelen kérelmek megjelennek a gráfon. Az alábbi képen az elmúlt 30 percben összesített névtelen kérelmek láthatók.

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="A blob Storage-hoz tartozó összesített névtelen kérelmeket bemutató képernyőkép":::

A riasztási szabályt úgy is konfigurálhatja, hogy értesítést kapjon, ha egy adott számú névtelen kérelem kerül a Storage-fiókjába. További információ: [metrikus riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../../azure-monitor/alerts/alerts-metric.md).

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>Naplók elemzése a névtelen kérelmeket fogadó tárolók azonosításához

Az Azure Storage-naplók rögzítik a Storage-fiókkal kapcsolatos kérések részleteit, beleértve a kérelem engedélyezésének módját is. A naplók elemzésével meghatározhatja, hogy mely tárolók kapják meg a névtelen kérelmeket.

Ha a névtelen kérelmek kiértékeléséhez a kérelmeket az Azure Storage-fiókjába kívánja naplózni, Azure Monitor (előzetes verzió) Azure Storage-naplózást is használhat. További információ: az [Azure Storage figyelése](./monitor-blob-storage.md).

Az Azure Storage Azure Monitor támogatja a naplózási lekérdezések használatát a naplófájlok elemzéséhez. A naplók lekérdezéséhez használhat Azure Log Analytics munkaterületet. További információ a naplók lekérdezéséről [: oktatóanyag: log Analytics lekérdezések első lépései](../../azure-monitor/logs/log-analytics-tutorial.md).

> [!NOTE]
> Az Azure Storage naplózásának előzetes verziója Azure Monitor csak az Azure nyilvános felhőben támogatott. A kormányzati felhők nem támogatják az Azure Storage Azure Monitor-vel való naplózását.

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Diagnosztikai beállítás létrehozása a Azure Portalban

Az Azure Storage-beli adatAzure Monitor és az Azure Log Analytics való elemzéséhez először létre kell hoznia egy diagnosztikai beállítást, amely meghatározza, hogy milyen típusú kérelmeket és milyen tárolási szolgáltatásokat szeretne naplózni. Ha diagnosztikai beállítást szeretne létrehozni a Azure Portalban, kövesse az alábbi lépéseket:

1. Hozzon létre egy új Log Analytics munkaterületet az Azure Storage-fiókot tartalmazó előfizetésben. A Storage-fiók naplózásának konfigurálása után a naplók a Log Analytics munkaterületen lesznek elérhetők. További információ: [log Analytics munkaterület létrehozása a Azure Portalban](../../azure-monitor/logs/quick-create-workspace.md).
1. Az Azure Portalon nyissa meg a tárfiókot.
1. A figyelés szakaszban válassza a **diagnosztikai beállítások (előzetes verzió)** lehetőséget.
1. A blob Storage-ba irányuló kérelmek naplózásához válassza a **blob** elemet.
1. Válassza a **Diagnosztikai beállítások megadása** lehetőséget.
1. Adja meg a diagnosztikai beállítás nevét.
1. A **Kategória részletei** területen, a **napló** szakaszban válassza ki a naplózandó kérelmek típusát. Az összes névtelen kérelem olvasási kérelem lesz, ezért a névtelen kérelmek rögzítéséhez válassza a **StorageRead** lehetőséget.
1. A **célhely részletei** területen válassza **a Küldés log Analyticsba** lehetőséget. Válassza ki az előfizetését és a korábban létrehozott Log Analytics munkaterületet, ahogy az az alábbi képen is látható.

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="A naplózási kérelmek diagnosztikai beállításainak létrehozását bemutató képernyőkép":::

Miután létrehozta a diagnosztikai beállítást, a rendszer a Storage-fiókra irányuló kérelmeket a beállításnak megfelelően naplózza. További információ: [diagnosztikai beállítás létrehozása az erőforrás-naplók és-metrikák az Azure-ban való összegyűjtéséhez](../../azure-monitor/essentials/diagnostic-settings.md).

A Azure Monitor Azure Storage-naplókban elérhető mezőkre vonatkozó hivatkozásokat itt tekintheti meg: [erőforrás-naplók (előzetes verzió)](./monitor-blob-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-anonymous-requests"></a>Névtelen kérelmek lekérdezési naplói

Az Azure Storage-naplók a Azure Monitorban tartalmazzák azt a hitelesítési típust, amelyet egy kérésnek a Storage-fiókhoz való elvégzéséhez használt. A napló lekérdezésében szűrje a **AuthenticationType** tulajdonságot a névtelen kérések megtekintéséhez.

Ha a blob Storage-hoz való névtelen kérelmek esetében az elmúlt 7 nap naplóit szeretné lekérni, nyissa meg Log Analytics munkaterületét. Ezután illessze be a következő lekérdezést egy új napló-lekérdezésbe, és futtassa azt:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

A lekérdezésen alapuló riasztási szabályt úgy is konfigurálhat, hogy értesítést kapjon a névtelen kérelmekről. További információ: a [naplók létrehozása, megtekintése és kezelése Azure monitor használatával](../../azure-monitor/alerts/alerts-log.md).

## <a name="remediate-anonymous-public-access"></a>Névtelen nyilvános hozzáférés szervizelése

Miután kiértékelte a névtelen kérelmeket tárolók és Blobok számára a Storage-fiókban, a nyilvános hozzáférés korlátozásához vagy megtiltásához is végezhet műveleteket. Ha előfordulhat, hogy a Storage-fiók egyes tárolóinak elérhetőnek kell lenniük a nyilvános hozzáféréshez, a Storage-fiókban lévő egyes tárolók nyilvános hozzáférési beállításait is konfigurálhatja. Ez a lehetőség a nyilvános hozzáférés legrészletesebbebb szabályozását teszi lehetővé. További információ: [a tároló nyilvános hozzáférési szintjének beállítása](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

A fokozott biztonság érdekében a teljes Storage-fiókhoz való nyilvános hozzáférést nem engedélyezheti. A Storage-fiók nyilvános hozzáférési beállítása felülbírálja a fiókban lévő tárolók egyedi beállításait. Ha a Storage-fiókhoz való nyilvános hozzáférést nem engedélyezi, a nyilvános hozzáférés engedélyezésére konfigurált tárolók már nem érhetőek el névtelenül. További információ: [a Storage-fiók nyilvános olvasási hozzáférésének engedélyezése vagy letiltása](anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account).

Ha a forgatókönyv megköveteli, hogy bizonyos tárolóknak elérhetőknek kell lenniük a nyilvános hozzáféréshez, célszerű lehet áthelyezni ezeket a tárolókat és a blobokat a nyilvános hozzáférésre fenntartott Storage-fiókokba. Ezután letilthatja a nyilvános hozzáférést bármely más Storage-fiókhoz.

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>Annak ellenőrzése, hogy a blobhoz való nyilvános hozzáférés nem engedélyezett

Annak ellenőrzéséhez, hogy egy adott blobhoz való nyilvános hozzáférés nem engedélyezett-e, megkísérelheti letölteni a blobot az URL-címén keresztül. Ha a letöltés sikeres, a blob továbbra is nyilvánosan elérhető. Ha a blob nem érhető el nyilvánosan, mert a Storage-fiókhoz való nyilvános hozzáférés nem engedélyezett, akkor egy hibaüzenet jelenik meg, amely jelzi, hogy a nyilvános hozzáférés nem engedélyezett ezen a Storage-fiókon.

Az alábbi példa bemutatja, hogyan használható a PowerShell a blob letöltésére az URL-cím használatával. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>Annak ellenőrzése, hogy a tároló nyilvános hozzáférési beállításának módosítása nem engedélyezett-e

Annak ellenőrzéséhez, hogy a tároló nyilvános hozzáférési beállítása nem módosítható-e, miután a Storage-fiókhoz nem engedélyezett a nyilvános hozzáférés, megkísérelheti módosítani a beállítást. A tároló nyilvános hozzáférési beállításának módosítása sikertelen lesz, ha a nyilvános hozzáférés nem engedélyezett a Storage-fiók esetében.

Az alábbi példa bemutatja, hogyan lehet a PowerShell használatával módosítani egy tároló nyilvános hozzáférési beállításait. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>Győződjön meg arról, hogy az engedélyezett nyilvános hozzáférésű tároló létrehozása nem engedélyezett

Ha a Storage-fiókhoz nem engedélyezett a nyilvános hozzáférés, akkor nem fog tudni új tárolót létrehozni, amelyen engedélyezve van a nyilvános hozzáférés. Az ellenőrzéshez próbáljon meg egy olyan tárolót létrehozni, amelyen engedélyezve van a nyilvános hozzáférés.

Az alábbi példa bemutatja, hogyan lehet a PowerShell használatával olyan tárolót létrehozni, amelyen engedélyezve van a nyilvános hozzáférés. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

### <a name="check-the-public-access-setting-for-multiple-accounts"></a>Több fiók nyilvános hozzáférésének beállítása

Ha szeretné, hogy az optimális teljesítmény érdekében a Storage-fiókok egy csoportján belül ellenőrizzék a nyilvános hozzáférési beállításokat, használhatja az Azure Resource Graph Explorert a Azure Portal. Ha többet szeretne megtudni az Erőforrásgrafikon Explorer használatáról, tekintse meg a gyors útmutató [: az első Resource Graph-lekérdezés futtatása az Azure Resource Graph Explorerben](../../governance/resource-graph/first-query-portal.md).

A **AllowBlobPublicAccess** tulajdonság alapértelmezés szerint nincs beállítva a Storage-fiókhoz, és nem ad vissza értéket, amíg explicit módon be nem állítja azt. A Storage-fiók engedélyezi a nyilvános hozzáférést, ha a tulajdonság értéke **Null** vagy **igaz**.

A következő lekérdezés futtatása az Erőforrásgrafikon Explorerben a Storage-fiókok listáját adja vissza, és megjeleníti az egyes fiókok nyilvános hozzáférési beállításait:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
```

Az alábbi képen egy előfizetésen belüli lekérdezés eredményei láthatók. Vegye figyelembe, hogy azoknál a tárolási fiókoknál, amelyeknél a **AllowBlobPublicAccess** tulajdonság explicit módon be van állítva, **igaz** vagy **hamis értékként** jelenik meg az eredményben. Ha a **AllowBlobPublicAccess** tulajdonság nincs beállítva egy Storage-fiókhoz, akkor a lekérdezés eredményeiben üresként (vagy nullként) jelenik meg.

:::image type="content" source="media/anonymous-read-access-prevent/check-public-access-setting-accounts.png" alt-text="Képernyőfelvétel a nyilvános hozzáférési beállítások lekérdezési eredményeiről a Storage-fiókok között":::

## <a name="use-azure-policy-to-audit-for-compliance"></a>A megfelelőség naplózása Azure Policy használata

Ha nagy számú Storage-fiókkal rendelkezik, érdemes lehet naplózást végeznie, hogy ellenőrizze, hogy a fiókok konfigurálva vannak-e a nyilvános hozzáférés megakadályozására. A Storage-fiókok megfelelőségének naplózásához használja a Azure Policy. A Azure Policy egy olyan szolgáltatás, amellyel olyan szabályzatokat hozhat létre, rendelhet hozzá és kezelhet, amelyek szabályokat alkalmaznak az Azure-erőforrásokra. Azure Policy segít megőrizni ezeket az erőforrásokat a vállalati szabványoknak és a szolgáltatói szerződéseknek. További információ: [Azure Policy áttekintése](../../governance/policy/overview.md).

### <a name="create-a-policy-with-an-audit-effect"></a>Naplózási hatású házirend létrehozása

A Azure Policy olyan hatásokat támogat, amelyek meghatározzák, hogy mi történik, ha egy házirend-szabályt kiértékelnek egy erőforrással szemben. A naplózási effektus figyelmeztetést hoz létre, ha egy erőforrás nem felel meg az előírásoknak, de nem állítja le a kérést. A hatásokkal kapcsolatos további információkért lásd: [Azure Policy effektusok ismertetése](../../governance/policy/concepts/effects.md).

Az alábbi lépéseket követve hozhat létre egy olyan házirendet, amely a Azure Portalhoz tartozó Storage-fiók nyilvános hozzáférési beállításához tartozó naplózási hatással van:

1. A Azure Portal navigáljon a Azure Policy szolgáltatáshoz.
1. A **szerzői műveletek** szakaszban válassza a **definíciók** lehetőséget.
1. Új házirend-definíció létrehozásához válassza a **házirend-definíció hozzáadása** lehetőséget.
1. A **definíció helye** mezőnél válassza a **továbbiak** lehetőséget, hogy megadja a naplózási házirend erőforrásának helyét.
1. Adja meg a szabályzat nevét. Igény szerint megadhat egy leírást és egy kategóriát is.
1. A **házirend-szabály** területen adja hozzá a következő házirend-definíciót a **' policyrule osztály** szakaszhoz.

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
              "equals": "false"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. Mentse a szabályzatot.

### <a name="assign-the-policy"></a>A szabályzat hozzárendelése

Ezután rendelje hozzá a szabályzatot egy erőforráshoz. A szabályzat hatóköre megfelel az adott erőforrásnak és az alatta lévő erőforrásoknak. További információ a szabályzat-hozzárendelésről: [Azure Policy hozzárendelési struktúra](../../governance/policy/concepts/assignment-structure.md).

A szabályzatnak a Azure Portal való hozzárendeléséhez kövesse az alábbi lépéseket:

1. A Azure Portal navigáljon a Azure Policy szolgáltatáshoz.
1. A **szerzői műveletek** szakaszban válassza a **hozzárendelések** lehetőséget.
1. Új szabályzat-hozzárendelés létrehozásához válassza a **házirend hozzárendelése** lehetőséget.
1. A **hatókör** mezőben válassza ki a szabályzat-hozzárendelés hatókörét.
1. A **házirend-definíció** mezőben válassza a **továbbiak** gombot, majd válassza ki az előző szakaszban meghatározott házirendet a listából.
1. Adja meg a szabályzat-hozzárendelés nevét. A leírás megadása nem kötelező.
1. Hagyja *engedélyezve* a **házirend-kényszerítési** beállítást. Ez a beállítás nincs hatással a naplózási házirendre.
1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a hozzárendelés létrehozásához.

### <a name="view-compliance-report"></a>Megfelelőségi jelentés megtekintése

A szabályzat hozzárendelése után megtekintheti a megfelelőségi jelentést. A naplózási házirend megfelelőségi jelentése olyan információkat biztosít, amelyekkel a tárolási fiókok nem felelnek meg a szabályzatnak. További információ: házirend- [megfelelőségi adatok beolvasása](../../governance/policy/how-to/get-compliance-data.md).

Több percet is igénybe vehet, amíg a megfelelőségi jelentés elérhetővé válik a szabályzat-hozzárendelés létrehozása után.

Ha meg szeretné tekinteni a megfelelőségi jelentést a Azure Portalban, kövesse az alábbi lépéseket:

1. A Azure Portal navigáljon a Azure Policy szolgáltatáshoz.
1. Válassza a **megfelelőség** lehetőséget.
1. Szűrje az eredményeket az előző lépésben létrehozott szabályzat-hozzárendelés nevére. A jelentés azt jeleníti meg, hogy hány erőforrás felel meg a szabályzatnak.
1. További részletekért tekintse meg a jelentés részletezését, beleértve a nem megfelelő tárolási fiókok listáját.

    :::image type="content" source="media/anonymous-read-access-prevent/compliance-report-policy-portal.png" alt-text="A blob Public Access naplózási szabályzatának megfelelőségi jelentését bemutató képernyőkép":::

## <a name="use-azure-policy-to-enforce-authorized-access"></a>A jogosult hozzáférés kikényszeríthető Azure Policy használata

Azure Policy támogatja a felhő irányítását, mivel biztosítja, hogy az Azure-erőforrások megfeleljenek a követelményeknek és a szabványoknak. Annak biztosítása érdekében, hogy a szervezeten belüli Storage-fiókok csak a jogosult kérelmeket engedélyezzék, létrehozhat egy olyan házirendet, amely megakadályozza egy olyan új Storage-fiók létrehozását, amely lehetővé teszi a névtelen kérések használatát. Ez a szabályzat azt is megakadályozza, hogy az összes konfigurációs módosítás egy meglévő fiókon legyen, ha az adott fiókhoz tartozó nyilvános hozzáférési beállítás nem felel meg a házirendnek.

A kényszerítési házirend a megtagadási effektus használatával megakadályozza, hogy egy olyan kérést hozzon létre vagy módosítson, amely lehetővé teszi a nyilvános hozzáférést a Storage-fiók számára. A hatásokkal kapcsolatos további információkért lásd: [Azure Policy effektusok ismertetése](../../governance/policy/concepts/effects.md).

Ha olyan házirendet szeretne létrehozni, amely megtagadási hatással van egy olyan nyilvános hozzáférési beállításra, amely engedélyezi a névtelen kérelmeket, kövesse az [Azure Policy használata a megfelelőség naplózására](#use-azure-policy-to-audit-for-compliance)című részben leírt lépéseket, de adja meg a következő JSON-t a szabályzat-definíció **' policyrule osztály** szakaszában:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Miután létrehozta a szabályzatot a megtagadási hatállyal, és hozzárendeli egy hatókörhöz, a felhasználók nem hozhatnak létre olyan Storage-fiókot, amely lehetővé teszi a nyilvános hozzáférést. A felhasználó nem végezhet olyan konfigurációs módosításokat egy meglévő Storage-fiókon, amely jelenleg lehetővé teszi a nyilvános hozzáférést. Hiba történt a kísérlet során. A fiók létrehozásának vagy konfigurálásának folytatásához a Storage-fiókhoz tartozó nyilvános hozzáférési beállítást **false** értékre kell állítani.

Az alábbi képen látható az a hiba, amely akkor fordul elő, ha olyan Storage-fiókot próbál létrehozni, amely lehetővé teszi a nyilvános hozzáférést (az új fiók alapértelmezése), ha egy megtagadási hatással rendelkező szabályzat megköveteli, hogy a nyilvános hozzáférés ne legyen engedélyezve.

:::image type="content" source="media/anonymous-read-access-prevent/deny-policy-error.png" alt-text="A házirend megsértése esetén a Storage-fiók létrehozásakor előforduló hibát ábrázoló képernyőkép":::

## <a name="permissions-for-allowing-or-disallowing-public-access"></a>A nyilvános hozzáférés engedélyezésére vagy letiltására vonatkozó engedélyek

A Storage-fiók **AllowBlobPublicAccess** tulajdonságának beállításához a felhasználónak rendelkeznie kell a Storage-fiókok létrehozásához és kezeléséhez szükséges engedélyekkel. Az ilyen engedélyeket biztosító Azure szerepköralapú hozzáférés-vezérlési (Azure-RBAC) szerepkörök közé tartozik a **Microsoft. Storage/storageAccounts/Write** vagy a **Microsoft. Storage \* /storageAccounts/** művelet. A művelettel rendelkező beépített szerepkörök a következők:

- A Azure Resource Manager [tulajdonosi](../../role-based-access-control/built-in-roles.md#owner) szerepkör
- A Azure Resource Manager [közreműködő](../../role-based-access-control/built-in-roles.md#contributor) szerepkör
- A [Storage-fiók közreműködői](../../role-based-access-control/built-in-roles.md#storage-account-contributor) szerepköre

Ezek a szerepkörök nem biztosítanak hozzáférést a Storage-fiókban lévő adatAzure Active Directory (Azure AD) használatával. Ezek közé tartoznak azonban a **Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/művelet**, amely hozzáférést biztosít a fiók hozzáférési kulcsaihoz. Ezzel az engedéllyel a felhasználók a fiók hozzáférési kulcsainak használatával férhetnek hozzá a Storage-fiókokban lévő összes adattal.

A szerepkör-hozzárendeléseket a Storage-fiók szintjére vagy annál magasabbra kell korlátozni ahhoz, hogy a felhasználók engedélyezzék vagy letiltsák a nyilvános hozzáférést a Storage-fiók számára. A szerepkör hatókörével kapcsolatos további információkért lásd: [Az Azure RBAC hatókörének megismerése](../../role-based-access-control/scope-overview.md).

Ügyeljen arra, hogy ezeknek a szerepköröknek a hozzárendelését csak azokra korlátozza, akiknek szükségük van egy Storage-fiók létrehozására vagy a tulajdonságainak frissítésére. Használja a legalacsonyabb jogosultsági szint elvét annak biztosítására, hogy a felhasználók a lehető legkevesebb engedélyekkel rendelkezzenek a feladataik végrehajtásához. További információ az Azure RBAC való hozzáférés kezeléséről: [Az Azure RBAC kapcsolatos ajánlott eljárások](../../role-based-access-control/best-practices.md).

> [!NOTE]
> A klasszikus előfizetés-rendszergazdai szerepkörök szolgáltatás rendszergazdája és Co-Administrator tartalmazza a Azure Resource Manager [tulajdonosi](../../role-based-access-control/built-in-roles.md#owner) szerepkörének megfelelőt. A **tulajdonosi** szerepkör tartalmazza az összes műveletet, így az egyik rendszergazdai szerepkörrel rendelkező felhasználó is létrehozhat és kezelhet Storage-fiókokat. További információ: [klasszikus előfizetés-rendszergazdai szerepkörök, Azure-szerepkörök és Azure ad-rendszergazdai szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="next-steps"></a>Következő lépések

- [Névtelen nyilvános olvasási hozzáférés konfigurálása a tárolók és a Blobok számára](anonymous-read-access-configure.md)
- [Nyilvános tárolók és Blobok elérése névtelenül a .NET-tel](anonymous-read-access-client.md)
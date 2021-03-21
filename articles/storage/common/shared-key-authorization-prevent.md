---
title: Megosztott kulccsal való engedélyezés tiltása (előzetes verzió)
titleSuffix: Azure Storage
description: Ha szeretné megkövetelni, hogy az ügyfelek az Azure AD-t használják a kérések engedélyezéséhez, akkor letilthatja a kéréseket a megosztott kulccsal (előzetes verzió) engedélyezett Storage-fiókhoz.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/11/2021
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: b7290abe102d22bb87c87c3c9d13ee99c127b942
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199914"
---
# <a name="prevent-shared-key-authorization-for-an-azure-storage-account-preview"></a>Azure Storage-fiókhoz tartozó megosztott kulcs engedélyezésének tiltása (előzetes verzió)

Az Azure Storage-fiókokra vonatkozó minden biztonságos kérelemnek engedélyezve kell lennie. Alapértelmezés szerint a kérelmeket Azure Active Directory (Azure AD) hitelesítő adatokkal vagy a megosztott kulcsos hitelesítéshez tartozó fiók-hozzáférési kulcs használatával lehet engedélyezni. A két hitelesítési típus közül az Azure AD kiváló biztonságot és könnyű használatot biztosít a megosztott kulcson keresztül, és a Microsoft számára ajánlott. Ha szeretné megkövetelni, hogy az ügyfelek az Azure AD-t használják a kérések engedélyezéséhez, akkor letilthatja a kéréseket a megosztott kulccsal (előzetes verzió) engedélyezett Storage-fiókhoz.

Ha egy Storage-fiókhoz nem engedélyezi a megosztott kulcs engedélyezését, az Azure Storage elutasítja az adott fiókhoz tartozó minden további kérelmet, amely jogosult a fiók hozzáférési kulcsaira. Csak az Azure AD-vel rendelkező biztonságos kérelmek sikeresek lesznek. Az Azure AD használatával kapcsolatos további információkért lásd: a [blobok és várólisták hozzáférésének engedélyezése Azure Active Directory használatával](storage-auth-aad.md).

> [!IMPORTANT]
> A megosztott kulcs engedélyezésének letiltása jelenleg **előzetes** verzióban érhető el. Tekintse meg az Azure-szolgáltatásokra vonatkozó, a bétaverzióban, az előzetes verzióban, vagy más módon még nem közzétett, általánosan elérhetővé vált jogi feltételekhez tartozó [Microsoft Azure előzetes verziójának kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .

Ez a cikk azt ismerteti, hogyan lehet megállapítani a megosztott kulcsos hitelesítéssel küldött kéréseket, és hogyan javíthatja a megosztott kulcsokat a Storage-fiókhoz. Ha szeretné megtudni, hogyan regisztrálhat az előzetes verzióra, tekintse meg [az előzetes](#about-the-preview)verzióról szóló témakört.

## <a name="detect-the-type-of-authorization-used-by-client-applications"></a>Az ügyfélalkalmazások által használt engedélyezési típus észlelése

Ha nem engedélyezi a megosztott kulcs engedélyezését egy Storage-fiók esetében, a megosztott kulcs engedélyezéséhez tartozó fiók-hozzáférési kulcsokat használó ügyfelektől érkező kérések sikertelenek lesznek. Annak megismeréséhez, hogy a megosztott kulcs engedélyezésének letiltása milyen hatással lehet az ügyfélalkalmazások alkalmazására a módosítás előtt, engedélyezze a naplózást és a metrikákat a Storage-fiókhoz. Ezután elemezheti a kérések mintáit egy adott időtartamon belül, hogy meghatározza a kérések engedélyezésének módját.

A metrikák használatával meghatározhatja, hogy a Storage-fiók hány kérést fogadjon el, amelyek a megosztott kulccsal vagy közös hozzáférésű aláírással (SAS) vannak engedélyezve. Naplók használatával határozza meg, hogy mely ügyfelek küldik el ezeket a kérelmeket.

Az előzetes verzióban közös hozzáférésű aláírással végzett kérelmek értelmezésével kapcsolatos további információkért tekintse meg [az előzetes](#about-the-preview)verzióról szóló témakört.

### <a name="monitor-how-many-requests-are-authorized-with-shared-key"></a>A megosztott kulccsal rendelkező kérelmek számának figyelése

A Storage-fiókra irányuló kérések engedélyezésének nyomon követéséhez használja az Azure Metrikaböngésző a Azure Portal. További információ a Metrikaböngészőről: az [Azure Metrikaböngésző első lépései](../../azure-monitor/essentials/metrics-getting-started.md).

Kövesse az alábbi lépéseket egy olyan metrika létrehozásához, amely nyomon követi a megosztott kulccsal vagy SAS-val végzett kérelmeket:

1. Az Azure Portalon nyissa meg a tárfiókot. A **figyelés** szakaszban válassza a **metrikák** lehetőséget.
1. Válassza a **Metrika hozzáadása** lehetőséget. A **metrika** párbeszédpanelen a következő értékeket kell megadnia:
    1. Hagyja meg a **hatókör** mezőt a Storage-fiók nevére.
    1. Adja meg a **metrikai névteret** a *fiókhoz*. Ez a mérőszám a Storage-fiókkal kapcsolatos összes kérésről jelentést küld.
    1. Állítsa a **metrika** mezőt a *tranzakciók* értékre.
    1. Állítsa az **Összesítés** mezőt Sum ( *összeg*) értékre.

    Az új metrika megjeleníti a tranzakciók számának összegét a Storage-fiókhoz egy adott időtartamon belül. Az eredményül kapott metrika a következő képen látható módon jelenik meg:

    :::image type="content" source="media/shared-key-authorization-prevent/configure-metric-account-transactions.png" alt-text="Képernyőfelvétel a metrikák a megosztott kulccsal vagy SAS-vel végzett tranzakciók összegének konfigurálásáról":::

1. Ezután a **szűrő hozzáadása** gombra kattintva hozzon létre egy szűrőt a mérőszámban az engedélyezési típushoz.
1. A **szűrő** párbeszédpanelen a következő értékeket kell megadnia:
    1. Állítsa a **tulajdonság** értékét *hitelesítésre*.
    1. Állítsa az **operátor** mezőt az egyenlőségjel (=) értékre.
    1. A **Values (értékek** ) mezőben válassza a *fiók kulcs* és *sas* elemet.
1. A jobb felső sarokban válassza ki azt az időtartományt, amelynek meg szeretné tekinteni a metrikáját. Azt is jelezheti, hogy a kérelmek összesítésének milyen részletességgel kell megjelennie, ha az intervallumokat 1 perc és 1 hónap között adja meg. Például állítsa be az **időtartományt** 30 napra, az **időrészletességet** pedig 1 napra, hogy az elmúlt 30 napban összesítse a kérelmeket.

Miután konfigurálta a metrikát, a Storage-fiókra irányuló kérések megjelennek a gráfon. Az alábbi képen a megosztott kulccsal rendelkező vagy SAS-tokenrel létrehozott kérelmek láthatók. A kérelmek összesítése naponta történik az elmúlt harminc napban.

:::image type="content" source="media/shared-key-authorization-prevent/metric-shared-key-requests.png" alt-text="A megosztott kulccsal rendelkező összesített kérelmeket bemutató képernyőfelvétel":::

A riasztási szabályt úgy is beállíthatja, hogy értesítést kapjon arról, ha a megosztott kulccsal rendelkező bizonyos számú kérelem a Storage-fiókra vonatkozik. További információ: [metrikus riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../../azure-monitor/alerts/alerts-metric.md).

### <a name="analyze-logs-to-identify-clients-that-are-authorizing-requests-with-shared-key-or-sas"></a>Naplók elemzése a megosztott kulccsal vagy SAS-vel érkező kéréseket engedélyező ügyfelek azonosításához

Az Azure Storage-naplók rögzítik a Storage-fiókkal kapcsolatos kérések részleteit, beleértve a kérelem engedélyezésének módját is. A naplók elemzésével meghatározhatja, hogy mely ügyfelek engedélyezik a megosztott kulccsal vagy SAS-jogkivonattal történő kérelmeket.

Ha a kérelmeket az Azure Storage-fiókjába kívánja naplózni, hogy kiértékelje a jogosultságokat, az Azure Storage-naplózást Azure Monitor (előzetes verzió) használatával is használhatja. További információ: az [Azure Storage figyelése](../blobs/monitor-blob-storage.md).

Az Azure Storage Azure Monitor támogatja a naplózási lekérdezések használatát a naplófájlok elemzéséhez. A naplók lekérdezéséhez használhat Azure Log Analytics munkaterületet. További információ a naplók lekérdezéséről [: oktatóanyag: log Analytics lekérdezések első lépései](../../azure-monitor/logs/log-analytics-tutorial.md).

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Diagnosztikai beállítás létrehozása a Azure Portalban

Az Azure Storage-beli adatAzure Monitor és az Azure Log Analytics való elemzéséhez először létre kell hoznia egy diagnosztikai beállítást, amely meghatározza, hogy milyen típusú kérelmeket és milyen tárolási szolgáltatásokat szeretne naplózni. Ha diagnosztikai beállítást szeretne létrehozni a Azure Portalban, kövesse az alábbi lépéseket:

1. Regisztráljon az [Azure Storage naplózási Azure monitor előzetes](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)verziójában.
1. Hozzon létre egy új Log Analytics munkaterületet az Azure Storage-fiókot tartalmazó előfizetésben, vagy használjon egy meglévő Log Analytics-munkaterületet. A Storage-fiók naplózásának konfigurálása után a naplók a Log Analytics munkaterületen lesznek elérhetők. További információ: [log Analytics munkaterület létrehozása a Azure Portalban](../../azure-monitor/logs/quick-create-workspace.md).
1. Az Azure Portalon nyissa meg a tárfiókot.
1. A figyelés szakaszban válassza a **diagnosztikai beállítások (előzetes verzió)** lehetőséget.
1. Válassza ki azt az Azure Storage-szolgáltatást, amelyhez be szeretné jelentkezni a kérelmeket. Például válassza a **blob** lehetőséget a blob Storage-ba irányuló kérelmek naplózásához.
1. Válassza a **Diagnosztikai beállítások megadása** lehetőséget.
1. Adja meg a diagnosztikai beállítás nevét.
1. A **Kategória részletei** között, a **napló** szakaszban válassza az **StorageRead**, a **StorageWrite** és a **StorageDelete** lehetőséget, hogy naplózza az összes adatkérést a kiválasztott szolgáltatásba.
1. A **célhely részletei** területen válassza **a Küldés log Analyticsba** lehetőséget. Válassza ki az előfizetését és a korábban létrehozott Log Analytics munkaterületet, ahogy az az alábbi képen is látható.

    :::image type="content" source="media/shared-key-authorization-prevent/create-diagnostic-setting-logs.png" alt-text="A naplózási kérelmek diagnosztikai beállításainak létrehozását bemutató képernyőkép":::

A Storage-fiókban minden egyes Azure Storage-erőforráshoz diagnosztikai beállítást hozhat létre.

Miután létrehozta a diagnosztikai beállítást, a rendszer a Storage-fiókra irányuló kérelmeket a beállításnak megfelelően naplózza. További információ: [diagnosztikai beállítás létrehozása az erőforrás-naplók és-metrikák az Azure-ban való összegyűjtéséhez](../../azure-monitor/essentials/diagnostic-settings.md).

A Azure Monitor Azure Storage-naplókban elérhető mezőkre vonatkozó hivatkozásokat itt tekintheti meg: [erőforrás-naplók (előzetes verzió)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-requests-made-with-shared-key-or-sas"></a>Megosztott kulccsal vagy SAS-vel készített kérelmek lekérdezési naplói

Az Azure Storage-naplók a Azure Monitorban tartalmazzák azt a hitelesítési típust, amelyet egy kérésnek a Storage-fiókhoz való elvégzéséhez használt. A megosztott kulccsal vagy SAS-vel rendelkező, az elmúlt hét napban benyújtott kérelmek naplófájljainak beolvasásához nyissa meg Log Analytics munkaterületét. Ezután illessze be a következő lekérdezést egy új napló-lekérdezésbe, és futtassa. Ez a lekérdezés azokat a tíz IP-címet jeleníti meg, amelyeket a leggyakrabban elküldött kérelmeket a megosztott kulccsal vagy SAS-vel való hitelesítésre adott meg:

```kusto
StorageBlobLogs
| where AuthenticationType in ("AccountKey", "SAS") and TimeGenerated > ago(7d)
| summarize count() by CallerIpAddress, UserAgentHeader, AccountName
| top 10 by count_ desc
```

A lekérdezésen alapuló riasztási szabályt úgy is konfigurálhat, hogy értesítést kapjon a megosztott kulccsal vagy SAS-vel felhatalmazott kérelmekről. További információ: a [naplók létrehozása, megtekintése és kezelése Azure monitor használatával](../../azure-monitor/alerts/alerts-log.md).

## <a name="remediate-authorization-via-shared-key"></a>Hitelesítés szervizelése megosztott kulcson keresztül

Miután elvégezte a Storage-fiókra vonatkozó kérések engedélyezését, megteheti, hogy meggátolja a hozzáférést a megosztott kulcson keresztül. Először is frissítenie kell minden olyan alkalmazást, amely megosztott kulcsos hitelesítést használ az Azure AD használatához. A naplók és a metrikák figyeléséhez lásd: az ügyfélalkalmazások által az áttérés nyomon követéséhez [használt engedélyezési típus észlelése](#detect-the-type-of-authorization-used-by-client-applications) . Az Azure AD blob-és üzenetsor-adatokkal való használatával kapcsolatos további információkért lásd: [a blobok és várólisták hozzáférésének engedélyezése Azure Active Directory használatával](storage-auth-aad.md).

Ha biztos abban, hogy nyugodtan el tudja utasítani a megosztott kulccsal rendelkező kéréseket, akkor a Storage-fiók **AllowSharedKeyAccess** tulajdonságát **false** értékre állíthatja.

A **AllowSharedKeyAccess** tulajdonság alapértelmezés szerint nincs beállítva, és nem ad vissza értéket, amíg explicit módon be nem állítja azt. A Storage-fiók engedélyezi a megosztott kulccsal rendelkező kérelmeket, ha a tulajdonság értéke **Null** vagy **igaz**.

> [!WARNING]
> Ha bármely ügyfél jelenleg megosztott kulccsal fér hozzá a Storage-fiókban lévő adatokhoz, akkor a Microsoft azt javasolja, hogy az ügyfeleket az Azure AD-ba telepítse át, mielőtt a megosztott kulcshoz hozzáfér a Storage-fiókhoz.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Az alábbi lépéseket követve engedélyezheti a megosztott kulcs engedélyezését egy Storage-fiókhoz a Azure Portalban:

1. Az Azure Portalon nyissa meg a tárfiókot.
1. Keresse meg a **konfigurációs** beállítást a **Beállítások** területen.
1. A **megosztott kulcs hozzáférésének engedélyezése** **Letiltva** értékre.

    :::image type="content" source="media/shared-key-authorization-prevent/shared-key-access-portal.png" alt-text="A fiók megosztott kulcshoz való hozzáférésének tiltását bemutató képernyőkép":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha nem szeretné engedélyezni a megosztott kulcs engedélyezését egy olyan Storage-fiókhoz, amely PowerShell-lel rendelkezik, telepítse az az [. Storage PowerShell-modult](https://www.powershellgallery.com/packages/Az.Storage)3.4.0 vagy újabb verzióra. Ezután konfigurálja a **AllowSharedKeyAccess** tulajdonságot egy új vagy meglévő Storage-fiókhoz.

Az alábbi példa bemutatja, hogyan lehet letiltani a megosztott kulccsal való hozzáférést egy meglévő Storage-fiókhoz a PowerShell használatával. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -AllowSharedKeyAccess $false
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha egy Storage-fiókhoz az Azure CLI-vel szeretné engedélyezni a megosztott kulcs engedélyezését, telepítse az Azure CLI 2.20.0 vagy újabb verzióját. További információ: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ezután konfigurálja a **allowSharedKeyAccess** tulajdonságot egy új vagy meglévő Storage-fiókhoz.

Az alábbi példa bemutatja, hogyan lehet letiltani a megosztott kulccsal való hozzáférést egy meglévő Storage-fiókhoz az Azure CLI-vel. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-shared-key-access false
```

---

Ha nem engedélyezte a megosztott kulcs engedélyezését, a megosztott kulcsos hitelesítéssel rendelkező Storage-fiókhoz való kérelem sikertelen lesz, hibakód: 403 (tiltott). Az Azure Storage olyan hibaüzenetet ad vissza, amely azt jelzi, hogy a kulcs alapú hitelesítés nem engedélyezett a Storage-fiókban.

### <a name="verify-that-shared-key-access-is-not-allowed"></a>Ellenőrizze, hogy a megosztott kulcshoz való hozzáférés nem engedélyezett-e

Annak ellenőrzéséhez, hogy a megosztott kulcs engedélyezése már nem engedélyezett-e, megkísérelheti egy adatművelet meghívását a fiók hozzáférési kulcsával. A következő példa megkísérli létrehozni a tárolót a hozzáférési kulcs használatával. A hívás sikertelen lesz, ha a megosztott kulcs engedélyezése nem engedélyezett a Storage-fiók esetében. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key> \
    --auth-mode key
```

> [!NOTE]
> A névtelen kérelmek nem engedélyezettek, és akkor is folytatódnak, ha beállította a Storage-fiókot és-tárolót a névtelen nyilvános olvasási hozzáféréshez. További információ: [Névtelen nyilvános olvasási hozzáférés beállítása tárolók és Blobok](../blobs/anonymous-read-access-configure.md)számára.

### <a name="check-the-shared-key-access-setting-for-multiple-accounts"></a>Több fiók közös kulcsú hozzáférésének beállítása

Ha szeretné, hogy a megosztott kulcs hozzáférési beállítása a Storage-fiókok és az optimális teljesítmény között legyen, használhatja az Azure Resource Graph Explorert a Azure Portal. Ha többet szeretne megtudni az Erőforrásgrafikon Explorer használatáról, tekintse meg a gyors útmutató [: az első Resource Graph-lekérdezés futtatása az Azure Resource Graph Explorerben](../../governance/resource-graph/first-query-portal.md).

A következő lekérdezés futtatása az Erőforrásgrafikon Explorerben a Storage-fiókok listáját adja vissza, és megjeleníti az egyes fiókok megosztott kulcsokra vonatkozó hozzáférési beállításait:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowSharedKeyAccess = parse_json(properties).allowSharedKeyAccess
| project subscriptionId, resourceGroup, name, allowSharedKeyAccess
```

## <a name="permissions-for-allowing-or-disallowing-shared-key-access"></a>A megosztott kulcsok elérésének engedélyezéséhez vagy letiltásához szükséges engedélyek

A Storage-fiók **AllowSharedKeyAccess** tulajdonságának beállításához a felhasználónak rendelkeznie kell a Storage-fiókok létrehozásához és kezeléséhez szükséges engedélyekkel. Az ilyen engedélyeket biztosító Azure szerepköralapú hozzáférés-vezérlési (Azure-RBAC) szerepkörök közé tartozik a **Microsoft. Storage/storageAccounts/Write** vagy a **Microsoft. Storage \* /storageAccounts/** művelet. A művelettel rendelkező beépített szerepkörök a következők:

- A Azure Resource Manager [tulajdonosi](../../role-based-access-control/built-in-roles.md#owner) szerepkör
- A Azure Resource Manager [közreműködő](../../role-based-access-control/built-in-roles.md#contributor) szerepkör
- A [Storage-fiók közreműködői](../../role-based-access-control/built-in-roles.md#storage-account-contributor) szerepköre

Ezek a szerepkörök nem biztosítanak hozzáférést a Storage-fiókban lévő adatAzure Active Directory (Azure AD) használatával. Ezek közé tartoznak azonban a **Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/művelet**, amely hozzáférést biztosít a fiók hozzáférési kulcsaihoz. Ezzel az engedéllyel a felhasználók a fiók hozzáférési kulcsainak használatával férhetnek hozzá a Storage-fiókokban lévő összes adattal.

A szerepkör-hozzárendeléseket a Storage-fiók szintjére kell korlátozni, hogy a felhasználók engedélyezzék vagy letiltsák a megosztott kulcsos hozzáférést a Storage-fiókhoz. A szerepkör hatókörével kapcsolatos további információkért lásd: [Az Azure RBAC hatókörének megismerése](../../role-based-access-control/scope-overview.md).

Ügyeljen arra, hogy ezeknek a szerepköröknek a hozzárendelését csak azokra korlátozza, akiknek szükségük van egy Storage-fiók létrehozására vagy a tulajdonságainak frissítésére. Használja a legalacsonyabb jogosultsági szint elvét annak biztosítására, hogy a felhasználók a lehető legkevesebb engedélyekkel rendelkezzenek a feladataik végrehajtásához. További információ az Azure RBAC való hozzáférés kezeléséről: [Az Azure RBAC kapcsolatos ajánlott eljárások](../../role-based-access-control/best-practices.md).

> [!NOTE]
> A klasszikus előfizetés-rendszergazdai szerepkörök szolgáltatás rendszergazdája és Co-Administrator tartalmazza a Azure Resource Manager [tulajdonosi](../../role-based-access-control/built-in-roles.md#owner) szerepkörének megfelelőt. A **tulajdonosi** szerepkör tartalmazza az összes műveletet, így az egyik rendszergazdai szerepkörrel rendelkező felhasználó is létrehozhat és kezelhet Storage-fiókokat. További információ: [klasszikus előfizetés-rendszergazdai szerepkörök, Azure-szerepkörök és Azure ad-rendszergazdai szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="understand-how-disallowing-shared-key-affects-sas-tokens"></a>Ismerje meg, hogy a megosztott kulcs letiltása hogyan befolyásolja az SAS-tokeneket

Ha a megosztott kulcshoz való hozzáférés nem engedélyezett a Storage-fiók esetében, az Azure Storage a SAS és a kérés által megadott szolgáltatás típusa alapján kezeli az SAS-jogkivonatokat. Az alábbi táblázat bemutatja, hogyan történik az egyes SAS-típusok engedélyezése, és hogy az Azure Storage hogyan fogja kezelni ezt az SAS-t, ha a **AllowSharedKeyAccess** tulajdonsága **hamis**.

| SAS típusa | Engedélyezési típus | Viselkedés, ha a AllowSharedKeyAccess hamis |
|-|-|-|
| Felhasználói delegálási SAS (csak blob Storage) | Azure AD | A kérelem engedélyezett. A Microsoft azt javasolja, hogy a felhasználói delegálás SAS-t használja, ha lehetséges, a kiváló biztonság érdekében. |
| Szolgáltatás SAS | Megosztott kulcsos | Az összes Azure Storage-szolgáltatásra vonatkozó kérelem megtagadva. |
| Fiók SAS | Megosztott kulcsos | Az összes Azure Storage-szolgáltatásra vonatkozó kérelem megtagadva. |

A közös hozzáférésű aláírásokkal kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](storage-sas-overview.md).

## <a name="consider-compatibility-with-other-azure-tools-and-services"></a>Más Azure-eszközökkel és-szolgáltatásokkal való kompatibilitás megfontolása

Számos Azure-szolgáltatás közös kulcsú hitelesítést használ az Azure Storage-vel való kommunikációhoz. Ha nem engedélyezi a megosztott kulcs engedélyezését egy Storage-fiók esetében, akkor ezek a szolgáltatások nem férhetnek hozzá az adott fiókban lévő adataihoz, és előfordulhat, hogy az alkalmazásai hátrányosan befolyásolhatják.

Néhány Azure-eszköz lehetővé teszi az Azure AD-hitelesítés használatát az Azure Storage eléréséhez. Az alábbi táblázat néhány népszerű Azure-eszközt felsorol, és megállapítja, hogy az Azure AD-t használhatják-e az Azure Storage-ba irányuló kérések engedélyezésére.

| Azure-eszköz | Azure AD-hitelesítés az Azure Storage-ba |
|-|-|
| Azure Portal | Támogatott. A Azure Portal Azure AD-fiókjával való engedélyezésével kapcsolatos információkért lásd: [a blob-adatokhoz való hozzáférés engedélyezésének kiválasztása a Azure Portal](../blobs/authorize-data-operations-portal.md). |
| AzCopy | BLOB Storage esetén támogatott. További információ a AzCopy-műveletek engedélyezéséről: [válassza ki, hogyan adja meg az engedélyezési hitelesítő adatokat](storage-use-azcopy-v10.md#choose-how-youll-provide-authorization-credentials) a AzCopy dokumentációjában. |
| Azure Storage Explorer | BLOB Storage és csak Azure Data Lake Storage Gen2 esetén támogatott. Az Azure AD-hozzáférés a várólista-tárolóhoz nem támogatott. Ügyeljen arra, hogy kiválassza a megfelelő Azure AD-bérlőt. További információ: [Bevezetés a Storage Explorer használatába](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) |
| Azure PowerShell | Támogatott. További információ az Azure ad-val kapcsolatos PowerShell-parancsok engedélyezéséről: [PowerShell-parancsok futtatása Azure ad-beli hitelesítő adatokkal a Blobok adatainak eléréséhez](../blobs/authorize-data-operations-powershell.md) , illetve [PowerShell-parancsok futtatása Azure ad-beli hitelesítő adatokkal a várólista adatainak eléréséhez](../queues/authorize-data-operations-powershell.md). |
| Azure CLI | Támogatott. További információ arról, hogyan engedélyezhető az Azure CLI-parancsok használata az Azure AD-vel a blob-és üzenetsor-adatok eléréséhez: [Azure CLI-parancsok futtatása Azure ad-beli hitelesítő adatokkal a blob-vagy üzenetsor-adatok eléréséhez](../blobs/authorize-data-operations-cli.md). |
| Azure IoT Hub | Támogatott. További információ: [IoT hub virtuális hálózatok támogatása](../../iot-hub/virtual-network-support.md). |
| Azure Cloud Shell | A Azure Cloud Shell a Azure Portal integrált rendszerhéja. Azure Cloud Shell a fájlok megőrzését a Storage-fiókban lévő Azure-fájlmegosztás tárolja. Ezek a fájlok elérhetetlenné válnak, ha a megosztott kulcs engedélyezése nem engedélyezett a Storage-fiók esetében. További információ: [a Microsoft Azure Files Storage összekötése](../../cloud-shell/overview.md#connect-your-microsoft-azure-files-storage). <br /><br /> Ha a Azure Cloud Shell parancsainak futtatásával szeretné felügyelni azokat a Storage-fiókokat, amelyekhez a megosztott kulcsokhoz való hozzáférés nem engedélyezett, először győződjön meg arról, hogy a szükséges engedélyeket megadta a fiókoknak az Azure RBAC-on keresztül. További információ: [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../../role-based-access-control/overview.md). |

## <a name="transition-azure-files-and-table-storage-workloads"></a>Áttérési Azure Files és táblázatos tárolási számítási feladatok

Az Azure Storage csak a blob-és üzenetsor-tárolásra vonatkozó kérelmek esetében támogatja az Azure AD-hitelesítést. Ha nem engedélyezi a megosztott kulccsal való engedélyezést egy Storage-fiók esetében, akkor a megosztott kulcs-engedélyezést használó Azure Files vagy table Storage-kérelmek sikertelenek lesznek. Mivel a Azure Portal mindig megosztott kulcsos hitelesítést használ a fájl-és Table-adateléréshez, ha a Storage-fiókhoz nem engedélyezi a megosztott kulccsal való engedélyezést, akkor nem fog tudni hozzáférni a fájlhoz vagy a táblához a Azure Portal.

A Microsoft azt javasolja, hogy a megosztott kulccsal való hozzáférés letiltása előtt telepítse át a Azure Files vagy a Table Storage-adatok egy külön Storage-fiókba való áttelepítését, vagy ne alkalmazza ezt a beállítást olyan Storage-fiókokra, amelyek támogatják a Azure Files vagy a Table Storage munkaterheléseket.

Ha nem engedélyezi a megosztott kulcs elérését egy Storage-fiókhoz, az nem érinti a Azure Files SMB-kapcsolatait.

## <a name="about-the-preview"></a>Az előzetes verzió ismertetése

A megosztott kulcs engedélyezésének előzetes verziója az Azure nyilvános felhőben érhető el. A szolgáltatás csak a Azure Resource Manager telepítési modellt használó Storage-fiókok esetében támogatott. További információ arról, hogy mely tárolási fiókok használják a Azure Resource Manager telepítési modellt: a [Storage-fiókok típusai](storage-account-overview.md#types-of-storage-accounts).

Az előzetes verzió az alábbi szakaszokban ismertetett korlátozásokat tartalmazza.

### <a name="metrics-and-logging-report-all-requests-made-with-a-sas-regardless-of-how-they-are-authorized"></a>Metrikák és naplózási jelentés az SAS-val kapcsolatos összes kérelem, függetlenül attól, hogy milyen jogosultsággal rendelkeznek

Az Azure-metrikák és a bejelentkezés Azure Monitor nem tesz különbséget az előzetes verzióban található különböző típusú közös hozzáférési aláírások között. Az Azure Metrikaböngésző **sas** -szűrője és az Azure Storage-beli **sas** -mezője Azure monitor a bármely sas-típussal jóváhagyott jelentési kérelmeket. Azonban a közös hozzáférésű aláírások különböző típusai eltérőek, és másképp viselkednek, ha a megosztott kulcshoz való hozzáférés nem engedélyezett:

- A szolgáltatás SAS-jogkivonata vagy a fiók SAS-tokenje megosztott kulccsal van ellátva, és a blob Storage-ra vonatkozó kérelemben nem engedélyezett, ha a **AllowSharedKeyAccess** tulajdonság **hamis** értékre van állítva.
- A felhasználói delegálási SAS engedélyezve van az Azure AD-ben, és a blob Storage iránti kérelemben engedélyezett, ha a **AllowSharedKeyAccess** tulajdonság **hamis** értékre van állítva.

Ha a Storage-fiók forgalmát értékeli, vegye figyelembe, hogy az [ügyfélalkalmazások által használt engedélyezési típus észlelése](#detect-the-type-of-authorization-used-by-client-applications) című témakörben leírtak szerint a metrikák és a naplók is tartalmazhatnak felhasználói DELEGÁLÁSi sas-vel készített kérelmeket. Ha többet szeretne megtudni arról, hogy az Azure Storage hogyan válaszol az SAS-re, ha a **AllowSharedKeyAccess** tulajdonság értéke **false (hamis**), tekintse meg a [megosztott kulcs tiltása a sas-jogkivonatokat érintő](#understand-how-disallowing-shared-key-affects-sas-tokens)tudnivalókat ismertető témakört.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Storage-beli adathozzáférés engedélyezése](storage-auth.md)
- [Blobokhoz és várólistákhoz való hozzáférés engedélyezése Azure Active Directory használatával](storage-auth-aad.md)
- [Engedélyezés megosztott kulccsal](/rest/api/storageservices/authorize-with-shared-key)

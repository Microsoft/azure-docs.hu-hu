---
title: Transport Layer Security (TLS) minimálisan szükséges verziójának érvényesítése a bejövő kérelmekhez
titleSuffix: Azure Storage
description: Konfigurálja a Storage-fiókot úgy, hogy az Azure Storage-ra irányuló ügyfelek számára a Transport Layer Security (TLS) minimális verzióját igényli.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 257cd8dce2a080203f116a6f0d5b7c7ebd6d13f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593176"
---
# <a name="enforce-a-minimum-required-version-of-transport-layer-security-tls-for-requests-to-a-storage-account"></a>Transport Layer Security (TLS) minimálisan szükséges verziójának kikényszerítés a Storage-fiókra irányuló kérelmekhez

Az ügyfélalkalmazás és az Azure Storage-fiók közötti kommunikáció titkosítása Transport Layer Security (TLS) használatával történik. A TLS egy szabványos titkosítási protokoll, amely biztosítja az adatvédelem és az adatok integritását az ügyfelek és a szolgáltatások között az interneten keresztül. A TLS-vel kapcsolatos további információkért lásd: [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security).

Az Azure Storage jelenleg a TLS protokoll három verzióját támogatja: 1,0, 1,1 és 1,2. Az Azure Storage TLS 1,2-t használ a nyilvános HTTPS-végpontokon, de a TLS 1,0 és a TLS 1,1 továbbra is támogatott a visszamenőleges kompatibilitás érdekében.

Alapértelmezés szerint az Azure Storage-fiókok lehetővé teszik, hogy az ügyfelek a TLS, TLS 1,0 vagy újabb rendszer legrégebbi verziójával küldjék el és fogadják az adatfogadást. A szigorúbb biztonsági intézkedések betartatásához beállíthatja a Storage-fiókját, hogy megkövetelje, hogy az ügyfelek a TLS újabb verziójával küldjék el és fogadják az adataikat. Ha egy Storage-fiókhoz a TLS minimális verziója szükséges, akkor a régebbi verzióval végrehajtott kérelmek sikertelenek lesznek.

Ez a cikk azt ismerteti, hogyan lehet a Storage-fiókok számára a biztonságos TLS-t folyamatosan kezelni a FOGD (észlelés – szervizelés – naplózási irányítás) keretrendszer használatával.

További információ a TLS adott verziójának megadásáról az ügyfélalkalmazások kérelmének elküldésekor: [Transport Layer Security (TLS) konfigurálása ügyfélalkalmazás számára](transport-layer-security-configure-client-version.md).

## <a name="detect-the-tls-version-used-by-client-applications"></a>Az ügyfélalkalmazások által használt TLS-verzió észlelése

A Storage-fiók minimális TLS-verziójának kikényszerített elutasítása esetén olyan ügyfelektől érkező kéréseket kell elutasítania, amelyek adatokat küldenek a TLS egy régebbi verziójával. Ha meg szeretné tudni, hogy a minimális TLS-verzió konfigurálása milyen hatással lehet az ügyfélalkalmazások használatára, a Microsoft javasolja, hogy engedélyezze a naplózást az Azure Storage-fiókhoz, és elemezze a naplókat egy idő elteltével a TLS-ügyfélalkalmazások által használt verziók észlelése érdekében.

A kérelmek Azure Storage-fiókba való naplózásához és az ügyfél által használt TLS-verzió meghatározásához használhatja az Azure Storage-naplózást Azure Monitor (előzetes verzió). További információ: az [Azure Storage figyelése](../blobs/monitor-blob-storage.md).

Az Azure Storage Azure Monitor támogatja a naplózási lekérdezések használatát a naplófájlok elemzéséhez. A naplók lekérdezéséhez használhat Azure Log Analytics munkaterületet. További információ a naplók lekérdezéséről [: oktatóanyag: log Analytics lekérdezések első lépései](../../azure-monitor/logs/log-analytics-tutorial.md).

Az Azure Storage-beli adatAzure Monitor és az Azure Log Analytics való elemzéséhez először létre kell hoznia egy diagnosztikai beállítást, amely meghatározza, hogy milyen típusú kérelmeket és milyen tárolási szolgáltatásokat szeretne naplózni. Az Azure Monitor Azure Storage-naplók nyilvános előzetes verzióban érhetők el, és elérhetők az előzetes teszteléshez az összes nyilvános felhőben. Ez az előzetes verzió lehetővé teszi a Blobok (például a Azure Data Lake Storage Gen2), a fájlok, a várólisták és a táblák naplófájljainak naplózását. Ha diagnosztikai beállítást szeretne létrehozni a Azure Portalban, kövesse az alábbi lépéseket:

1. Hozzon létre egy új Log Analytics munkaterületet az Azure Storage-fiókot tartalmazó előfizetésben. A Storage-fiók naplózásának konfigurálása után a naplók a Log Analytics munkaterületen lesznek elérhetők. További információ: [log Analytics munkaterület létrehozása a Azure Portalban](../../azure-monitor/logs/quick-create-workspace.md).
1. Az Azure Portalon nyissa meg a tárfiókot.
1. A figyelés szakaszban válassza a **diagnosztikai beállítások (előzetes verzió)** lehetőséget.
1. Válassza ki azt az Azure Storage-szolgáltatást, amelyhez be szeretné jelentkezni a kérelmeket. Például válassza a **blob** lehetőséget a blob Storage-ba irányuló kérelmek naplózásához.
1. Válassza a **Diagnosztikai beállítások megadása** lehetőséget.
1. Adja meg a diagnosztikai beállítás nevét.
1. A **Kategória részletei** területen, a **napló** szakaszban válassza ki a naplózandó kérelmek típusát. Az olvasási, írási és törlési kérelmek naplózása is megadható. Ha például a **StorageRead** és a **StorageWrite** lehetőséget választja, a a kiválasztott szolgáltatásba írja be az olvasási és írási kérelmeket.
1. A **célhely részletei** területen válassza **a Küldés log Analyticsba** lehetőséget. Válassza ki az előfizetését és a korábban létrehozott Log Analytics munkaterületet, ahogy az az alábbi képen is látható.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="A naplózási kérelmek diagnosztikai beállításainak létrehozását bemutató képernyőkép":::

Miután létrehozta a diagnosztikai beállítást, a rendszer a Storage-fiókra irányuló kérelmeket a beállításnak megfelelően naplózza. További információ: [diagnosztikai beállítás létrehozása az erőforrás-naplók és-metrikák az Azure-ban való összegyűjtéséhez](../../azure-monitor/essentials/diagnostic-settings.md).

A Azure Monitor Azure Storage-naplókban elérhető mezőkre vonatkozó hivatkozásokat itt tekintheti meg: [erőforrás-naplók (előzetes verzió)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview).

### <a name="query-logged-requests-by-tls-version"></a>Naplózott kérelmek lekérdezése TLS-verzió alapján

Az Azure Storage-naplók Azure Monitor tartalmazzák a TLS-verziót, amely a kérések egy Storage-fiókba küldésére szolgál. A **TlsVersion** tulajdonság használatával ellenőrizhető a naplózott kérelem TLS-verziója.

A Log Analytics munkaterület megnyitásával megállapíthatja, hogy a blob Storage-hoz képest hány kérelem történt a TLS különböző verzióival az elmúlt hét napban. Ezután illessze be a következő lekérdezést egy új napló-lekérdezésbe, és futtassa. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

Az eredmények a TLS egyes verzióira vonatkozó kérelmek számának számát mutatják:

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="Képernyőfelvétel a log Analytics-lekérdezés eredményeiről a TLS-verzió visszaküldéséhez":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>Naplózott kérelmek lekérdezése a hívó IP-címe és a felhasználói ügynök fejléce alapján

Az Azure Storage Azure Monitor a hívó IP-címét és a felhasználói ügynök fejlécét is tartalmazza, amellyel kiértékelheti, hogy mely ügyfélalkalmazások férhetnek hozzá a Storage-fiókhoz. Ezeket az értékeket elemezve eldöntheti, hogy az ügyfélalkalmazások frissíteni kell-e a TLS újabb verziójának használatára, vagy az ügyfél kérelmének elutasításához, ha az nem a TLS minimális verziójával lett elküldve.

A következő lekérdezés új naplózási lekérdezésbe való beillesztésével meghatározhatja, hogy mely ügyfelek kezdeményezték a TLS 1,2-nél régebbi TLS-verziót, majd futtassa azt. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="remediate-security-risks-with-a-minimum-version-of-tls"></a>Biztonsági kockázatok szervizelése a TLS minimális verziójával

Ha biztos abban, hogy a TLS régebbi verzióit használó ügyfelektől érkező forgalom minimális, vagy a TLS egy régebbi verziójával végrehajtott sikertelen kérelmek esetében elfogadható, akkor elkezdheti a minimális TLS-verzió kényszerítését a Storage-fiókban. Ha azt szeretné, hogy az ügyfelek a TLS minimális verzióját használják a Storage-fiókra irányuló kérések elvégzésére, az adataira vonatkozó biztonsági kockázat minimálisra csökkentése érdekében egy stratégia részét képezi.

> [!IMPORTANT]
> Ha olyan szolgáltatást használ, amely az Azure Storage-hoz csatlakozik, győződjön meg arról, hogy a szolgáltatás a TLS megfelelő verzióját használja az Azure Storage-ba irányuló kérések elküldéséhez, mielőtt beállítja a szükséges minimális verziót a Storage-fiókhoz.

### <a name="configure-the-minimum-tls-version-for-a-storage-account"></a>A Storage-fiók minimális TLS-verziójának konfigurálása

A Storage-fiók minimális TLS-verziójának konfigurálásához állítsa be a **MinimumTlsVersion** verzióját a fiókhoz. Ez a tulajdonság minden olyan Storage-fiókhoz elérhető, amely a Azure Resource Manager telepítési modellel lett létrehozva. További információ a Azure Resource Manager telepítési modellről: a [Storage-fiók áttekintése](storage-account-overview.md).

A **MinimumTlsVersion** tulajdonság alapértelmezés szerint nincs beállítva, és nem ad vissza értéket, amíg explicit módon be nem állítja azt.  Ha a tulajdonság értéke **Null**, a Storage-fiók engedélyezi a TLS 1,0-es vagy újabb verziójával küldött kérelmeket.

# <a name="portal"></a>[Portál](#tab/portal)

Amikor létrehoz egy Azure Portal Storage-fiókot, a rendszer alapértelmezés szerint a TLS minimális verzióját 1,2-re állítja.

Ha egy meglévő Storage-fiók minimális TLS-verzióját szeretné konfigurálni a Azure Portal, kövesse az alábbi lépéseket:

1. Az Azure Portalon nyissa meg a tárfiókot.
1. Válassza ki a **konfigurációs** beállítást.
1. A **TLS minimális verziója** területen a legördülő listából válassza ki az ebben a Storage-fiókban található adatok eléréséhez szükséges TLS minimális verzióját az alábbi ábrán látható módon.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="A TLS minimális verziójának konfigurálását bemutató képernyőkép a Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha egy Storage-fiók minimális TLS-verzióját szeretné konfigurálni a PowerShell-lel, telepítse [Azure PowerShell 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) vagy újabb verziót. Ezután konfigurálja a **MinimumTLSVersion** tulajdonságot egy új vagy meglévő Storage-fiókhoz. A **MinimumTlsVersion** érvényes értékei: `TLS1_0` , `TLS1_1` és `TLS1_2` .

A következő példa létrehoz egy Storage-fiókot, és beállítja a **MinimumTLSVersion** a TLS 1,1-re, majd frissíti a fiókot, és beállítja a **MinimumTLSVersion** a TLS 1,2-re. A példa az egyes esetekben a tulajdonság értékét is lekéri. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with MinimumTlsVersion set to TLS 1.1.
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS `
    -MinimumTlsVersion TLS1_1

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion

# Update the MinimumTlsVersion version for the storage account to TLS 1.2.
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -MinimumTlsVersion TLS1_2

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-vel rendelkező Storage-fiókok minimális TLS-verziójának konfigurálásához telepítse az Azure CLI 2.9.0 vagy újabb verzióját. További információ: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ezután konfigurálja a **minimumTlsVersion** tulajdonságot egy új vagy meglévő Storage-fiókhoz. A **minimumTlsVersion** érvényes értékei: `TLS1_0` , `TLS1_1` és `TLS1_2` .

A következő példa létrehoz egy Storage-fiókot, és beállítja a **minimumTLSVersion** a TLS 1,1-re. Ezután frissíti a fiókot, és beállítja a **minimumTLSVersion** tulajdonságot a TLS 1,2 értékre. A példa az egyes esetekben a tulajdonság értékét is lekéri. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --min-tls-version TLS1_1

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --min-tls-version TLS1_2

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv
```

# <a name="template"></a>[Sablon](#tab/template)

Egy sablonhoz tartozó Storage-fiók minimális TLS-verziójának konfigurálásához hozzon létre egy sablont a **MinimumTLSVersion** tulajdonsággal, a, vagy a értékre `TLS1_0` `TLS1_1` `TLS1_2` . A következő lépések azt ismertetik, hogyan lehet sablont létrehozni a Azure Portalban.

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget.
1. A **Keresés a piactéren** mezőbe írja be a **sablon központi telepítése** kifejezést, majd nyomja le az **ENTER** billentyűt.
1. Válassza **template Deployment (üzembe helyezés egyéni sablonok használatával) (előzetes verzió)**, válassza a **Létrehozás** lehetőséget, majd **a szerkesztőben válassza a saját sablon** létrehozása lehetőséget.
1. A sablon szerkesztőjében illessze be a következő JSON-t egy új fiók létrehozásához, és állítsa be a TLS 1,2-es minimális verzióját. Ne felejtse el lecserélni a helyőrzőket a saját értékeire a szögletes zárójelben.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'tls')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "minimumTlsVersion": "TLS1_2"
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. Mentse a sablont.
1. Adja meg az erőforráscsoport paramétert, majd válassza a **felülvizsgálat + létrehozás** gombot a sablon telepítéséhez és a **MinimumTLSVersion** tulajdonsággal konfigurált Storage-fiók létrehozásához.

---

> [!NOTE]
> A Storage-fiók minimális TLS-verziójának frissítése után akár 30 másodpercig is eltarthat, amíg a módosítás teljesen propagálva lesz.

A TLS minimális verziójának konfigurálásához az Azure Storage erőforrás-szolgáltató 2019-04-01-es vagy újabb verziójára van szükség. További információ: [Azure Storage erőforrás-szolgáltató REST API](/rest/api/storagerp/).

### <a name="check-the-minimum-required-tls-version-for-multiple-accounts"></a>Több fiókhoz tartozó minimálisan szükséges TLS-verzió keresése

Ha szeretné megtekinteni a minimálisan szükséges TLS-verziót egy olyan Storage-fiókon belül, amely optimális teljesítménnyel rendelkezik, használhatja az Azure Resource Graph Explorert a Azure Portal. Ha többet szeretne megtudni az Erőforrásgrafikon Explorer használatáról, tekintse meg a gyors útmutató [: az első Resource Graph-lekérdezés futtatása az Azure Resource Graph Explorerben](../../governance/resource-graph/first-query-portal.md).

A következő lekérdezés futtatása az Erőforrásgrafikon Explorerben a Storage-fiókok listáját adja vissza, és megjeleníti az egyes fiókok minimális TLS-verzióját:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

### <a name="test-the-minimum-tls-version-from-a-client"></a>A TLS minimális verziójának tesztelése egy ügyfélről

Annak ellenőrzéséhez, hogy a Storage-fiók minimálisan szükséges TLS-verziója tiltja-e a régebbi verzióval indított hívásokat, beállíthatja, hogy az ügyfél a TLS régebbi verzióját használja. További információ az ügyfélnek a TLS adott verziójának használatára való konfigurálásáról: [Transport Layer Security (TLS) konfigurálása ügyfélalkalmazás számára](transport-layer-security-configure-client-version.md).

Amikor egy ügyfél olyan TLS-verzióval fér hozzá egy Storage-fiókhoz, amely nem felel meg a fiókhoz konfigurált minimális TLS-verziónak, az Azure Storage a 400-as hibakódú hibát (hibás kérés) adja vissza, és egy üzenet jelzi, hogy a használt TLS-verzió nem jogosult kérések létrehozására ezen a Storage-fiókon keresztül.

## <a name="use-azure-policy-to-audit-for-compliance"></a>A megfelelőség naplózása Azure Policy használata

Ha nagy számú Storage-fiókkal rendelkezik, érdemes lehet naplózást végeznie, hogy minden fiók konfigurálva legyen a vállalat által igényelt minimális TLS-verzióra. A Storage-fiókok megfelelőségének naplózásához használja a Azure Policy. A Azure Policy egy olyan szolgáltatás, amellyel olyan szabályzatokat hozhat létre, rendelhet hozzá és kezelhet, amelyek szabályokat alkalmaznak az Azure-erőforrásokra. Azure Policy segít megőrizni ezeket az erőforrásokat a vállalati szabványoknak és a szolgáltatói szerződéseknek. További információ: [Azure Policy áttekintése](../../governance/policy/overview.md).

### <a name="create-a-policy-with-an-audit-effect"></a>Naplózási hatású házirend létrehozása

A Azure Policy olyan hatásokat támogat, amelyek meghatározzák, hogy mi történik, ha egy házirend-szabályt kiértékelnek egy erőforrással szemben. A naplózási effektus figyelmeztetést hoz létre, ha egy erőforrás nem felel meg az előírásoknak, de nem állítja le a kérést. A hatásokkal kapcsolatos további információkért lásd: [Azure Policy effektusok ismertetése](../../governance/policy/concepts/effects.md).

Az alábbi lépéseket követve hozhat létre olyan házirendet, amely a Azure Portal minimális TLS-verziójának naplózási hatásával rendelkezik:

1. A Azure Portal navigáljon a Azure Policy szolgáltatáshoz.
1. A **szerzői műveletek** szakaszban válassza a **definíciók** lehetőséget.
1. Új házirend-definíció létrehozásához válassza a **házirend-definíció hozzáadása** lehetőséget.
1. A **definíció helye** mezőnél válassza a **továbbiak** lehetőséget, hogy megadja a naplózási házirend erőforrásának helyét.
1. Adja meg a szabályzat nevét. Igény szerint megadhat egy leírást és egy kategóriát is.
1. A **házirend-szabály** területen adja hozzá a következő házirend-definíciót a **' policyrule osztály** szakaszhoz.

    ```json
    {
      "policyRule": {
        "if": {
          "allOf": [
            {
              "field": "type",
              "equals": "Microsoft.Storage/storageAccounts"
            },
            {
              "not": {
                "field": "Microsoft.Storage/storageAccounts/minimumTlsVersion",
                "equals": "TLS1_2"
              }
            }
          ]
        },
        "then": {
          "effect": "audit"
        }
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

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/compliance-report-policy-portal.png" alt-text="A minimális TLS-verzióra vonatkozó naplózási szabályzat megfelelőségi jelentését bemutató képernyőkép":::

## <a name="use-azure-policy-to-enforce-the-minimum-tls-version"></a>Azure Policy használata a TLS minimális verziójának betartatásához

Azure Policy támogatja a felhő irányítását, mivel biztosítja, hogy az Azure-erőforrások megfeleljenek a követelményeknek és a szabványoknak. A szervezeten belüli Storage-fiókok minimális TLS-verziójának betartatásához létrehozhat egy olyan házirendet, amely megakadályozza egy olyan új Storage-fiók létrehozását, amely a minimális TLS-követelményt a TLS egy régebbi verziójára állítja be, mint amelyet a szabályzat diktál. Ez a szabályzat azt is megakadályozza, hogy az összes konfigurációs módosítás egy meglévő fiókon legyen, ha az adott fiókhoz tartozó minimális TLS-verzió beállítása nem felel meg a házirendnek.

A kényszerítési házirend a megtagadási effektus használatával megakadályozza a Storage-fiók létrehozásához vagy módosításához szükséges kérelmeket, így a minimális TLS-verzió már nem felel meg a szervezet szabványainak. A hatásokkal kapcsolatos további információkért lásd: [Azure Policy effektusok ismertetése](../../governance/policy/concepts/effects.md).

Ha olyan házirendet szeretne létrehozni, amely megtagadási hatással van a TLS 1,2-nél alacsonyabb minimális TLS-verzióra, kövesse az [Azure Policy használata a megfelelőség naplózásához](#use-azure-policy-to-audit-for-compliance)című témakörben leírt lépéseket, de adja meg a következő JSON-t a szabályzat-definíció **' policyrule osztály** szakaszában:

```json
{
  "policyRule": {
    "if": {
      "allOf": [
        {
          "field": "type",
          "equals": "Microsoft.Storage/storageAccounts"
        },
        {
          "not": {
            "field": "Microsoft.Storage/storageAccounts/minimumTlsVersion",
            "equals": "TLS1_2"
          }
        }
      ]
    },
    "then": {
      "effect": "deny"
    }
  }
}
```

Miután létrehozta a szabályzatot a megtagadási hatállyal, és hozzárendeli egy hatókörhöz, a felhasználó nem hozhat létre olyan Storage-fiókot, amely legalább 1,2-nél régebbi TLS-verzióval rendelkezik. A felhasználó nem végezhet olyan konfigurációs módosításokat egy meglévő Storage-fiókban, amelyekhez jelenleg legalább 1,2-nél régebbi TLS-verzió szükséges. Hiba történt a kísérlet során. A fiók létrehozásának vagy konfigurálásának folytatásához a Storage-fiókhoz szükséges minimális TLS-verziót 1,2-re kell állítani.

Az alábbi képen látható az a hiba, amely akkor fordul elő, ha olyan Storage-fiókot próbál létrehozni, amely a TLS 1,0-as minimális TLS-verzióra van beállítva (ez az alapértelmezett érték egy új fiók esetében), ha egy megtagadási hatással rendelkező szabályzat megköveteli, hogy a TLS 1,2-es minimális TLS-verzió legyen beállítva.

:::image type="content" source="media/transport-layer-security-configure-minimum-version/deny-policy-error.png" alt-text="A házirend megsértése esetén a Storage-fiók létrehozásakor előforduló hibát ábrázoló képernyőkép":::

## <a name="permissions-necessary-to-require-a-minimum-version-of-tls"></a>A TLS minimális verziójának megköveteléséhez szükséges engedélyek

A Storage-fiók **MinimumTlsVersion** tulajdonságának beállításához a felhasználónak rendelkeznie kell a Storage-fiókok létrehozásához és kezeléséhez szükséges engedélyekkel. Az ilyen engedélyeket biztosító Azure szerepköralapú hozzáférés-vezérlési (Azure-RBAC) szerepkörök közé tartozik a **Microsoft. Storage/storageAccounts/Write** vagy a **Microsoft. Storage \* /storageAccounts/** művelet. A művelettel rendelkező beépített szerepkörök a következők:

- A Azure Resource Manager [tulajdonosi](../../role-based-access-control/built-in-roles.md#owner) szerepkör
- A Azure Resource Manager [közreműködő](../../role-based-access-control/built-in-roles.md#contributor) szerepkör
- A [Storage-fiók közreműködői](../../role-based-access-control/built-in-roles.md#storage-account-contributor) szerepköre

Ezek a szerepkörök nem biztosítanak hozzáférést a Storage-fiókban lévő adatAzure Active Directory (Azure AD) használatával. Ezek közé tartoznak azonban a **Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/művelet**, amely hozzáférést biztosít a fiók hozzáférési kulcsaihoz. Ezzel az engedéllyel a felhasználók a fiók hozzáférési kulcsainak használatával férhetnek hozzá a Storage-fiókokban lévő összes adattal.

A szerepkör-hozzárendeléseket a Storage-fiók szintjére vagy annál magasabbra kell korlátozni ahhoz, hogy a felhasználó megkövetelje a TLS minimális verzióját a Storage-fiókhoz. A szerepkör hatókörével kapcsolatos további információkért lásd: [Az Azure RBAC hatókörének megismerése](../../role-based-access-control/scope-overview.md).

Ügyeljen arra, hogy ezeknek a szerepköröknek a hozzárendelését csak azokra korlátozza, akiknek szükségük van egy Storage-fiók létrehozására vagy a tulajdonságainak frissítésére. Használja a legalacsonyabb jogosultsági szint elvét annak biztosítására, hogy a felhasználók a lehető legkevesebb engedélyekkel rendelkezzenek a feladataik végrehajtásához. További információ az Azure RBAC való hozzáférés kezeléséről: [Az Azure RBAC kapcsolatos ajánlott eljárások](../../role-based-access-control/best-practices.md).

> [!NOTE]
> A klasszikus előfizetés-rendszergazdai szerepkörök szolgáltatás rendszergazdája és Co-Administrator tartalmazza a Azure Resource Manager [tulajdonosi](../../role-based-access-control/built-in-roles.md#owner) szerepkörének megfelelőt. A **tulajdonosi** szerepkör tartalmazza az összes műveletet, így az egyik rendszergazdai szerepkörrel rendelkező felhasználó is létrehozhat és kezelhet Storage-fiókokat. További információ: [klasszikus előfizetés-rendszergazdai szerepkörök, Azure-szerepkörök és Azure ad-rendszergazdai szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="network-considerations"></a>Hálózati szempontok

Amikor egy ügyfél kérelmet küld a Storage-fióknak, az ügyfél először a Storage-fiók nyilvános végpontját hozza létre a kérések feldolgozása előtt. A TLS-verzió minimális beállítása a kapcsolatok létrehozása után van bejelölve. Ha a kérelem a TLS egy korábbi verzióját használja, mint amit a beállítás meghatároz, a kapcsolódás továbbra is sikeres lesz, de a kérelem végül sikertelen lesz. Az Azure Storage nyilvános végpontokkal kapcsolatos további információkért lásd: [erőforrás URI-szintaxisa](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#resource-uri-syntax).

## <a name="next-steps"></a>Következő lépések

- [Transport Layer Security (TLS) konfigurálása ügyfélalkalmazás számára](transport-layer-security-configure-client-version.md)
- [Biztonsági javaslatok a blob Storage-hoz](../blobs/security-recommendations.md)

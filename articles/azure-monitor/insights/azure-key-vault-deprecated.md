---
title: Azure Key Vault megoldás a Azure Monitorban | Microsoft Docs
description: Azure Key Vault naplók áttekintéséhez használhatja a Azure Monitor Azure Key Vault megoldását.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/27/2019
ms.openlocfilehash: 24869841a106a68234cfec600d9a98e519f8c6cc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734196"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Azure Key Vault elemzési megoldás a Azure Monitor

> [!NOTE]
> Ez a megoldás elavult. [A Key Vault Azure monitor használatát javasoljuk](./key-vault-insights-overview.md).

![Key Vault szimbólum](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A Azure Monitor Azure Key Vault megoldásával áttekintheti Azure Key Vault AuditEvent naplóit.

A megoldás használatához engedélyeznie kell a Azure Key Vault diagnosztika naplózását, és Log Analytics munkaterületre kell irányítani a diagnosztikát. A naplókat nem szükséges az Azure Blob Storage-ba írni.

> [!NOTE]
> Január 2017-án a naplók Key Vaultból való küldésének támogatott módja Log Analytics megváltozott. Ha az Ön által használt Key Vault megoldás a címben *(elavult)* jelenik meg, tekintse át a [Migrálás a régi Key Vault megoldásból](#migrating-from-the-old-key-vault-solution) című témakört a követendő lépésekhez.
>
>

## <a name="install-and-configure-the-solution"></a>A megoldás telepítése és konfigurálása
A Azure Key Vault megoldás telepítéséhez és konfigurálásához kövesse az alábbi utasításokat:

1. A Azure Key Vault megoldás Log Analytics-munkaterülethez való hozzáadásához használja a [Solutions Gallery Azure monitor-megoldások hozzáadása](./solutions.md) a-ból című témakörben leírt eljárást.
2. Diagnosztikai naplózás engedélyezése a figyelni kívánt Key Vault-erőforrások számára a [portál](#enable-key-vault-diagnostics-in-the-portal) vagy a [PowerShell](#enable-key-vault-diagnostics-using-powershell) használatával

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Key Vault diagnosztika engedélyezése a portálon

1. A Azure Portal navigáljon a figyelni kívánt Key Vault erőforráshoz
2. Válassza a *diagnosztikai beállítások* elemet a következő oldal megnyitásához

   ![Képernyőkép a diagnosztikai beállítások oldaláról a Key Vault erőforrás ContosoKVSCUS. a diagnosztika bekapcsolásának lehetősége kiemelve.](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Kattintson a *diagnosztika bekapcsolása* elemre a következő oldal megnyitásához

   ![A diagnosztikai beállítások konfigurálására szolgáló lap képernyőképe. A Log Analytics, a AuditEvent és a AllMetrics küldésének lehetőségei be vannak jelölve.](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Adjon nevet a diagnosztikai beállításnak.
5. Kattintson a *küldés log Analyticsre* jelölőnégyzetre.
6. Válasszon ki egy meglévő Log Analytics munkaterületet, vagy hozzon létre egy munkaterületet
7. A *AuditEvent* -naplók engedélyezéséhez kattintson a log (napló) alatt lévő jelölőnégyzetre.
8. A *Mentés* gombra kattintva engedélyezheti a diagnosztika naplózását log Analytics munkaterületre.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Key Vault diagnosztika engedélyezése a PowerShell használatával
A következő PowerShell-parancsfájl egy példát mutat be a `Set-AzDiagnosticSetting` Key Vault erőforrás-naplózásának engedélyezésére:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Azure Key Vault adatgyűjtési adatok áttekintése
Azure Key Vault a megoldás közvetlenül a Key Vaultból gyűjt diagnosztikai naplókat.
A naplókat nem szükséges az Azure Blob Storage-ba írni, és nem szükséges ügynök az adatgyűjtés során.

Az alábbi táblázat az adatgyűjtés módszereit és a Azure Key Vault adatok gyűjtésének egyéb részleteit mutatja be.

| Platform | Közvetlen ügynök | System Center Operations Manager Agent | Azure | Operations Manager kötelező? | A felügyeleti csoporton keresztül elküldett Operations Manager ügynöki adatkezelés | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | érkezéskor |

## <a name="use-azure-key-vault"></a>Az Azure Key Vault használata
A [megoldás telepítése](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?source=intercept.nl&tab=Overview)után tekintse meg a Key Vault adatait az Azure monitor **áttekintés** oldalának **Key Vault Analytics** csempére kattintva. Nyissa meg ezt a lapot a **Azure monitor** menüjéből, és kattintson a **továbbiak** elemre az **áttekintések** szakaszban. 

![Képernyőkép a Azure Monitor áttekintés oldalon található Key Vault Analytics csempéről, amely a Key Vault műveleti kötetének időbeli alakulását ábrázolja.](media/azure-key-vault/log-analytics-keyvault-tile.png)

Miután rákattintott a **Key Vault Analytics** csempére, megtekintheti a naplók összefoglalóit, és a következő kategóriákban részletezheti a részleteket:

* Az összes Key Vault-művelet mennyisége az idő múlásával
* Sikertelen művelet-kötetek az idő függvényében
* Átlagos működési késés a művelet szerint
* A szolgáltatás minősége a 1000 MS-nál nagyobb mennyiségű műveletekkel és a több mint 1000 MS-nál nagyobb műveletek listájának üzemeltetéséhez

![Képernyőkép a Azure Key Vault-irányítópultról, amely az összes művelet, a sikertelen műveletek és az átlagos működési késések grafikai adatokkal rendelkező csempéit jeleníti meg.](media/azure-key-vault/log-analytics-keyvault01.png)

![Képernyőkép a Azure Key Vault-irányítópultról, amely az átlagos működési késés, a szolgáltatás minősége és az ajánlott keresések esetében tartalmazza az adatcsempéket.](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Bármely művelet részleteinek megtekintése
1. Az **Áttekintés** lapon kattintson a **Key Vault Analytics** csempére.
2. Az **Azure Key Vault** irányítópulton tekintse át az egyik penge összefoglaló adatait, majd kattintson az egyikre a részletes információk megtekintéséhez a naplóbeli keresés oldalon.

    Bármelyik naplóbeli keresési oldalon megtekintheti az eredményeket idő szerint, részletes eredményekkel és a naplóbeli keresési előzményekkel. Az eredmények szűkítéséhez az aspektusok alapján is szűrheti az eredményeket.

## <a name="azure-monitor-log-records"></a>Naplóbejegyzések Azure Monitor
A Azure Key Vault megoldás **elemzi azokat a** rekordokat, amelyek a Azure Diagnostics [AuditEvent-naplóiból](../../key-vault/general/logging.md) gyűjtött típusú kulcstartókkal rendelkeznek.  A rekordok tulajdonságai a következő táblázatban találhatók:  

| Tulajdonság | Leírás |
|:--- |:--- |
| `Type` |*AzureDiagnostics* |
| `SourceSystem` |*Azure* |
| `CallerIpAddress` |A kérést küldő ügyfél IP-címe |
| `Category` | *AuditEvent* |
| `CorrelationId` |Egy nem kötelező GUID, amelyet az ügyfél alkalmazhat az ügyféloldali és a szolgáltatásoldali (Key Vault) naplók egyeztetéséhez. |
| `DurationMs` |A REST API-kérelem végrehajtásának ideje ezredmásodpercben. Ez az idő nem tartalmazza a hálózati késést, így előfordulhat, hogy az ügyfél oldalán mért idő nem egyezik meg ezzel az idővel. |
| `httpStatusCode_d` |A kérelem által visszaadott HTTP-állapotkód (például *200*) |
| `id_s` |A kérelem egyedi azonosítója |
| `identity_claim_appid_g` | Az alkalmazás AZONOSÍTÓjának GUID azonosítója |
| `OperationName` |A művelet neve [Azure Key Vault naplózásban](../../key-vault/general/logging.md) dokumentálva |
| `OperationVersion` |Az ügyfél által kért REST API-verzió (például *2015-06-01*) |
| `requestUri_s` |A kérelem URI-ja |
| `Resource` |A Key Vault neve |
| `ResourceGroup` |A Key Vault erőforráscsoport |
| `ResourceId` |Az Azure Resource Manager szerinti erőforrás-azonosító. Key Vault naplók esetében ez a Key Vault erőforrás-azonosító. |
| `ResourceProvider` |*Microsoft. KEYVAULT* |
| `ResourceType` | *BOLTÍVEK* |
| `ResultSignature` |HTTP-állapot (például *OK*) |
| `ResultType` |REST API kérelem eredménye (például *sikeres*) |
| `SubscriptionId` |Az Key Vaultt tartalmazó előfizetés Azure-előfizetési azonosítója |

## <a name="migrating-from-the-old-key-vault-solution"></a>Áttelepítés a régi Key Vault megoldásból
Január 2017-án a naplók Key Vaultból való küldésének támogatott módja Log Analytics megváltozott. Ezek a változások a következő előnyöket nyújtják:
+ A naplók közvetlenül egy Log Analytics munkaterületre íródnak, és nem kell használni a Storage-fiókot
+ Kevesebb késés attól az időponttól kezdve, amikor a naplók generálva lesznek Log Analytics
+ Kevesebb konfigurációs lépés
+ Az Azure Diagnostics összes típusának általános formátuma

A frissített megoldás használata:

1. [A diagnosztika konfigurálása egy Log Analytics munkaterületre való közvetlen elküldése Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. Engedélyezze a Azure Key Vault megoldást az [Solutions Gallery Azure monitor-megoldások hozzáadása](./solutions.md) című témakörben ismertetett eljárás használatával.
3. A mentett lekérdezések, irányítópultok vagy riasztások frissítése az új adattípus használatára
   + A típus a következőtől változik: AzureDiagnostics. A ResourceType használatával szűrheti Key Vault naplókat.
   + A: helyett `KeyVaults` használja a `AzureDiagnostics | where ResourceType'=="VAULTS"`
   + Mezők: (a mezők nevei megkülönböztetik a kis-és nagybetűket)
   + Minden olyan mezőnél, amely \_ \_ a névben s, d vagy g utótaggal rendelkezik \_ , módosítsa az első karaktert a kisbetű értékre.
   + Minden olyan mezőnél, amelynél a \_ név utótagja szerepel, az adat a beágyazott mezők nevei alapján egyedi mezőkbe van bontva. A hívó egyszerű felhasználóneve például egy mezőben tárolódik. `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   + A mező CallerIpAddress CallerIPAddress módosult
   + A RemoteIPCountry mező már nem létezik
4. Távolítsa el a *Key Vault Analytics (elavult)* megoldást. Ha a PowerShellt használja, használja a `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

A módosítás előtt gyűjtött adatok nem láthatók az új megoldásban. Továbbra is lekérdezheti ezeket az adattípusokat a régi típusú és mezőnevek használatával.

## <a name="troubleshooting"></a>Hibaelhárítás
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Következő lépések
* A részletes Azure Key Vault-információk megtekintéséhez használja [a Azure monitor a naplózási lekérdezéseket](../logs/log-query-overview.md) .


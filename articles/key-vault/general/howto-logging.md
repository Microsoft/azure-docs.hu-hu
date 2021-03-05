---
title: Azure Key Vault naplózás engedélyezése
description: A Azure Key Vault naplózásának engedélyezése, amely az Ön által megadott Azure Storage-fiókban tárolja az adatokat.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 9ec1e59a5599ca2e95578eacc1484932956ebf16
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204014"
---
# <a name="how-to-enable-key-vault-logging"></a>Key Vault naplózás engedélyezése

Egy vagy több kulcstartó létrehozása után valószínűleg figyelnie kell a kulcstartók elérésének módját és időpontját. A szolgáltatással kapcsolatos részletes információkért lásd: [Key Vault naplózása](logging.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy meglévő kulcstároló.  
* [Azure Cloud Shell](https://shell.azure.com) – bash-környezet
* A Key Vault naplóihoz elegendő tárhely az Azure-ban.

Az útmutató parancsai a bash-környezetek [Cloud Shellhoz](https://shell.azure.com) vannak formázva.

## <a name="connect-to-your-key-vault-subscription"></a>Kapcsolódás a Key Vault-előfizetéshez

A Key naplózás beállításának első lépése a kulcstartót tartalmazó előfizetéshez csatlakozik. Ez különösen akkor fontos, ha több előfizetése van társítva a fiókjához.

Az Azure CLI-vel megtekintheti az összes előfizetést az az [Account List](/cli/azure/account#az_account_list) paranccsal, majd kapcsolódhat egyet az [az Account set](/cli/azure/account#az_account_set)használatával:

```azurecli-interactive
az account list

az account set --subscription "<subscriptionID>"
```

A Azure PowerShell segítségével először a [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-4.7.0) parancsmaggal listázhatja előfizetéseit, majd csatlakozhat egyet a [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-4.7.0) parancsmag használatával: 

```powershell-interactive
Get-AzSubscription

Set-AzContext -SubscriptionId "<subscriptionID>"
```

## <a name="create-a-storage-account-for-your-logs"></a>Storage-fiók létrehozása a naplókhoz

Habár meglévő Storage-fiókot is használhat a naplókhoz, egy új Storage-fiókot hozunk létre, amely Key Vault naplókhoz van hozzárendelve. 

A könnyebb kezelhetőség érdekében ugyanazt az erőforráscsoportot fogjuk használni, mint amely a kulcstartót is tartalmazza. Az [Azure CLI](quick-create-cli.md) rövid útmutatójában [és Azure PowerShell](quick-create-powershell.md)rövid útmutatójában ez az erőforráscsoport a **myResourceGroup** nevű, a hely pedig *eastus*. Cserélje le ezeket az értékeket a megfelelő értékre. 

A Storage-fiók nevét is meg kell adnia. A Storage-fiókok nevének egyedinek kell lennie 3 és 24 karakter között, és csak számokból és kisbetűkből állhat.  Végül a "Standard_LRS" SKU Storage-fiókját fogjuk létrehozni.

Az Azure CLI-vel használja az az [Storage Account Create](/cli/azure/storage/account#az_storage_account_create) parancsot.

```azurecli-interactive
az storage account create --name "<your-unique-storage-account-name>" -g "myResourceGroup" --sku "Standard_LRS"
```

A Azure PowerShell használatával használja a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount?view=azps-4.7.0) parancsmagot. Meg kell adnia az erőforráscsoport számára megfelelő helyet.

```powershell
 New-AzStorageAccount -ResourceGroupName myResourceGroup -Name "<your-unique-storage-account-name>" -Type "Standard_LRS" -Location "eastus"
```

Mindkét esetben jegyezze fel a Storage-fiók azonosítóját. Az Azure CLI-művelet az "id" értéket adja vissza a kimenetben. Az "id" Azure PowerShellhoz való beszerzéséhez használja a [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-4.7.0) , és rendelje hozzá a kimenetet a $SA változóhoz. Ezután megtekintheti a Storage-fiókot $sa. id használatával. (A "$sa. A környezeti "tulajdonságot a cikk későbbi részében is használni fogjuk.)

```powershell-interactive
$sa = Get-AzStorageAccount -Name "<your-unique-storage-account-name>" -ResourceGroup "myResourceGroup"
$sa.id
```

A Storage-fiók "id" formátuma a "/Subscriptions/<saját előfizetés-azonosító>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/<az egyedi-Storage-Account-Name>" formátumban történik.

> [!NOTE]
> Ha úgy dönt, hogy meglévő Storage-fiókot használ, ugyanazt az előfizetést kell használnia, mint a kulcstartó, és a klasszikus üzemi modell helyett a Azure Resource Manager üzemi modellt kell használnia.

## <a name="obtain-your-key-vault-resource-id"></a>A Key Vault erőforrás-AZONOSÍTÓjának beszerzése

A [CLI](quick-create-cli.md) rövid útmutató és a [PowerShell](quick-create-powershell.md)rövid útmutatójában egy egyedi névvel rendelkező kulcsot hozott létre.  Ezt a nevet újra használhatja az alábbi lépésekben.  Ha nem emlékszik a kulcstartó nevére, használhatja az Azure CLI az Key [Vault List](/cli/azure/keyvault#az_keyvault_list) parancsot vagy a Azure PowerShell [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault?view=azps-4.7.0) parancsmagot a listázáshoz.

Az erőforrás-AZONOSÍTÓjának megkereséséhez használja a kulcstartó nevét.  Az Azure CLI-vel használja az az Key [Vault show](/cli/azure/keyvault#az_keyvault_show) parancsot.

```azurecli-interactive
az keyvault show --name "<your-unique-keyvault-name>"
```

A Azure PowerShell használatával használja a [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault?view=azps-4.7.0) parancsmagot.

```powershell-interactive
Get-AzKeyVault -VaultName "<your-unique-keyvault-name>"
```

A Key Vault erőforrás-azonosítója a "/Subscriptions/<saját előfizetés-azonosító>/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/<a-Unique-kulcstartó-Name>" formátumban jelenik meg. Jegyezze fel a következő lépéshez.

## <a name="enable-logging-using-azure-powershell"></a>Naplózás engedélyezése Azure PowerShell használatával

A Key Vault naplózásának engedélyezéséhez az Azure CLI az monitor Diagnostics [-Settings Create](/cli/azure/monitor/diagnostic-settings) parancsot vagy a [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) parancsmagot használjuk a Storage-fiók azonosítójával és a Key Vault erőforrás-azonosítóval együtt.

```azurecli-interactive
az monitor diagnostic-settings create --storage-account "<storage-account-id>" --resource "<key-vault-resource-id>" --name "Key vault logs" --logs '[{"category": "AuditEvent","enabled": true}]' --metrics '[{"category": "AllMetrics","enabled": true}]'
```

A Azure PowerShell használatával a [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) parancsmagot használjuk, és az **-enabled** jelzőt beállítottuk, hogy a **$true** és a kategória beállítása `AuditEvent` (az egyetlen kategória a Key Vault naplózáshoz):

```powershell-interactive
Set-AzDiagnosticSetting -ResourceId "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category "AuditEvent"
```

Igény szerint megadhat egy adatmegőrzési szabályt a naplókhoz, így a régebbi naplók automatikusan törlődnek a megadott idő elteltével. Beállíthatja például a set adatmegőrzési szabályzatot, amely a 90 napnál régebbi naplókat automatikusan törli.

<!-- With the Azure CLI, use the [az monitor diagnostic-settings update](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_update) command. 

```azurecli-interactive
az monitor diagnostic-settings update 
```
-->

A Azure PowerShell használatával használja a [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) parancsmagot. 

```powershell-interactive
Set-AzDiagnosticSetting "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

A naplózott elemek:

* Az összes hitelesített REST API kérelem, beleértve a sikertelen kérelmeket a hozzáférési engedélyek, a rendszerhibák vagy a hibás kérelmek eredményeképpen.
* Maga a kulcstartó műveletei, beleértve a létrehozást, a törlést, a kulcstartó-hozzáférési szabályzatok beállítását és a Key Vault-attribútumok, például címkék frissítését.
* A Key vaultban lévő kulcsokkal és titkos kulcsokkal kapcsolatos műveletek, beleértve a következőket:
  * A kulcsok vagy titkos kódok létrehozása, módosítása vagy törlése.
  * A kulcsok aláírása, ellenőrzése, titkosítása, visszafejtése, becsomagolása és kicsomagolása, a titkok beolvasása és a kulcsok és titkok listázása (és azok verziói).
* A 401-es választ eredményező, nem hitelesített kérelmek. Ilyenek például azok a kérelmek, amelyek nem rendelkeznek olyan tulajdonosi jogkivonattal, amely nem formázott vagy lejárt, vagy érvénytelen tokent tartalmaz.  
* Event Grid a közeljövőben lejáró értesítési események, a lejárt és a tár hozzáférési szabályzata megváltozott (az új verzió esemény nincs naplózva). Az események naplózása attól függetlenül történik, hogy van-e esemény-előfizetés létrehozva a Key vaultban. További információ: Event Grid- [esemény sémája Key Vault](../../event-grid/event-schema-key-vault.md)

## <a name="access-your-logs"></a>A naplók elérése

Key Vault naplókat a megadott Storage-fiókban található "elemzések – naplók-AuditEvent" tároló tárolja. A naplók megtekintéséhez le kell töltenie a blobokat.

Először sorolja fel a tárolóban lévő összes blobot.  Az Azure CLI-vel használja az az [Storage blob List](/cli/azure/storage/blob#az_storage_blob_list) parancsot.

```azurecli-interactive
az storage blob list --account-name "<your-unique-storage-account-name>" --container-name "insights-logs-auditevent"
```

A Azure PowerShell használatával a tárolóban található összes blobot használja a [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob?view=azps-4.7.0) listához, írja be a következőt:

```powershell
Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context
```

Ahogy az Azure CLI-parancs vagy az Azure PowerShell-parancsmag kimenetében látható, a Blobok neve a következő: formátumban jelenik meg `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json` . A dátum- és időértékek az UTC hivatkozási időzónát használják.

Mivel ugyanazt a Storage-fiókot használhatja több erőforráshoz tartozó naplók összegyűjtésére, a blob nevében lévő teljes erőforrás-azonosító hasznos lehet, ha csak a szükséges blobokat szeretné elérni vagy letölteni. Előtte azonban nézzük meg, hogyan tölthető le az összes blob.

Az Azure CLI-vel használja az az [Storage blob Download](/cli/azure/storage/blob#az_storage_blob_download) parancsot, adja át a Blobok nevét, valamint annak a fájlnak az elérési útját, ahová menteni kívánja az eredményeket.

```azurecli-interactive
az storage blob download --container-name "insights-logs-auditevent" --file <path-to-file> --name "<blob-name>" --account-name "<your-unique-storage-account-name>"
```

A Azure PowerShell használatával a [gt-AzStorageBlobs](/powershell/module/az.storage/get-azstorageblob?view=azps-4.7.0) parancsmaggal lekérheti a Blobok listáját, majd a [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent?view=azps-4.7.0) parancsmaghoz tartozó csatornán keresztül letöltheti a naplókat a kiválasztott elérési útra.

```powershell-interactive
$blobs = Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context | Get-AzStorageBlobContent -Destination "<path-to-file>"
```

Ha ezt a második parancsmagot a PowerShellben futtatja, a **/** Blobok neveiben lévő határolójel teljes mappastruktúrát hoz létre a célmappában. Ezt a struktúrát fogja használni a Blobok fájlként való letöltéséhez és tárolásához.

A blobok egyenkénti letöltéséhez használjon helyettesítő elemeket. Például:

* Ha több kulcstárolóval rendelkezik, de csak a CONTOSOKEYVAULT3 nevűhöz szeretne naplókat letölteni:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Ha több erőforráscsoporttal rendelkezik, de csak egyhez szeretne naplókat letölteni, használja a `-Blob '*/RESOURCEGROUPS/<resource group name>/*'` parancsot:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Ha a 2019 januári hónapban szeretné letölteni az összes naplót, használja a következőt `-Blob '*/year=2019/m=01/*'` :

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Most már készen áll a naplók tartalmának megtekintésére. Mielőtt azonban beköltözünk erre, két további parancsot kell tudnia:

A naplók olvasásával kapcsolatos további információkért lásd [: Key Vault naplózás: a Key Vault naplók értelmezése](logging.md#interpret-your-key-vault-logs)

## <a name="use-azure-monitor-logs"></a>Az Azure Monitor-naplók használata

A Key Vault naplók áttekintéséhez használhatja a Azure Monitor naplók Key Vault megoldását `AuditEvent` . Azure Monitor naplókban a naplók segítségével elemezheti az adatokat, és lekérheti a szükséges információkat.

További információk, például a beállításának módja: [Azure Key Vault Azure monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Következő lépések

- Elméleti információk, beleértve a Key Vault naplók értelmezését: [Key Vault naplózás](logging.md)
- A .NET-alapú webalkalmazásokban Azure Key Vaultt használó oktatóanyagért lásd: [Azure Key Vault használata webalkalmazásból](tutorial-net-create-vault-azure-web-app.md).
- Programozási hivatkozások: [Azure Key Vault developer’s guide](developers-guide.md) (Az Azure Key Vault fejlesztői útmutatója).

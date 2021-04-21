---
title: Key Vault-referenciák használata
description: Megtudhatja, hogyan állíthatja be a Azure App Service és Azure Functions referenciák Azure Key Vault használatát. Tegye Key Vault titkos kódokat elérhetővé az alkalmazáskód számára.
author: mattchenderson
ms.topic: article
ms.date: 02/05/2021
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 1e6f46b205790d81a3e76d2aafbcf7e13dbb5afd
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815215"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Referencia Key Vault és App Service és Azure Functions

Ez a témakör azt mutatja be, hogyan dolgozhat titkos Azure Key Vault a App Service vagy Azure Functions alkalmazásban anélkül, hogy kódváltozásra lenne szükség. [Azure Key Vault](../key-vault/general/overview.md) szolgáltatás központi titkos kulcsok kezelését biztosítja, teljes körű vezérléssel a hozzáférési házirendek és a naplózási előzmények felett.

## <a name="granting-your-app-access-to-key-vault"></a>Hozzáférés megadása az alkalmazás számára Key Vault

Ahhoz, hogy titkos kulcsokat Key Vault, rendelkeznie kell egy létrehozott tárolóval, és engedélyt kell adni az alkalmazásnak a hozzáférésre.

1. Hozzon létre egy kulcstartót a [Key Vault útmutató alapján.](../key-vault/secrets/quick-create-cli.md)

1. Hozzon létre [egy rendszer által hozzárendelt felügyelt identitást](overview-managed-identity.md) az alkalmazáshoz.

   > [!NOTE] 
   > Key Vault hivatkozások jelenleg csak a rendszer által hozzárendelt felügyelt identitásokat támogatják. Felhasználó által hozzárendelt identitások nem használhatók.

1. Hozzon [létre egy hozzáférési szabályzatot Key Vault](../key-vault/general/security-features.md#privileged-access) korábban létrehozott alkalmazásidentitáshoz. Engedélyezze a "Titkos ok lekért" engedélyt a szabályzaton. Ne konfigurálja az "engedélyezett alkalmazást" vagy a beállításokat, mert ez nem kompatibilis `applicationId` a felügyelt identitásokkal.

### <a name="access-network-restricted-vaults"></a>Hozzáférés a hálózattal korlátozott tárolókhoz

> [!NOTE]
> A Linux-alapú alkalmazások jelenleg nem tudják feloldani a titkos kulcsokat egy hálózattal korlátozott kulcstartóból, kivéve, ha az alkalmazást egy [App Service Environment.](./environment/intro.md)

Ha a tároló hálózati [korlátozásokkal](../key-vault/general/overview-vnet-service-endpoints.md)van konfigurálva, akkor arról is gondoskodnia kell, hogy az alkalmazás hálózati hozzáféréssel rendelkezik.

1. Győződjön meg arról, hogy az alkalmazás rendelkezik konfigurált kimenő hálózati képességekkel a következő App Service és hálózati beállítások [Azure Functions leírtak szerint:](../azure-functions/functions-networking-options.md). [](./networking-features.md)

2. Győződjön meg arról, hogy a tároló konfigurációs fiókjai annak a hálózatnak vagy alhálózatnak a fiókjai, amelyen keresztül az alkalmazás hozzá fog férni.

> [!IMPORTANT]
> A tároló virtuális hálózati integráción keresztüli elérése jelenleg nem kompatibilis a titkos kulcsok adott verzió nélküli [automatikus frissítésével.](#rotation)

## <a name="reference-syntax"></a>Referenciaszintaxis

A Key Vault típusú hivatkozás a következő formát követi, ahol a helyére az alábbi lehetőségek `@Microsoft.KeyVault({referenceString})` `{referenceString}` egyikét kell bevetni:

> [!div class="mx-tdBreakAll"]
> | Referenciasring                                                            | Description                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | A **SecretUri** a titkos adatsík teljes adatsík-URI-ját Key Vault, opcionálisan beleértve a verziót is, például: `https://myvault.vault.azure.net/secrets/mysecret/` vagy `https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931`  |
> | VaultName=_vaultName_; SecretName=_secretName_; SecretVersion=_secretVersion_ | A **VaultName** paramétert kötelező megadni, és az erőforrás Key Vault kell lennie. A **SecretName** paraméter kötelező, és a cél titkos kód nevének kell lennie. A **SecretVersion** nem kötelező, de ha van, akkor a titkos használhatja. |

Egy teljes referencia például a következőre hasonlít:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/)
```

Alternatív megoldás:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret)
```

## <a name="rotation"></a>Változtatás

> [!IMPORTANT]
> [A tároló virtuális hálózati integráción](#access-network-restricted-vaults) keresztüli elérése jelenleg nem kompatibilis a titkos kulcsok adott verzió nélküli automatikus frissítésével.

Ha a hivatkozásban nincs megadva verzió, akkor az alkalmazás a legújabb verziót fogja használni, amely a Key Vault. Ha újabb verziók válnak elérhetővé , például egy rotációs esemény esetén, az alkalmazás egy napon belül automatikusan frissül, és használatba veszi a legújabb verziót. Az alkalmazáson végrehajtott konfigurációs módosítások azonnal frissülnek az összes hivatkozott titkos fájl legújabb verziójára.

## <a name="source-application-settings-from-key-vault"></a>Forrásalkalmazás beállításai a Key Vault

Key Vault hivatkozások az alkalmazásbeállítások értékeiként [](configure-common.md#configure-app-settings)használhatók, így a hely konfigurációja helyett a titkos Key Vault is megtarthatja őket. Az alkalmazásbeállításokat a rendszer biztonságosan titkosítja az Key Vault.

Ha alkalmazásbeállításhoz Key Vault használni, állítsa be a hivatkozást a beállítás értékeként. Az alkalmazás a szokásos módon hivatkozhat a titkos kulcsra a kulcsán keresztül. Nincs szükség kódváltozásra.

> [!TIP]
> A hivatkozásokat használó Key Vault legtöbb alkalmazásbeállítást pontbeállításként kell megjelölni, mivel minden környezethez külön tárolót kell használnia.

### <a name="azure-resource-manager-deployment"></a>Az Azure Resource Manager üzembe helyezése

Amikor az erőforrások üzembe helyezését Azure Resource Manager sablonokkal automatizálja, előfordulhat, hogy a függőségeket egy adott sorrendben kell sorrendbe rendelnie ahhoz, hogy ez a funkció működjön. Vegye figyelembe, hogy az alkalmazásbeállításokat saját erőforrásként kell meghatároznia ahelyett, hogy egy tulajdonságot használ a `siteConfig` hely definíciójában. Ennek az az oka, hogy a helyet először meg kell határozni, hogy a rendszer által hozzárendelt identitás vele legyen létrehozva, és használható legyen a hozzáférési szabályzatban.

A függvényalkalmazás példasablonja a következőre hasonlíthat:

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> Ebben a példában a forrásvezérlés központi telepítése az alkalmazásbeállításoktól függ. Ez általában nem biztonságos működés, mivel az alkalmazásbeállítás frissítése aszinkron módon működik. Mivel azonban az alkalmazásbeállítást is tartalmazta, a frissítés szinkron `WEBSITE_ENABLE_SYNC_UPDATE_SITE` módon fog frissülni. Ez azt jelenti, hogy a forrásvezérlő üzembe helyezése csak akkor kezdődik meg, ha az alkalmazásbeállítások teljesen frissültek.

## <a name="troubleshooting-key-vault-references"></a>Hibaelhárítási Key Vault hivatkozások

Ha egy hivatkozás nem oldható fel megfelelően, a rendszer a referenciaértéket használja helyette. Ez azt jelenti, hogy az alkalmazásbeállításokhoz egy környezeti változó jön létre, amelynek az értéke szintaxissal `@Microsoft.KeyVault(...)` rendelkezik. Ez hibákat okozhat az alkalmazásnak, mivel egy bizonyos struktúra titkos struktúráját várja.

Ezt leggyakrabban a hozzáférési szabályzat hibás [konfigurációja Key Vault oka.](#granting-your-app-access-to-key-vault) Ennek oka lehet azonban egy már nem létező titkos kód vagy magában a referenciaszintaxisban lévő szintaktikai hiba.

Ha a szintaxis helyes, a hiba egyéb okait az aktuális feloldási állapot a portálon való megtekintésével tudja megtekinteni. Lépjen az Alkalmazásbeállítások lapra, és válassza a "Szerkesztés" lehetőséget a kérdéses hivatkozáshoz. A beállítás konfigurációja alatt az állapotinformációknak kell jelenniük, beleértve az esetleges hibákat is. Ezek hiánya azt jelenti, hogy a referenciaszintaxis érvénytelen.

Az egyik beépített érzékelővel további információkat is kaphat.

### <a name="using-the-detector-for-app-service"></a>Az érzékelő használata App Service

1. A portálon lépjen az alkalmazáshoz.
2. Válassza a **Problémák diagnosztizálása és megoldása** lehetőséget.
3. Válassza **a Rendelkezésre állás és teljesítmény,** majd a Nem elérhető **webalkalmazás lehetőséget.**
4. Keresse **Key Vault az Alkalmazásbeállítások diagnosztikát,** majd kattintson a **További információ elemre.**


### <a name="using-the-detector-for-azure-functions"></a>Az érzékelő használata Azure Functions

1. A portálon lépjen az alkalmazáshoz.
2. Lépjen a **Platform funkciói felületre.**
3. Válassza a **Problémák diagnosztizálása és megoldása** lehetőséget.
4. Válassza **az Availability and Performance (Rendelkezésre állás és teljesítmény)** lehetőséget, majd válassza a **Function app down (A függvényalkalmazás nem működik) vagy a Reporting errors (Hibák jelentése) lehetőséget.**
5. Kattintson a **Key Vault Application Settings Diagnostics elemre.**

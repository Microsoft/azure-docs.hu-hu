---
title: Key Vault-referenciák használata
description: Megtudhatja, hogyan állíthatja be Azure App Service és Azure Functions Azure Key Vault-hivatkozások használatára. Key Vault titkokat elérhetővé teheti az alkalmazás kódjában.
author: mattchenderson
ms.topic: article
ms.date: 02/05/2021
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 69fc0d6f3c4e18b34555a099f4e28e278ca3bdad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "100635387"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Key Vault referenciák használata App Service és Azure Functions

Ebből a témakörből megtudhatja, hogyan dolgozhat fel a titkokat a App Service vagy Azure Functions alkalmazásban Azure Key Vault a kód módosítása nélkül. A [Azure Key Vault](../key-vault/general/overview.md) egy olyan szolgáltatás, amely központosított titkok felügyeletét teszi lehetővé a hozzáférési házirendek és a naplózási előzmények teljes körű szabályozásával.

## <a name="granting-your-app-access-to-key-vault"></a>Az alkalmazás hozzáférésének biztosítása Key Vault

A Key Vault titkainak beolvasásához létre kell hoznia egy tárolót, és engedélyt kell adni az alkalmazásnak az eléréséhez.

1. Hozzon létre egy kulcstartót a [Key Vault](../key-vault/secrets/quick-create-cli.md)rövid útmutató alapján.

1. Hozzon létre egy [rendszerhez rendelt felügyelt identitást](overview-managed-identity.md) az alkalmazáshoz.

   > [!NOTE] 
   > Key Vault referenciák jelenleg csak a rendszer által hozzárendelt felügyelt identitásokat támogatják. Felhasználó által hozzárendelt identitások nem használhatók.

1. Hozzon létre egy [hozzáférési szabályzatot a Key Vaultban](../key-vault/general/secure-your-key-vault.md#key-vault-access-policies) a korábban létrehozott alkalmazás-identitáshoz. A "Get" Secret engedély engedélyezése a szabályzathoz. Ne konfigurálja a "meghatalmazott alkalmazást" vagy a `applicationId` beállításokat, mivel ez nem kompatibilis a felügyelt identitással.

   > [!IMPORTANT]
   > Key Vault hivatkozások nem képesek a Key vaultban tárolt titkos kódok feloldására [hálózati korlátozásokkal](../key-vault/general/overview-vnet-service-endpoints.md) , kivéve, ha az alkalmazás egy [app Service Environmenton](./environment/intro.md)belül fut.

## <a name="reference-syntax"></a>Hivatkozás szintaxisa

A Key Vault hivatkozás az űrlapra mutat `@Microsoft.KeyVault({referenceString})` , ahol a `{referenceString}` a következő lehetőségek egyikével helyettesíti:

> [!div class="mx-tdBreakAll"]
> | Hivatkozási sztring                                                            | Description                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri =_SecretUri_                                                       | A **SecretUri** a titkos kulcs teljes adatsík URI-ja legyen Key Vaultban, opcionálisan egy verziót is beleértve, például `https://myvault.vault.azure.net/secrets/mysecret/` vagy `https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931`  |
> | VaultName =_VaultName_; SecretName =_SecretName_; Titkoskulcsverziója =_titkoskulcsverziója_ | A **VaultName** kötelező megadni, és meg kell adni a Key Vault erőforrás nevét. A **SecretName** megadása kötelező, és a célként megadott titkos kód nevének kell lennie. A **titkoskulcsverziója** nem kötelező, de ha a jelen esetben a használni kívánt titok verzióját jelzi. |

A teljes hivatkozás például a következőhöz hasonló lesz:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/)
```

Alternatív megoldás:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret)
```

## <a name="rotation"></a>Változtatás

Ha egy verzió nincs megadva a hivatkozásban, akkor az alkalmazás a Key Vault található legújabb verziót fogja használni. Ha az újabb verziók elérhetővé válnak, például egy rotációs eseménnyel, akkor az alkalmazás automatikusan frissül, és egy napon belül elkezdi használni a legújabb verziót. Az alkalmazásban végrehajtott összes konfigurációs módosítás azonnali frissítést eredményez az összes hivatkozott titok legújabb verzióihoz.

## <a name="source-application-settings-from-key-vault"></a>A forrásoldali Alkalmazásbeállítások Key Vault

Key Vault referenciák az [Alkalmazásbeállítások](configure-common.md#configure-app-settings)értékeiként használhatók, ami lehetővé teszi, hogy a titkokat a hely konfigurációja helyett Key Vault tartsa. Az Alkalmazásbeállítások biztonságosan titkosítva maradnak, de ha titkos felügyeleti képességekre van szüksége, a Key Vaultba kell lépniük.

Ha Key Vault hivatkozást szeretne használni egy alkalmazás-beállításhoz, állítsa a hivatkozást a beállítás értékeként. Az alkalmazás a szokásos módon hivatkozhat a titkos kulcsra. Nincs szükség kód módosítására.

> [!TIP]
> Key Vault hivatkozásokat használó legtöbb Alkalmazásbeállítások tárolóhely-beállításokként vannak megjelölve, mivel minden környezethez külön tárolókat kell megadni.

### <a name="azure-resource-manager-deployment"></a>Az Azure Resource Manager üzembe helyezése

Az erőforrás-telepítések Azure Resource Manager sablonokon keresztüli automatizálásakor előfordulhat, hogy a funkció működéséhez egy adott sorrendben kell sorba rendeznie a függőségeket. Fontos megjegyezni, hogy az alkalmazás beállításait saját erőforrásként kell meghatároznia ahelyett, hogy egy tulajdonságot kellene használnia `siteConfig` a hely definíciójában. Ennek az az oka, hogy a helyet először meg kell határozni, hogy a rendszer által hozzárendelt identitás létre legyen hozva, és a hozzáférési házirendben is használható legyen.

Egy Function alkalmazáshoz tartozó példa pszeudo-sablon a következőhöz hasonló lehet:

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
> Ebben a példában a verziókövetés központi telepítése az alkalmazásbeállításoktől függ. Ez általában nem biztonságos viselkedés, mivel az Alkalmazásbeállítások frissítése aszinkron módon történik. Mivel azonban az alkalmazás beállítása is megtörtént `WEBSITE_ENABLE_SYNC_UPDATE_SITE` , a frissítés szinkronban van. Ez azt jelenti, hogy a verziókövetés központi telepítése csak akkor kezdődik el, ha az Alkalmazásbeállítások teljes mértékben frissültek.

## <a name="troubleshooting-key-vault-references"></a>Hibaelhárítási Key Vault referenciái

Ha egy hivatkozás nem oldható fel megfelelően, a rendszer a hivatkozási értéket fogja használni. Ez azt jelenti, hogy az Alkalmazásbeállítások esetében létrejön egy környezeti változó, amelynek az értéke `@Microsoft.KeyVault(...)` szintaxissal rendelkezik. Ez azt eredményezheti, hogy az alkalmazás hibát jelez, mivel egy adott struktúra titkát várta.

Ez általában a [Key Vault hozzáférési házirend](#granting-your-app-access-to-key-vault)helytelen konfigurációja miatt fordul elő. Az is előfordulhat azonban, hogy egy titkos kulcs már nem létezik, vagy szintaktikai hiba történt a hivatkozásban.

Ha a szintaxis helyes, a hiba egyéb okait a portál aktuális feloldási állapotának ellenőrzésével tekintheti meg. Navigáljon az Alkalmazásbeállítások elemre, és válassza a szerkesztés lehetőséget a szóban forgó hivatkozáshoz. A beállítás beállítása alatt meg kell jelennie az állapotadatok között, beleértve az esetleges hibákat is. Ezek hiánya azt jelenti, hogy a hivatkozás szintaxisa érvénytelen.

A beépített érzékelők egyikét is használhatja további információk megszerzéséhez.

### <a name="using-the-detector-for-app-service"></a>A App Service detektorának használata

1. A portálon navigáljon az alkalmazáshoz.
2. Válassza a **Problémák diagnosztizálása és megoldása** lehetőséget.
3. Válassza a **rendelkezésre állás és teljesítmény** lehetőséget, majd válassza a **webalkalmazás lehetőséget.**
4. Keresse meg **Key Vault az Alkalmazásbeállítások diagnosztikát** , és kattintson a **További információ** elemre.


### <a name="using-the-detector-for-azure-functions"></a>A Azure Functions detektorának használata

1. A portálon navigáljon az alkalmazáshoz.
2. Navigáljon a **platform szolgáltatásaihoz.**
3. Válassza a **Problémák diagnosztizálása és megoldása** lehetőséget.
4. Válassza a **rendelkezésre állás és teljesítmény** lehetőséget, majd válassza a **Function app Down vagy a jelentéskészítési hibák lehetőséget.**
5. Kattintson **Key Vault alkalmazás-beállítások diagnosztika** elemre.

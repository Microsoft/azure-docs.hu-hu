---
title: Saját kulcs konfigurálása a Azure Service Bus inaktív adatok titkosításához
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a saját kulcsát a Azure Service Bus adatok titkosításához.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: 5d14c8953819575d1c2688520838135efc7121e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100378315"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Ügyfél által felügyelt kulcsok konfigurálása a Azure Service Bus adatok titkosításához a Azure Portal használatával
A Azure Service Bus Premium az Azure Storage Service Encryption (Azure SSE) szolgáltatásban tárolt adatok titkosítását teszi lehetővé. Az Service Bus Premium az Azure Storage-t használja az adattároláshoz. Az Azure Storage-ban tárolt összes adattal a Microsoft által felügyelt kulcsokkal van titkosítva. Ha saját kulcsot (más néven Bring Your Own Key (BYOK) vagy ügyfél által felügyelt kulcsot) használ, az adatok továbbra is a Microsoft által felügyelt kulccsal lesznek titkosítva, a Microsoft által felügyelt kulcs pedig az ügyfél által felügyelt kulcs használatával lesz titkosítva. Ez a funkció lehetővé teszi a Microsoft által felügyelt kulcsok titkosításához használt ügyfél által felügyelt kulcsok elérésének létrehozását, elforgatását, letiltását és visszavonását. A BYOK funkció engedélyezése egy egyszeri telepítési folyamat a névtérben.

Az ügyfél által felügyelt kulcs a szolgáltatás oldalának titkosítására vonatkozik. 
- Ezt a funkciót [Azure Service Bus prémium](service-bus-premium-messaging.md) szint támogatja. A standard szintű Service Bus névterek esetében nem engedélyezhető.
- A titkosítás csak új vagy üres névterekhez engedélyezhető. Ha a névtér bármely várólistát vagy témakört tartalmaz, a titkosítási művelet sikertelen lesz.

A Azure Key Vault segítségével kezelheti a kulcsokat, és naplózhatja a kulcshasználat. Létrehozhatja saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához. További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../key-vault/general/overview.md)

Ez a cikk bemutatja, hogyan konfigurálhat egy Key vaultot az ügyfél által felügyelt kulcsokkal a Azure Portal használatával. Ha meg szeretné tudni, hogyan hozhat létre kulcstartót a Azure Portal használatával, tekintse meg a rövid útmutató [: Azure Key Vault létrehozása a Azure Portal használatával](../key-vault/general/quick-create-portal.md)című témakört.

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok Azure Service Bus való használata megköveteli, hogy a kulcstartónak két szükséges tulajdonsága legyen konfigurálva. Ezek a következők:  **Soft delete** és **not Purge**. Ezek a tulajdonságok alapértelmezés szerint engedélyezve vannak, amikor új kulcstartót hoz létre a Azure Portal. Ha azonban egy meglévő kulcstartón kell engedélyeznie ezeket a tulajdonságokat, akkor a PowerShellt vagy az Azure CLI-t kell használnia.

## <a name="enable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok engedélyezése
Az ügyfél által felügyelt kulcsok Azure Portal való engedélyezéséhez kövesse az alábbi lépéseket:

1. Navigáljon a Service Bus Premium-névtérhez.
2. A Service Bus névtér **Beállítások** lapján válassza a **titkosítás** lehetőséget.
3. Válassza ki az **ügyfél által felügyelt kulcs titkosítását a nyugalmi** állapotban, ahogy az az alábbi képen is látható.

    ![Felhasználó által kezelt kulcs engedélyezése](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Key Vault beállítása kulcsokkal

Miután engedélyezte az ügyfél által felügyelt kulcsokat, hozzá kell rendelnie az ügyfél által felügyelt kulcsot a Azure Service Bus névtérhez. A Service Bus csak Azure Key Vault használatát támogatja. Ha az előző szakaszban az **ügyfél által felügyelt kulcs** beállítással engedélyezi a titkosítást, a kulcsot Azure Key Vaultba kell importálnia. Emellett a kulcsoknak is rendelkeznie kell a **Soft delete** szolgáltatással, és **nem szabad kiüríteni** a kulcsot. Ezeket a beállításokat a [PowerShell](../key-vault/general/key-vault-recovery.md) vagy a [parancssori](../key-vault/general/key-vault-recovery.md)felület használatával lehet konfigurálni.

1. Új kulcstartó létrehozásához kövesse [az Azure Key Vault rövid](../key-vault/general/overview.md)útmutatót. A meglévő kulcsok importálásával kapcsolatos további információkért lásd: [a kulcsok, a titkok és a tanúsítványok ismertetése](../key-vault/general/about-keys-secrets-certificates.md).
1. Ha be szeretné kapcsolni a törlés és a kiürítés védelmét a tároló létrehozásakor, használja az az kulcstartó [create](/cli/azure/keyvault#az-keyvault-create) parancsot.

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Ha a kiürítési védelmet egy meglévő tárolóba kívánja hozzáadni (amely már rendelkezik a helyreállított törlés engedélyezésével), használja az az kulcstartó [Update](/cli/azure/keyvault#az-keyvault-update) parancsot.

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. A kulcsok létrehozásához kövesse az alábbi lépéseket:
    1. Új kulcs létrehozásához válassza a **Létrehozás/importálás** elemet a **kulcsok** menüből a **Beállítások** területen.
        
        ![A létrehozó/Importálás gomb kiválasztása](./media/configure-customer-managed-key/select-generate-import.png)

    1. Adja **meg a** kívánt nevet a kulcs **létrehozásához** és megadásához.

        ![Kulcs létrehozása](./media/configure-customer-managed-key/create-key.png) 

    1. Most kiválaszthatja ezt a kulcsot, hogy társítsa a Service Bus névteret a titkosításhoz a legördülő listából. 

        ![Kulcs kiválasztása a Key vaultból](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > A redundancia érdekében legfeljebb 3 kulcsot adhat hozzá. Abban az esetben, ha a kulcsok egyike lejárt, vagy nem érhető el, a rendszer a többi kulcsot használja a titkosításhoz.
        
    1. Adja meg a kulcs részleteit, és kattintson a **kiválasztás** elemre. Ez lehetővé teszi a Microsoft által felügyelt kulcs titkosítását a kulccsal (ügyfél által felügyelt kulcs). 


    > [!IMPORTANT]
    > Ha a Geo vész-helyreállítással együtt szeretné használni az ügyfél által felügyelt kulcsot, tekintse át ezt a szakaszt. 
    >
    > Ha engedélyezni szeretné a Microsoft által felügyelt kulcs titkosítását egy ügyfél által felügyelt kulccsal, a rendszer a megadott Azure-kulcstartón a Service Bus felügyelt identitására vonatkozó [hozzáférési szabályzatot](../key-vault/general/secure-your-key-vault.md) hoz létre. Ez biztosítja az Azure kulcstartónak az Azure Service Bus névtérből való szabályozott hozzáférését.
    >
    > Ennek oka:
    > 
    >   * Ha a Geo vész- [helyreállítási](service-bus-geo-dr.md) funkció már engedélyezve van a Service Bus névtérhez, és engedélyezni szeretné az ügyfél által felügyelt kulcsot, 
    >     * A párosítás megszüntetése
    >     * Állítsa be az elsődleges és a másodlagos névtér felügyelt identitásához tartozó [hozzáférési házirendet](../key-vault/general/assign-access-policy-portal.md) a kulcstartóba.
    >     * Állítsa be a titkosítást az elsődleges névtérben.
    >     * Az elsődleges és a másodlagos névterek újbóli párosítása.
    > 
    >   * Ha azt szeretné, hogy a Geo-DR olyan Service Bus névteren legyen engedélyezve, amelyben az ügyfél által felügyelt kulcs már be van állítva, akkor
    >     * Állítsa be a másodlagos névtér felügyelt identitásához tartozó [hozzáférési házirendet](../key-vault/general/assign-access-policy-portal.md) a Key vaultba.
    >     * Az elsődleges és a másodlagos névterek párosítása.


## <a name="rotate-your-encryption-keys"></a>A titkosítási kulcsok elforgatása

A Key vaultban az Azure Key Vaults rotációs mechanizmus használatával forgathatja el a kulcsot. Az aktiválási és a lejárati dátumok is megadhatók a kulcs elforgatásának automatizálására. A Service Bus szolgáltatás felderíti az új kulcs-verziókat, és automatikusan elkezdi használni őket.

## <a name="revoke-access-to-keys"></a>Kulcsok elérésének visszavonása

A titkosítási kulcsokhoz való hozzáférés visszavonása nem törli az Service Busból származó adatok törlését. Azonban az adatok nem érhetők el a Service Bus névtérből. A titkosítási kulcsot a hozzáférési házirendben vagy a kulcs törlésével vonhatja vissza. További információ a hozzáférési házirendekről és a Key Vault biztonságossá [tételéről a kulcstartó biztonságos eléréséről](../key-vault/general/secure-your-key-vault.md).

A titkosítási kulcs visszavonása után a titkosított névtér Service Bus szolgáltatása inműködőképes lesz. Ha a kulcshoz való hozzáférés engedélyezve van, vagy a törölt kulcs vissza lett állítva, Service Bus a szolgáltatás kiválasztja a kulcsot, hogy hozzáférjen az adatokhoz a titkosított Service Bus névtérből.

## <a name="caching-of-keys"></a>Kulcsok gyorsítótárazása
A Service Bus-példány 5 percenként lekérdezi a felsorolt titkosítási kulcsokat. Gyorsítótárazza és azokat a következő lekérdezésre használja, amely 5 perc után következik be. Amíg legalább egy kulcs elérhető, a várólisták és a témakörök elérhetők. Ha az összes felsorolt kulcs nem érhető el a lekérdezésekben, az összes várólista és témakör elérhetetlenné válik. 

További részletek: 

- A Service Bus szolgáltatás 5 percenként lekérdezi a névtér rekordjában felsorolt összes ügyfél által felügyelt kulcsot:
    - Ha egy kulcs el lett forgatva, a rekord frissül az új kulccsal.
    - Ha a kulcsot visszavonták, a kulcsot a rendszer eltávolítja a rekordból.
    - Ha az összes kulcs vissza lett vonva, a névtér titkosítási állapota **Visszavonva** értékre van állítva. Az adatok nem érhetők el a Service Bus névtérből. 
    

## <a name="use-resource-manager-template-to-enable-encryption"></a>A titkosítás engedélyezése Resource Manager-sablon használatával
Ez a szakasz bemutatja, hogyan végezheti el a következő feladatokat **Azure Resource Manager sablonok** használatával. 

1. Hozzon létre egy **prémium** szintű Service Bus névteret egy **felügyelt szolgáltatás identitásával**.
2. Hozzon létre egy **kulcstartót** , és adja meg a szolgáltatás identitásának hozzáférését a kulcstartóhoz. 
3. Frissítse a Service Bus névteret a Key Vault-információkkal (kulcs/érték). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Prémium szintű Service Bus névtér létrehozása felügyelt szolgáltatás identitásával
Ebből a szakaszból megtudhatja, hogyan hozhat létre egy Azure Service Bus névteret felügyelt szolgáltatás identitásával egy Azure Resource Manager sablonnal és a PowerShell használatával. 

1. Hozzon létre egy Azure Resource Manager sablont egy olyan Service Bus prémium szintű névtér létrehozásához, amelynek felügyelt szolgáltatás identitása van. Nevezze el a következő fájlt: **CreateServiceBusPremiumNamespace.js**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Hozzon létre egy sablon-paraméter nevű fájlt: **CreateServiceBusPremiumNamespaceParams.js**. 

    > [!NOTE]
    > Cserélje le a következő értékeket: 
    > - `<ServiceBusNamespaceName>` -Service Bus névtér neve
    > - `<Location>` – A Service Bus névtér helye

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. Futtassa a következő PowerShell-parancsot a sablon üzembe helyezéséhez Premium Service Bus névtér létrehozásához. Ezután kérje le a Service Bus névtér AZONOSÍTÓját, hogy később használhassa. `{MyRG}`A parancs futtatása előtt cserélje le az nevet az erőforráscsoport nevére.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Service Bus névtér-identitás hozzáférésének engedélyezése a Key vaulthoz

1. A következő parancs futtatásával hozzon létre egy Key vaultot a **kiürítési védelemmel** , és engedélyezze a helyreállítható **törlést** . 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    VAGY
    
    Futtassa a következő parancsot egy **meglévő kulcstartó** frissítéséhez. A parancs futtatása előtt határozza meg az erőforráscsoport és a kulcstároló nevének értékét. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Állítsa be a Key Vault hozzáférési házirendjét úgy, hogy a Service Bus névtér felügyelt identitása hozzáférhessen a Key Vault kulcs értékéhez. Használja a Service Bus névtér AZONOSÍTÓját az előző szakaszból. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Adatok titkosítása Service Bus névtérben az ügyfél által felügyelt kulccsal a Key vaultból
A következő lépéseket eddig végrehajtotta: 

1. Létrehozott egy prémium szintű névteret egy felügyelt identitással.
2. Hozzon létre egy kulcstartót, és biztosítson hozzáférést a Key vaulthoz a felügyelt identitáshoz. 

Ebben a lépésben frissíteni fogja a Service Bus névteret a Key Vault-információkkal. 

1. Hozzon létre egy **UpdateServiceBusNamespaceWithEncryption.js** nevű JSON-fájlt a következő tartalommal: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. Hozzon létre egy sablon-paramétert tartalmazó fájlt: **UpdateServiceBusNamespaceWithEncryptionParams.json**.

    > [!NOTE]
    > Cserélje le a következő értékeket: 
    > - `<ServiceBusNamespaceName>` -Service Bus névtér neve
    > - `<Location>` – A Service Bus névtér helye
    > - `<KeyVaultName>` – A Key Vault neve
    > - `<KeyName>` – A kulcs neve a Key vaultban  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. Futtassa a következő PowerShell-parancsot a Resource Manager-sablon üzembe helyezéséhez. `{MyRG}`A parancs futtatása előtt cserélje le az nevet az erőforráscsoport nevére. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:
- [Service Bus áttekintése](service-bus-messaging-overview.md)
- [Key Vault áttekintése](../key-vault/general/overview.md)
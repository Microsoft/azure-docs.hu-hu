---
title: Saját kulcs konfigurálása az Azure Service Bus adatok titkosításához
description: Ez a cikk bemutatja, hogyan konfigurálhatja a saját kulcsát a Azure Service Bus adatok titkosításához.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: de716b9f14191ba057c83a060104e64937c4192a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816007"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Ügyfél által kezelt kulcsok konfigurálása az Azure Service Bus adatok titkosításához az Azure Portal
Azure Service Bus Premium az Azure Storage Service Encryption (Azure SSE) használatával titkosítja az Storage Service Encryption adatait. Service Bus Premium az Azure Storage-ban tárolja az adatokat. Az Azure Storage-ban tárolt összes adat a Microsoft által kezelt kulcsokkal van titkosítva. Ha saját kulcsot használ (más néven Bring Your Own Key (BYOK) vagy ügyfél által felügyelt kulcsot), az adatok titkosítása továbbra is a Microsoft által felügyelt kulccsal történik, de a Microsoft által felügyelt kulcs is titkosítva lesz az ügyfél által felügyelt kulccsal. Ez a funkció lehetővé teszi a Microsoft által kezelt kulcsok titkosításához használt, felhasználó által kezelt kulcsokhoz való hozzáférések létrehozására, váltására, letiltására és visszavonására. A BYOK funkció engedélyezése egy egyszeres beállítási folyamat a névtéren.

A szolgáltatásoldali titkosításnak van néhány kikötése az ügyfél által felügyelt kulccsal. 
- Ezt a funkciót a [prémium Azure Service Bus támogatja.](service-bus-premium-messaging.md) Nem engedélyezhető a standard szintű Service Bus névterek esetében.
- A titkosítás csak új vagy üres névterek esetén engedélyezhető. Ha a névtér üzenetsorokat vagy témaköröket tartalmaz, a titkosítási művelet sikertelen lesz.

A kulcsokat Azure Key Vault és naplózhatja a kulcshasználatot. Létrehozhatja a saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat a kulcsok létrehozásához. További információ a [Azure Key Vault: Mi a Azure Key Vault?](../key-vault/general/overview.md)

Ez a cikk bemutatja, hogyan konfigurálhatja a kulcstartót felhasználó által kezelt kulcsokkal a Azure Portal. A kulcstartók a Azure Portal használatával való létrehozásáról a gyorsútmutató: Azure Key Vault létrehozása a [Azure Portal.](../key-vault/general/quick-create-portal.md)

> [!IMPORTANT]
> A felhasználó által kezelt kulcsok és Azure Service Bus használatához a kulcstartónak két kötelező tulajdonsággal kell konfigurálva. Ezek a következőek:  **Soft Delete és** Do Not **Purge**. Ezek a tulajdonságok alapértelmezés szerint engedélyezve vannak, amikor új kulcstartót hoz létre a Azure Portal. Ha azonban egy meglévő kulcstartón kell engedélyeznie ezeket a tulajdonságokat, a PowerShellt vagy az Azure CLI-t kell használnia.

## <a name="enable-customer-managed-keys"></a>Felhasználó által kezelt kulcsok engedélyezése
Ha engedélyezni szeretné az ügyfél által kezelt kulcsokat a Azure Portal kövesse az alábbi lépéseket:

1. Lépjen a prémium Service Bus névterhez.
2. A **névtér** Beállítások lapján válassza Service Bus Titkosítás **lehetőséget.**
3. Válassza ki **az Ügyfél által kezelt kulcstitkosítást** az alábbi képen látható módon.

    ![Felhasználó által kezelt kulcs engedélyezése](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Kulcstartó beállítása kulcsokkal

Miután engedélyezi az ügyfél által kezelt kulcsokat, társítania kell az ügyfél által felügyelt kulcsot a Azure Service Bus névterében. Service Bus csak a Azure Key Vault. Ha engedélyezi az előző **szakaszban** a Titkosítás ügyfél által felügyelt kulccsal beállítást, a kulcsot importálni kell a Azure Key Vault. Emellett a kulcsokhoz a Soft **Delete** és **a Do Not Purge** (Végleges törlés) beállításnak kell konfigurálva lennie. Ezek a beállítások a PowerShell vagy a [parancssori felület használatával](../key-vault/general/key-vault-recovery.md) [konfigurálhatóak.](../key-vault/general/key-vault-recovery.md)

1. Új kulcstartó létrehozásához kövesse a Azure Key Vault [útmutatót.](../key-vault/general/overview.md) A meglévő kulcsok importálására vonatkozó további információkért lásd: About keys, secrets, and certificates (Tudnivalók a [kulcsokról, titkos kulcsokról és tanúsítványokról).](../key-vault/general/about-keys-secrets-certificates.md)
1. A tároló létrehozásakor a soft delete és a purge protection is bekapcsolandó az [az keyvault create paranccsal.](/cli/azure/keyvault#az_keyvault_create)

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Ha végleges törlés elleni védelmet szeretne hozzáadni egy meglévő tárolóhoz (amely már rendelkezik engedélyezett törléssel), használja [az az keyvault update](/cli/azure/keyvault#az_keyvault_update) parancsot.

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Kulcsok létrehozásához kövesse az alábbi lépéseket:
    1. Új kulcs létrehozásához válassza a Kulcsok **menü Létrehozás/Importálás** parancsát **a** Beállítások **alatt.**
        
        ![Válassza a Generate/Import (Generálás/Importálás) gombot](./media/configure-customer-managed-key/select-generate-import.png)

    1. A **Beállítások alatt** adja meg a **Generate** (Generálás) lehetőséget, és adjon nevet a kulcsnak.

        ![Kulcs létrehozása](./media/configure-customer-managed-key/create-key.png) 

    1. Ezt a kulcsot kiválasztva társíthatja a Service Bus a titkosításhoz a legördülő listából. 

        ![Kulcs kiválasztása a Key Vaultból](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > A redundancia biztosításához legfeljebb 3 kulcsot adhat hozzá. Ha az egyik kulcs lejárt vagy nem érhető el, a többi kulcs titkosításra lesz használva.
        
    1. Adja meg a kulcs adatait, majd kattintson a **Kijelölés gombra.** Ez lehetővé teszi a Microsoft által felügyelt kulcs titkosítását az Ön kulcsával (ügyfél által felügyelt kulcs). 


    > [!IMPORTANT]
    > Ha ügyfél által felügyelt kulcsot és geo-vészhelyreállítást is használni akar, tekintse át ezt a szakaszt. 
    >
    > A Microsoft által felügyelt kulcs ügyfél által felügyelt [](../key-vault/general/security-features.md) kulccsal való titkosításának engedélyezéséhez hozzáférési szabályzat van beállítva a Service Bus felügyelt identitásához a megadott Azure KeyVaulton. Ez biztosítja az Azure KeyVault szabályozott hozzáférését a Azure Service Bus névtérből.
    >
    > A következő miatt:
    > 
    >   * Ha [a földrajzi vészhelyreállítás](service-bus-geo-dr.md) már engedélyezve van a Service Bus-névtérben, és ön szeretné engedélyezni az ügyfél által felügyelt kulcsot, akkor 
    >     * A párosítás lebontása
    >     * [Állítsa be a felügyelt identitás hozzáférési](../key-vault/general/assign-access-policy-portal.md) szabályzatát mind az elsődleges, mind a másodlagos névtér számára a kulcstartóhoz.
    >     * Állítsa be a titkosítást az elsődleges névtéren.
    >     * Párosítsa újra az elsődleges és a másodlagos névteret.
    > 
    >   * Ha engedélyezni szeretné a Geo-DR-t egy olyan Service Bus, ahol az ügyfél által felügyelt kulcs már be van állítva, akkor:
    >     * [Állítsa be a másodlagos névtér](../key-vault/general/assign-access-policy-portal.md) felügyelt identitásának hozzáférési szabályzatát a kulcstartóhoz.
    >     * Párosítsa az elsődleges és a másodlagos névteret.


## <a name="rotate-your-encryption-keys"></a>A titkosítási kulcsok forgása

A kulcstartóban tárolt kulcs rotációja az Azure Key Vault rotációs mechanizmusának használatával használhatja. Az aktiválási és lejárati dátumok a kulcsrotálás automatizálására is beállíthatók. A Service Bus szolgáltatás észleli az új kulcsverziókat, és automatikusan elkezdi használni őket.

## <a name="revoke-access-to-keys"></a>Kulcsokhoz való hozzáférés visszavonása

A titkosítási kulcsokhoz való hozzáférés visszaútja nem véglegesen kiüríti az adatokat a Service Bus. Az adatok azonban nem érhetők el a Service Bus névtérből. A titkosítási kulcsot a hozzáférési szabályzaton keresztül vagy a kulcs törlésével vonhatja vissza. További információ a hozzáférési szabályzatról és a kulcstartó biztonságossá tétele a kulcstartóhoz való [biztonságos hozzáférésről.](../key-vault/general/security-features.md)

A titkosítási kulcs visszavonása után a Service Bus szolgáltatás nem fog működőképessé válni a titkosított névtéren. Ha a kulcshoz való hozzáférés engedélyezve van, vagy a törölt kulcsot visszaállítják, az Service Bus szolgáltatás kiválasztja a kulcsot, hogy hozzáférjen a titkosított Service Bus adatokhoz.

## <a name="caching-of-keys"></a>Kulcsok gyorsítótárazása
A Service Bus példány 5 percenként lekérdezi a felsorolt titkosítási kulcsokat. Gyorsítótárazza és használja őket a következő, 5 perc utáni lekérdezésig. Amíg legalább egy kulcs elérhető, az üzenetsorok és témakörök elérhetők. Ha a lekérdezés során az összes felsorolt kulcs elérhetetlenné válik, az összes üzenetsor és témakör elérhetetlenné válik. 

További részletek: 

- A Service Bus 5 percenként lekérdezi a névtér rekordja által kezelt összes felhasználó által kezelt kulcsot:
    - Ha egy kulcs el lett forgatva, a rekord frissül az új kulccsal.
    - Ha egy kulcsot visszavontak, a rendszer eltávolítja a kulcsot a rekordból.
    - Ha minden kulcs vissza lett vonva, a névtér titkosítási állapota **Revoked (Visszavonva) lesz.** Az adatok nem érhetők el a Service Bus névtérből. 
    

## <a name="use-resource-manager-template-to-enable-encryption"></a>Titkosítás engedélyezése Resource Manager sablon használatával
Ez a szakasz bemutatja, hogyan használhatja a következő feladatokat **Azure Resource Manager sablonokkal.** 

1. Hozzon létre **egy prémium** Service Bus névteret egy **felügyeltszolgáltatás-identitással.**
2. Hozzon létre **egy kulcstartót,** és adjon hozzáférést a szolgáltatásidentitásnak a kulcstartóhoz. 
3. Frissítse a Service Bus a kulcstartó adataival (kulcs/érték). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Prémium szintű Service Bus létrehozása felügyeltszolgáltatás-identitással
Ez a szakasz bemutatja, hogyan hozhat létre egy Azure Service Bus-névteret felügyeltszolgáltatás-identitással egy Azure Resource Manager és PowerShell használatával. 

1. Hozzon létre Azure Resource Manager sablont egy felügyeltszolgáltatás-identitással Service Bus szintű prémium szintű névtér létrehozásához. Nevezze el a fájlt: **CreateServiceBusPremiumNamespace.jsa következőn:** 

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
2. Hozzon létre egy nevű sablonparaméter-CreateServiceBusPremiumNamespaceParams.js **a következőn:**. 

    > [!NOTE]
    > Cserélje le a következő értékeket: 
    > - `<ServiceBusNamespaceName>` – A Service Bus neve
    > - `<Location>` – A Service Bus helye

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
3. Futtassa a következő PowerShell-parancsot a sablon üzembe helyezéséhez egy prémium szintű Service Bus létrehozásához. Ezután lekéri a névtér Service Bus, hogy később használni tudja. A parancs futtatása előtt cserélje le a helyére az erőforráscsoport `{MyRG}` nevét.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Hozzáférés Service Bus kulcstartóhoz a névtéridentitás számára

1. Futtassa a következő parancsot egy kulcstartó létrehozásához, amelynél engedélyezve van a végleges törlés **elleni** védelem és **a soft-delete.** 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    (OR)
    
    Futtassa a következő parancsot egy meglévő **kulcstartó frissítéséhez.** A parancs futtatása előtt adja meg az erőforráscsoport- és kulcstartónevek értékeit. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Állítsa be a kulcstartó hozzáférési szabályzatát úgy, hogy a Service Bus névtér felügyelt identitása hozzáfér a kulcstartó kulcsértékhez. Használja az előző szakaszban Service Bus névtér azonosítóját. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Adatok titkosítása Service Bus kulcstartóból ügyfél által felügyelt kulccsal a névtérben
Eddig a következő lépéseket tette: 

1. Létrehozott egy prémium szintű névteret egy felügyelt identitással.
2. Hozzon létre egy kulcstartót, és hozzáférést biztosít a felügyelt identitás számára a kulcstartóhoz. 

Ebben a lépésben frissíteni fogja a Service Bus kulcstartó-információkkal. 

1. Hozzon létre egyUpdateServiceBusNamespaceWithEncryption.js **nevű** JSON-fájlt a következő tartalommal: 

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

2. Hozzon létre egy sablonparaméter-fájlt: **UpdateServiceBusNamespaceWithEncryptionParams.jsa következőn:**.

    > [!NOTE]
    > Cserélje le a következő értékeket: 
    > - `<ServiceBusNamespaceName>` – A Service Bus neve
    > - `<Location>` – A Service Bus helye
    > - `<KeyVaultName>` – A kulcstartó neve
    > - `<KeyName>` – A kulcstartóban tárolt kulcs neve  

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
3. Futtassa a következő PowerShell-parancsot a Resource Manager üzembe helyezéséhez. A parancs futtatása előtt cserélje le a helyére az `{MyRG}` erőforráscsoport nevét. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:
- [Service Bus áttekintés](service-bus-messaging-overview.md)
- [Key Vault áttekintés](../key-vault/general/overview.md)
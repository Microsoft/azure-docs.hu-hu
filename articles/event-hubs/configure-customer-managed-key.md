---
title: Saját kulcs konfigurálása az Azure Event Hubs adatok titkosításához
description: Ez a cikk bemutatja, hogyan konfigurálhatja a saját kulcsát a Azure Event Hubs adatok titkosításához.
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: e3dd7cb1158294102d9bfe67629c80ae01ccdd17
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775186"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Ügyfél által kezelt kulcsok konfigurálása az Azure Event Hubs adatok titkosításához az Azure Portal
Azure Event Hubs az Azure Storage Service Encryption (Azure SSE) használatával titkosítja az Storage Service Encryption adatait. A Event Hubs szolgáltatás az Azure Storage használatával tárolja az adatokat. Az Azure Storage-ban tárolt összes adat a Microsoft által kezelt kulcsokkal van titkosítva. Ha saját kulcsot használ (más néven Bring Your Own Key (BYOK) vagy ügyfél által felügyelt kulcsot), az adatok titkosítása továbbra is a Microsoft által felügyelt kulccsal történik, de a Microsoft által felügyelt kulcs is titkosítva lesz az ügyfél által felügyelt kulccsal. Ez a funkció lehetővé teszi a Microsoft által kezelt kulcsok titkosításához használt, felhasználó által kezelt kulcsokhoz való hozzáférések létrehozására, váltására, letiltására és visszavonására. A BYOK funkció engedélyezése egy egyszeres beállítási folyamat a névtéren.

> [!NOTE]
> - A BYOK funkciót a Event Hubs [egybérlős](event-hubs-dedicated-overview.md) fürtök támogatják. Nem engedélyezhető szabványos névterek Event Hubs számára.
> - A titkosítás csak új vagy üres névterek esetén engedélyezhető. Ha a névtér eseményközpontokat tartalmaz, a titkosítási művelet sikertelen lesz.

A kulcsokat Azure Key Vault és naplózhatja a kulcshasználatot. Létrehozhatja a saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat a kulcsok létrehozásához. További információ a [Azure Key Vault: Mi a Azure Key Vault?](../key-vault/general/overview.md)

Ez a cikk bemutatja, hogyan konfigurálhatja a kulcstartót felhasználó által kezelt kulcsokkal a Azure Portal. A kulcstartók a Azure Portal használatával való létrehozásáról a gyorsútmutató: Azure Key Vault létrehozása a [Azure Portal.](../key-vault/general/quick-create-portal.md)

> [!IMPORTANT]
> Az ügyfél által kezelt kulcsok és Azure Event Hubs kulcstartó két kötelező tulajdonsággal van konfigurálva. Ezek a következőek:  **Soft Delete és** Do Not **Purge**. Ezek a tulajdonságok alapértelmezés szerint engedélyezve vannak, amikor új kulcstartót hoz létre a Azure Portal. Ha azonban egy meglévő kulcstartón kell engedélyeznie ezeket a tulajdonságokat, a PowerShellt vagy az Azure CLI-t kell használnia.

## <a name="enable-customer-managed-keys"></a>Felhasználó által kezelt kulcsok engedélyezése
Ha engedélyezni szeretné az ügyfél által kezelt kulcsokat a Azure Portal kövesse az alábbi lépéseket:

1. Keresse meg a dedikált Event Hubs fürtöt.
1. Válassza ki azt a névteret, amelyen engedélyezni szeretné a BYOK-t.
1. A **névtér** Beállítások lapján válassza Event Hubs Titkosítás **lehetőséget.** 
1. Válassza ki **az Ügyfél által kezelt kulcstitkosítást** az alábbi képen látható módon. 

    ![Felhasználó által kezelt kulcs engedélyezése](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Kulcstartó beállítása kulcsokkal
Miután engedélyezi az ügyfél által kezelt kulcsokat, társítania kell az ügyfél által felügyelt kulcsot a Azure Event Hubs névterében. Event Hubs csak a Azure Key Vault. Ha engedélyezi az előző **szakaszban** a Titkosítás ügyfél által felügyelt kulccsal beállítást, a kulcsot importálni kell a Azure Key Vault. Emellett a kulcsokhoz a Soft **Delete** és **a Do Not Purge** (Végleges törlés) beállításnak kell konfigurálva lennie. Ezek a beállítások a PowerShell vagy a [parancssori felület használatával](../key-vault/general/key-vault-recovery.md) [konfigurálhatóak.](../key-vault/general/key-vault-recovery.md)

1. Új kulcstartó létrehozásához kövesse a Azure Key Vault [útmutatót.](../key-vault/general/overview.md) A meglévő kulcsok importálására vonatkozó további információkért lásd: About keys, secrets, and certificates (Tudnivalók a [kulcsokról, titkos kulcsokról és tanúsítványokról).](../key-vault/general/about-keys-secrets-certificates.md)
1. A tároló létrehozásakor a soft delete és a purge protection is bekapcsolandó az [az keyvault create paranccsal.](/cli/azure/keyvault#az_keyvault_create)

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Ha végleges törlés elleni védelmet szeretne hozzáadni egy meglévő tárolóhoz (amely már rendelkezik engedélyezett törléssel), használja [az az keyvault update](/cli/azure/keyvault#az_keyvault_update) parancsot.

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Kulcsok létrehozásához kövesse az alábbi lépéseket:
    1. Új kulcs létrehozásához válassza a Kulcsok **menü Létrehozás/Importálás** parancsát **a** Beállítások **alatt.**
        
        ![Válassza a Generate/Import (Generálás/Importálás) gombot](./media/configure-customer-managed-key/select-generate-import.png)
    1. A **Beállítások alatt** adja meg a **Generate** (Generálás) lehetőséget, és adjon nevet a kulcsnak.

        ![Kulcs létrehozása](./media/configure-customer-managed-key/create-key.png) 
    1. Ezt a kulcsot kiválasztva társíthatja a Event Hubs névtérhez a titkosításhoz a legördülő listából. 

        ![Kulcs kiválasztása a Key Vaultból](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Adja meg a kulcs adatait, majd kattintson a **Kijelölés gombra.** Ez lehetővé teszi a Microsoft által felügyelt kulcs titkosítását az Ön kulcsával (ügyfél által felügyelt kulcs). 


## <a name="rotate-your-encryption-keys"></a>A titkosítási kulcsok forgása
A kulcstartóban az Azure Key Vault rotációs mechanizmusával rotációt használhat a kulcstartóban. Az aktiválási és lejárati dátumok beállíthatók a kulcsrotálás automatizálására is. A Event Hubs szolgáltatás észleli az új kulcsverziókat, és automatikusan elkezdi használni őket.

## <a name="revoke-access-to-keys"></a>Kulcsokhoz való hozzáférés visszavonása
A titkosítási kulcsokhoz való hozzáférés visszafordítása nem véglegesen kiüríti az adatokat a Event Hubs. Az adatok azonban nem érhetők el a Event Hubs névtérről. A titkosítási kulcsot a hozzáférési szabályzaton keresztül vagy a kulcs törlésével vonhatja vissza. További információ a hozzáférési szabályzatról és a kulcstartó biztonságossá tétele a kulcstartóhoz való [biztonságos hozzáférésről.](../key-vault/general/security-overview.md)

A titkosítási kulcs visszavonása után a Event Hubs névtér szolgáltatása működésképtelenné válik. Ha a kulcshoz való hozzáférés engedélyezve van, vagy a törlési kulcs vissza van állítani, az Event Hubs szolgáltatás kiválasztja a kulcsot, hogy hozzáférjen a titkosított Event Hubs adatokat.

## <a name="set-up-diagnostic-logs"></a>Diagnosztikai naplók beállítása 
A BYOK-kompatibilis névterek diagnosztikai naplóinak beállítása megadja a szükséges információkat a műveletekkel kapcsolatban. Ezek a naplók engedélyezhetők, és később streamelhetők egy eseményközpontba, vagy naplóelemzéssel elemezhetők, vagy streamelhetők a tárolóba a testre szabott elemzések elvégzéséhez. További információ a diagnosztikai naplókról: [Az Azure Diagnosztikai naplók áttekintése.](../azure-monitor/essentials/platform-logs-overview.md)

## <a name="enable-user-logs"></a>Felhasználói naplók engedélyezése
Kövesse az alábbi lépéseket az ügyfél által kezelt kulcsok naplóinak engedélyezéséhez.

1. A Azure Portal keresse meg a BYOK-t engedélyező névteret.
1. A **Figyelés alatt válassza** a Diagnosztikai beállítások **lehetőséget.**

    ![Diagnosztikai beállítások kiválasztása](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Válassza **a +Diagnosztikai beállítás hozzáadása lehetőséget.** 

    ![Válassza a Diagnosztikai beállítás hozzáadása lehetőséget](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Adjon meg **egy nevet,** és válassza ki, hová szeretné streamelni a naplókat.
1. Válassza **a CustomerManagedKeyUserLogs gombra, majd** a Mentés **gombra.** Ez a művelet engedélyezi a BYOK naplóit a névtérben.

    ![Felhasználó által kezelt kulcs felhasználói naplói lehetőség kiválasztása](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Naplóséma 
A rendszer minden naplót JavaScript Object Notation (JSON) formátumban tárol. Minden bejegyzés olyan sztringmezőkkel rendelkezik, amelyek az alábbi táblázatban leírt formátumot használják. 

| Név | Leírás |
| ---- | ----------- | 
| TaskName (Feladat neve) | A meghiúsult feladat leírása. |
| Tevékenységazonosító | A nyomkövetéshez használt belső azonosító. |
| category | Meghatározza a feladat besorolását. Ha például a kulcstartóban található kulcs le van tiltva, akkor az információkategória lenne, vagy ha egy kulcsot nem lehet kivetni, az hibába eshet. |
| resourceId | Azure Resource Manager erőforrás-azonosító |
| keyVault | A kulcstartó teljes neve. |
| kulcs | A névtér titkosításához használt Event Hubs. |
| version | A használt kulcs verziója. |
| művelet | A kulcstartó kulcsán végrehajtott művelet. Letilthatja/engedélyezheti például a kulcsot, burkothat vagy kicsomagíthat |
| code | A művelethez társított kód. Példa: A 404-es hibakód azt jelenti, hogy a kulcs nem található. |
| message | A művelethez kapcsolódó hibaüzenetek |

Az alábbi példa egy ügyfél által felügyelt kulcs naplóját példázhatja:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>Titkosítás engedélyezése Resource Manager sablon használatával
Ez a szakasz bemutatja, hogyan használhatja a következő feladatokat **Azure Resource Manager sablonokkal.** 

1. Hozzon **létre Event Hubs névteret** egy felügyeltszolgáltatás-identitással.
2. Hozzon **létre egy kulcstartót,** és adjon hozzáférést a szolgáltatásidentitásnak a kulcstartóhoz. 
3. Frissítse a Event Hubs névteret a kulcstartó adataival (kulcs/érték). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Felügyeltszolgáltatás-Event Hubs fürt és névtér létrehozása
Ez a szakasz bemutatja, hogyan hozhat létre egy Azure Event Hubs-névteret felügyeltszolgáltatás-identitással egy Azure Resource Manager és PowerShell használatával. 

1. Hozzon létre Azure Resource Manager sablont egy felügyeltszolgáltatás Event Hubs identitást Event Hubs névtér létrehozásához. Nevezze el a fájlt: **CreateEventHubClusterAndNamespace.jsa következőn:** 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Hozzon létre egy nevű sablonparaméter-fájlt **CreateEventHubClusterAndNamespaceParams.jsa következőn:**. 

    > [!NOTE]
    > Cserélje le a következő értékeket: 
    > - `<EventHubsClusterName>` – A Event Hubs neve    
    > - `<EventHubsNamespaceName>` – A Event Hubs neve
    > - `<Location>` – A Event Hubs helye

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. Futtassa a következő PowerShell-parancsot a sablon üzembe helyezéséhez egy új Event Hubs létrehozásához. Ezután lekéri a névtér Event Hubs, hogy később használni tudja. A parancs futtatása előtt cserélje le a helyére az erőforráscsoport `{MyRG}` nevét.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Hozzáférés Event Hubs kulcstartóhoz a névtér identitása számára

1. Futtassa a következő parancsot egy kulcstartó létrehozásához, amelynél engedélyezve van a végleges törlés **elleni** védelem és **a soft-delete.** 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    (OR)    
    
    Futtassa a következő parancsot egy meglévő **kulcstartó frissítéséhez.** A parancs futtatása előtt adja meg az erőforráscsoport és a kulcstartó nevének értékeit. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Állítsa be úgy a kulcstartó-hozzáférési szabályzatot, hogy a Event Hubs névtér felügyelt identitása hozzáfér a kulcstartóban tárolt kulcsértékhez. Használja az előző szakaszban Event Hubs névtér azonosítóját. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>A névtérben Event Hubs adatok titkosítása ügyfél által felügyelt kulccsal a Key Vaultból
Eddig a következő lépéseket tette: 

1. Létrehozott egy prémium névteret egy felügyelt identitással.
2. Hozzon létre egy kulcstartót, és hozzáférést biztosít a felügyelt identitás számára a kulcstartóhoz. 

Ebben a lépésben frissíteni fogja a Event Hubs kulcstartó-információkkal. 

1. Hozzon létre egyCreateEventHubClusterAndNamespace.js **nevű** JSON-fájlt a következő tartalommal: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
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

2. Hozzon létre egy sablonparaméter-fájlt: **UpdateEventHubClusterAndNamespaceParams.jsa következőn:**. 

    > [!NOTE]
    > Cserélje le a következő értékeket: 
    > - `<EventHubsClusterName>` – A Event Hubs neve.        
    > - `<EventHubsNamespaceName>` – A Event Hubs neve
    > - `<Location>` – A Event Hubs helye
    > - `<KeyVaultName>` – A kulcstartó neve
    > - `<KeyName>` – A kulcstartóban tárolt kulcs neve

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
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
3. Futtassa a következő PowerShell-parancsot a Resource Manager üzembe helyezéséhez. A parancs futtatása előtt cserélje le a helyére az erőforráscsoport `{MyRG}` nevét. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Hibaelhárítás
Ajánlott eljárásként mindig engedélyezze a naplókat az előző szakaszban látható módon. Segít a tevékenységek nyomon követésében, ha a BYOK-titkosítás engedélyezve van. Emellett segít a problémák hatókörének megszabadulásában is.

A BYOK-titkosítás engedélyezésekor gyakran előforduló hibakódok a következők.

| Művelet | Hibakód | Az adatok eredményül kapott állapota |
| ------ | ---------- | ----------------------- | 
| Kulcstartó wrap/unwrap engedélyének eltávolítása | 403 |    Hozzáférhetetlen |
| Az AAD-szerepkörtagság eltávolítása egy olyan AAD-rendszerbiztonsági tagból, amely megadta a wrap/unwrap engedélyt | 403 |  Hozzáférhetetlen |
| Titkosítási kulcs törlése a kulcstartóból | 404 | Hozzáférhetetlen |
| Kulcstartó törlése | 404 | Nem érhető el (feltételezi, hogy a soft-delete engedélyezve van, ami kötelező beállítás).) |
| A titkosítási kulcs lejárati időszakának módosítása úgy, hogy az már lejárt | 403 |   Hozzáférhetetlen  |
| Az NBF módosítása (nem korábban), hogy a kulcstitkosítási kulcs ne aktív | 403 | Hozzáférhetetlen  |
| Válassza az **MSFT-szolgáltatások engedélyezése** lehetőséget a Key Vault tűzfalához, vagy egyéb módon letiltja a titkosítási kulccsal védett kulcstartóhoz való hálózati hozzáférést | 403 | Hozzáférhetetlen |
| A kulcstartó átköltöztetve egy másik bérlőre | 404 | Hozzáférhetetlen |  
| Időszakos hálózati probléma vagy DNS/AAD/MSI-szolgáltatáskimaradás |  | Gyorsítótárazott adattitkosítási kulccsal érhető el |

> [!IMPORTANT]
> A Geo-DR BYOK titkosítást használó névtérben való engedélyezéséhez a párosításhoz használt másodlagos névtérnek egy dedikált fürtben kell lennie, és engedélyeznie kell rajta egy rendszer által hozzárendelt felügyelt identitást. További információ: [Azure-erőforrások felügyelt identitása.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:
- [Event Hubs áttekintése](event-hubs-about.md)
- [Key Vault áttekintés](../key-vault/general/overview.md)
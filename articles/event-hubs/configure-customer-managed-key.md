---
title: Saját kulcs konfigurálása az Azure-Event Hubs inaktív adatok titkosításához
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja saját kulcsát az Azure Event Hubs-adatok titkosításához.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 00e33bc3464aed1829968b7957e48455eaa04447
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933786"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Ügyfél által felügyelt kulcsok konfigurálása az Azure Event Hubs-adatok inaktív titkosításához a Azure Portal használatával
Az Azure Event Hubs az Azure Storage Service Encryption (Azure SSE) segítségével titkosítja az inaktív adatok titkosítását. Az Event Hubs szolgáltatás az Azure Storage-t használja az adattároláshoz. Az Azure Storage-ban tárolt összes adattal a Microsoft által felügyelt kulcsokkal van titkosítva. Ha saját kulcsot (más néven Bring Your Own Key (BYOK) vagy ügyfél által felügyelt kulcsot) használ, az adatok továbbra is a Microsoft által felügyelt kulccsal lesznek titkosítva, a Microsoft által felügyelt kulcs pedig az ügyfél által felügyelt kulcs használatával lesz titkosítva. Ez a funkció lehetővé teszi a Microsoft által felügyelt kulcsok titkosításához használt ügyfél által felügyelt kulcsok elérésének létrehozását, elforgatását, letiltását és visszavonását. A BYOK funkció engedélyezése egy egyszeri telepítési folyamat a névtérben.

> [!NOTE]
> A BYOK képességet [Event Hubs dedikált egybérlős](event-hubs-dedicated-overview.md) fürtök támogatják. Nem engedélyezhető a standard Event Hubs névterekhez.

A Azure Key Vault segítségével kezelheti a kulcsokat, és naplózhatja a kulcshasználat. Létrehozhatja saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához. További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../key-vault/general/overview.md)

Ez a cikk bemutatja, hogyan konfigurálhat egy Key vaultot az ügyfél által felügyelt kulcsokkal a Azure Portal használatával. Ha meg szeretné tudni, hogyan hozhat létre kulcstartót a Azure Portal használatával, tekintse meg a rövid útmutató [: Azure Key Vault létrehozása a Azure Portal használatával](../key-vault/general/quick-create-portal.md)című témakört.

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok Azure-Event Hubs való használata megköveteli, hogy a kulcstartó két szükséges tulajdonsággal rendelkezzen. Ezek a következők:  **Soft delete** és **not Purge**. Ezek a tulajdonságok alapértelmezés szerint engedélyezve vannak, amikor új kulcstartót hoz létre a Azure Portal. Ha azonban egy meglévő kulcstartón kell engedélyeznie ezeket a tulajdonságokat, akkor a PowerShellt vagy az Azure CLI-t kell használnia.

## <a name="enable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok engedélyezése
Az ügyfél által felügyelt kulcsok Azure Portal való engedélyezéséhez kövesse az alábbi lépéseket:

1. Navigáljon a dedikált Event Hubs-fürthöz.
1. Válassza ki azt a névteret, amelyen engedélyezni szeretné a BYOK.
1. A Event Hubs névtér **Beállítások** lapján válassza a **titkosítás** lehetőséget. 
1. Válassza ki az **ügyfél által felügyelt kulcs titkosítását a nyugalmi** állapotban, ahogy az az alábbi képen is látható. 

    ![Felhasználó által kezelt kulcs engedélyezése](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Key Vault beállítása kulcsokkal
Az ügyfél által felügyelt kulcsok engedélyezése után hozzá kell rendelnie az ügyfél által felügyelt kulcsot az Azure Event Hubs-névtérhez. A Event Hubs csak Azure Key Vault használatát támogatja. Ha az előző szakaszban az **ügyfél által felügyelt kulcs** beállítással engedélyezi a titkosítást, a kulcsot Azure Key Vaultba kell importálnia. Emellett a kulcsoknak is rendelkeznie kell a **Soft delete** szolgáltatással, és **nem szabad kiüríteni** a kulcsot. Ezeket a beállításokat a [PowerShell](../key-vault/general/key-vault-recovery.md) vagy a [parancssori](../key-vault/general/key-vault-recovery.md)felület használatával lehet konfigurálni.

1. Új kulcstartó létrehozásához kövesse [az Azure Key Vault rövid](../key-vault/general/overview.md)útmutatót. A meglévő kulcsok importálásával kapcsolatos további információkért lásd: [a kulcsok, a titkok és a tanúsítványok ismertetése](../key-vault/general/about-keys-secrets-certificates.md).
1. Ha be szeretné kapcsolni a törlés és a kiürítés védelmét a tároló létrehozásakor, használja az az kulcstartó [create](/cli/azure/keyvault#az-keyvault-create) parancsot.

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Ha a kiürítési védelmet egy meglévő tárolóba kívánja hozzáadni (amely már rendelkezik a helyreállított törlés engedélyezésével), használja az az kulcstartó [Update](/cli/azure/keyvault#az-keyvault-update) parancsot.

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. A kulcsok létrehozásához kövesse az alábbi lépéseket:
    1. Új kulcs létrehozásához válassza a **Létrehozás/importálás** elemet a **kulcsok** menüből a **Beállítások** területen.
        
        ![A létrehozó/Importálás gomb kiválasztása](./media/configure-customer-managed-key/select-generate-import.png)
    1. Adja **meg a** kívánt nevet a kulcs **létrehozásához** és megadásához.

        ![Kulcs létrehozása](./media/configure-customer-managed-key/create-key.png) 
    1. Most kiválaszthatja ezt a kulcsot, hogy társítsa a Event Hubs névteret a titkosításhoz a legördülő listából. 

        ![Kulcs kiválasztása a Key vaultból](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Adja meg a kulcs részleteit, és kattintson a **kiválasztás** elemre. Ez lehetővé teszi a Microsoft által felügyelt kulcs titkosítását a kulccsal (ügyfél által felügyelt kulcs). 


## <a name="rotate-your-encryption-keys"></a>A titkosítási kulcsok elforgatása
A Key vaultban az Azure Key Vaults rotációs mechanizmus használatával forgathatja el a kulcsot. Az aktiválási és a lejárati dátumok is megadhatók a kulcs elforgatásának automatizálására. A Event Hubs szolgáltatás felderíti az új kulcs-verziókat, és automatikusan elkezdi használni őket.

## <a name="revoke-access-to-keys"></a>Kulcsok elérésének visszavonása
A titkosítási kulcsokhoz való hozzáférés visszavonása nem törli az Event Hubsból származó adatok törlését. Azonban az adatok nem érhetők el a Event Hubs névtérből. A titkosítási kulcsot a hozzáférési házirendben vagy a kulcs törlésével vonhatja vissza. További információ a hozzáférési házirendekről és a Key Vault biztonságossá [tételéről a kulcstartó biztonságos eléréséről](../key-vault/general/secure-your-key-vault.md).

A titkosítási kulcs visszavonása után a titkosított névtér Event Hubs szolgáltatása inműködőképes lesz. Ha a kulcshoz való hozzáférés engedélyezve van, vagy a törlési kulcs vissza van állítva, Event Hubs szolgáltatás a kulcsot fogja kiválasztani, hogy hozzáférhessen az adatokhoz a titkosított Event Hubs névtérből.

## <a name="set-up-diagnostic-logs"></a>Diagnosztikai naplók beállítása 
A diagnosztikai naplók beállítása a BYOK-kompatibilis névterekhez megadja a szükséges információkat a műveletekről. Ezeket a naplókat engedélyezheti és később továbbíthatja az Event hub számára, vagy elemezheti a log Analytics szolgáltatásban, vagy továbbíthatja a tárolóba a testreszabott elemzések elvégzéséhez. További információ a diagnosztikai naplókról: [Az Azure diagnosztikai naplók áttekintése](../azure-monitor/platform/platform-logs-overview.md).

## <a name="enable-user-logs"></a>Felhasználói naplók engedélyezése
Az alábbi lépéseket követve engedélyezheti a naplók számára az ügyfél által felügyelt kulcsokat.

1. A Azure Portal Navigáljon arra a névtérre, amelyen engedélyezve van a BYOK.
1. A **figyelés** területen válassza a **diagnosztikai beállítások** lehetőséget.

    ![Diagnosztikai beállítások kiválasztása](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Válassza a **+ diagnosztikai beállítások hozzáadása** elemet. 

    ![Válassza a diagnosztikai beállítás hozzáadása elemet.](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Adjon meg egy **nevet** , és válassza ki, hová szeretné továbbítani a naplókat.
1. Válassza a **CustomerManagedKeyUserLogs** és a **Mentés** lehetőséget. Ez a művelet engedélyezi a BYOK lévő naplókat a névtérben.

    ![Válassza az ügyfél által felügyelt kulcs felhasználói naplók lehetőséget](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Napló sémája 
Az összes napló JavaScript Object Notation (JSON) formátumban van tárolva. Minden bejegyzés tartalmaz egy karakterlánc-mezőt, amely az alábbi táblázatban ismertetett formátumot használja. 

| Név | Leírás |
| ---- | ----------- | 
| Feladatnév | A sikertelen feladat leírása. |
| Tevékenységazonosító | A nyomon követéshez használt belső azonosító. |
| category | Meghatározza a feladat besorolását. Ha például a kulcstartó kulcsa le van tiltva, akkor az egy információs kategória lenne, vagy ha egy kulcs nem csomagolható ki, a hiba a következő lehet:. |
| resourceId | Erőforrás-azonosító Azure Resource Manager |
| keyVault | A Key Vault teljes neve. |
| kulcs | Az Event Hubs névtér titkosításához használt kulcsnév. |
| version | A használt kulcs verziószáma. |
| művelet | A Key vaultban a kulcsban végrehajtott művelet. Például letilthatja/engedélyezheti a kulcsot, becsomagolhatja vagy kicsomagolhatja |
| code | A művelethez társított kód. Példa: hibakód, 404 azt jelenti, hogy a kulcs nem található. |
| message | A művelethez társított hibaüzenetek |

Íme egy példa egy ügyfél által felügyelt kulcs naplójára:

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

## <a name="use-resource-manager-template-to-enable-encryption"></a>A titkosítás engedélyezése Resource Manager-sablon használatával
Ez a szakasz bemutatja, hogyan végezheti el a következő feladatokat **Azure Resource Manager sablonok** használatával. 

1. Hozzon létre egy **Event Hubs névteret** egy felügyelt szolgáltatás identitásával.
2. Hozzon létre egy **kulcstartót** , és adja meg a szolgáltatás identitásának hozzáférését a kulcstartóhoz. 
3. Frissítse a Event Hubs névteret a Key Vault-információkkal (kulcs/érték). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Event Hubs-fürt és-névtér létrehozása felügyelt szolgáltatás identitásával
Ebből a szakaszból megtudhatja, hogyan hozhat létre egy felügyelt szolgáltatás-identitással rendelkező Azure Event Hubs névteret egy Azure Resource Manager sablonnal és a PowerShell használatával. 

1. Hozzon létre egy Azure Resource Manager sablont egy felügyelt szolgáltatás identitásával rendelkező Event Hubs névtér létrehozásához. Nevezze el a következő fájlt: **CreateEventHubClusterAndNamespace.js**: 

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
2. Hozzon létre egy sablon-paraméter nevű fájlt: **CreateEventHubClusterAndNamespaceParams.js**. 

    > [!NOTE]
    > Cserélje le a következő értékeket: 
    > - `<EventHubsClusterName>` – A Event Hubs-fürt neve    
    > - `<EventHubsNamespaceName>` -Event Hubs névtér neve
    > - `<Location>` – A Event Hubs névtér helye

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
3. Futtassa a következő PowerShell-parancsot a sablon üzembe helyezéséhez Event Hubs névtér létrehozásához. Ezután kérje le a Event Hubs névtér AZONOSÍTÓját, hogy később használhassa. `{MyRG}`A parancs futtatása előtt cserélje le az nevet az erőforráscsoport nevére.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Event Hubs névtér-identitás hozzáférésének engedélyezése a Key vaulthoz

1. A következő parancs futtatásával hozzon létre egy Key vaultot a **kiürítési védelemmel** , és engedélyezze a helyreállítható **törlést** . 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    VAGY    
    
    Futtassa a következő parancsot egy **meglévő kulcstartó** frissítéséhez. A parancs futtatása előtt határozza meg az erőforráscsoport és a kulcstároló nevének értékét. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Állítsa be a Key Vault hozzáférési házirendjét úgy, hogy a Event Hubs névtér felügyelt identitása hozzáférhessen a Key Vault kulcs értékéhez. Használja a Event Hubs névtér AZONOSÍTÓját az előző szakaszból. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Adatok titkosítása Event Hubs névtérben az ügyfél által felügyelt kulccsal a Key vaultból
A következő lépéseket eddig végrehajtotta: 

1. Létrehozott egy prémium szintű névteret egy felügyelt identitással.
2. Hozzon létre egy kulcstartót, és biztosítson hozzáférést a Key vaulthoz a felügyelt identitáshoz. 

Ebben a lépésben frissíteni fogja a Event Hubs névteret a Key Vault-információkkal. 

1. Hozzon létre egy **CreateEventHubClusterAndNamespace.js** nevű JSON-fájlt a következő tartalommal: 

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

2. Hozzon létre egy sablon-paramétert tartalmazó fájlt: **UpdateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Cserélje le a következő értékeket: 
    > - `<EventHubsClusterName>` – A Event Hubs-fürt neve.        
    > - `<EventHubsNamespaceName>` -Event Hubs névtér neve
    > - `<Location>` – A Event Hubs névtér helye
    > - `<KeyVaultName>` – A Key Vault neve
    > - `<KeyName>` – A kulcs neve a Key vaultban

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
3. Futtassa a következő PowerShell-parancsot a Resource Manager-sablon üzembe helyezéséhez. `{MyRG}`A parancs futtatása előtt cserélje le az nevet az erőforráscsoport nevére. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Hibaelhárítás
Ajánlott eljárásként mindig engedélyezze a naplókat, például az előző szakaszban láthatókat. Segít a tevékenységek nyomon követésében, ha a BYOK titkosítás engedélyezve van. Emellett segít a problémák megoldásában.

A következő gyakori hibakódokat kell megkeresnie, amikor a BYOK-titkosítás engedélyezve van.

| Művelet | Hibakód | Eredményül kapott állapot |
| ------ | ---------- | ----------------------- | 
| Kicsomagolási/kicsomagolási engedély eltávolítása a kulcstartóból | 403 |    Nem érhető el |
| HRE-szerepkör tagságának eltávolítása egy olyan HRE, amely a wrap/unwrap engedélyt adta | 403 |  Nem érhető el |
| Titkosítási kulcs törlése a Key vaultból | 404 | Nem érhető el |
| A Key Vault törlése | 404 | Nem érhető el (feltételezi, hogy a helyreállítható törlés engedélyezve van, ami kötelező beállítás.) |
| A lejárati idő módosítása a titkosítási kulcson úgy, hogy az már lejárt | 403 |   Nem érhető el  |
| A NBF módosítása (nem korábban), így a kulcs titkosítási kulcsa nem aktív | 403 | Nem érhető el  |
| A Key Vault-tűzfalhoz tartozó **MSFT-szolgáltatások engedélyezése** lehetőség kiválasztásával vagy egyéb módon blokkolja a titkosítási kulccsal rendelkező kulcstartó hálózati hozzáférését. | 403 | Nem érhető el |
| A Key Vault áthelyezése másik bérlőre | 404 | Nem érhető el |  
| Átmeneti hálózati hiba vagy DNS-/HRE/MSI-leállás |  | Elérhető a gyorsítótárazott adattitkosítási kulcs használatával |

> [!IMPORTANT]
> Ha engedélyezni szeretné, hogy a Geo-DR olyan névtérben legyen, amely a BYOK-titkosítást használja, a párosítás másodlagos névterének dedikált fürtben kell lennie, és rendelkeznie kell egy hozzá tartozó, rendszerhez rendelt felügyelt identitással. További információ: [felügyelt identitások az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:
- [Event Hubs áttekintése](event-hubs-about.md)
- [Key Vault áttekintése](../key-vault/general/overview.md)
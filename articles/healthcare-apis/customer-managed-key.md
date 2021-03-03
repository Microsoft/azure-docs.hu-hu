---
title: Ügyfél által felügyelt kulcsok konfigurálása a FHIR készült Azure API-hoz
description: A FHIR Azure API-n keresztül a saját kulcs funkcióját is támogatja a Cosmos DB
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: ginle
ms.openlocfilehash: f810769529569309656193d41f28cca201a85c07
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719219"
---
# <a name="configure-customer-managed-keys-at-rest"></a>Ügyfél által felügyelt kulcsok konfigurálása nyugalmi állapotban

Amikor új Azure API-t hoz létre a FHIR-fiókhoz, a rendszer alapértelmezés szerint a Microsoft által felügyelt kulcsokkal titkosítja adatait. Most hozzáadhat egy második titkosítási réteget az adataihoz saját maga által választott és felügyelt kulcs használatával.

Az Azure-ban ez általában az ügyfél Azure Key Vault található titkosítási kulcs használatával valósítható meg. Az Azure SQL, az Azure Storage és a Cosmos DB néhány példa erre a képességre. A FHIR készült Azure API a Cosmos DB által nyújtott támogatást használja. Fiók létrehozásakor lehetősége van Azure Key Vault kulcs URI azonosítójának megadására. Ezt a kulcsot a rendszer a Cosmos DB az adatbázis-fiók üzembe helyezésekor adja át. FHIR-kérés esetén Cosmos DB lekéri a kulcsot, és a használatával titkosítja vagy visszafejti az adatait. Az első lépésekhez tekintse át az alábbi hivatkozásokat:

- [Az Azure-előfizetéshez tartozó Azure Cosmos DB erőforrás-szolgáltató regisztrálása](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Az Azure Key Vault-példány konfigurálása](../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Hozzáférési szabályzat hozzáadása az Azure Key Vault-példányhoz](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Kulcs létrehozása Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="using-azure-portal"></a>Az Azure Portal használata

Az Azure API FHIR-Azure Portal fiókhoz való létrehozásakor a "További beállítások" lapon a "Database Settings" (adatbázis-beállítások) alatt az "adattitkosítási" konfigurációs beállítás látható. Alapértelmezés szerint a szolgáltatás által felügyelt kulcs beállítás lesz kiválasztva. 

A kulcsot a kulcstartóból választhatja ki:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Kiválasztó":::

Itt megadhatja a Azure Key Vault kulcsot az "ügyfél által felügyelt kulcs" lehetőség kiválasztásával. Itt megadhatja a kulcs URI-JÁT:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Azure API létrehozása a FHIR-hez":::

A meglévő FHIR-fiókok esetében az alábbi módon tekintheti meg a kulcs titkosítási lehetőségeit (szolgáltatás vagy ügyfél által felügyelt kulcs) az "adatbázis" panelen. A konfigurációs beállítás kiválasztása után nem módosítható. A kulcsot azonban módosíthatja és frissítheti.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Adatbázis":::

Emellett a megadott kulcs új verzióját is létrehozhatja, amely után az adatok a szolgáltatás megszakítása nélkül lesznek titkosítva az új verzióval. A kulcshoz való hozzáférés eltávolításával is eltávolíthatja az adathozzáférést. Ha a kulcs le van tiltva, a lekérdezések hibát eredményeznek. Ha a kulcs újból engedélyezve van, a lekérdezések ismét sikeresek lesznek.




## <a name="using-azure-powershell"></a>Az Azure PowerShell használata

A Azure Key Vault kulcs URI-ja segítségével az alábbi PowerShell-parancs futtatásával konfigurálhatja a CMK a PowerShell használatával:

```powershell
New-AzHealthcareApisService
    -Name "myService"
    -Kind "fhir-R4"
    -ResourceGroupName "myResourceGroup"
    -Location "westus2"
    -CosmosKeyVaultKeyUri "https://<my-vault>.vault.azure.net/keys/<my-key>"
```

## <a name="using-azure-cli"></a>Az Azure parancssori felület használata

A PowerShell-metódushoz hasonlóan a CMK is konfigurálhatja, ha átadja a Azure Key Vault kulcs URI-JÁT a `key-vault-key-uri` paraméterben, és az alábbi CLI-parancsot futtatja: 

```azurecli-interactive
az healthcareapis service create
    --resource-group "myResourceGroup"
    --resource-name "myResourceName"
    --kind "fhir-R4"
    --location "westus2"
    --cosmos-db-configuration key-vault-key-uri="https://<my-vault>.vault.azure.net/keys/<my-key>"

```
## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager sablon használata

A Azure Key Vault kulcs URI-ja segítségével a CMK úgy konfigurálhatja, hogy a **Tulajdonságok** objektum **keyVaultKeyUri** tulajdonságában átadja azt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "services_myService_name": {
            "defaultValue": "myService",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.HealthcareApis/services",
            "apiVersion": "2020-03-30",
            "name": "[parameters('services_myService_name')]",
            "location": "westus2",
            "kind": "fhir-R4",
            "properties": {
                "accessPolicies": [],
                "cosmosDbConfiguration": {
                    "offerThroughput": 400,
                    "keyVaultKeyUri": "https://<my-vault>.vault.azure.net/keys/<my-key>"
                },
                "authenticationConfiguration": {
                    "authority": "https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47",
                    "audience": "[concat('https://', parameters('services_myService_name'), '.azurehealthcareapis.com')]",
                    "smartProxyEnabled": false
                },
                "corsConfiguration": {
                    "origins": [],
                    "headers": [],
                    "methods": [],
                    "maxAge": 0,
                    "allowCredentials": false
                }
            }
        }
    ]
}
```

A sablont a következő PowerShell-parancsfájllal is telepítheti:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan konfigurálhatja az ügyfelek által felügyelt kulcsokat a Azure Portal, a PowerShell, a CLI és a Resource Manager-sablon használatával. Az esetlegesen felmerülő kérdésekkel kapcsolatban tekintse meg a Azure Cosmos DB gyakori kérdések című szakaszt: 
 
>[!div class="nextstepaction"]
>[Cosmos DB: a CMK beállítása](../cosmos-db/how-to-setup-cmk.md#frequently-asked-questions)
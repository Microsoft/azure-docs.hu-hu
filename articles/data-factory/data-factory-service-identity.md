---
title: Felügyelt identitás Data Factoryhoz
description: Ismerkedjen meg Azure Data Factory felügyelt identitásával.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: jingwang
ms.openlocfilehash: 65512f8e46b5545929a798392ac5f19ddeab39ed
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562460"
---
# <a name="managed-identity-for-data-factory"></a>Felügyelt identitás Data Factoryhoz

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk segítséget nyújt a felügyelt identitások Data Factory (korábbi nevén Managed Service Identity/MSI) és működésének megismeréséhez.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés

Az adatok előállítójának létrehozásakor a rendszer felügyelt identitást hozhat létre a gyári létrehozással együtt. A felügyelt identitás egy Azure Active Directory számára regisztrált felügyelt alkalmazás, amely az adott adatelőállítót jelöli.

A Data Factory felügyelt identitása a következő funkciókat nyújtja:

- [Tárolja a hitelesítő adatokat Azure Key Vaultban](store-credentials-in-key-vault.md), amelyekben a rendszer az adat-előállító felügyelt identitását használja Azure Key Vault hitelesítéshez.
- A felügyelt identitásos hitelesítéssel fér hozzá az adattárakhoz vagy a számítási feladatokhoz, például az Azure Blob Storage, az Azure Adatkezelő, a Azure Data Lake Storage Gen1, a Azure Data Lake Storage Gen2, a Azure SQL Database, az Azure SQL felügyelt példánya, az Azure szinapszis Analytics, a REST, a Databricks-tevékenység Részletekért olvassa el az összekötő és a tevékenységgel kapcsolatos cikkeket.

## <a name="generate-managed-identity"></a>Felügyelt identitás előállítása

A Data Factory felügyelt identitása a következőképpen jön létre:

- Ha a **Azure Portal vagy a PowerShell** használatával hoz létre adatelőállítót, a rendszer mindig automatikusan létrehozza a felügyelt identitást.
- Ha az **SDK**-n keresztül hozza létre az adatelőállítót, akkor a felügyelt identitás csak akkor jön létre, ha a gyári objektumban a létrehozáshoz a "Identity = New FactoryIdentity ()" lehetőséget adta meg. Lásd: példa a .net gyors üzembe helyezése [– adatelőállító létrehozása](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Ha a **Rest APIon** keresztül hoz létre adatelőállítót, akkor a felügyelt identitás csak akkor jön létre, ha a kérelem törzsében megadja az "Identity" szakaszt. Lásd: példa a REST rövid útmutatóban [– adatok létrehozása Factory](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Ha úgy találja, hogy az adatok előállítója nem rendelkezik a [felügyelt identitások beolvasására](#retrieve-managed-identity) vonatkozó felügyelt identitással, akkor explicit módon létrehozhatja az egyiket, ha az Identity kezdeményezővel programozottan frissíti az adatok előállítóját:

- [Felügyelt identitás előállítása a PowerShell használatával](#generate-managed-identity-using-powershell)
- [Felügyelt identitás előállítása REST API használatával](#generate-managed-identity-using-rest-api)
- [Felügyelt identitás létrehozása Azure Resource Manager sablon használatával](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Felügyelt identitás előállítása az SDK használatával](#generate-managed-identity-using-sdk)

>[!NOTE]
>- A felügyelt identitás nem módosítható. Egy olyan adat-előállító frissítése, amely már rendelkezik felügyelt identitással, nem lesz hatással, a felügyelt identitás változatlan marad.
>- Ha olyan adatelőállítót frissít, amely már rendelkezik felügyelt identitással, és nem ad meg "Identity" paramétert a gyári objektumban, vagy nincs megadva az "Identity" szakasz a REST-kérelem törzsében, hibaüzenet jelenik meg.
>- Amikor töröl egy adatgyárat, a társított felügyelt identitást a rendszer törli.

### <a name="generate-managed-identity-using-powershell"></a>Felügyelt identitás előállítása a PowerShell használatával

Hívja **meg a set-AzDataFactoryV2** parancsot, majd az "Identity" mezőket az újonnan generált értékekkel látja el:

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>Felügyelt identitás előállítása REST API használatával

Hívja meg az alábbi API-t a kérelem törzsének "Identity" szakaszában:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Kérelem törzse**: adja hozzá az "Identity": {"type": "SystemAssigned"}.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Válasz**: a felügyelt identitás automatikusan létrejön, és az "Identity" szakasz ennek megfelelően van feltöltve.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Felügyelt identitás létrehozása Azure Resource Manager sablon használatával

**Sablon**: adja hozzá az "Identity": {"type": "SystemAssigned"}.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>Felügyelt identitás előállítása az SDK használatával

Hívja meg a adat-előállító create_or_update függvényt a következővel: Identity = New FactoryIdentity (). Mintakód a .NET használatával:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Felügyelt identitás beolvasása

A felügyelt identitást Azure Portal vagy programozott módon kérheti le. A következő részekben néhány minta látható.

>[!TIP]
> Ha nem látja a felügyelt identitást, akkor a gyári frissítéssel [létrehozhatja a felügyelt identitást](#generate-managed-identity) .

### <a name="retrieve-managed-identity-using-azure-portal"></a>Felügyelt identitás beolvasása a Azure Portal használatával

A felügyelt identitás adatait a Azure Portal-> a adat-előállító > tulajdonságai között találja.

- Felügyelt identitás objektumának azonosítója
- Felügyelt identitás bérlője

A felügyelt identitás adatai is megjelennek, ha társított szolgáltatást hoz létre, amely támogatja a felügyelt identitások hitelesítését, például az Azure Blob, a Azure Data Lake Storage, a Azure Key Vault stb.

Az engedélyek megadásakor az Azure-erőforrás Access Control (IAM) lapján > szerepkör-hozzárendelés hozzáadása – > hozzáférés hozzárendelése a következőhöz: > Select Data Factory a rendszer által hozzárendelt felügyelt identitás alatt – > a gyári név alapján válassza a kiválasztás lehetőséget. vagy Általánosságban elmondható, hogy az objektumazonosító vagy az adatok előállítójának neve (felügyelt identitás neve) segítségével megkeresi ezt az identitást. Ha be kell szereznie a felügyelt identitás alkalmazás-AZONOSÍTÓját, használhatja a PowerShellt.

### <a name="retrieve-managed-identity-using-powershell"></a>Felügyelt identitás beolvasása a PowerShell használatával

A rendszer a felügyelt identitás résztvevő-AZONOSÍTÓját és a bérlő AZONOSÍTÓját adja vissza, amikor a következő módon kap egy adott adatgyárat. Hozzáférés biztosítása a **PrincipalId** használatával:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Az alkalmazás AZONOSÍTÓját a rendszerbiztonsági tag fenti AZONOSÍTÓjának másolásával szerezheti be, majd az alábbi parancs futtatásával Azure Active Directory parancsot az egyszerű azonosító paraméterként.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

### <a name="retrieve-managed-identity-using-rest-api"></a>Felügyelt identitás beolvasása a REST API használatával

A rendszer a felügyelt identitás résztvevő-AZONOSÍTÓját és a bérlő AZONOSÍTÓját adja vissza, amikor a következő módon kap egy adott adatgyárat.

Hívja meg az alábbi API-t a kérelemben:

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}?api-version=2018-06-01
```

**Válasz**: az alábbi példában láthatóhoz hasonló választ fog kapni. Az "Identity" szakasz ennek megfelelően van feltöltve.

```json
{
    "name":"<dataFactoryName>",
    "identity":{
        "type":"SystemAssigned",
        "principalId":"554cff9e-XXXX-XXXX-XXXX-90c7d9ff2ead",
        "tenantId":"72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{
        "provisioningState":"Succeeded",
        "createTime":"2020-02-12T02:22:50.2384387Z",
        "version":"2018-06-01",
        "factoryStatistics":{
            "totalResourceCount":0,
            "maxAllowedResourceCount":0,
            "factorySizeInGbUnits":0,
            "maxAllowedFactorySizeInGbUnits":0
        }
    },
    "eTag":"\"03006b40-XXXX-XXXX-XXXX-5e43617a0000\"",
    "location":"<region>",
    "tags":{

    }
}
```

> [!TIP] 
> A felügyelt identitás ARM-sablonból való lekéréséhez vegyen fel egy **kimenet** SZAKASZT az ARM JSON-ban:

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.DataFactory/factories', parameters('<dataFactoryName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő témaköröket, amelyek bemutatják, hogy mikor és hogyan kell használni az adatok gyári felügyelt identitását

- [Hitelesítő adatok tárolása Azure Key Vaultban](store-credentials-in-key-vault.md)
- [Adatok másolása az Azure-erőforrások hitelesítéséhez tartozó felügyelt identitások használatával vagy Azure Data Lake Storeba](connector-azure-data-lake-store.md)

Lásd: [felügyelt identitások az Azure-erőforrásokhoz – áttekintés](../active-directory/managed-identities-azure-resources/overview.md) az Azure-erőforrások felügyelt identitásának további hátteréről, amelyet az adatok gyári felügyelt identitása alapul.
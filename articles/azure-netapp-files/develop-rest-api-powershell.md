---
title: Fejlesztés a PowerShell használatával történő REST API Azure NetApp Fileshoz | Microsoft Docs
description: Leírja, hogyan kezdheti el a Azure NetApp Files REST API a PowerShell használatával.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/02/2020
ms.author: b-juche
ms.openlocfilehash: 2e169bb4f7be8b52657d2caf8f05643875a8348c
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180377"
---
# <a name="develop-for-azure-netapp-files-with-rest-api-using-powershell"></a>Fejlesztés a REST API a PowerShell használatával történő Azure NetApp Files

A Azure NetApp Files szolgáltatás REST API a HTTP-műveleteket definiálja az erőforrásokon, például a NetApp-fiókon, a kapacitás-készleten, a köteteken és a pillanatképeken. Ez a cikk segítséget nyújt a Azure NetApp Files REST API a PowerShell használatával való használatának megkezdésében.

## <a name="azure-netapp-files-rest-api-specification"></a>Azure NetApp Files REST API-specifikáció

A Azure NetApp Files REST API specifikációját a [githubon](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)keresztül tesszük közzé:

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Hozzáférés a Azure NetApp Files REST API  

1. Ha még nem tette meg, [telepítse az Azure CLI](/cli/azure/install-azure-cli) -t.
2. Egyszerű szolgáltatásnév létrehozása a Azure Active Directoryban (Azure AD):
   1. Ellenőrizze, hogy rendelkezik-e [megfelelő engedélyekkel](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

   2. Adja meg az alábbi parancsot az Azure CLI-ben:  

      ```azurepowershell
      $RBAC_SP = az ad sp create-for-rbac --name <YOURSPNAMEGOESHERE> | ConvertFrom-Json         
      ```

      Az egyszerű szolgáltatásnév adatainak megjelenítéséhez írja be a következőt:, `$RBAC_SP` majd nyomja le az ENTER billentyűt.

      ```output
      appId       : appID displays here
      displayName : displayName
      name        : http://SP_Name
      password    : super secret password
      tenant      : your tenant shows here
      ```
        
      A rendszer menti a kimenetet a változó objektumba `$RBAC_SP` . A, a és az értékeket fogjuk használni `$RBAC_SP.appId` `$RBAC_SP.password` `$RBAC_SP.tenant` .

3. OAuth hozzáférési jogkivonat igénylése:

    A cikkben szereplő példák a PowerShellt használják. Számos olyan API-eszközt is használhat, mint például a [Poster](https://www.getpostman.com/), az [álmatlanság](https://insomnia.rest/)és a [Paw](https://paw.cloud/).  

    A `$RBAC_SP` változó használatával most megkapjuk a tulajdonosi jogkivonatot. 
    
    ```azurepowershell
    $body = "grant_type=client_credentials&client_id=$($RBAC_SP.appId)&client_secret=$($RBAC_SP.password)&resource=https://management.azure.com/"
    $BearerToken = Invoke-RestMethod -Method Post -body $body -Uri https://login.microsoftonline.com/$($RBAC_SP.tenant)/oauth2/token
    ```
    A kimenet egy tulajdonosi jogkivonat objektumot tartalmaz. A hozzáférési tokent a következő beírásával tekintheti meg: `$BearerToken.access_token` . Az objektumazonosító a következő példához hasonlóan néz ki:

    ```output
    eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9
    ```

    A megjelenített jogkivonat 3600 másodpercig érvényes. Ezt követően új jogkivonatot kell kérnie. A rendszer menti a tokent a változóba, és a következő lépésben fogja használni.

4. Hozza létre az `headers` objektumot:

    ```azurepowershell  
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer $($BearerToken.access_token)")
    $headers.Add("Content-Type", "application/json")
    ```

5. A REST APIhoz való hozzáférésének ellenőrzéséhez küldje el a tesztet, és adja meg a jogkivonatot:

    ```azurepowershell
    $SubId = (Get-AzureRmContext).Subscription.Id 
    Invoke-RestMethod -Method Get -Headers $headers -Uri https://management.azure.com/subscriptions/$SubId/providers/Microsoft.Web/sites?api-version=2019-11-01
    ```

## <a name="examples-using-the-api"></a>Az API-t használó példák  

Ez a cikk az alábbi URL-címet használja a kérelmek alaptervéhez. Ez az URL-cím a Azure NetApp Files névtér gyökerére mutat. 

`https://management.azure.com/subscriptions/$SUBID/resourceGroups/$RESOURCEGROUP/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01`

A következő példáknak a saját értékeivel való futtatása előtt érdemes változó értékeket rendelni. A PowerShell-változók beírásával érhetők el `$variablename` .
A PowerShell-változók a használatával rendelhetők hozzá `$variablename = “value”` .

```azurepowershell
$Region = “westus2" 
$ResourceGroup = “MYTest-RG-63" 
$ANFvnetname = “NetAppFilesVnet-63"
$ANFvnetCIDR = “10.63.64.0/18"
$ANFsubnet = “NetAppFilesSubnet-63"
$ANFsubnetCIDR = “10.63.120.0/28"
$ANFsubnetID = “/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.Network/virtualNetworks/$ANFvnetname/subnets/$ANFSubnet"
$ANFAccount = “TestoftheAPI"
$ANFCapacityPool = “ANFTestPool"
$ANFServicelevel = “Standard"
$ANFVolume = “ANFTestVolume"
$ANFVolumeShareName = “Share-TEST"
$ANFVolumesize = 100GB
$ANFSnapshot = “ANFTestSnapshot"
```

### <a name="put-request-examples"></a>Példák PUT-kérelemre

A PUT-kérelem használatával új objektumokat hozhat létre a Azure NetApp Filesban, az alábbi példáknak megfelelően. A PUT kérelem törzse tartalmazza a módosítások JSON formátumú adatát. A PowerShell-parancsnak szövegként kell szerepelnie, vagy fájlként hivatkoznia kell rá. Ha fájlba szeretné hivatkozni a törzsre, mentse a JSON-példát egy fájlba, és adja hozzá `-body (Get-Content @<filename>)` a PowerShell-parancshoz.

```azurepowershell
    #create a NetApp account  
    $body = "{ 
        `"name`": `"$ANFAccount`", 
        `"type`": `"Microsoft.NetApp/netAppAccounts`", 
        `"location`": `"$Region`", 
        `"properties`": { 
            `"name`": `"$ANFAccount`" 
        }
    } "

    $api_version = "2020-02-01"
    Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount`?api-version=$api_version"  
```

```azurepowershell
    #create a capacity pool  
    $body = "{
      `"location`": `"$Region`",
      `"properties`": {
        `"size`": " + 4TB + ",
        `"serviceLevel`": `"Standard`"
      }
    }"
    $api_version = "2020-02-01"
    Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool`?api-version=$api_version" 
```

```azurepowershell
    #create a volume  
    $body = "{
        `"name`": `"$ANFVolume`",
        `"type`": `"Microsoft.NetApp/netAppAccounts/capacityPools/volumes`",
        `"location`": `"$Region`",
        `"properties`": {
            `"serviceLevel`": `"$ANFServicelevel`",
            `"usageThreshold`": `"$ANFVolumesize`",
            `"creationToken`": `"$ANFVolumeShareName`",
            `"snapshotId`": `"`",
            `"subnetId`": `"$ANFSubnetID`"
        }
    }"
    $api_version = "2020-02-01"
    Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool/volumes/$ANFVolume`?api-version=$api_version" 
```

```azurepowershell
    #create a volume snapshot
    $body = "{
        `"name`": `"$ANFAccount/$ANFCapacityPool/$ANFVolume/$ANFSnapshot`",
        `"type`": `"Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots`",
        `"location`": `"$Region`",
        `"properties`": {
            `"name`": `"$ANFSnapshot`",
            `"fileSystemId`": `"$FileSystemID`"
        }
    }"
    $api_version = '2020-02-01'
    Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool/volumes/$ANFVolume/Snapshots/$ANFSnapshot`?api-version=$api_version"
```

### <a name="json-examples"></a>JSON-példák

Az alábbi példa bemutatja, hogyan hozhat létre egy NetApp-fiókot:

```json
    { 
        "name": "MYNETAPPACCOUNT", 
        "type": "Microsoft.NetApp/netAppAccounts", 
        "location": "westus2", 
        "properties": { 
            "name": "MYNETAPPACCOUNT" 
        }
    } 
```

Az alábbi példa bemutatja, hogyan hozhat létre egy kapacitás-készletet: 

```json
    {
        "name": "MYNETAPPACCOUNT/POOLNAME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
        "location": "westus2",
        "properties": {
            "name": "POOLNAME",
            "size": "4398046511104",
            "serviceLevel": "Premium"
        }
    }
```

Az alábbi példa bemutatja, hogyan hozhat létre új kötetet. (A kötethez tartozó alapértelmezett protokoll a NFSV3.) 

```json
    {
        "name": "MYNEWVOLUME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes",
        "location": "westus2",
        "properties": {
            "serviceLevel": "Premium",
            "usageThreshold": "322122547200",
            "creationToken": "MY-FILEPATH",
            "snapshotId": "",
            "subnetId": "/subscriptions/$SUBID/resourceGroups/$RESOURCEGROUP/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
        }
    }
```

Az alábbi példa bemutatja, hogyan hozhat létre egy kötet pillanatképét: 

```json
    {
        "name": "apitest2/apiPool01/apiVol01/snap02",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
        "location": "westus2",
        "properties": {
            "name": "snap02",
            "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
        }
    }
```

> [!NOTE] 
> Meg kell adnia `fileSystemId` egy pillanatkép létrehozásához.  Az értéket lekérheti a `fileSystemId` Get kéréssel egy kötetre. 

### <a name="get-request-examples"></a>Példák a kérelmek beszerzésére

Hiba történik, ha az erőforrás nem létezik. A GET kérelem használatával lekérdezheti Azure NetApp Files objektumait egy előfizetésben, az alábbi példákban láthatók:

```azurepowershell
#get NetApp accounts 
Invoke-RestMethod -Method Get -Headers $headers -Uri https://management.azure.com/subscriptions/$SUBID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01 | ConvertTo-Json
```

```azurepowershell
#get capacity pools for NetApp account 
Invoke-RestMethod -Method Get -Headers $headers -Uri https://management.azure.com/subscriptions/$SUBID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFACCOUNT/capacityPools?api-version=2019-11-01 | ConvertTo-Json
```

```azurepowershell
#get volumes in NetApp account & capacity pool 
Invoke-RestMethod -Method Get -Headers $headers -Uri https://management.azure.com/subscriptions/$SUBID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFACCOUNT/capacityPools/$ANFCAPACITYPOOL/volumes?api-version=2019-11-01 | ConvertTo-Json
```

```azurepowershell
#get snapshots for a volume 
Invoke-RestMethod -Method Get -Headers $headers -Uri https://management.azure.com/subscriptions/$SUBID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFACCOUNT/capacityPools/$ANFCAPACITYPOOL/volumes/$ANFVOLUME/snapshots?api-version=2019-11-01 | ConvertTo-Json
```

### <a name="complete-powershell-scripts"></a>PowerShell-parancsfájlok befejezése
Ez a szakasz a PowerShell minta parancsfájljait mutatja be.

```azurepowershell
    <#
    Disclaimer 
    The sample scripts are not supported under any Microsoft standard support program or service. The sample scripts are provided AS IS without warranty of any kind. Microsoft further disclaims all implied warranties including, without limitation, any implied warranties of merchantability or of fitness for a particular purpose. The entire risk arising out of the use or performance of the sample scripts and documentation remains with you. In no event shall Microsoft, its authors, or anyone else involved in the creation, production, or delivery of the scripts be liable for any damages whatsoever (including, without limitation, damages for loss of business profits, business interruption, loss of business information, or other pecuniary loss) arising out of the use of or inability to use the sample scripts or documentation, even if Microsoft has been advised of the possibility of such damages.
    #>
    
    $Region = "westus2"
    $SubID = (Get-AzureRmContext).Subscription.Id
    $MyRandomID =  Get-Random -Minimum 100 -Maximum 999
    $ResourceGroup = "MYTest-RG-" + $MyRandomID
    $ANFAccount = "ANFTestAccount-$Region-" + $MyRandomID
    $ANFCapacityPool =  "ANFTestPool-$Region-" + $MyRandomID
    $ANFVolume = "ANFTestVolume-$Region-" + $MyRandomID
    $ANFVolumesize = 100GB
    $ANFVolumeShareName = "Share-" + $MyRandomID
    $ANFSnapshot = "ANFTestSnapshot-$Region-" + $MyRandomID
    $ANFServicelevel = "Standard"
    $Octet2 = Get-Random -Minimum 1 -Maximum 254
    $ANFvnetname = "NetAppFilesVnet-$Octet2-$Region-" + $MyRandomID
    $ANFvnetCIDR = "10.$Octet2.64.0/18"
    $ANFsubnet = "NetAppFilesSubnet-$Octet2-$Region-" + $MyRandomID
    $ANFsubnetCIDR = "10.$Octet2.120.0/28"
    $vmsubnet = "VM-subnet-$Octet2-$Region-" + $MyRandomID
    $vmsubnetCIDR = "10.$Octet2.121.0/24"
    
    $BearerToken = (az account get-access-token | ConvertFrom-Json).accessToken
    
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer $BearerToken")
    $headers.Add("Content-Type", "application/json")
    
    " ** Creating Resource Group $ResourceGroup *************"
    $api_version = "2020-01-01"
    $body = "    {
      `"location`": `"$Region`",
      `"name`": `"$ResourceGroup`"
    },"
    $response = Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body -Uri "https://management.azure.com/subscriptions/$SubID/resourcegroups/$ResourceGroup`?api-version=$api_version" 
    While ($response.properties.provisioningState -notmatch "Succeeded") {
       $response.properties.provisioningState
       sleep 5
       $response = Invoke-RestMethod -Method ‘GET’ -Headers $headers -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup`?api-version=$api_version" 
       } 
    
    " ** Creating Virtual Network $ANFvnetname *************"
    $body = "{
      `"properties`": {
        `"addressSpace`": {
          `"addressPrefixes`": [
            `"$ANFvnetCIDR`"
          ]
        },
        `"subnets`": [
          {
            `"name`": `"$ANFsubnet`",
            `"properties`": {
              `"addressPrefix`": `"$ANFsubnetCIDR`",
              `"delegations`": [
                {
                  `"name`": `"Microsoft.NetApp`",
                  `"properties`": {
                    `"serviceName`": `"Microsoft.NetApp/volumes`"
                  }
                }
              ]
            }
          },
         {
            `"name`": `"$vmsubnet`",
            `"properties`": {
              `"addressPrefix`": `"$vmsubnetCIDR`"
                }
         }
        ]
      },
      `"location`": `"$Region`"
    }"
    
    $api_version = "2020-03-01"
    $response = Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.Network/virtualNetworks/$ANFvnetname`?api-version=$api_version" 
    While ($response.properties.provisioningState -notmatch "Succeeded") {
           sleep 5
           $response = Invoke-RestMethod -Method ‘GET’ -Headers $headers -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.Network/virtualNetworks/$ANFvnetname`?api-version=$api_version" 
           $response.properties.provisioningState
           }  
    $ANFsubnetID = $response.properties.subnets.id[0]
    
    " ** Creating ANF Account $ANFAccount *************"
    $body = "{ 
        `"name`": `"$ANFAccount`", 
        `"type`": `"Microsoft.NetApp/netAppAccounts`", 
        `"location`": `"$Region`", 
        `"properties`": { 
            `"name`": `"$ANFAccount`" 
        }
    } "
    $api_version = "2020-02-01"
    $response = Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount`?api-version=$api_version" 
    $response.properties.provisioningState
    While ($response.properties.provisioningState -notmatch "Succeeded") {
       $response.properties.provisioningState
       sleep 5
       $response = Invoke-RestMethod -Method ‘GET’ -Headers $headers -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount`?api-version=$api_version" 
       }  
    
    " ** Creating Capacity Pool $ANFCapacityPool *************"
    $body = "{
      `"location`": `"$Region`",
      `"properties`": {
        `"size`": " + 4TB + ",
        `"serviceLevel`": `"Standard`"
      }
    }"
    $api_version = "2020-02-01"
    $response = Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool`?api-version=$api_version" 
    $response.properties.provisioningState
    While ($response.properties.provisioningState -notmatch "Succeeded") {
       $response.properties.provisioningState
       sleep 5
       $response = Invoke-RestMethod -Method ‘GET’ -Headers $headers -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount`?api-version=$api_version" 
       }  
    
    " ** Creating ANF Volume $ANFVolume *************"
    $body = "{
        `"name`": `"$ANFVolume`",
        `"type`": `"Microsoft.NetApp/netAppAccounts/capacityPools/volumes`",
        `"location`": `"$Region`",
        `"properties`": {
            `"serviceLevel`": `"$ANFServicelevel`",
            `"usageThreshold`": `"$ANFVolumesize`",
            `"creationToken`": `"$ANFVolumeShareName`",
            `"snapshotId`": `"`",
            `"subnetId`": `"$ANFSubnetID`"
        }
    }"
    $api_version = "2020-02-01"
    $response = Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool/volumes/$ANFVolume`?api-version=$api_version" 
    While ($response.properties.provisioningState -notmatch "Succeeded") {
       $response.properties.provisioningState
       sleep 15
       $response = Invoke-RestMethod -Method ‘GET’ -Headers $headers -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool/volumes/$ANFVolume`?api-version=$api_version" 
       } 
    $Volume = $response 
    $FileSystemID = $response.properties.fileSystemId
    
    " ** Creating ANF Volume Snapshot $ANFSnapshot *************"
    $body = "{
        `"name`": `"$ANFAccount/$ANFCapacityPool/$ANFVolume/$ANFSnapshot`",
        `"type`": `"Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots`",
        `"location`": `"$Region`",
        `"properties`": {
            `"name`": `"$ANFSnapshot`",
            `"fileSystemId`": `"$FileSystemID`"
        }
    }"
    $api_version = '2020-02-01'
    $response = Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool/volumes/$ANFVolume/Snapshots/$ANFSnapshot`?api-version=$api_version" 
    While ($response.properties.provisioningState -notmatch "Succeeded") {
       $response.properties.provisioningState
       sleep 5
       $response = Invoke-RestMethod -Method ‘GET’ -Headers $headers -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool/volumes/$ANFVolume/Snapshots/$ANFSnapshot`?api-version=$api_version" 
       }  
```

## <a name="next-steps"></a>Következő lépések

[Lásd a Azure NetApp Files REST API referenciát](/rest/api/netapp/)
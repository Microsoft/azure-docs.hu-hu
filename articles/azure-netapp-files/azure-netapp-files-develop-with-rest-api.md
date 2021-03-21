---
title: Fejlesztés a Azure NetApp Fileshoz REST APIkal | Microsoft Docs
description: A Azure NetApp Files szolgáltatás REST API az olyan erőforrások HTTP-műveleteit definiálja, mint például a NetApp-fiók, a kapacitás-készlet, a kötetek és a pillanatképek.
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
ms.openlocfilehash: c5993dc1dc645319e272ab310a97bc3ff8ac495d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102174240"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Fejlesztés az Azure NetApp Filesra a REST API-val 

A Azure NetApp Files szolgáltatás REST API a HTTP-műveleteket definiálja az erőforrásokon, például a NetApp-fiókon, a kapacitás-készleten, a köteteken és a pillanatképeken. Ez a cikk a Azure NetApp Files REST API használatának első lépéseiben nyújt segítséget.

## <a name="azure-netapp-files-rest-api-specification"></a>Azure NetApp Files REST API-specifikáció

A Azure NetApp Files REST API specifikációját a [githubon](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)keresztül tesszük közzé:

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Hozzáférés a Azure NetApp Files REST API  

1. Ha még nem tette meg, [telepítse az Azure CLI](/cli/azure/install-azure-cli) -t.
2. Egyszerű szolgáltatásnév létrehozása a Azure Active Directoryban (Azure AD):
   1. Ellenőrizze, hogy rendelkezik-e [megfelelő engedélyekkel](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

   2. Adja meg az alábbi parancsot az Azure CLI-ben: 
    
        ```azurecli
        az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE
        ```

      A parancs kimenete az alábbi példához hasonló:  

        ```output
        { 
            "appId": "appIDgoeshere", 
            "displayName": "APPNAME", 
            "name": "http://APPNAME", 
            "password": "supersecretpassword", 
            "tenant": "tenantIDgoeshere" 
        } 
        ```

      Tartsa meg a parancs kimenetét.  Szüksége lesz a `appId` , a `password` és az `tenant` értékekre. 

3. OAuth hozzáférési jogkivonat igénylése:

    A cikkben szereplő példák a cURL használatát mutatják be. Számos olyan API-eszközt is használhat, mint például a [Poster](https://www.getpostman.com/), az [álmatlanság](https://insomnia.rest/)és a [Paw](https://paw.cloud/).  

    Cserélje le az alábbi példában szereplő változókat a fenti 2. lépésből származó parancs kimenetére. 
    
    ```azurecli
    curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token
    ```

    A kimenet a következő példához hasonló hozzáférési jogkivonatot biztosít:

    `eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9`

    A megjelenített jogkivonat 3600 másodpercig érvényes. Ezt követően új jogkivonatot kell kérnie. 
    Mentse a jogkivonatot egy szövegszerkesztőbe.  A következő lépéshez szüksége lesz rá.

4. A REST APIhoz való hozzáférésének ellenőrzéséhez küldje el a tesztet, és adja meg a jogkivonatot:

    ```azurecli
    curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2019-11-01
    ```

## <a name="examples-using-the-api"></a>Az API-t használó példák  

Ez a cikk az alábbi URL-címet használja a kérelmek alaptervéhez. Ez az URL-cím a Azure NetApp Files névtér gyökerére mutat. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01`

A `SUBIDGOESHERE` `RESOURCEGROUPGOESHERE` következő példákban szereplő értékeket cserélje le a saját értékeire. 

### <a name="get-request-examples"></a>Példák a kérelmek beszerzésére

A GET kérelem használatával lekérdezheti Azure NetApp Files objektumait egy előfizetésben, az alábbi példákban láthatók: 

```azurecli
#get NetApp accounts 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01
```

```azurecli
#get capacity pools for NetApp account 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2019-11-01
```

```azurecli
#get volumes in NetApp account & capacity pool 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2019-11-01
```

```azurecli
#get snapshots for a volume 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2019-11-01
```

### <a name="put-request-examples"></a>Példák PUT-kérelemre

A PUT-kérelem használatával új objektumokat hozhat létre a Azure NetApp Filesban, az alábbi példáknak megfelelően. A PUT kérelem törzse tartalmazhatja a módosítások JSON formátumú adatát is. A curl-parancsnak szövegfájlként vagy hivatkozásként kell szerepelnie. Ha fájlba szeretné hivatkozni a törzsre, mentse a JSON-példát egy fájlba, és adja hozzá `-d @<filename>` a curl parancshoz.

```azurecli
#create a NetApp account  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2019-11-01
```

```azurecli
#create a capacity pool  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2019-11-01
```

```azurecli
#create a volume  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2019-11-01
```

```azurecli
 #create a volume snapshot  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2019-11-01
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
        "subnetId": "/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
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

## <a name="next-steps"></a>Következő lépések

[Lásd a Azure NetApp Files REST API referenciát](/rest/api/netapp/)
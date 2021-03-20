---
title: Azure API Management Soft-Delete (előzetes verzió) | Microsoft Docs
description: A Soft delete lehetővé teszi a törölt API Management példányok helyreállítását.
ms.service: api-management
ms.topic: conceptual
author: vladvino
ms.author: apimpm
ms.date: 11/27/2020
ms.openlocfilehash: e2842f3e428abb4f0eb628dbb8e446f2714d5d89
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101652385"
---
# <a name="api-management-soft-delete-preview"></a>API Management Soft-Delete (előzetes verzió)

API Management Soft-Delete (előzetes verzió) használatával helyreállíthatja és visszaállíthatja a nemrég törölt API Management (APIM) példányokat.

> [!IMPORTANT]
> `2020-06-01-preview`A jelen cikkben ismertetett lépések alapján csak a és újabb API-verziókkal törölt API Management-példányok lesznek törölve és helyreállítva. A korábbi API-verziók használatával törölt APIM-példányok továbbra is törlődnek. Azure PowerShell és az Azure CLI jelenleg nem használja a `2020-06-01-preview` verziót, és a rendszer a törlési viselkedést is eredményezi.

## <a name="supporting-interfaces"></a>Támogató felületek

A Soft-delete funkció [Rest APIon](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/restore)keresztül érhető el.

> [!TIP]
> Az Azure REST API-k meghívására szolgáló tippekkel és eszközökkel kapcsolatban tekintse meg az [azure REST API-referenciát](/rest/api/azure/) .

| Művelet | Description | API Management névtér | API minimális verziója |
|--|--|--|--|
| [Létrehozás vagy frissítés](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) | Létrehoz vagy frissít egy API Management szolgáltatást.  | API Management szolgáltatás | Bármelyik |
| [Létrehozás vagy frissítés](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) `restore` **igaz** értékre beállított tulajdonsággal | API Management szolgáltatás törlésének visszavonása, ha korábban már törölve lett. Ha `restore` meg van adva, és az `true` összes többi tulajdonságra van beállítva, figyelmen kívül lesz hagyva.  | API Management szolgáltatás |  2020-06-01 – előzetes verzió |
| [Törlés](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/delete) | Egy meglévő API Management-szolgáltatás törlése. | API Management szolgáltatás | 2020-06-01 – előzetes verzió|
| [Beolvasás név szerint](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) | A Soft-Deleted API Management szolgáltatás név szerint történő beolvasása. | Törölt szolgáltatások | 2020-06-01 – előzetes verzió |
| [Listázás előfizetés alapján](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) | Felsorolja az adott előfizetés törléséhez rendelkezésre álló összes helyreállítható szolgáltatást. | Törölt szolgáltatások | 2020-06-01 – előzetes verzió
| [Véglegesen töröl](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) | Kiüríti API Management szolgáltatást (törli a törlési lehetőség nélkül). | Törölt szolgáltatások | 2020-06-01 – előzetes verzió

## <a name="soft-delete-behavior"></a>Helyreállítható törlés működése

A API Management-példány létrehozásához bármely API-verziót használhat, azonban a `2020-06-01-preview` vagy újabb verziókat kell használnia a APIM-példány törléséhez (és lehetősége van a helyreállításra).

API Management példány törlésekor a szolgáltatás törölve lesz, így elérhetetlenné válik bármely APIM művelet számára. Ebben az állapotban a APIM-példány csak listázható, állítható helyre vagy törölhető (véglegesen törlődik).

Ugyanakkor az Azure a APIM-példányhoz tartozó mögöttes adatok törlését is ütemezni fogja az előre meghatározott (48 órás) adatmegőrzési időköz után. A példányhoz tartozó DNS-rekord a megőrzési időtartam időtartamára is megmarad. Nem lehet felhasználni egy olyan API Management-példány nevét, amelyet a rendszer a megőrzési időszak lejárta után töröl.

Ha a APIM példánya 48 órán belül nem állítható helyre, akkor a rendszer nem fogja tudni törölni (helyreállíthatatlan). Dönthet úgy is, hogy [kiüríti](#purge-a-soft-deleted-apim-instance) (véglegesen törli) az APIM-példányát, lemondva a visszatartási időszak törlését.

## <a name="list-deleted-apim-instances"></a>Törölt APIM-példányok listázása

Az [előfizetési](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) műveletek alapján ellenőrizheti, hogy van-e helyreállítható APIM-példány (Törlés visszavonása) a [törölt szolgáltatások használatával](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) .

### <a name="get-a-soft-deleted-instance-by-name"></a>A törölt példányok neve alapján

Használja az Azure [](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) - `{subscriptionId}` `{location}` `{serviceName}` előfizetése, az erőforrás helye és a API Management példány neve alapján az API Management Get by Name műveletet

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Ha a törlés visszavonására van lehetőség, az Azure a és a APIM-példányának egy rekordját fogja visszaadni, `deletionDate` `scheduledPurgeDate` például:

```json
{
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ApiManagement/locations/southcentralus/deletedservices/apimtest",
    "name": "apimtest",
    "type": "Microsoft.ApiManagement/deletedservices",
    "location": "South Central US",
    "properties": {
        "serviceId": "/subscriptions/########-####-####-####-############/resourceGroups/apimtestgroup/providers/Microsoft.ApiManagement/service/apimtest",
        "scheduledPurgeDate": "2020-11-26T19:40:26.3596893Z",
        "deletionDate": "2020-11-24T19:40:50.1013572Z"
    }
}
```

### <a name="list-all-soft-deleted-instances-for-a-given-subscription"></a>Egy adott előfizetés összes lágyan törölt példányának listázása

Használja a API Management [listát előfizetési](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) művelettel, az `{subscriptionId}` előfizetés-azonosítóval való helyettesítéssel:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/deletedservices?api-version=2020-06-01-preview
```

Ez a lista visszaadja az összes, az adott előfizetéshez elérhető, törlésre kész szolgáltatást, amely megjeleníti a és a értéket `deletionDate` `scheduledPurgeDate` .

## <a name="recover-a-deleted-apim-instance"></a>Törölt APIM-példány helyreállítása

Használja az Azure-előfizetés, az erőforráscsoport-név és a API Management neve API Management [létrehozási vagy frissítési](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) műveletét, `{subscriptionId}` `{resourceGroup}` valamint a és a helyettesítését `{apimServiceName}` :

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ApiManagement/service/{apimServiceName}?api-version=2020-06-01-preview
```

. . . és állítsa be a `restore` tulajdonságot `true` a kérelem törzsében. (Ha meg van adva ez a jelző, és *igaz* értékre van állítva, az összes többi tulajdonság figyelmen kívül lesz hagyva.) Például:

```json
{
  "properties": {
    "publisherEmail": "help@contoso.com",
    "publisherName": "Contoso",
    "restore": true
  },
  "sku": {
    "name": "Developer",
    "capacity": 1
  },
  "location": "South Central US"
}
```

## <a name="purge-a-soft-deleted-apim-instance"></a>Helyreállítható APIM-példány törlése

Az Azure- [](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) `{subscriptionId}` `{location}` `{serviceName}` előfizetése, az erőforrás helye és a API Management neve helyett használja a API Management kiürítési műveletet, a és a helyettesítését:

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Ezzel véglegesen törli a API Management példányt az Azure-ból.

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a hosszú távú API Management biztonsági mentési és helyreállítási lehetőségekkel:

- [Vészhelyreállítás a biztonsági mentés és visszaállítás használatával az Azure API Managementben](api-management-howto-disaster-recovery-backup-restore.md)
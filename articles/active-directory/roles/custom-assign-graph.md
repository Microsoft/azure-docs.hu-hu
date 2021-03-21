---
title: Azure AD-rendszergazdai szerepkörök kiosztása Microsoft Graph API-val | Microsoft Docs
description: Azure AD-rendszergazdai szerepkörök kiosztása és eltávolítása Graph APIekkel Azure Active Directory
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67e77c2dd08386279beeb06ff9081fa2669afa73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102487834"
---
# <a name="assign-custom-admin-roles-using-the-microsoft-graph-api-in-azure-active-directory"></a>Egyéni rendszergazdai szerepkörök kiosztása a Microsoft Graph API használatával Azure Active Directory 

Automatizálhatja, hogyan rendeljen hozzá szerepköröket a felhasználói fiókokhoz a Microsoft Graph API használatával. Ez a cikk a roleAssignments POST, GET és DELETE műveleteit ismerteti.

## <a name="required-permissions"></a>Szükséges engedélyek

A szerepkörök hozzárendeléséhez vagy eltávolításához globális rendszergazda vagy Kiemelt szerepkörű rendszergazdai fiók használatával csatlakozhat az Azure AD-szervezethez.

## <a name="post-operations-on-roleassignment"></a>RoleAssignment utáni műveletek

### <a name="example-1-create-a-role-assignment-between-a-user-and-a-role-definition"></a>1. példa: szerepkör-hozzárendelés létrehozása a felhasználó és a szerepkör-definíció között

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
Content-type: application/json
```

Törzs

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "directoryScopeId":"/"  // Don't use "resourceScope" attribute in Azure AD role assignments. It will be deprecated soon.
}
```

Reagálás

``` HTTP
HTTP/1.1 201 Created
```

### <a name="example-2-create-a-role-assignment-where-the-principal-or-role-definition-does-not-exist"></a>2. példa: szerepkör-hozzárendelés létrehozása, amelyben a rendszerbiztonsági tag vagy szerepkör-definíció nem létezik

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Törzs

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "directoryScopeId":"/"  //Don't use "resourceScope" attribute in Azure AD role assignments. It will be deprecated soon.
}
```

Reagálás

``` HTTP
HTTP/1.1 404 Not Found
```
### <a name="example-3-create-a-role-assignment-on-a-single-resource-scope"></a>3. példa: szerepkör-hozzárendelés létrehozása egyetlen erőforrás-hatókörön

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Törzs

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"e9b2b976-1dea-4229-a078-b08abd6c4f84",    //role template ID of a custom role
    "directoryScopeId":"/13ff0c50-18e7-4071-8b52-a6f08e17c8cc"  //object ID of an application
}
```

Reagálás

``` HTTP
HTTP/1.1 201 Created
```

### <a name="example-4-create-an-administrative-unit-scoped-role-assignment-on-a-built-in-role-definition-which-is-not-supported"></a>4. példa: felügyeleti egység hatókörű szerepkör-hozzárendelésének létrehozása beépített szerepkör-definícióban, amely nem támogatott

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Törzs

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"29232cdf-9323-42fd-ade2-1d097af3e4de",    //role template ID of Exchange Administrator
    "directoryScopeId":"/administrativeUnits/13ff0c50-18e7-4071-8b52-a6f08e17c8cc"    //object ID of an administrative unit
}
```

Reagálás

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "message":"The given built-in role is not supported to be assigned to a single resource scope."
        }
    }
}
```

A felügyeleti egység hatóköre csak a beépített szerepkörök egy részhalmazát engedélyezi. A felügyeleti egységekben támogatott beépített szerepkörök listáját [ebben a dokumentációban](admin-units-assign-roles.md) találja.

## <a name="get-operations-on-roleassignment"></a>Műveletek beolvasása a RoleAssignment

### <a name="example-5-get-role-assignments-for-a-given-principal"></a>5. példa: szerepkör-hozzárendelések beolvasása egy adott résztvevőhöz

GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId+eq+'<object-id-of-principal>'
```

Reagálás

``` HTTP
HTTP/1.1 200 OK
{
"value":[
            { 
                "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
                "directoryScopeId":"/"  
            } ,
            {
                "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"fe930be7-5e62-47db-91af-98c3a49a38b1",
                "directoryScopeId":"/"
            }
        ]
}
```

### <a name="example-6-get-role-assignments-for-a-given-role-definition"></a>6. példa: szerepkör-hozzárendelések beolvasása egy adott szerepkör-definícióhoz.

GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=roleDefinitionId+eq+'<object-id-or-template-id-of-role-definition>'
```

Reagálás

``` HTTP
HTTP/1.1 200 OK
{
"value":[
            {
                "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"fe930be7-5e62-47db-91af-98c3a49a38b1",
                "directoryScopeId":"/"
            }
     ]
}
```

### <a name="example-7-get-a-role-assignment-by-id"></a>7. példa: szerepkör-hozzárendelés beolvasása azonosító alapján.

GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Reagálás

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1",
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "directoryScopeId":"/"
}
```

### <a name="example-8-get-role-assignments-for-a-given-scope"></a>8. példa: szerepkör-hozzárendelések beolvasása egy adott hatókörhöz


GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=directoryScopeId+eq+'/d23998b1-8853-4c87-b95f-be97d6c6b610'
```

Reagálás

``` HTTP
HTTP/1.1 200 OK
{
"value":[
            { 
                "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
                "directoryScopeId":"/d23998b1-8853-4c87-b95f-be97d6c6b610"
            } ,
            {
                "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
                "directoryScopeId":"/d23998b1-8853-4c87-b95f-be97d6c6b610"
            }
        ]
}
```

## <a name="delete-operations-on-roleassignment"></a>Műveletek törlése a RoleAssignment

### <a name="example-9-delete-a-role-assignment-between-a-user-and-a-role-definition"></a>9. példa: szerepkör-hozzárendelés törlése egy felhasználó és egy szerepkör-definíció között.

DELETE

``` HTTP
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Reagálás
``` HTTP
HTTP/1.1 204 No Content
```

### <a name="example-10-delete-a-role-assignment-that-no-longer-exists"></a>10. példa: már nem létező szerepkör-hozzárendelés törlése

DELETE

``` HTTP
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Reagálás

``` HTTP
HTTP/1.1 404 Not Found
```

### <a name="example-11-delete-a-role-assignment-between-self-and-global-administrator-role-definition"></a>11. példa: szerepkör-hozzárendelés törlése az önkiszolgáló és a globális rendszergazda szerepkör-definíció között

DELETE

``` HTTP
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Reagálás

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Removing self from Global Administrator built-in role is not allowed"},
            "values":null
        }
    }
}
```

Megakadályozhatja, hogy a felhasználók töröljék a saját globális rendszergazdai szerepkörét, hogy elkerülje azt a helyzetet, amelyben a bérlőnek nulla globális rendszergazdája van. Az önmagukhoz rendelt egyéb szerepkörök eltávolítása engedélyezett.

## <a name="next-steps"></a>Következő lépések

* Nyugodtan ossza meg velünk az [Azure ad rendszergazdai szerepkörökkel foglalkozó fórumát](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
* A szerepkörökkel és a rendszergazdai szerepkör-hozzárendeléssel kapcsolatos további tudnivalókért lásd: [rendszergazdai szerepkörök hozzárendelése](permissions-reference.md)
* Az alapértelmezett felhasználói engedélyek összehasonlítását lásd: a [vendég és a tag alapértelmezett felhasználói engedélyeinek összehasonlítása](../fundamentals/users-default-permissions.md)
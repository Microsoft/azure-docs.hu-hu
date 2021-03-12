---
title: Azonosító objektumok azonosítóinak keresése a hitelesítéshez – Azure API for FHIR
description: Ez a cikk azt ismerteti, hogyan lehet megkeresni a FHIR Azure API-hoz való hitelesítésének konfigurálásához szükséges azonosító objektumok azonosítóit
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 706d7e081743f2bab1f593e00dc792f218a000ea
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018563"
---
# <a name="find-identity-object-ids-for-authentication-configuration"></a>Azonosító objektumok azonosítóinak keresése a hitelesítési konfigurációhoz

Ebből a cikkből megtudhatja, hogyan találhatja meg az Azure API FHIR való konfigurálásához szükséges Identity Object-azonosítókat, hogy [külső vagy másodlagos Active Directory bérlőt használjon](configure-local-rbac.md) adatsíkon.

## <a name="find-user-object-id"></a>Felhasználói objektum AZONOSÍTÓjának keresése

Ha a felhasználó felhasználónevet tartalmaz, a `myuser@contoso.com` következő PowerShell-paranccsal keresheti meg a felhasználókat `ObjectId` :

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@contoso.com'").ObjectId
```

vagy használhatja az Azure CLI-t is:

```azurecli-interactive
az ad user show --id myuser@contoso.com --query objectId --out tsv
```

## <a name="find-service-principal-object-id"></a>Szolgáltatásnév objektum AZONOSÍTÓjának keresése

Tegyük fel, hogy regisztrált egy [szolgáltatás-ügyfélalkalmazás](register-service-azure-ad-client-app.md) , és szeretné engedélyezni a szolgáltatás ügyfelének számára a FHIR készült Azure API elérését, a következő PowerShell-paranccsal megkeresheti az ügyfél-szolgáltatásnév objektum-azonosítóját:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

hol `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` található a szolgáltatás ügyfélalkalmazás-azonosítója. Azt is megteheti, hogy a `DisplayName` szolgáltatás ügyfelét használja:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

Ha az Azure CLI-t használja, használhatja a következőket:

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX --query objectId --out tsv
```

## <a name="find-a-security-group-object-id"></a>Biztonsági csoport objektum-AZONOSÍTÓjának keresése

Ha meg szeretné találni egy biztonsági csoport objektumazonosítóát, használja a következő PowerShell-parancsot:

```azurepowershell-interactive
$(Get-AzureADGroup -Filter "DisplayName eq 'mygroup'").ObjectId
```
Ahol a az `mygroup` Önt érdeklő csoport neve.

Ha az Azure CLI-t használja, használhatja a következőket:

```azurecli-interactive
az ad group show --group "mygroup" --query objectId --out tsv
```

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan találhatja meg az Azure API FHIR való konfigurálásához szükséges Identity Object-azonosítókat külső vagy másodlagos Azure Active Directory bérlő használatához. Ezután olvassa el, hogyan használhatók az objektumazonosítók a helyi RBAC-beállítások konfigurálásához:
 
>[!div class="nextstepaction"]
>[Helyi RBAC-beállítások konfigurálása](configure-local-rbac.md)

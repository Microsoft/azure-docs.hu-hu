---
title: Erőforrás-alkalmazás regisztrálása az Azure AD-ben – Azure API a FHIR-hez
description: Egy erőforrás-(vagy API-) alkalmazás regisztrálása Azure Active Directoryban, így az ügyfélalkalmazások hozzáférést igényelhetnek az erőforráshoz hitelesítéskor.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 8d70a7b44893ba9c9a0cc2d1d01c65e8e1584e0f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018996"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Erőforrás-alkalmazás regisztrálása Azure Active Directory

Ebből a cikkből megtudhatja, hogyan regisztrálhat egy erőforrás-(vagy API-) alkalmazást a Azure Active Directory. Az erőforrás-alkalmazás a FHIR-kiszolgáló API-nak Azure Active Directory ábrázolása, és az ügyfélalkalmazások hitelesítéskor igényelhetnek hozzáférést az erőforráshoz. Az erőforrás-alkalmazást a OAuth szóhasználatában *célközönségének* is nevezzük.

## <a name="azure-api-for-fhir"></a>Azure API for FHIR

Ha a FHIR készült Azure API-t használja, a szolgáltatás telepítésekor automatikusan létrejön egy erőforrás-alkalmazás. Ha a FHIR tartozó Azure API-t ugyanabban a Azure Active Directory bérlőben használja, mint az alkalmazás üzembe helyezése során, kihagyhatja ezt a útmutatóból, és helyette üzembe helyezheti az Azure API-t a FHIR-hoz az első lépésekhez.

Ha más Azure Active Directory bérlőt használ (az előfizetéséhez nem társítva), importálhatja a FHIR-erőforrás-alkalmazáshoz tartozó Azure API-t a bérlőbe a PowerShell segítségével:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

vagy használhatja az Azure CLI-t is:

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>FHIR-kiszolgáló az Azure-hoz

Ha az Azure-hoz készült nyílt forráskódú FHIR-kiszolgálót használja, kövesse a [GitHub](https://github.com/microsoft/fhir-server/blob/master/docs/Register-Resource-Application.md) -tárházban található lépéseket egy erőforrás-alkalmazás regisztrálásához. 

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan regisztrálhat egy erőforrás-alkalmazást Azure Active Directoryban. Következő lépésként regisztrálja a bizalmas ügyfélalkalmazás alkalmazását.
 
>[!div class="nextstepaction"]
>[Bizalmas ügyfélalkalmazás regisztrálása](register-confidential-azure-ad-client-app.md)
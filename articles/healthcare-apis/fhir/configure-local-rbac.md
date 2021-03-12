---
title: Helyi szerepköralapú hozzáférés-vezérlés (helyi RBAC) konfigurálása a FHIR készült Azure API-hoz
description: Ez a cikk azt ismerteti, hogyan konfigurálható az Azure API a FHIR számára külső Azure AD-bérlő használatához adatsíkon
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: matjazl
ms.openlocfilehash: 096e4e3ecbcedaec674e074a2baccbb336e03c94
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019303"
---
# <a name="configure-local-rbac-for-fhir"></a>A FHIR helyi RBAC konfigurálása 

Ez a cikk azt ismerteti, hogyan konfigurálhatja a FHIR készült Azure API-t külső, másodlagos Azure Active Directory bérlő használatára az adatsíkok hozzáférésének kezeléséhez. Ezt a módot csak akkor használja, ha az előfizetéshez társított Azure Active Directory bérlőt nem lehet használni.

> [!NOTE]
> Ha a FHIR szolgáltatás adatsíkja úgy van konfigurálva, hogy az Ön előfizetéséhez társított elsődleges Azure Active Directory bérlőt használja, az [Azure RBAC használatával rendeljen hozzá adatsík-szerepköröket](configure-azure-rbac.md).

## <a name="add-service-principal"></a>Szolgáltatásnév hozzáadása

A helyi RBAC lehetővé teszi, hogy külső Azure Active Directory bérlőt használjon a FHIR-kiszolgálóval. Annak érdekében, hogy a helyi RBAC-rendszer engedélyezze a csoporttagság ellenőrzését ebben a bérlőben, a FHIR készült Azure API-nak rendelkeznie kell egy egyszerű szolgáltatással a bérlőben. Ez az egyszerű szolgáltatás automatikusan létrejön a FHIR Azure API-t üzembe helyező előfizetésekhez kötött bérlők számára, de abban az esetben, ha a bérlő nem rendelkezik előfizetéssel, akkor a bérlői rendszergazdának a következő parancsok egyikével kell létrehoznia ezt a szolgáltatásnevet:

A `Az` PowerShell-modul használata:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

vagy használhatja a PowerShell- `AzureAd` modult:

```azurepowershell-interactive
New-AzureADServicePrincipal -AppId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

vagy használhatja az Azure CLI-t is:

```azurecli-interactive
az ad sp create --id 3274406e-4e0a-4852-ba4f-d7226630abb7
```

## <a name="configure-local-rbac"></a>Helyi RBAC konfigurálása

A FHIR Azure API-ját úgy is beállíthatja, hogy külső vagy másodlagos Azure Active Directory bérlőt használjon a **hitelesítési** panelen:

![Helyi RBAC-hozzárendelések](media/rbac/local-rbac-guids.png).

A hatóság mezőbe írjon be egy érvényes Azure Active Directory bérlőt. Miután a bérlő érvényesítve lett, aktiválva kell lennie az **engedélyezett objektumazonosítók** mezőnek, és megadhatja az azonosító objektumok azonosítóinak listáját. Ezek az azonosítók a következő azonosítók lehetnek:

* Egy Azure Active Directory felhasználó.
* Egy Azure Active Directory egyszerű szolgáltatás.
* Egy Azure Active Directory biztonsági csoport.

További részletekért olvassa el a következő cikket: [személyazonossági objektumok azonosítóinak megkeresése](find-identity-object-ids.md) .

A szükséges objektumazonosítók megadása után kattintson a **Mentés** gombra, és várjon, amíg a rendszer menti a módosításokat, mielőtt a hozzárendelt felhasználókkal, szolgáltatásokkal vagy csoportokkal megpróbál hozzáférni az adatsíkon.

## <a name="caching-behavior"></a>Gyorsítótárazási viselkedés

A FHIR készült Azure API akár 5 percig is gyorsítótárazza a döntéseket. Ha hozzáférést biztosít a FHIR-kiszolgálóhoz az engedélyezett objektumazonosítók listájához való hozzáadásával, vagy eltávolíthatja őket a listáról, akkor várhatóan akár öt percet is igénybe vehet, amíg a módosítások érvénybe lépnek.

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan rendelhet hozzá FHIR adatsíkok-hozzáférést külső (másodlagos) Azure Active Directory bérlő használatával. További információ a FHIR készült Azure API további beállításairól:
 
>[!div class="nextstepaction"]
>[További beállítások Azure API a FHIR-hez](azure-api-for-fhir-additional-settings.md)

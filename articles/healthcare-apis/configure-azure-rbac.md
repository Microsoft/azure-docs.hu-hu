---
title: Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) konfigurálása a FHIR készült Azure API-hoz
description: Ez a cikk azt ismerteti, hogyan konfigurálható az Azure RBAC az Azure API-hoz a FHIR-adatsíkon
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: matjazl
ms.reviewer: dseven
ms.openlocfilehash: 1df2d112814c93261c364883f4b834e0bdb657bb
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91839911"
---
# <a name="configure-azure-rbac-for-fhir"></a>Az Azure-RBAC konfigurálása a FHIR 

Ebből a cikkből megtudhatja, hogyan használhatja az Azure [szerepköralapú hozzáférés-vezérlést (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/) az Azure API FHIR-adatsíkokhoz való hozzárendeléséhez. Az Azure RBAC az adatsík-hozzáférés hozzárendelésének előnyben részesített módszerei az Azure-előfizetéshez társított Azure Active Directory-bérlőben. Ha külső Azure Active Directory bérlőt használ, tekintse meg a [helyi RBAC-hozzárendelési referenciát](configure-local-rbac.md).

## <a name="confirm-azure-rbac-mode"></a>Azure RBAC mód megerősítése

Az Azure RBAC használatához a FHIR készült Azure API-t úgy kell konfigurálni, hogy az Azure-előfizetés bérlőjét használja az adatsíkon, és ne legyenek hozzárendelve azonosító objektumok azonosítói. A beállítások ellenőrzéséhez tekintse meg a FHIR készült Azure API **hitelesítés** paneljét:

:::image type="content" source="media/rbac/confirm-azure-rbac-mode.png" alt-text="Azure RBAC mód megerősítése":::

A **szolgáltatót** az előfizetéshez társított Azure Active Directory-bérlőre kell beállítani, és nem lehetnek GUID-azonosítók az **engedélyezett objektumazonosítók**feliratú mezőben. Azt is láthatja, hogy a mező le van tiltva, és a felirat azt jelzi, hogy az Azure-RBAC kell használni az adatsíkok szerepköreinek hozzárendeléséhez.

## <a name="assign-roles"></a>Szerepkörök hozzárendelése

Ahhoz, hogy a felhasználók, az egyszerű szolgáltatások vagy a csoportok hozzáférhessenek a FHIR-adatsíkon, kattintson a **hozzáférés-vezérlés (iam)**, majd a **szerepkör-hozzárendelések** elemre, és kattintson a **+ Hozzáadás**gombra:

:::image type="content" source="media/rbac/add-azure-rbac-role-assignment.png" alt-text="Azure RBAC mód megerősítése":::

A **szerepkör** kiválasztása területen keresse meg a FHIR adatsíkjahoz tartozó beépített szerepkörök egyikét:

:::image type="content" source="media/rbac/built-in-fhir-data-roles.png" alt-text="Azure RBAC mód megerősítése":::

A következők közül választhat:

* FHIR-Adatolvasó: képes olvasni (és keresni) az FHIR.
* FHIR-adatíró: a FHIR-információk olvasására, írására és törlésére is képes.
* FHIR-adatátadó: olvasási és exportálási ( `$export` operátori) adatküldés.
* FHIR adatok közreműködői: az összes adatsík műveletet végrehajthatja.

Ha ezek a szerepkörök nem elégségesek a szükséges igényekhez, [létrehozhat egyéni szerepköröket](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell)is.

A **kiválasztás** mezőben keresse meg azt a felhasználót, szolgáltatásnevet vagy csoportot, amelyhez hozzá szeretné rendelni a szerepkört.

## <a name="caching-behavior"></a>Gyorsítótárazási viselkedés

A FHIR készült Azure API akár 5 percig is gyorsítótárazza a döntéseket. Ha hozzáférést biztosít a FHIR-kiszolgálóhoz az engedélyezett objektumazonosítók listájához való hozzáadásával, vagy eltávolíthatja őket a listáról, akkor várhatóan akár öt percet is igénybe vehet, amíg a módosítások érvénybe lépnek.

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan rendelhet Azure-szerepköröket a FHIR adatsíkon. További információ a FHIR készült Azure API további beállításairól:
 
>[!div class="nextstepaction"]
>[További beállítások a FHIR készült Azure API-hoz](azure-api-for-fhir-additional-settings.md)

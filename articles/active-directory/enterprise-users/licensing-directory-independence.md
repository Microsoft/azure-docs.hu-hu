---
title: Több bérlői interakció jellemzői – Azure AD | Microsoft Docs
description: A Azure Active Directory-szervezetek adatfüggetlenségének megismerése
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: overview
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ce3e4c6a7708fba15560564577c9b01722c8aec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96548052"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Ismerje meg, hogyan hatnak több Azure Active Directory szervezet

Azure Active Directory (Azure AD) esetében minden Azure AD-szervezet teljesen független: olyan társ, amely logikailag független a többi felügyelt Azure AD-szervezettől. A szervezetek közötti függetlenség magában foglalja az erőforrások függetlenségét, a felügyelet függetlenségét és a szinkronizálás függetlenségét. A szervezetek között nincs szülő-gyermek kapcsolat.

## <a name="resource-independence"></a>Erőforrás-függetlenség

* Ha egy szervezeten belül hoz létre vagy töröl egy Azure AD-erőforrást, az nem érinti a többi szervezet erőforrásait, a külső felhasználók részleges kivételével.
* Ha egy szervezetnél regisztrálja az egyik tartománynevet, azt más szervezetek nem használhatják.

## <a name="administrative-independence"></a>Rendszergazdai függetlenség

Ha a "contoso" szervezet nem rendszergazda felhasználója létrehoz egy test Organization "test" tesztet, akkor:

* Alapértelmezés szerint a szervezetet létrehozó felhasználó külső felhasználóként lesz hozzáadva az új szervezethez, és hozzárendelte a globális rendszergazdai szerepkört az adott szervezethez.
* A "contoso" szervezet rendszergazdái nem rendelkeznek közvetlen rendszergazdai jogosultsággal a (z) "test" szervezethez, kivéve, ha a "test" rendszergazdája kifejezetten megadja nekik ezeket a jogosultságokat. A "contoso" rendszergazdái azonban szabályozhatják a "teszt" szervezet hozzáférését, ha a "test" nevű felhasználói fiókot vezérlik.
* Ha egy szervezetben egy felhasználóhoz ad hozzá vagy távolít el egy Azure AD-szerepkört, a változás nem érinti azokat a szerepköröket, amelyeket a felhasználó más Azure AD-szervezethez rendelt.

## <a name="synchronization-independence"></a>Szinkronizálási függetlenség

Az egyes Azure AD-szervezeteket egymástól függetlenül konfigurálhatja:

* Az Azure AD Connect eszköz egyetlen AD-erdővel történő szinkronizáláshoz.
* A Forefront Identity Manager Azure Active Directory-összekötője, amely egy vagy több helyszíni erdővel és/vagy nem Azure AD-adatforrásokkal szinkronizálja az adatokat.

## <a name="add-an-azure-ad-organization"></a>Azure AD-szervezet hozzáadása

Ha Azure AD-szervezetet szeretne felvenni a Azure Portalba, jelentkezzen be [a Azure Portalba](https://portal.azure.com) egy olyan fiókkal, amely az Azure ad globális rendszergazdája, és válassza az **új** lehetőséget.

> [!NOTE]
> A többi Azure-erőforrástól eltérően az Azure AD-szervezetek nem az Azure-előfizetések alárendelt erőforrásai. Ha az Azure-előfizetését megszakították vagy lejártak, az Azure AD-szervezet adatait Azure PowerShell, a Microsoft Graph API-val vagy a Microsoft 365 felügyeleti központtal is elérheti. [Egy másik előfizetést is társíthat a szervezethez](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Következő lépések

Az Azure AD licencelési szempontjaival és az ajánlott eljárásokkal kapcsolatban lásd: [Mi a Azure Active Directory Licensing?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).

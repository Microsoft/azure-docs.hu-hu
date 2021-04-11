---
title: A Privileged Identity Management-Azure Active Directory használatára vonatkozó licencfeltételek | Microsoft Docs
description: A Azure AD Privileged Identity Management (PIM) használatára vonatkozó licencelési követelményeket ismerteti.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 04/05/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d0428f73c4df3d97002def03823ff8cced1d647
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552631"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>A Privileged Identity Management használatára vonatkozó licencfeltételek

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatához a címtárnak érvényes licenccel kell rendelkeznie. A licenceket továbbá a rendszergazdák és a kapcsolódó felhasználók számára is hozzá kell rendelni. Ez a cikk a Privileged Identity Management használatára vonatkozó licencfeltételeket ismerteti.

## <a name="valid-licenses"></a>Érvényes licencek

A [!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)] PIM-t és annak összes beállítását kell használnia. Jelenleg az Azure AD-hez és az Azure-erőforrás szerepköreihez (előzetes verzió) való hozzáférésre vonatkozó hozzáférési felülvizsgálat hatókörét a bérlőben aktív prémium szintű Azure Active Directory P2 kiadással lehet elérni. Az egyszerű szolgáltatások licencelési modellje a szolgáltatás általános elérhetősége miatt véglegesítve lesz, és további licencekre lehet szükség.

## <a name="licenses-you-must-have"></a>Szükséges licencek

Győződjön meg arról, hogy a címtár legalább annyi prémium szintű Azure AD P2-licenccel rendelkezik, mint a következő feladatokat végző alkalmazottakkal:

- A PIM használatával felügyelt Azure AD-vagy Azure-szerepköröknek megfelelő jogosultsággal rendelkező felhasználók
- Azok a felhasználók, akik jogosult tagokként vagy Kiemelt jogosultsági szintű hozzáférési csoportok tulajdonosaként vannak hozzárendelve
- A PIM-beli aktiválási kérések jóváhagyása vagy elutasítása a felhasználók számára
- Hozzáférési felülvizsgálathoz rendelt felhasználók
- Hozzáférési felülvizsgálatokat végrehajtó felhasználók

Prémium szintű Azure AD P2-licencek **nem** szükségesek a következő feladatokhoz:

- Nincs szükség licencre a PIM-t beállító felhasználók számára, a házirendek konfigurálásához, a riasztások fogadásához és a hozzáférési felülvizsgálatok beállításához.

További információ a licencekről: [licencek kiosztása vagy eltávolítása a Azure Active Directory portál használatával](../fundamentals/license-users-groups.md).

## <a name="example-license-scenarios"></a>Példa licencelési forgatókönyvekre

Íme néhány példa a licencekre, amelyek segítségével meghatározhatja a szükséges licencek számát.

| Eset | Számítás | Licencek száma |
| --- | --- | --- |
| A Woodgrove Bank 10 rendszergazdával rendelkezik különböző részlegek és 2 globális rendszergazda számára, amelyek a PIM-t konfigurálja és kezelik. Így öt rendszergazda jogosult. | Öt licenc a jogosult rendszergazdák számára | 5 |
| A Graphic Design Institute 25 rendszergazdával rendelkezik, amelyből 14 felügyeli a PIM-t. A szerepkör-aktiválás jóváhagyást igényel, és a szervezeten belül három különböző felhasználó is jóváhagyhatja az aktiválásokat. | 14 licenc a jogosult szerepkörökhöz + három jóváhagyó | 17 |
| A contoso 50-rendszergazdákat tartalmaz, amelyek 42 a PIM használatával kezelhetők. A szerepkör-aktiválás jóváhagyást igényel, és a szervezet öt különböző felhasználót tud jóváhagyni. A contoso a rendszergazdai szerepkörökhöz és a véleményezők számára kijelölt felhasználók havi felülvizsgálatát is elvégzi, akik a felhasználók felettesei, akik közül hat nem a PIM által kezelt rendszergazdai szerepkörben van. | 42 licencek a jogosult szerepkörökhöz + öt jóváhagyó + hat felülvizsgáló | 53 |

## <a name="when-a-license-expires"></a>Licenc lejáratakor

Ha egy prémium szintű Azure AD P2, EMS E5 vagy próbaverziós licenc lejár, Privileged Identity Management funkciók többé nem lesznek elérhetők a címtárban:

- Az Azure AD-szerepkörökhöz tartozó állandó szerepkör-hozzárendelések nem lesznek hatással.
- A Azure Portal Privileged Identity Management szolgáltatása, valamint az Graph API-parancsmagok és a Privileged Identity Management PowerShell-kapcsolatai többé nem lesznek elérhetők a felhasználók számára a Kiemelt szerepkörök aktiválásához, a privilegizált hozzáférés felügyeletéhez, illetve a Kiemelt szerepkörök hozzáférési felülvizsgálatának elvégzéséhez.
- Az Azure AD-szerepkörök jogosult szerepkör-hozzárendelései el lesznek távolítva, mivel a felhasználók többé nem tudják aktiválni a Kiemelt szerepköröket.
- Az Azure AD-szerepkörök folyamatos hozzáférési felülvizsgálatai megszűnnek, és Privileged Identity Management a konfigurációs beállítások törlődnek.
- Privileged Identity Management többé nem fog e-maileket küldeni a szerepkör-hozzárendelés változásairól.

## <a name="next-steps"></a>Következő lépések

- [A Privileged Identity Management üzembe helyezése](pim-deployment-plan.md)
- [A Privileged Identity Management használatának első lépései](pim-getting-started.md)
- [Nem felügyelhető szerepkörök Privileged Identity Management](pim-roles.md)
- [Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása a PIM-ben](pim-resource-roles-start-access-review.md)
- [Azure AD-szerepkörök hozzáférési felülvizsgálatának létrehozása a PIM-ben](pim-how-to-start-security-review.md)

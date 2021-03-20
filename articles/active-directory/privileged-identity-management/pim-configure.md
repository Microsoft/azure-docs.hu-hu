---
title: Mi az Privileged Identity Management? – Azure AD | Microsoft Docs
description: Áttekintést nyújt Azure AD Privileged Identity Management (PIM) szolgáltatásról.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 09/29/2020
ms.author: curtand
ms.custom: pim ; azuread-video-2020
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3c7003ce3e8c65f6fdb7f3d847947114aee251f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92370185"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Mi az az Azure AD Privileged Identity Management?

 A Privileged Identity Management (PIM) Azure Active Directory (Azure AD) szolgáltatása, amely lehetővé teszi a szervezet fontos erőforrásaihoz való hozzáférés kezelését, vezérlését és figyelését. Ezek az erőforrások az Azure AD-ben, az Azure-ban és más Microsoft Online Services-szolgáltatásokban (például Microsoft 365 vagy Microsoft Intune) lévő erőforrásokat tartalmaznak. A következő videó ismerteti a PIM fontos fogalmait és funkcióit.
<br><br>

> [!VIDEO https://www.youtube.com/embed/f-0K7mRUPpQ]

## <a name="reasons-to-use"></a>Használat okai

A szervezetek szeretnék csökkenteni azon személyek számát, akiknek hozzáférésük van az információkhoz vagy az erőforrásokhoz, mivel ez csökkenti annak az esélyét, hogy egy rosszindulatú szereplő hozzáfér az adott hozzáféréshez, vagy ha egy jogosult felhasználó véletlenül kihathat egy bizalmas erőforrásra. A felhasználóknak azonban továbbra is jogosultsági szintű műveleteket kell végrehajtaniuk az Azure AD-ben, az Azure-ban, a Microsoft 365ban vagy az SaaS-alkalmazásokban. A szervezetek az Azure-erőforrásokhoz és az Azure AD-hez is igénybe vehetik az igény szerinti jogosultságokat. Szükség van arra, hogy a felhasználók hogyan használják a rendszergazdai jogosultságokat.

## <a name="what-does-it-do"></a>Mit csinál?

A Privileged Identity Management időalapú és jóváhagyási alapú szerepkör-aktiválást biztosít az Ön számára fontos erőforrásokra vonatkozó túlzott, szükségtelen vagy nem használt hozzáférési engedélyek kockázatának csökkentése érdekében. A Privileged Identity Management főbb funkciói:

- Az Azure AD-hez és **Az Azure-** erőforrásokhoz való, igény szerinti, Kiemelt jogosultságokkal rendelkező hozzáférés biztosítása
- Az erőforrásokhoz való **időhöz kötött** hozzáférés kiosztása a kezdő és a záró dátumok használatával
- **Jóváhagyás** megkövetelése a Kiemelt szerepkörök aktiválásához
- A **többtényezős hitelesítés** betartatása bármely szerepkör aktiválásához
- **Indoklás** használata a felhasználók aktiválásának megértéséhez
- **Értesítések** beszerzése a Kiemelt szerepkörök aktiválása esetén
- **Hozzáférési felülvizsgálatok** végrehajtása annak biztosításához, hogy a felhasználóknak továbbra is szükségük van
- **Naplózási előzmények** letöltése belső vagy külső naplózáshoz

## <a name="what-can-i-do-with-it"></a>Mire használhatom?

A Privileged Identity Management beállítása után a bal oldali navigációs menüben megjelennek a **feladatok**, a **kezelés** és a **tevékenységek** beállításai. Rendszergazdaként olyan lehetőségek közül választhat, mint például az **Azure ad-szerepkörök** kezelése, az **Azure-erőforrások** szerepköreinek kezelése vagy a privilegizált hozzáférési csoportok. Ha kiválasztja, hogy mit szeretne kezelni, megjelenik az adott lehetőséghez tartozó megfelelő beállítások.

![A Azure Portal Privileged Identity Management képernyőképe](./media/pim-configure/pim-quickstart.png)

## <a name="who-can-do-what"></a>Ki teheti?

Privileged Identity Management Azure AD-szerepkörök esetében csak a Kiemelt szerepkörű rendszergazda vagy globális rendszergazdai szerepkörrel rendelkező felhasználók kezelhetik a többi rendszergazda hozzárendeléseit. A [Privileged Identity Management kezeléséhez hozzáférést biztosíthat más rendszergazdáknak](pim-how-to-give-access-to-pim.md). A globális rendszergazdák, a biztonsági rendszergazdák, a globális olvasók és a biztonsági olvasók is megtekinthetik Privileged Identity Management Azure AD szerepköreinek hozzárendeléseit.

Privileged Identity Management Azure-beli erőforrás-szerepköreihez csak az előfizetés rendszergazdája, az erőforrás tulajdonosa, vagy egy erőforrás felhasználói hozzáférés-rendszergazdája kezelheti a többi rendszergazda hozzárendeléseit. Azok a felhasználók, akik Kiemelt szerepkörű rendszergazdák, biztonsági rendszergazdák vagy biztonsági olvasók, nem rendelkeznek hozzáféréssel az Azure-beli erőforrás-szerepkörök hozzárendeléseinek megtekintéséhez Privileged Identity Managementban.

## <a name="scenarios"></a>Forgatókönyvek

A Privileged Identity Management a következő forgatókönyveket támogatja:

### <a name="privileged-role-administrator-permissions"></a>Kiemelt szerepkörű rendszergazdai jogosultságok

- Jóváhagyás engedélyezése speciális szerepkörökhöz
- Jóváhagyó felhasználók vagy csoportok meghatározása a kérelmek jóváhagyásához
- Speciális szerepkörökhöz tartozó kérelmek és jóváhagyások előzményeinek megtekintése

### <a name="approver-permissions"></a>Jóváhagyói engedélyek

- Függőben lévő jóváhagyások (kérelmek) megtekintése
- Szerepkör-jogosultságszint-emelési kérések jóváhagyása vagy elutasítása (egyetlen és tömeges)
- Jóváhagyás vagy elutasítás indoklásának megadása

### <a name="eligible-role-user-permissions"></a>Jogosult szerepkör felhasználói engedélyei

- Jóváhagyást igénylő szerepkör aktiválásának kérelme
- Az aktiválási kérelem állapotának megtekintése
- Az aktiválás jóváhagyása után feladatok végrehajtása az Azure AD-ben

## <a name="terminology"></a>Terminológia

A Privileged Identity Management és a dokumentációjának jobb megismeréséhez tekintse át a következő feltételeket.

| Kifejezés vagy fogalom | Szerepkör-hozzárendelési kategória | Description |
| --- | --- | --- |
| támogatható | Típus | Szerepkör-hozzárendelés, amely megköveteli, hogy a felhasználó egy vagy több műveletet hajtson végre a szerepkör használatához. Ha egy felhasználó jogosult a szerepkörre, az azt jelenti, hogy aktiválhatja a szerepkört, amikor kiemelt feladatokat kell elvégeznie. A hozzáférés nem különbözik a jogosult szerepkör-hozzárendeléssel rendelkező valakinek. Az egyetlen különbség, hogy néhány embernek nincs szüksége arra, hogy minden alkalommal hozzáférjen. |
| active | Típus | Szerepkör-hozzárendelés, amely nem követeli meg, hogy a felhasználó bármilyen műveletet hajtson végre a szerepkör használatához. Az aktívként hozzárendelt felhasználók rendelkeznek a szerepkörhöz hozzárendelt jogosultságokkal. |
| aktiválás |  | Egy vagy több művelet végrehajtásának folyamata olyan szerepkör használatára, amelyhez a felhasználó jogosult. A műveletek tartalmazhatják a többtényezős hitelesítés (MFA) ellenőrzését, üzleti indoklást biztosítanak, vagy a kijelölt jóváhagyók jóváhagyását kérik. |
| rendelt | Állapot | Aktív szerepkör-hozzárendeléssel rendelkező felhasználó. |
| aktivált | Állapot | A jogosult szerepkör-hozzárendeléssel rendelkező felhasználók elvégezték a szerepkör aktiválásához szükséges műveleteket, és most aktívak.  Az aktiválás után a felhasználó használhatja a szerepkört egy előre konfigurált időszakra, mielőtt újra aktiválni kellene. |
| állandó jogosultság | Időtartam | Szerepkör-hozzárendelés, amelyben a felhasználó mindig jogosult a szerepkör aktiválásához. |
| állandó aktív | Időtartam | Szerepkör-hozzárendelés, amelyben a felhasználók minden művelet végrehajtása nélkül bármikor használhatják a szerepkört. |
| jogosultság lejárata | Időtartam | Szerepkör-hozzárendelés, amelyben a felhasználó jogosult a szerepkör aktiválására a megadott kezdési és befejezési dátumon belül. |
| aktív lejárat | Időtartam | Szerepkör-hozzárendelés, amelyben a felhasználó a megadott kezdési és befejezési dátumon belüli műveletek végrehajtása nélkül használhatja a szerepkört. |
| igény szerinti (JIT) hozzáférés |  | Olyan modell, amelyben a felhasználók ideiglenes engedélyeket kapnak a Kiemelt feladatok végrehajtásához, ami megakadályozza, hogy a rosszindulatú vagy jogosulatlan felhasználók hozzáférjenek az engedélyek lejárta után. A hozzáférés csak akkor adható meg, ha a felhasználóknak szüksége van rá. |
| a minimális jogosultsági szintű hozzáférés elve |  | Ajánlott biztonsági eljárás, amelyben minden felhasználó csak a végrehajtásra jogosult feladatok végrehajtásához szükséges minimális jogosultságokkal rendelkezik. Ez a gyakorlat lekicsinyíti a globális rendszergazdák számát, és bizonyos helyzetekben adott rendszergazdai szerepköröket használ. |

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

További információ a felhasználók licencéről: a [Privileged Identity Management használatára vonatkozó licencfeltételek](subscription-requirements.md).

## <a name="next-steps"></a>Következő lépések

- [A Privileged Identity Management használatára vonatkozó licencfeltételek](subscription-requirements.md)
- [Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez](../roles/security-planning.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [A Privileged Identity Management üzembe helyezése](pim-deployment-plan.md)

---
title: Felügyeleti egységek a Azure Active Directoryban | Microsoft Docs
description: A Azure Active Directoryban lévő engedélyek részletesebb delegálásához használjon felügyeleti egységeket.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49f2c290c69fcadd594d6cbd5879e7d9f5304a42
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558015"
---
# <a name="administrative-units-in-azure-active-directory"></a>Felügyeleti egységek a Azure Active Directory

Ez a cikk a Azure Active Directory (Azure AD) felügyeleti egységeit ismerteti. A felügyeleti egység olyan Azure AD-erőforrás, amely más Azure AD-erőforrások tárolója lehet. Egy felügyeleti egység csak felhasználókat és csoportokat tartalmazhat.

A felügyeleti egységek az Ön által definiált szervezet bármely részén korlátozzák a szerepkör engedélyeit. Előfordulhat például, hogy a felügyeleti egységekkel delegálja az [ügyfélszolgálati rendszergazda](permissions-reference.md#helpdesk-administrator) szerepkört a regionális támogatási szakértőknek, így csak az általuk támogatott régióban kezelhetik a felhasználókat.

## <a name="deployment-scenario"></a>Központi telepítési forgatókönyv

Hasznos lehet az adminisztrációs hatókört olyan szervezeti egységek használatával korlátozni, amelyek bármilyen típusú független részlegből állnak. Vegyünk például egy olyan nagy egyetemet, amely számos autonóm iskola (üzleti iskola, mérnöki iskola stb.) számára készült. Minden iskolában van egy olyan rendszergazda, aki felügyeli a hozzáférést, a felhasználókat, és szabályzatokat állít be az iskolához.

A központi rendszergazda:

- Hozzon létre egy rendszergazdai jogosultságokkal rendelkező szerepkört csak az Azure AD-felhasználók számára a Business School felügyeleti egységben.
- Hozzon létre egy felügyeleti egységet a vállalati iskola számára.
- A felügyeleti egységet csak az üzleti tanulók és a munkatársak tölthetik fel.
- Adja hozzá az üzleti iskola informatikai csapatát a szerepkörhöz, valamint annak hatókörét.

## <a name="license-requirements"></a>Licenckövetelmények

A felügyeleti egységek használatához prémium szintű Azure Active Directory licencre van szükség minden felügyeleti egység rendszergazdájához, és ingyenes Azure Active Directory licenceket a felügyeleti egység tagjai számára. További információ: [Bevezetés a prémium szintű Azure ad](../fundamentals/active-directory-get-started-premium.md)használatába.

## <a name="manage-administrative-units"></a>Felügyeleti egységek kezelése

A felügyeleti egységeket a Azure Portal, a PowerShell-parancsmagok és a parancsfájlok használatával, vagy Microsoft Graph kezelheti. További információkért lásd:

- [Szerepkörök létrehozása, eltávolítása, feltöltése és hozzáadása a felügyeleti egységekhez](admin-units-manage.md): teljes körű útmutató eljárásokat tartalmaz.
- [Munka a felügyeleti egységekkel](/powershell/azure/active-directory/working-with-administrative-units): azt ismerteti, hogyan használhatók a felügyeleti egységek a PowerShell használatával.
- [Felügyeleti egység Graph-támogatás](/graph/api/resources/administrativeunit): részletes dokumentációt biztosít a Microsoft Graph felügyeleti egységekhez.

### <a name="plan-your-administrative-units"></a>A felügyeleti egységek megtervezése

Az Azure AD-erőforrások logikus csoportosításához rendszergazdai egységeket használhat. Olyan szervezet, amelynek az IT-részlege globálisan elszórtan található, létrehozhat olyan felügyeleti egységeket, amelyek releváns földrajzi határokat határoznak meg. Egy másik forgatókönyvben, ahol egy globális szervezet olyan alszervezetekkel rendelkezik, amelyek a műveleteiben félig autonómok, a felügyeleti egységek az alszervezeteket jelölhetik.

A felügyeleti egységek létrehozási feltételeit a szervezet egyedi követelményei vezérlik. A felügyeleti egységek közösen határozzák meg a szerkezetet Microsoft 365 szolgáltatások között. Javasoljuk, hogy a felügyeleti egységeket a használatuk Microsoft 365-szolgáltatásokban való használatának szem előtt tartásával készítse elő. Ha általános erőforrásokat szeretne hozzárendelni egy felügyeleti egység alatt Microsoft 365, akkor a felügyeleti egységek maximális értékét is elérheti.

A szervezeten belüli felügyeleti egységek létrehozását várhatóan a következő szakaszokon keresztül érheti el:

1. **Kezdeti elfogadás**: a szervezet kezdeti feltételek alapján kezdi meg a felügyeleti egységek létrehozását, és a feltételek finomításakor a felügyeleti egységek száma növekedni fog.
1. **Metszés**: a feltételek meghatározása után a rendszer törli a már nem szükséges felügyeleti egységeket.
1. **Stabilizáció**: a szervezeti struktúra definiálva van, és a felügyeleti egységek száma nem változik jelentősen a rövid távon.

## <a name="currently-supported-scenarios"></a>Jelenleg támogatott forgatókönyvek

Globális rendszergazdaként vagy Kiemelt szerepkörű rendszergazdaként az Azure AD-portálon a következőket teheti:

- Felügyeleti egységek létrehozása
- Felhasználók és csoportok hozzáadása a felügyeleti egységek tagjaihoz
- Rendelje hozzá a munkatársakat a felügyeleti egységhez – hatókörön belüli rendszergazdai szerepkörökhöz.

Felügyeleti egység – a hatókörrel rendelkező rendszergazdák a felügyeleti egységben lévő felhasználók alapszintű felügyeletére használhatják a Microsoft 365 felügyeleti központot. A felügyeleti egység hatókörével rendelkező csoport rendszergazdája a PowerShell, a Microsoft Graph és a Microsoft 365 felügyeleti központ használatával kezelheti a csoportokat.

>[!Note]
>Csak az ebben a részben ismertetett funkciók érhetők el a Microsoft 365 felügyeleti központban. Nem érhetők el szervezeti szintű szolgáltatások a felügyeleti egység hatókörével rendelkező Azure AD-szerepkörhöz.

A következő szakaszok ismertetik a felügyeleti egységek jelenlegi támogatását.

### <a name="administrative-unit-management"></a>Felügyeleti egység kezelése

| Engedélyek |   Gráf/PowerShell   | Azure AD-portál | Microsoft 365 felügyeleti központ |
| --- | --- | --- | --- |
| Felügyeleti egységek létrehozása és törlése   |    Támogatott    |   Támogatott   |    Nem támogatott |
| Felügyeleti egység tagjainak egyenkénti hozzáadása és eltávolítása    |   Támogatott    |   Támogatott   |    Nem támogatott |
| Felügyeleti egység tagjainak tömeges hozzáadása és eltávolítása CSV-fájlok használatával   |    Nem támogatott     |  Támogatott   |    Nincs támogatási csomag |
| Felügyeleti egység kiosztása – hatókörrel rendelkező rendszergazdák  |     Támogatott    |   Támogatott    |   Nem támogatott |
| Felügyeleti egység tagjainak dinamikus hozzáadása és eltávolítása attribútumok alapján | Nem támogatott | Nem támogatott | Nem támogatott

### <a name="user-management"></a>Felhasználókezelés

| Engedélyek |   Gráf/PowerShell   | Azure AD-portál | Microsoft 365 felügyeleti központ |
| --- | --- | --- | --- |
| Felügyeleti egység – a felhasználói tulajdonságok, jelszavak és licencek hatókörön belüli kezelése   |    Támogatott     |  Támogatott   |   Támogatott |
| Felügyeleti egység – a felhasználói bejelentkezések hatókörének blokkolása és feloldása    |   Támogatott   |    Támogatott   |    Támogatott |
| Felügyeleti egység – a felhasználó többtényezős hitelesítési hitelesítő adatainak hatókörön belüli kezelése   |    Támogatott   |   Támogatott   |   Nem támogatott |

### <a name="group-management"></a>Csoportkezelés

| Engedélyek |   Gráf/PowerShell   | Azure AD-portál | Microsoft 365 felügyeleti központ |
| --- | --- | --- | --- |
| Felügyeleti egység – a csoport tulajdonságainak és tagjainak hatókörön belüli kezelése     |  Támogatott   |    Támogatott    |  Nem támogatott |
| Felügyeleti egység – a csoportos licencelés hatókörön belüli kezelése   |    Támogatott  |    Támogatott   |   Nem támogatott |

A felügyeleti egységek hatóköre csak a felügyeleti engedélyekre vonatkozik. Nem akadályozzák meg, hogy a tagok vagy a rendszergazdák az [alapértelmezett felhasználói engedélyeiket](../fundamentals/users-default-permissions.md) használják a felügyeleti egységen kívüli más felhasználók, csoportok vagy erőforrások tallózására. A Microsoft 365 felügyeleti központban a hatókörön kívüli rendszergazda felügyeleti egységeken kívüli felhasználók ki vannak szűrve. Más felhasználók azonban az Azure AD-portálon, a PowerShellben és más Microsoft-szolgáltatásokban is tallózhatók.

## <a name="next-steps"></a>Következő lépések

- [Felügyeleti egységek kezelése](admin-units-manage.md)
- [Felhasználók kezelése felügyeleti egységekben](admin-units-add-manage-users.md)
- [Csoportok kezelése felügyeleti egységekben](admin-units-add-manage-groups.md)
- [Hatókörrel rendelkező szerepkörök társítása egy felügyeleti egységhez](admin-units-assign-roles.md)

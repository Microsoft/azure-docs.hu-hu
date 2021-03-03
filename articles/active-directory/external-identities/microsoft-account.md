---
title: Microsoft-fiók (MSA) identitás-szolgáltató az Azure AD-ben
description: Az Azure AD használatával engedélyezheti egy külső felhasználó (vendég) számára, hogy bejelentkezzen az Azure AD-alkalmazásokba a Microsoft-fiók (MSA).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d3380277d0e653fd5cb03069b7d91cb363dd95
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693142"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities-preview"></a>Microsoft-fiók (MSA) – külső identitások identitás-szolgáltatója (előzetes verzió)

> [!NOTE]
> A Microsoft-fiók identitás-szolgáltatója Azure Active Directory nyilvános előzetes verziója. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A B2B vendég felhasználói saját személyes Microsoft-fiókjaikat használhatják a VÁLLALATKÖZI együttműködésre további konfigurálás nélkül. A vendég felhasználók beválthatják a VÁLLALATKÖZI együttműködési meghívókat, vagy a saját Microsoft-fiók segítségével elvégezhetik a regisztrációs felhasználói folyamatokat.

A Microsoft-fiókokat a felhasználók úgy állíthatják be, hogy hozzáférjenek a Microsoft-termékekhez és a felhőalapú szolgáltatásokhoz, például az Outlookhoz, a OneDrive, az Xbox LIVE-hoz vagy a Microsoft 365hoz. A rendszer létrehozza és tárolja a fiókot a Microsoft által futtatott Microsoft fogyasztói identitás-fiókban.

## <a name="guest-sign-in-using-microsoft-accounts"></a>Vendég bejelentkezés Microsoft-fiókok használatával

A Microsoft-fiók alapértelmezés szerint a külső identitások identitás-szolgáltatóinak listájában érhető el. Nincs szükség további konfigurálásra ahhoz, hogy a vendég felhasználók bejelentkezzenek a Microsoft-fiók a Meghívási folyamat vagy egy önkiszolgáló bejelentkezési felhasználói folyamat használatával.

![Microsoft-fiók az Identity Providers listában](media/microsoft-account/microsoft-account-identity-provider.png)

### <a name="microsoft-account-in-the-invitation-flow"></a>Microsoft-fiók a Meghívási folyamat során

Amikor [meghívja a vendég felhasználót](add-users-administrator.md) a B2B-együttműködésre, megadhatja a Microsoft-fiókt a bejelentkezéshez használt e-mail-címként.

![Meghívás Microsoft-fiók használatával](media/microsoft-account/microsoft-account-invite.png)

### <a name="microsoft-account-in-self-service-sign-up-user-flows"></a>Microsoft-fiók önkiszolgáló bejelentkezési felhasználói folyamatokban

A Microsoft-fiók egy identitás-szolgáltatói lehetőség az önkiszolgáló bejelentkezési felhasználói folyamatokhoz. A felhasználók saját Microsoft-fiókjaik használatával regisztrálhatják alkalmazásaikat. Először engedélyeznie kell az [önkiszolgáló regisztrációt](self-service-sign-up-user-flow.md) a bérlő számára. Ezután beállíthat egy felhasználói folyamatot az alkalmazáshoz, és a bejelentkezési lehetőségek egyikének kiválasztásával kiválaszthatja a Microsoft-fiókot.

![Önkiszolgáló bejelentkezési felhasználói folyamat Microsoft-fiók](media/microsoft-account/microsoft-account-user-flow.png)

## <a name="next-steps"></a>Következő lépések

- [Azure Active Directory B2B csoportmunka-felhasználók hozzáadása](add-users-administrator.md)
- [Önkiszolgáló regisztráció hozzáadása egy alkalmazáshoz](self-service-sign-up-user-flow.md)
---
title: Licenc önkiszolgáló jelszó-visszaállítás – Azure Active Directory
description: További információ a Azure Active Directory az önkiszolgáló jelszó-visszaállítás licencelési követelményeinek különbségéről
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f8f270f740951b95b0d4c00cbcf9b08e20a8e0c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96743724"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Licencelési követelmények Azure Active Directory önkiszolgáló jelszó-visszaállításhoz

Az ügyfélszolgálati hívások csökkentése és a hatékonyság elvesztése, ha egy felhasználó nem tud bejelentkezni az eszközre vagy alkalmazásba, a Azure Active Directory (Azure AD) felhasználói fiókjait engedélyezheti az önkiszolgáló jelszó-visszaállítás (SSPR) számára. A SSPR-t alkotó szolgáltatások közé tartozik a jelszó módosítása, az Alaphelyzetbe állítás, a zárolás feloldása és a visszaírási egy helyszíni címtárban. Az alapszintű SSPR funkciók a Microsoft 365 Vállalati verzió standard vagy újabb verzióban érhetők el, az összes prémium szintű Azure AD SKU pedig díjmentes.

Ez a cikk az önkiszolgáló jelszó-visszaállítás különböző módszereit ismerteti és használhatja. A díjszabással és a számlázással kapcsolatos részletekért tekintse meg az [Azure ad díjszabási oldalát](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Kiadások és szolgáltatások összehasonlítása

A SSPR felhasználónként licenccel rendelkezik. A megfelelőség fenntartása érdekében a szervezeteknek hozzá kell rendelniük a megfelelő licencet a felhasználókhoz.

Az alábbi táblázat a jelszó módosítására, alaphelyzetbe állítására vagy helyszíni visszaírási vonatkozó különböző SSPR-forgatókönyveket ismerteti, amelyek a szolgáltatást biztosítják.

| Szolgáltatás | Azure AD Free | Microsoft 365 Vállalati verzió standard | Prémium Microsoft 365 Vállalati verzió | Prémium szintű Azure AD P1 vagy P2 |
| --- |:---:|:---:|:---:|:---:|
| **Csak felhőalapú felhasználói jelszó módosítása**<br />Ha a felhasználó az Azure AD-ban ismeri a jelszavát, és módosítani szeretné azt valami újat. | ● | ● | ● | ● |
| **Csak felhőalapú felhasználói jelszó alaphelyzetbe állítása**<br />Ha egy Azure AD-felhasználó elfelejtette a jelszavát, és vissza kell állítania azt. | | ● | ● | ● |
| **Hibrid felhasználói jelszó módosítása vagy alaphelyzetbe állítása helyszíni visszaírási**<br />Ha az Azure AD-ben egy olyan felhasználó van, amely egy helyszíni címtárból szinkronizálva van, Azure AD Connect szeretné módosítani vagy visszaállítani a jelszavát, és az új jelszót is vissza kell írnia a helyszíni környezetbe. | | | ● | ● |

> [!WARNING]
> Az önálló Microsoft 365 az alapszintű és a standard szintű licencelési csomagok nem támogatják a helyszíni visszaírási SSPR. A helyszíni visszaírási szolgáltatáshoz prémium szintű Azure AD P1, Premium P2 vagy Microsoft 365 Vállalati verzió Premium rendszer szükséges.

További licencelési információkért, beleértve a költségeket, tekintse meg a következő lapokat:

* [Díjszabás Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Szolgáltatások és képességek Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Vállalati](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Csoport-vagy felhasználó-alapú licencelés engedélyezése

Az Azure AD támogatja a csoport alapú licencelést. A rendszergazdák tömegesen rendelhetnek hozzá licenceket egy adott felhasználói csoporthoz ahelyett, hogy egyszerre rendelnek hozzájuk egyet. További információ: [a licencekkel kapcsolatos problémák kiosztása, ellenőrzése és megoldása](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Néhány Microsoft-szolgáltatás nem érhető el minden helyen. Ahhoz, hogy egy licencet hozzá lehessen rendelni egy felhasználóhoz, a rendszergazdának meg kell adnia a **használat helye** tulajdonságot a felhasználónál. A licencek hozzárendelése a Azure Portal **felhasználói**  >  **profil**  >  **beállításai** szakaszában végezhető el. *Ha a csoport licenc-hozzárendelését használja, a megadott használati hely nélküli felhasználók öröklik a címtár helyét.*

## <a name="next-steps"></a>Következő lépések

A SSPR megkezdéséhez kövesse az alábbi oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag: az önkiszolgáló jelszó-visszaállítás engedélyezése (SSPR)](tutorial-enable-sspr.md)
---
title: Felhasználók, csoportok, licencelés és szerepkörök a Azure Active Directory
description: A felhasználók és a hozzárendelt licencek, a rendszergazdai szerepkörök és a csoporttagságok közti kapcsolatok az Azure Active Directoryban
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.reviewer: krbain
ms.date: 12/02/2020
ms.topic: overview
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
services: active-directory
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c253e75c7924a05c940b1d09b6f0c3fd281f696
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056946"
---
# <a name="what-is-enterprise-user-management"></a>Mi a vállalati felhasználók felügyelete?

A cikk az Azure AD-rendszergazdák számára mutatja be a felhasználókra vonatkozó felső szintű [identitáskezelési](../fundamentals/active-directory-whatis.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) feladatok közti kapcsolatot a csoportok, a licencek, az üzembe helyezett vállalati alkalmazások és a rendszergazdai szerepkörök tekintetében. A szervezet növekedtével az Azure AD-csoportok és rendszergazdai szerepkörök használatával a következőket teheti:

* Licencek hozzárendelése egyének helyett csoportokhoz
* Engedélyek delegálása az Azure AD felügyeleti tevékenységeinek korlátozottabb jogosultságokkal rendelkező szerepkörökre való elosztásához
* Vállalati alkalmazások hozzáférésének hozzárendelése csoportokhoz

## <a name="assign-users-to-groups"></a>Felhasználók hozzárendelése csoportokhoz

A csoportok segítségével az Azure AD-ben licenceket rendelhet hozzá egyszerre sok felhasználóhoz, illetve felhasználói hozzáféréseket rendelhet hozzá a telepített vállalati alkalmazásokhoz. A csoportok segítségével az összes rendszergazdai szerepkört hozzárendelheti, kivéve az Azure AD globális rendszergazdáját, vagy megadhat hozzáférést a külső erőforrásokhoz, például SaaS-alkalmazásokhoz vagy SharePoint-webhelyekhez.

A nagyobb rugalmasság és a csoporttagság-felügyeleti tevékenységek csökkentése érdekében [dinamikus csoportokat](groups-create-rule.md) is használhat az Azure AD-ben a csoporttagság automatikus kiterjesztésére és szűkítésére. Minden egyedi felhasználóhoz, amely egy vagy több dinamikus csoport tagja, egy Azure AD Premium P1-licencre lesz szükség.

## <a name="assign-licenses-to-groups"></a>Licencek hozzárendelése csoportokhoz

Ha a licenceket külön rendeli hozzá vagy törli a felhasználókról, az sok időt és nagy figyelmet vehet igénybe. Ha a [licenceket inkább csoportokhoz rendeli](../fundamentals/license-users-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context), a nagy volumenű licenc-hozzárendelési tevékenységeket egyszerűbbé teheti.

Amikor az Azure AD-ben a felhasználó egy licencelt csoport tagja lesz, automatikusan megkapja a megfelelő licenceket. Amikor a felhasználók elhagyják a csoportot, az Azure AD törli a hozzárendelt licenceket. Az Azure AD-csoportok nélkül PowerShell-szkripteket kellene írnia vagy a Graph API-t használnia a felhasználói licencek tömeges hozzáadásához vagy eltávolításához a szervezethez csatlakozó vagy azt elhagyó felhasználók számára.

Ha nem áll elég licenc rendelkezésre, vagy valamilyen hiba lép fel, például egyszerre hozzá nem rendelhető szolgáltatáscsomagok esetén, a csoport licencelési problémáinak állapotát az Azure Portalon tekintheti meg.

>[!NOTE]
>A csoportalapú licencelési funkció jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verzióban a funkció bármilyen fizetős Azure Active Directory- (Azure AD-) licenccsomaggal vagy próbaverzióval elérhető.

## <a name="delegate-administrator-roles"></a>Rendszergazdai szerepkörök delegálása

Számos nagyméretű szervezet szeretne a felhasználó számára megfelelő lehetőségeket adni a munkavégzéshez anélkül, hogy a nagy hatalmú globális rendszergazdai szerepkört osztaná ki például az olyan felhasználók számára, akiknek alkalmazásokat kell tudnia regisztrálni. Íme egy példa az új Azure AD-rendszergazdai szerepkörökre, amelyekkel az alkalmazásfelügyeleti feladatok finomabb tagoltsággal oszthatók ki:

 Szerepkörnév | Engedélyek összegzése
 --------- | -------------------
 **Alkalmazás-rendszergazda** | Hozzáadhatja és felügyelheti a vállalati alkalmazásokat és az alkalmazásregisztrációkat, és konfigurálhatja a proxyalkalmazás-beállításokat. Az alkalmazás-rendszergazdák megtekinthetik a feltételes hozzáférési házirendeket és eszközöket, de nem kezelhetik azokat.
 **Felhőalkalmazás-rendszergazda** | Hozzáadhatja és felügyelheti a vállalati alkalmazásokat és a vállalati alkalmazásregisztrációkat. Ez a szerepkör az alkalmazás-rendszergazda összes engedélyével rendelkezik, azzal a különbséggel, hogy nem tudja kezelni az alkalmazásproxy-beállításokat.
**Alkalmazásfejlesztő** | Hozzáadhatja és frissítheti az alkalmazásregisztrációkat, de nem felügyelheti a vállalati alkalmazásokat és nem konfigurálhatja az alkalmazásproxykat.

Folyamatosan jelennek meg új Azure AD-rendszergazdai szerepkörök. A jelenleg elérhető szerepköröket az Azure Portalon vagy a [rendszergazdai szerepkörök engedélyeinek referenciájában](../roles/permissions-reference.md) tekintheti meg.

## <a name="assign-app-access"></a>Alkalmazás-hozzáférés hozzárendelése

Az Azure AD használatával csoportokhoz férhet hozzá az [Azure ad-szervezetben üzembe helyezett vállalati alkalmazásokhoz](../manage-apps/assign-user-or-group-access-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context). A csoportszintű alkalmazás-hozzárendelést a dinamikus csoportokkal kombinálva automatizálható a felhasználók alkalmazás-hozzáféréseinek hozzárendelése a szervezet növekedésével. A vállalati alkalmazások hozzáféréseinek kiosztásához Azure Active Directory Premium P1 vagy P2 szintű licenc szükséges.

Az Azure AD emellett lehetővé teszi az alkalmazások és a hozzáféréssel felruházott csoportok közötti adatáramlás finomhangolását. A [Vállalati alkalmazásokban](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) az alkalmazást megnyitva, majd a **Kiépítés** lehetőséget választva:

* Beállíthatja az automatikus kiépítést az olyan alkalmazások esetében, amelyek támogatják azt
* Megadhatja a hitelesítő adatokat az alkalmazás felhasználókezelési API-ja számára
* Beállíthatja a leképezéseket, amelyek azt szabályozzák, hogy melyik felhasználói attribútumok legyenek továbbítva az Azure AD és az alkalmazás között a felhasználói fiókok kiépítése vagy frissítése során
* Elindíthatja vagy leállíthatja az Azure AD kiépítési szolgáltatást valamely alkalmazásra, törölheti a kiépítési gyorsítótárat, vagy újraindíthatja a szolgáltatást
* Megtekintheti a **kiépítési tevékenységekre vonatkozó jelentést**, amely naplózza az Azure AD és az alkalmazás között létrehozott, frissített és törölt összes felhasználót és csoportot, valamint a **kiépítési hibákat tartalmazó jelentést**, amely a részletes hibaüzeneteket tartalmazza

## <a name="next-steps"></a>Következő lépések

Amennyiben még csak most ismerkedik az Azure AD rendszergazdai feladataival, az alapvető információkért tekintse meg [az Azure Active Directory alapjait](../fundamentals/index.yml) bemutató cikket.

Vagy mindjárt hozzá is láthat [a csoportok létrehozásának](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context), [a licencek hozzárendelésének](../fundamentals/license-users-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context), [az alkalmazás-hozzáférések hozzárendelésének](../manage-apps/assign-user-or-group-access-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) vagy [a rendszergazdai szerepkörök hozzárendelésének](../roles/permissions-reference.md).
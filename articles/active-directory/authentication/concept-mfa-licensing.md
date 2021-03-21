---
title: Azure AD Multi-Factor Authentication verziók és fogyasztási csomagok
description: Ismerje meg az Azure AD Multi-Factor Authentication ügyfelet, valamint az elérhető különböző módszereket és verziókat.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63d880146a0b068a5d097c452c14b28db4907098
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96743955"
---
# <a name="features-and-licenses-for-azure-ad-multi-factor-authentication"></a>Az Azure AD Multi-Factor Authentication szolgáltatásai és licencei

A szervezet felhasználói fiókjainak védeleméhez többtényezős hitelesítést kell használni. Ez a funkció különösen fontos olyan fiókoknál, amelyek privilegizált hozzáféréssel rendelkeznek az erőforrásokhoz. Az alapszintű multi-Factor Authentication funkciók a Microsoft 365 és az Azure Active Directory (Azure AD) rendszergazdái számára is elérhetők, többletköltség nélkül. Ha szeretné frissíteni a rendszergazdák vagy a többtényezős hitelesítés használatát a felhasználók többi részén, több módon is megvásárolhatja az Azure AD Multi-Factor Authentication.

> [!IMPORTANT]
> Ez a cikk az Azure AD Multi-Factor Authentication különböző módszereit ismerteti és használhatja. A díjszabással és a számlázással kapcsolatos részletekért tekintse meg az [Azure AD multi-Factor Authentication díjszabási oldalát](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-ad-multi-factor-authentication"></a>Az Azure AD Multi-Factor Authentication elérhető verziói

Az Azure AD-Multi-Factor Authentication a szervezet igényeitől függően néhány különböző módon használhatók és licenccel is rendelkezhetnek. Lehet, hogy már jogosult az Azure AD-Multi-Factor Authentication használatára a jelenleg használt Azure AD-, EMS-vagy Microsoft 365-licenccel függően. Az alábbi táblázat ismerteti az Azure AD Multi-Factor Authentication beszerzésének különböző módszereit, valamint néhány funkciót és használati esetet.

| Ha a felhasználó a | Képességek és használati esetek |
| --- | --- |
| Microsoft 365 Vállalati verzió Premium és EMS, vagy Microsoft 365 E3 és E5 | Az EMS E3, Microsoft 365 E3 és a Microsoft 365 Vállalati verzió Premium tartalmazza prémium szintű Azure AD P1. Az EMS E5 vagy Microsoft 365 E5 prémium szintű Azure AD P2-t tartalmazza. A következő szakaszokban ismertetett feltételes hozzáférési funkciók használatával többtényezős hitelesítést biztosíthat a felhasználóknak. |
| Prémium szintű Azure AD P1 | Az [Azure ad feltételes hozzáférés](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) használatával a felhasználók a többtényezős hitelesítésre való rákérdezéshez bizonyos esetekben vagy eseményeknél, az üzleti igényeknek megfelelően. |
| Prémium szintű Azure AD P2 | Biztosítja a legerősebb biztonsági helyzetet és a jobb felhasználói élményt. [Kockázatalapú feltételes hozzáférést biztosít](../conditional-access/howto-conditional-access-policy-risk.md) a prémium szintű Azure ad P1-funkciókhoz, amelyek alkalmazkodnak a felhasználói mintákhoz, és lekicsinyítik a többtényezős hitelesítési kéréseket. |
| Minden Microsoft 365 csomag | Az Azure AD Multi-Factor Authentication [felhasználónkénti alapon engedélyezhető](howto-mfa-userstates.md), illetve engedélyezhető vagy letiltható az összes felhasználó számára a [biztonsági alapértékek](../fundamentals/concept-fundamentals-security-defaults.md)használatával. Az Azure AD Multi-Factor Authentication kezelése a Microsoft 365 portálon keresztül történik. A jobb felhasználói élmény érdekében frissítsen prémium szintű Azure AD P1-re vagy P2-re, és használja a feltételes hozzáférést. További információ: [biztonságos Microsoft 365 erőforrások a többtényezős hitelesítéssel](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication). |
| Ingyenes Azure AD | A [biztonsági alapértelmezett beállítások](../fundamentals/concept-fundamentals-security-defaults.md) használatával engedélyezheti a többtényezős hitelesítést az összes felhasználó számára. Nem rendelkezik az engedélyezett felhasználók vagy forgatókönyvek részletes szabályozásával, de ez további biztonsági lépést is biztosít.<br /> Még akkor is, ha a biztonsági alapértékek nem használják a többtényezős hitelesítést mindenki számára, az *Azure ad globális rendszergazdai* szerepkörrel rendelkező felhasználók úgy konfigurálhatók, hogy a többtényezős hitelesítést használják. Az ingyenes szint ezen funkciója biztosítja, hogy a kritikus rendszergazdai fiókok védelmét a multi-Factor Authentication védi. |

## <a name="feature-comparison-of-versions"></a>A verziók összehasonlítása

A következő táblázat felsorolja az Azure AD Multi-Factor Authentication különböző verzióiban elérhető funkciókat. Tervezze meg a felhasználói hitelesítés biztonságossá tételének szükségességét, majd határozza meg, hogy melyik megközelítés teljesíti ezeket a követelményeket. Például bár a ingyenes Azure AD biztosít az Azure AD Multi-Factor Authenticationt biztosító biztonsági alapértékeket, csak a mobil hitelesítő alkalmazás használható a hitelesítési kéréshez, nem pedig telefonhíváshoz vagy SMS-hez. Ez a megközelítés akkor lehet korlátozás, ha nem biztos benne, hogy a Mobile Authentication alkalmazás telepítve van a felhasználó személyes eszközén.

| Szolgáltatás | Ingyenes Azure AD – biztonsági alapértékek | Ingyenes Azure AD – Azure AD globális rendszergazdák | Microsoft 365-alkalmazások | Prémium szintű Azure AD P1 vagy P2 |
| --- |:---:|:---:|:---:|:---:|
| Azure AD-bérlői rendszergazdai fiókok biztosítása MFA-val | ● | ● (Csak *Azure ad globális rendszergazdai* fiókok) | ● | ● |
| Mobile App második tényezőként | ● | ● | ● | ● |
| Telefonhívás második tényezőként | | ● | ● | ● |
| SMS második tényezőként | | ● | ● | ● |
| Az ellenőrzési módszerek rendszergazdai ellenőrzése | | ● | ● | ● |
| Csalási riasztás | | | | ● |
| MFA-jelentések | | | | ● |
| Egyéni üdvözlések a telefonhívásokhoz | | | | ● |
| Telefonos hívások egyéni hívóazonosító | | | | ● |
| Megbízható IP-címek | | | | ● |
| MFA megjegyzése megbízható eszközökön | | ● | ● | ● |
| MFA helyszíni alkalmazásokhoz | | | | ● |

## <a name="purchase-and-enable-azure-ad-multi-factor-authentication"></a>Azure AD-Multi-Factor Authentication vásárlása és engedélyezése

Az Azure AD Multi-Factor Authentication használatához Regisztráljon vagy vásároljon egy jogosult Azure AD-szintet. Az Azure AD négy kiadásban érhető el: ingyenes, Microsoft 365 alkalmazás, prémium P1 és prémium P2.

Az ingyenes kiadást egy Azure-előfizetés tartalmazza. Tekintse meg az [alábbi szakaszt](#azure-ad-free-tier) a biztonsági Alapértelmezések használatáról, illetve a fiókok védelméről az *Azure ad globális rendszergazdai* szerepkörrel.

A prémium szintű Azure AD kiadása a Microsoft képviselőjétől, a [nyílt mennyiségi licencelési programtól](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)és a [Cloud Solution Providers programtól](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409)érhető el. Az Azure és Microsoft 365 előfizetők prémium szintű Azure Active Directory P1 és P2 online is vásárolhatnak. [Jelentkezzen be](https://portal.office.com/Commerce/Catalog.aspx) a vásárláshoz.

Miután megvásárolta a szükséges Azure AD-szintet, [tervezze meg és telepítse az Azure ad multi-Factor Authentication](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>ingyenes Azure ADi szintű

Egy ingyenes Azure AD bérlő összes felhasználója használhatja az Azure AD-Multi-Factor Authentication a biztonsági alapértelmezett beállítások használatával. A Mobile Authentication alkalmazás az egyetlen módszer, amelyet az Azure AD-Multi-Factor Authentication használhat ingyenes Azure AD biztonsági Alapértelmezések használata esetén.

* [További információ az Azure AD biztonsági beállításairól](../fundamentals/concept-fundamentals-security-defaults.md)
* [A ingyenes Azure AD felhasználók biztonsági alapértelmezésének engedélyezése](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Ha nem szeretné engedélyezni az Azure AD-Multi-Factor Authentication az összes felhasználó számára, ehelyett csak az *Azure ad globális rendszergazdai* szerepkörrel rendelkező felhasználói fiókokat kell védelemmel ellátnia. Ez a megközelítés további hitelesítési kéréseket biztosít a kritikus rendszergazdai fiókokhoz. Az Azure AD Multi-Factor Authentication a használt fiók típusától függően az alábbi módszerek egyikével engedélyezhető:

* Ha Microsoft-fiókot használ, [regisztráljon a multi-Factor Authentication szolgáltatásra](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Ha nem Microsoft-fiókot használ, [kapcsolja be a többtényezős hitelesítést egy felhasználó vagy csoport számára az Azure ad-ben](howto-mfa-userstates.md).

## <a name="next-steps"></a>Következő lépések

* A költségekkel kapcsolatos további információkért lásd: az [Azure AD multi-Factor Authentication díjszabása](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
* [Mi a feltételes hozzáférés?](../conditional-access/overview.md)


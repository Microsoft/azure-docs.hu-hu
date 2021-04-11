---
title: Alkalmazás-felügyeleti rendszergazdai engedélyek delegálása – Azure AD | Microsoft Docs
description: Engedélyek megadása az alkalmazás-hozzáférés kezeléséhez Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9dcbb12f48a98a35013a80f986b67f75118d74c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060252"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Alkalmazás-regisztrációs engedélyek delegálása Azure Active Directory

Ez a cikk azt ismerteti, hogyan használhatók a Azure Active Directory (Azure AD) egyéni szerepkörei által biztosított engedélyek az alkalmazás-kezelési igények kielégítéséhez. Az Azure AD-ben a következő módokon delegálhatja az alkalmazás-létrehozási és-kezelési engedélyeket:

- Korlátozza, hogy kik hozhatnak [létre alkalmazásokat](#restrict-who-can-create-applications) és kezelhetik a létrehozott alkalmazásokat. Az Azure AD-ben alapértelmezés szerint minden felhasználó regisztrálhatja az alkalmazásokat, és kezelheti az általuk létrehozott alkalmazások minden aspektusát. Ez korlátozható úgy, hogy csak a kijelölt személyek számára engedélyezze az engedélyt.
- [Egy vagy több tulajdonos társítása egy alkalmazáshoz](#assign-application-owners). Ez egy egyszerű módja annak, hogy valaki az Azure AD-konfiguráció minden aspektusát felügyelni tudja egy adott alkalmazáshoz.
- [Egy beépített rendszergazdai szerepkör kiosztása](#assign-built-in-application-admin-roles) , amely hozzáférést biztosít az Azure ad-ben az összes alkalmazás konfigurációjának kezeléséhez. Ez az ajánlott módszer arra, hogy az informatikai szakértők hozzáférjenek a széles körű alkalmazás-konfigurációs engedélyek kezeléséhez anélkül, hogy hozzáférést kellene biztosítani az Azure AD más részeihez, amelyek nem kapcsolódnak az alkalmazás konfigurációjához.
- [Egyéni szerepkör létrehozása](#create-and-assign-a-custom-role-preview) , amely a nagyon konkrét engedélyeket definiálja, és hozzárendeli azt valakihöz, vagy egyetlen alkalmazás hatóköréhez korlátozott tulajdonosként, vagy a címtár hatókörében (az összes alkalmazás) korlátozott rendszergazdaként.

Fontos figyelembe venni a hozzáférést a fenti módszerek egyikének két okból való megadásával. Először is a felügyeleti feladatok elvégzésére való jogosultságok delegálása csökkenti a globális rendszergazda terhelését. Másodszor, a korlátozott engedélyek használata javítja a biztonsági helyzeteket, és csökkenti a jogosulatlan hozzáférés lehetséges lehetőségét. A szerepkör-biztonsági tervezéssel kapcsolatos útmutatásért lásd: az [Azure ad-ben az emelt szintű hozzáférés biztonságossá tétele a hibrid és a Felhőbeli](security-planning.md)környezetekben.

## <a name="restrict-who-can-create-applications"></a>Alkalmazások létrehozására jogosult felhasználók korlátozása

Az Azure AD-ben alapértelmezés szerint minden felhasználó regisztrálhatja az alkalmazásokat, és kezelheti az általuk létrehozott alkalmazások minden aspektusát. Mindenkinek lehetősége van arra is, hogy hozzájáruljon az alkalmazásoknak a céges adatokhoz való hozzáféréshez a nevükben. Dönthet úgy, hogy szelektíven adja meg ezeket az engedélyeket úgy, hogy a globális kapcsolókat "nem" értékre állítja, és hozzáadja a kiválasztott felhasználókat az alkalmazás fejlesztői szerepköréhez.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>Az alkalmazás regisztrációjának vagy az alkalmazások jóváhagyásának alapértelmezett lehetőségének letiltása

1. Jelentkezzen be az Azure AD-szervezetbe egy olyan fiókkal, amely jogosult a globális rendszergazdai szerepkörre az Azure AD-szervezetben.
1. Állítsa be a következők egyikét vagy mindkettőt:

    - A [szervezet felhasználói beállítások lapján](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)állítsa be, hogy a **felhasználók regisztrálhatják az alkalmazások** beállítást a nem értékre. Ez letiltja a felhasználók számára az alkalmazás-regisztrációk létrehozásának alapértelmezett lehetőségét.
    - A [vállalati alkalmazások felhasználói beállításainál](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)a **felhasználók megadhatják, hogy az alkalmazások a vállalati adatokhoz hozzáférjenek** a nem értékre. Ez letiltja az alapértelmezett képességet, hogy a felhasználók beleférjenek a céges adatokhoz való hozzáférésre a nevükben.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>Egyéni engedélyek megadása az alkalmazások létrehozásához és jóváhagyásához, ha az alapértelmezett képesség le van tiltva

Rendelje hozzá az alkalmazás fejlesztői szerepkörét, hogy lehetővé teszi az alkalmazások regisztrációjának létrehozását, ha a **felhasználók regisztrálhatják az alkalmazásokat** beállítás értéke nem. Ez a szerepkör lehetővé teszi az engedély megadását a saját nevében, amikor a felhasználók beleegyeznek a **vállalati adatokhoz való hozzáférésre az alkalmazásokban** a nem értékre van állítva. Rendszerviselkedés, amikor egy felhasználó új alkalmazás-regisztrációt hoz létre, a rendszer automatikusan hozzáadja őket az első tulajdonosként. A tulajdonosi engedélyek lehetővé teszi a felhasználó számára, hogy egy adott alkalmazás regisztrációjának vagy vállalati alkalmazásának minden aspektusát kezelhesse.

## <a name="assign-application-owners"></a>Alkalmazás-tulajdonosok kiosztása

A tulajdonosok hozzárendelése egy egyszerű módja annak, hogy az Azure AD-konfiguráció minden aspektusát felügyelje egy adott alkalmazás-regisztráció vagy vállalati alkalmazás számára. Rendszerviselkedésként, amikor egy felhasználó új alkalmazás-regisztrációt hoz létre, a rendszer automatikusan hozzáadja őket az első tulajdonosként. A tulajdonosi engedélyek lehetővé teszi a felhasználó számára, hogy egy adott alkalmazás regisztrációjának vagy vállalati alkalmazásának minden aspektusát kezelhesse. Az eredeti tulajdonos törölhető, és további tulajdonosok is hozzáadhatók.

### <a name="enterprise-application-owners"></a>Vállalati alkalmazások tulajdonosai

Tulajdonosként a felhasználók kezelhetik a vállalati alkalmazás szervezetre jellemző konfigurációját, például az egyszeri bejelentkezés konfigurációját, a létesítést és a felhasználói hozzárendeléseket. A tulajdonosok ezen kívül eltávolíthatnak vagy hozzáadhatnak más tulajdonosokat. A globális rendszergazdáktól eltérően a tulajdonosok csak a saját vállalati alkalmazásokat kezelhetik.

Bizonyos esetekben az alkalmazás-katalógusból létrehozott vállalati alkalmazások a vállalati alkalmazások és az alkalmazások regisztrálását is tartalmazzák. Ha ez igaz, a tulajdonos hozzáadásával a vállalati alkalmazás automatikusan hozzáadja a tulajdonost a megfelelő alkalmazás-regisztrációhoz a tulajdonosként.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>Tulajdonos társítása vállalati alkalmazásokhoz

1. Jelentkezzen be az [Azure ad-szervezetbe](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) egy olyan fiókkal, amely jogosult az alkalmazás-rendszergazda vagy a felhőalapú alkalmazás-rendszergazda számára a szervezet számára.
1. A szervezet [Alkalmazásregisztrációk lapján](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) válasszon ki egy alkalmazást az alkalmazás Áttekintés lapjának megnyitásához.
1. A **tulajdonosok** lehetőség kiválasztásával megtekintheti az alkalmazás tulajdonosainak listáját.
1. Válassza a **Hozzáadás** lehetőséget, ha egy vagy több tulajdonost szeretne hozzáadni az alkalmazáshoz.

> [!IMPORTANT]
> A felhasználók és az egyszerű szolgáltatások lehetnek az alkalmazások tulajdonosainak tulajdonosai. Csak a felhasználók lehetnek vállalati alkalmazások tulajdonosai. Csoport nem rendelhető hozzá tulajdonosként sem.
>
> A tulajdonosok hitelesítő adatokat adhatnak hozzá egy alkalmazáshoz, és a hitelesítő adatok használatával megszemélyesítik az alkalmazás identitását. Előfordulhat, hogy az alkalmazás több jogosultsággal rendelkezik, mint a tulajdonos, így magasabb szintű jogosultságot biztosít a tulajdonos felhasználói vagy szolgáltatásnév számára. Az alkalmazás tulajdonosai az alkalmazás engedélyeitől függően létrehozhatnak vagy frissíthetnek felhasználókat vagy más objektumokat az alkalmazás megszemélyesítése közben.

## <a name="assign-built-in-application-admin-roles"></a>Beépített alkalmazás-rendszergazdai szerepkörök kiosztása

Az Azure AD beépített rendszergazdai szerepkörökkel rendelkezik, amelyek hozzáférést biztosítanak az Azure AD-ben az összes alkalmazás konfigurációjának kezeléséhez. Ezek a szerepkörök ajánlottak az informatikai szakértők számára a széles körű alkalmazás-konfigurációs engedélyek kezeléséhez anélkül, hogy hozzáférést biztosítanak az Azure AD egyéb részeinek kezeléséhez, amelyek nem kapcsolódnak az alkalmazás konfigurációjához.

- Alkalmazás-rendszergazda: ebben a szerepkörben lévő felhasználók létrehozhatják és kezelhetik a vállalati alkalmazások, az alkalmazások regisztrációi és az alkalmazásproxy-beállítások összes aspektusát. Ez a szerepkör lehetővé teszi a delegált engedélyekkel való hozzájárulást, valamint az Microsoft Graph nélküli alkalmazás-engedélyek megadását is. Az ehhez a szerepkörhöz hozzárendelt felhasználók nem lesznek hozzáadva tulajdonosként új alkalmazás-regisztrációk vagy vállalati alkalmazások létrehozásakor.
- Cloud Application Administrator: az ebben a szerepkörben lévő felhasználók ugyanazok az engedélyek, mint az alkalmazás-rendszergazda szerepkör, kivéve az alkalmazásproxy felügyeletének képességét. Az ehhez a szerepkörhöz hozzárendelt felhasználók nem lesznek hozzáadva tulajdonosként új alkalmazás-regisztrációk vagy vállalati alkalmazások létrehozásakor.

További információért és a szerepkörök leírásának megtekintéséhez tekintse meg az [Azure ad beépített szerepkörei](permissions-reference.md)című témakört.

Az alkalmazás-rendszergazda vagy a Felhőbeli alkalmazás rendszergazdai szerepköreinek hozzárendeléséhez kövesse a [szerepkörök társítása felhasználók számára Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md) útmutatóban található utasításokat.

> [!IMPORTANT]
> Az alkalmazás-rendszergazdák és a felhőalapú alkalmazások rendszergazdái hitelesítő adatokat adhatnak hozzá egy alkalmazáshoz, és a hitelesítő adatok használatával megszemélyesítik az alkalmazás identitását. Előfordulhat, hogy az alkalmazás jogosultságokat emel a rendszergazdai szerepkör engedélyeivel szemben. Az ebben a szerepkörben található rendszergazda létrehozhat vagy frissíthet felhasználókat vagy más objektumokat az alkalmazás megszemélyesítése közben az alkalmazás engedélyeitől függően.
> Egyik szerepkör sem biztosít lehetőséget a feltételes hozzáférési beállítások kezelésére.

## <a name="create-and-assign-a-custom-role-preview"></a>Egyéni szerepkör létrehozása és társítása (előzetes verzió)

Az egyéni szerepkörök létrehozása és az egyéni szerepkörök hozzárendelésének lépései külön lépések:

- [Hozzon létre egy egyéni *szerepkör-definíciót*](custom-create.md) , és [adjon hozzá engedélyeket egy előre definiált listából](custom-available-permissions.md). Ezek ugyanazok a beépített szerepkörökben használt engedélyek.
- [Hozzon létre egy *szerepkör-hozzárendelést*](custom-assign-powershell.md) az egyéni szerepkör hozzárendeléséhez.

Ez az elkülönítés lehetővé teszi egyetlen szerepkör-definíció létrehozását, majd a különböző *hatókörökben* többszöri hozzárendelését. Az egyéni szerepkört a szervezetre kiterjedő hatókörben lehet hozzárendelni, vagy ha egyetlen Azure AD-objektum is hozzárendelhető a hatókörhöz. Egy objektum hatóköre például egyetlen alkalmazás regisztrálása. A különböző hatókörök használata esetén ugyanaz a szerepkör-definíció rendelhető hozzá az Sally szolgáltatáshoz a szervezet összes alkalmazás-regisztrációjában, majd csak a contoso-költségelszámolás alkalmazás regisztrálására.

Tippek az alkalmazások kezelésének delegálásához egyéni szerepkörök létrehozásakor és használatakor:
- Az egyéni szerepkörök csak az Azure AD-portál legfrissebb alkalmazás-regisztrációs feladataiban biztosítanak hozzáférést. Nem biztosítanak hozzáférést a régi alkalmazás-regisztrációk paneleken.
- Az egyéni szerepkörök nem biztosítanak hozzáférést az Azure AD-portálhoz, ha az "Azure AD felügyeleti portálhoz való hozzáférés korlátozása" felhasználói beállítás Igen értékre van állítva.
- Alkalmazásregisztrációk, hogy a felhasználó csak a "minden alkalmazás" lapon jelenjen meg a szerepkör-hozzárendelések használatával, megjelenik az alkalmazás regisztrációs lapján. Nem jelennek meg a "tulajdonban lévő alkalmazások" lapon.

Az egyéni szerepkörök alapjaival kapcsolatos további információkért tekintse meg az [Egyéni szerepkörök áttekintését](custom-overview.md), valamint az [Egyéni szerepkör létrehozását](custom-create.md) és [a szerepkör hozzárendelését](custom-assign-powershell.md)ismertető cikket.

## <a name="next-steps"></a>Következő lépések

- [Alkalmazás regisztrációs altípusai és engedélyei](custom-available-permissions.md)
- [Azure AD-rendszergazdai szerepkör-hivatkozás](permissions-reference.md)

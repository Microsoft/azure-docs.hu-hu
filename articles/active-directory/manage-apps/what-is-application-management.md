---
title: Mi az alkalmazások kezelése a Azure Active Directoryban?
description: Áttekintés a Azure Active Directory (AD) használatáról identitás-és hozzáférés-kezelési (IAM) rendszerként a Felhőbeli és a helyszíni alkalmazásokhoz.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 01/22/2021
ms.author: kenwith
ms.reviewer: ''
ms.openlocfilehash: 247e824997fd95434246e49c78bf167f36e146c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258591"
---
# <a name="what-is-application-management"></a>Mi az alkalmazáskezelés?

Az Azure AD egy identitás- és hozzáférés-kezelési (IAM-) rendszer. Központi helyet biztosít a digitális identitásokkal kapcsolatos információk tárolásához. A szoftveralkalmazásait konfigurálhatja az Azure AD, mint a felhasználói információkat tároló hely használatára. 

Az Azure AD-t úgy kell konfigurálni, hogy integrálható legyen az alkalmazással. Más szóval tudnia kell, hogy mely alkalmazások használják az identitásokhoz. Az Azure AD ismeri ezeket az alkalmazásokat, és azt, hogy miként kell kezelni őket, az alkalmazások felügyelete néven ismert.

Az alkalmazásokat a Azure Active Directory portál kezelés szakaszában található **vállalati alkalmazások** lapon kezelheti.

![A vállalati alkalmazások lehetőség az Azure AD-portál kezelés szakaszában.](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>Mi az identitás-és hozzáférés-kezelési (IAM) rendszer?
Az alkalmazás egy olyan szoftver, amely valamilyen célra használatos. A legtöbb alkalmazáshoz a felhasználóknak be kell jelentkezniük.

A központosított identitásrendszer egyetlen helyet biztosít a felhasználói adatok tárolásához, amelyeket az összes alkalmazás használhat. Ezeket a rendszereket identitás-és hozzáférés-kezelési (IAM) rendszereknek nevezzük. Azure Active Directory a Microsoft Cloud-hoz készült IAM rendszer.

>[!TIP]
>A IAM rendszer egyetlen helyet biztosít a felhasználói identitások nyomon követéséhez. Az Azure AD a Microsoft Cloud-hoz készült IAM rendszer.

## <a name="why-manage-applications-with-a-cloud-solution"></a>Miért jó az alkalmazásokat felhőalapú megoldással kezelni?

A szervezetekben gyakran több száz alkalmazás is van, amitől a felhasználók munkavégzése függ. A felhasználók sokféle eszközről és helyről érik el ezeket az alkalmazásokat. Minden alkalommal új alkalmazások kerülnek hozzáadásra, fejlesztésre és napnyugtara. Számos alkalmazás és hozzáférési pont esetében fontos, hogy olyan identitás-megoldást használjon, amely az összeset használja.

>[!TIP]
>Az Azure AD App Gallery számos népszerű alkalmazást tartalmaz, amelyek már előre konfigurálva vannak az Azure AD-ben való együttműködéshez.

## <a name="how-does-azure-ad-work-with-apps"></a>Hogyan működik az Azure AD az alkalmazásokkal?

Az Azure AD a közepén található, és identitáskezelést biztosít a Felhőbeli és a helyszíni alkalmazásokhoz. 

![Az Azure AD-n keresztül összevont alkalmazásokat bemutató diagram](media/what-is-application-management/app-management-overview.png)

>[!TIP]
>A [felhasználói kiépítés automatizálásával](../app-provisioning/user-provisioning.md) csökkentheti az adminisztrációs költségeket, így a felhasználók automatikusan hozzáadódnak az Azure ad-hez, amikor hozzáadja őket a vállalati HR-rendszerhez. 

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Milyen típusú alkalmazásokat lehet integrálni az Azure AD-vel?

Az Azure ad-t saját identitásrendszer használhatja bármely alkalmazáshoz. Számos alkalmazás már előre konfigurálva van, és minimális erőfeszítéssel állítható be. Ezek az előre konfigurált alkalmazások a [Azure ad alkalmazás galériában](/azure/active-directory/saas-apps/)lesznek közzétéve. 

Manuálisan is konfigurálhatja a legtöbb alkalmazást az egyszeri bejelentkezéshez, ha még nem szerepelnek a galériában. Az Azure AD több SSO-beállítást is biztosít. A legnépszerűbbek az SAML-alapú SSO-és OIDC-alapú egyszeri bejelentkezések. Ha többet szeretne megtudni az alkalmazások egyszeri bejelentkezés engedélyezéséhez való integrálásáról, tekintse meg az [egyszeri bejelentkezés lehetőségeit](sso-options.md). 

A szervezete helyszíni alkalmazásokat használ? Integrálhatja őket az App proxy használatával. További információ: [távoli hozzáférés biztosítása helyszíni alkalmazásokhoz az Azure ad alkalmazásproxy használatával](application-proxy.md).

>[!TIP]
>Saját üzletági alkalmazások létrehozásakor az egyszeri bejelentkezés támogatásához integrálhatja őket az Azure AD-vel. Ha többet szeretne megtudni az Azure AD-alkalmazások fejlesztéséről, tekintse meg a [Microsoft Identity platform](..//develop/v2-overview.md)című témakört.

## <a name="manage-risk-with-conditional-access-policies"></a>Kockázatkezelés feltételes hozzáférési szabályzatokkal

A [feltételes hozzáféréssel](../conditional-access/concept-conditional-access-cloud-apps.md) rendelkező Azure ad egyszeri bejelentkezés (SSO) összekapcsolása magas szintű biztonságot nyújt az alkalmazások eléréséhez. A feltételes hozzáférési szabályzatok részletes szabályozást biztosítanak az alkalmazásoknak a beállított feltételek alapján. 

## <a name="improve-productivity-with-single-sign-on"></a>Termelékenység növelése egyszeri bejelentkezéssel

Az egyszeri bejelentkezés (SSO) egységes felhasználói élményt biztosít a Microsoft 365 és az összes többi alkalmazás között. Elbúcsúzni a felhasználónevének és jelszavának folyamatos beírásával!

Az egyszeri bejelentkezéssel kapcsolatos további tudnivalókért tekintse meg a [Mi az az egyszeri bejelentkezés](what-is-single-sign-on.md)című témakört.

## <a name="address-governance-and-compliance"></a>Irányítás és megfelelőség

Alkalmazások figyelése a biztonsági incidenst és az esemény-figyelési (SIEM) eszközöket használó jelentéseken keresztül. A jelentéseket a portálról vagy az API-kból érheti el. Programozott módon naplózza, ki fér hozzá az alkalmazásokhoz és megszünteti az inaktív felhasználók hozzáférését a hozzáférési felülvizsgálatokkal.

## <a name="manage-costs"></a>Költségek kezelése

Az Azure AD-re történő átállással költségeket takarít meg és megszabadul a helyszíni infrastruktúra kezelésével járó gondoktól. Az Azure AD önkiszolgáló hozzáférést is nyújt az alkalmazásokhoz, amivel a rendszergazdák és felhasználók számára is időt takarít meg. Az egyszeri bejelentkezés használata esetén nincs szükség alkalmazásspecifikus jelszavakra. Az egyszeri bejelentkezéssel megtakaríthatók az új alkalmazásjelszavak kéréséhez és a jelszavak beszerzése közben kieső termelékenységhez kapcsolódó költségek.

Az emberi erőforrások célzott alkalmazásaihoz, illetve a nagy felhasználói csoporttal rendelkező egyéb alkalmazásokhoz az alkalmazás kiépítés használatával egyszerűbbé válik az élet. Az alkalmazás kiépítés automatizálja a felhasználók hozzáadásának és eltávolításának folyamatát. További információ: [Mi az az Application kiépítés?](../app-provisioning/user-provisioning.md)

## <a name="next-steps"></a>Következő lépések

- [Gyorsindítás sorozat az alkalmazás-kezelésben](view-applications-portal.md)
- [Ismerkedés az alkalmazások integrálásával](plan-an-application-integration.md)
- [Útmutató a kiépítés automatizálásához](../app-provisioning/user-provisioning.md)
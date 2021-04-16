---
title: Mi az alkalmazáskezelés a Azure Active Directory
description: A Azure Active Directory (AD) identitás- és hozzáférés-kezelési (IAM) rendszerként való használatának áttekintése a felhőbeli és helyszíni alkalmazásokhoz.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 01/22/2021
ms.author: iangithinji
ms.reviewer: ''
ms.openlocfilehash: 8bdb8eb9cf176f8e190769e38c81d02ad2985196
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376392"
---
# <a name="what-is-application-management"></a>Mi az alkalmazáskezelés?

Az Azure AD egy identitás- és hozzáférés-kezelési (IAM-) rendszer. Központi helyet biztosít a digitális identitásokkal kapcsolatos információk tárolásához. A szoftveralkalmazásait konfigurálhatja az Azure AD, mint a felhasználói információkat tároló hely használatára. 

Az Azure AD-t úgy kell konfigurálni, hogy integrálható legyen egy alkalmazással. Más szóval tudnia kell, hogy mely alkalmazások használják identitásokhoz. Az Azure AD-nek az alkalmazásokról és azok kezelési mikéntről való tudatában az úgynevezett alkalmazáskezelést.

Az alkalmazásokat a Vállalati **alkalmazások** lapon kezelheti, amely a portál Kezelés Azure Active Directory található.

![Az Azure AD portál Kezelés szakaszában található Vállalati alkalmazások lehetőség.](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>Mi az identitás- és hozzáférés-kezelési (IAM) rendszer?
Az alkalmazások valamilyen célra használt szoftverek. A legtöbb alkalmazáshoz be kell jelentkeznie a felhasználóknak.

A központosított identitásrendszer egyetlen helyet biztosít a felhasználói adatok tárolására, amelyet aztán az összes alkalmazás használni tud. Ezeket a rendszereket identitás- és hozzáférés-kezelési (IAM) rendszereknek is nevezik. Azure Active Directory a Microsoft-felhő IAM-rendszere.

>[!TIP]
>Az IAM-rendszerek egyetlen helyet kínálnak a felhasználói identitások nyomon követéséhez. Az Azure AD a Microsoft-felhő IAM-rendszere.

## <a name="why-manage-applications-with-a-cloud-solution"></a>Miért jó az alkalmazásokat felhőalapú megoldással kezelni?

A szervezetekben gyakran több száz alkalmazás is van, amitől a felhasználók munkavégzése függ. A felhasználók sokféle eszközről és helyről érik el ezeket az alkalmazásokat. Az új alkalmazásokat a rendszer mindig hozzáadja, fejleszti és megszünteti. Ilyen sok alkalmazás és hozzáférési pont esetén fontos, hogy olyan identitásmegoldást használjon, amely mindegyiket használja.

>[!TIP]
>Az Azure AD alkalmazásgyűjteménye számos népszerű alkalmazást tartalmaz, amelyek már előre konfigurálva vannak az Azure AD-val identitásszolgáltatóként való használatra.

## <a name="how-does-azure-ad-work-with-apps"></a>Hogyan működik az Azure AD az alkalmazásokkal?

Az Azure AD középen található, és identitáskezelést biztosít a felhőbeli és helyszíni alkalmazásokhoz. 

![Az Azure AD-n keresztül összevont alkalmazásokat bemutató ábra](media/what-is-application-management/app-management-overview.png)

>[!TIP]
>Csökkentheti az [](../app-provisioning/user-provisioning.md) adminisztratív költségeket a felhasználókiépítés automatizálása által, hogy a felhasználók automatikusan hozzáadódnak az Azure AD-hez, amikor hozzáadja őket a vállalati HR-rendszerhez. 

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Milyen típusú alkalmazások integrálhatók az Azure AD-be?

Az Azure AD-t akár bármilyen alkalmazáshoz használhatja identitásrendszerként. Számos alkalmazás már előre konfigurálva van, és minimális erőfeszítéssel beállítható. Ezek az előre konfigurált alkalmazások a Azure AD alkalmazás [katalógusban vannak közzétéve.](/azure/active-directory/saas-apps/) 

A legtöbb alkalmazást manuálisan konfigurálhatja egyszeri bejelentkezésre, ha még nem létezik a katalógusban. Az Azure AD számos SSO-lehetőséget kínál. A legnépszerűbbek az SAML-alapú SSO és az OIDC-alapú SSO. Az alkalmazások SSO-val való integrálásával kapcsolatos további információkért lásd az egyszeri [bejelentkezés beállításait.](sso-options.md) 

A szervezete helyszíni alkalmazásokat használ? Az alkalmazásproxyval integrálhatja őket. További tudnivalókért lásd: Távoli hozzáférés a helyszíni alkalmazásokhoz az [Azure AD](application-proxy.md)alkalmazásproxy.

>[!TIP]
>Saját üzletági alkalmazásai kiépítésekor integrálhatja őket az Azure AD-be az egyszeri bejelentkezés támogatása érdekében. Az Azure AD-alkalmazások fejlesztéséhez a Microsoft identitásplatformján [olvashat bővebben.](..//develop/v2-overview.md)

## <a name="manage-risk-with-conditional-access-policies"></a>Kockázatkezelés feltételes hozzáférési szabályzatokkal

Az Azure AD egyszeri bejelentkezés (SSO) és a [feltételes hozzáférés](../conditional-access/concept-conditional-access-cloud-apps.md) összekapcsolása magas szintű biztonságot nyújt az alkalmazások eléréséhez. A feltételes hozzáférési szabályzatok részletesen szabályozják az alkalmazásokat a beállított feltételek alapján. 

## <a name="improve-productivity-with-single-sign-on"></a>Termelékenység növelése egyszeri bejelentkezéssel

Az egyszeri bejelentkezés (SSO) egységes felhasználói élményt biztosít a Microsoft 365 és az összes többi ön által használt alkalmazás között. Mondja el, hogy folyamatosan adja meg a felhasználónevét és jelszavát!

Az egyszeri bejelentkezésről további információt az egyszeri bejelentkezést (A mi az egyszeri [bejelentkezés) oldalon olvashat.](what-is-single-sign-on.md)

## <a name="address-governance-and-compliance"></a>Irányítás és megfelelőség

Az alkalmazások figyelése biztonsági incidens- és eseményfigyelési (SIEM-) eszközöket (Security Incident and Event Monitoring, biztonsági incidens- és eseményfigyelési) eszközöket jelentéseket használva. A jelentéseket a portálról vagy az API-kból érheti el. Programozott módon naplózza, ki fér hozzá az alkalmazásokhoz és megszünteti az inaktív felhasználók hozzáférését a hozzáférési felülvizsgálatokkal.

## <a name="manage-costs"></a>Költségek kezelése

Az Azure AD-re történő átállással költségeket takarít meg és megszabadul a helyszíni infrastruktúra kezelésével járó gondoktól. Az Azure AD önkiszolgáló hozzáférést is nyújt az alkalmazásokhoz, amivel a rendszergazdák és felhasználók számára is időt takarít meg. Az egyszeri bejelentkezés használata esetén nincs szükség alkalmazásspecifikus jelszavakra. Az egyszeri bejelentkezéssel megtakaríthatók az új alkalmazásjelszavak kéréséhez és a jelszavak beszerzése közben kieső termelékenységhez kapcsolódó költségek.

Az emberierőforrás-központú alkalmazások vagy a felhasználók nagy készletét használó más alkalmazások esetében az alkalmazás-kiépítéssel megkönnyítheti az életét. Az alkalmazás kiépítése automatizálja a felhasználók hozzáadásának és eltávolításának folyamatát. További tudnivalókért lásd: [Mi az az alkalmazás-kiépítés?](../app-provisioning/user-provisioning.md)

## <a name="next-steps"></a>Következő lépések

- [Gyorsútmutató-sorozat az alkalmazáskezelésről](view-applications-portal.md)
- [Az alkalmazásintegráció első lépések](plan-an-application-integration.md)
- [A kiépítés automatizálásának elsajátítása](../app-provisioning/user-provisioning.md)
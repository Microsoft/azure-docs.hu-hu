---
title: Gyakori kérdések (GYIK) – Azure Active Directory | Microsoft Docs
description: Gyakori kérdések és válaszok az Azure és a Azure Active Directory, a jelszavas kezelés és az alkalmazás-hozzáférés terén.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: troubleshooting
ms.date: 11/12/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3b9f76eba9697d6ad47ecf8dc51689538923f88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101094479"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Gyakori kérdések a Azure Active Directory
Az Azure Active Directory (Azure AD) egy átfogó szolgáltatott identitási (IDaaS) megoldás, amely az identitások, a hozzáférés-kezelés és a biztonság minden szempontját lefedi.

További információkért lásd: [Mi az az Azure Active Directory?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Az Azure és az Azure Active Directory elérése
**K: Miért kapok "nem találhatók előfizetések" kifejezést, amikor megpróbálom elérni az Azure AD-t a Azure Portal?**

**V:** Az Azure Portal eléréséhez mindegyik felhasználónak engedélyekre van szüksége egy Azure-előfizetés keretében. Ha nem rendelkezik fizetős Microsoft 365 vagy Azure AD-előfizetéssel, aktiválnia kell egy ingyenes [Azure-fiókot](https://azure.microsoft.com/free/
) vagy egy fizetős előfizetést.

További információkért lásd:

* [How Azure subscriptions are associated with Active Directory? (Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directory-hoz?)](active-directory-how-subscriptions-associated-directory.md)

---
**K: milyen kapcsolat áll fenn az Azure AD, a Microsoft 365 és az Azure között?**

**V:** Az Azure AD általános identitás- és hozzáférési képességeket nyújt az összes webes szolgáltatáshoz. Akár Microsoft 365t, Microsoft Azuret, Intune-t vagy másokat használ, már használja az Azure AD-t a bejelentkezés és a hozzáférés-kezelés bekapcsolásához az összes szolgáltatáshoz.

A webes szolgáltatások használatához beállított összes felhasználó felhasználói fiókként van meghatározva egy vagy több Azure AD-példányban. Beállíthatja ezeket a fiókokat az ingyenes Azure AD-képességekhez, például a felhőalkalmazások eléréséhez.

Az Azure AD fizetős szolgáltatásai, például a Enterprise Mobility + Security kiegészítik az olyan webszolgáltatásokat, mint a Microsoft 365 és a Microsoft Azure átfogó, nagyvállalati szintű felügyeleti és biztonsági megoldásokkal.

---

**K: mi a különbség a tulajdonos és a globális rendszergazda között?**

**A:** Alapértelmezés szerint az Azure-előfizetésre feliratkozott személy a tulajdonos szerepkört rendeli hozzá az Azure-erőforrásokhoz. A tulajdonos Microsoft-fiók vagy munkahelyi vagy iskolai fiókot is használhat a címtárból, amelyhez az Azure-előfizetés társítva van.  Ez a szerepkör jogosult szolgáltatások kezelésére az Azure Portalon.

Ha másoknak ugyanazzal az előfizetéssel kell bejelentkezniük, és hozzá kell férniük a szolgáltatásokhoz, a megfelelő [beépített szerepkört](../../role-based-access-control/built-in-roles.md)is hozzárendelheti. További információ: Azure- [szerepkörök kiosztása a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).

Alapértelmezés szerint az Azure-előfizetésre feliratkozott személy a globális rendszergazdai szerepkört rendeli hozzá a címtárhoz. A globális rendszergazda hozzáfér az összes Azure AD Directory-szolgáltatáshoz. Az Azure AD különböző rendszergazdai szerepkörökkel rendelkezik a címtár és az identitással kapcsolatos funkciók kezeléséhez. Ezek a rendszergazdák hozzáférhetnek a Azure Portal különböző szolgáltatásaihoz. A rendszergazda szerepköre határozza meg, hogy mit tehetnek, például felhasználók létrehozása vagy szerkesztése, rendszergazdai szerepkörök kiosztása mások számára, felhasználói jelszavak alaphelyzetbe állítása, felhasználói licencek kezelése vagy tartományok kezelése.  Az Azure AD címtár-rendszergazdákkal és azok szerepköreivel kapcsolatos további információkért lásd: [felhasználó társítása rendszergazdai szerepkörökhöz Azure Active Directory](active-directory-users-assign-role-azure-portal.md) és [rendszergazdai szerepkörök kiosztása a Azure Active Directoryban](../roles/permissions-reference.md).

Emellett az Azure AD fizetős szolgáltatásai, például a Enterprise Mobility + Security kiegészítik az olyan webszolgáltatásokat, mint a Microsoft 365 és a Microsoft Azure, átfogó, nagyvállalati szintű felügyeleti és biztonsági megoldásokkal.

---
**K: Létezik olyan jelentés, amely megmutatja, hogy mikor járnak le az Azure AD-beli felhasználói licenceim?**

**A:** nem.  Ez jelenleg nem érhető el.

---

## <a name="get-started-with-hybrid-azure-ad"></a>Első lépések a Hybrid Azure AD-ben


**K: Hogyan hagyhatok el egy bérlőt, amikor közreműködőként vagyok hozzáadva?**

**V:** Amikor más szervezet bérlőjéhez van hozzáadva közreműködőként, a jobb felső sarokban lévő „bérlőváltóval” válthat a bérlők között.  Jelenleg nem hagyhatja el a meghívó szervezetet, és a Microsoft dolgozik ezen a funkción.  A funkció eléréséig megkérheti a meghívó szervezetet, hogy távolítsa el Önt a bérlők közül.

---
**K: Hogyan csatlakoztathatom a helyszíni címtáramat az Azure AD-hez?**

**V:** A helyszíni címtárát az Azure AD Connect használatával csatlakoztathatja az Azure AD-hez.

További információkért lásd: [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md).

---
**K: Hogyan állíthatok be egyszeri bejelentkezést a helyszíni címtár és a felhőalkalmazásaim között?**

**V:** Csak a helyszíni címtár és az Azure AD között kell beállítania egyszeri bejelentkezést (SSO-t). Amíg az Azure AD-n keresztül éri el a felhőalkalmazásokat, a szolgáltatás automatikusan átirányítja a felhasználókat, hogy megfelelően hitelesíteni tudják magukat a helyszíni hitelesítő adataikkal.

A helyszíni egyszeri bejelentkezés megvalósítása egyszerűen megvalósítható összevonási megoldásokkal, például Active Directory összevonási szolgáltatások (AD FS) (AD FS), vagy a jelszó-kivonat szinkronizálásának konfigurálásával. A Azure AD Connect konfigurációs varázslóval könnyedén telepítheti mindkét lehetőséget.

További információkért lásd: [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md).

---
**K: Az Azure AD-ben elérhető önkiszolgáló portál a szervezetemben lévő felhasználók számára?**

**V:** Igen, az Azure AD az [Azure AD Hozzáférési panelén](https://myapps.microsoft.com) keresztül biztosítja a felhasználóknak az önkiszolgáló felületet és az alkalmazások elérését. Ha Ön Microsoft 365 ügyfél, az [Office 365-portálon](https://portal.office.com)számos funkció közül választhat.

További információkért lásd: [Bevezetés a Hozzáférési panel használatába](../user-help/my-apps-portal-end-user-access.md).

---
**K: Az Azure AD segít a helyszíni infrastruktúrám kezelésében?**

**V:** Igen. Az Azure AD Premium Edition tartalmazza az Azure AD Connect Health eszközt. Az Azure AD Connect Health segít megfigyelni a helyszíni identitás-infrastruktúrát és a szinkronizálási szolgáltatásokat, és további betekintést nyújt a működésükbe.  

További információkért lásd: [A helyszíni identitás-infrastruktúra és a szinkronizálási szolgáltatások megfigyelése a felhőben](../hybrid/whatis-azure-ad-connect.md).  

---
## <a name="password-management"></a>Jelszókezelés
**K: használhatom az Azure AD-jelszavakat jelszó-szinkronizálás nélkül? (Ebben az esetben lehetséges az Azure AD önkiszolgáló jelszó-visszaállítás (SSPR) használata a jelszó-visszaírással, és nem tárolja a jelszavakat a felhőben?)**

**V:** Nem kell szinkronizálnia az Active Directory-jelszavakat az Azure AD-vel a visszaírás engedélyezéséhez. Összevont környezetben az Azure AD egyszeri bejelentkezés (SSO) a helyszíni címtárra támaszkodik a felhasználó hitelesítéséhez. Ehhez a forgatókönyvhöz nem kell nyomon követni a helyszíni jelszót az Azure AD-ben.

---
**K: Mennyi ideig tart a jelszavak visszaírása az Active Directoryban a helyszínen?**

**V:** A jelszavak visszaírása valós időben történik.

További részletekért lásd: [A jelszókezelés első lépései](../authentication/tutorial-enable-sspr.md).

---
**K: Használhatok jelszóvisszaírást rendszergazda által kezelt jelszavakhoz?**

**V:** Igen, ha engedélyezett a jelszóvisszaírás, a rendszergazda által végzett jelszóműveleteket a rendszer visszaírja a helyszíni környezetbe.  

<a name="for-more-answers-to-password-related-questions-see-password-management-frequently-asked-questions"></a>A jelszavakkal kapcsolatos kérdésekre adott további válaszokért lásd: [Jelszókezelés – gyakori kérdések](../authentication/active-directory-passwords-faq.md).
---
**K: Mit tehetek, ha nem emlékszem a meglévő Microsoft 365/Azure AD-jelszóra, miközben megpróbálom megváltoztatni a jelszavát?**

**V:** Ilyen helyzetben több lehetőség közül választhat.  Használjon önkiszolgáló jelszó-visszaállítást (SSPR), ha elérhető.  Az SSPR konfigurációjától függ, hogy működik-e.  További információkért lásd: [A jelszó-visszaállítási portál működése](../authentication/howto-sspr-deployment.md).

Microsoft 365 felhasználók esetében a rendszergazda alaphelyzetbe állíthatja a jelszót a [felhasználói jelszavak alaphelyzetbe állítása](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US)című témakörben leírtak szerint.

Azure AD-felhasználók esetében a rendszergazdák a következő módszerek valamelyikével állíthatják alaphelyzetbe a jelszót:

- [Fiókok alaphelyzetbe állítása az Azure Portalon](active-directory-users-reset-password-azure-portal.md)
- [A PowerShell használata](/powershell/module/msonline/set-msoluserpassword)


---
## <a name="security"></a>Biztonság
**K: Zárolja a rendszer a fiókokat egy adott számú hibás kísérlet után, vagy ennél kifinomultabb stratégiát alkalmaz?**

A fiókok zárolásához ennél kifinomultabb stratégiát alkalmazunk.  A zárolás a kérés IP-címe és a megadott jelszavak alapján történik. A zárolás időtartama annál hosszabb, minél inkább valószínűsíthető, hogy támadásról van szó.  

**K: bizonyos (gyakori) jelszavak elutasítása a "Ez a jelszó sokszor használatban van" üzenettel, ez a jelenlegi Active Directoryban használt jelszavakra vonatkozik?**

Ez olyan jelszavakra utal, amelyek globálisan gyakoriak, például a "password" és a "123456" bármely változata.

**K: Blokkolja a rendszer a kétes forrásokból (botnetek, TOR-végpontok) érkező bejelentkezési kéréseket a B2C-bérlőkön, vagy ehhez alap- vagy prémium szintű bérlőre van szükség?**

Az átjárónk szűri a kéréseket és bizonyos fokú védelmet biztosít a botnetek ellen. Ez minden B2C-bérlőre vonatkozik.

## <a name="application-access"></a>Alkalmazás-hozzáférés

**K: Hol találom az Azure Ad-vel előre integrált alkalmazások és azok képességeinek listáját?**

**V:** Az Azure AD a Microsoft vállalat, az alkalmazásszolgáltatók és a partnerek több mint 2600 előre integrált alkalmazásával rendelkezik. Mindegyik előre integrált alkalmazás támogatja az egyszeri bejelentkezést (SSO). Az egyszeri bejelentkezéssel a szervezeti hitelesítő adatokkal érheti el az alkalmazásokat. Néhány alkalmazás az automatikus üzembe helyezést és megszüntetést is támogatja.

Az előre integrált alkalmazások teljes listájáért lásd: [Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory).

---
**K: Mit tegyek, ha a kívánt alkalmazás nem szerepel az Azure AD Marketplace-en?**

**V:** Az Azure AD Premiumban bármely alkalmazást felveheti és konfigurálhatja. Az alkalmazás képességeitől és a beállításaitól függően beállíthatja az egyszeri bejelentkezést és az automatizált üzembe helyezést.  

További információkért lásd:

* [Egyszeri bejelentkezés konfigurálása az Azure Active Directory alkalmazáskatalógusában nem szereplő alkalmazásokhoz](../manage-apps/configure-saml-single-sign-on.md)
* [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](../app-provisioning/use-scim-to-provision-users-and-groups.md)

---
**K: Hogyan jelentkeznek be a felhasználók az alkalmazásokba az Azure AD-vel?**

**V:** Az Azure AD több módot nyújt arra, hogy a felhasználók megtekinthessék és elérhessék az alkalmazásaikat, például a következőket:

* Azure AD Hozzáférési panel
* A Microsoft 365 Application Launcher
* Közvetlen bejelentkezés az összevont alkalmazásokba
* Mélyhivatkozások az összevont, jelszóalapú vagy meglévő alkalmazásokhoz

További információ: [végfelhasználói élmények az alkalmazásokhoz](../manage-apps/end-user-experiences.md).

---
**K: Az Azure AD milyen különböző módokon teszi lehetővé a hitelesítést és az egyszeri bejelentkezést az alkalmazásokba?**

**V:** Az Azure AD számos szabványos protokollt támogat a hitelesítéshez és az engedélyezéshez, például ilyen a SAML 2.0, az OpenID Connect, az OAuth 2.0 és a WS-Federation. Az Azure AD a jelszótárolást és az automatikus bejelentkezési képességeket is támogatja olyan alkalmazásoknál, amelyek csak az űrlapalapú hitelesítést támogatják.  

További információkért lásd:

* [Hitelesítési forgatókönyvek az Azure AD-hez](../develop/authentication-vs-authorization.md)
* [Hitelesítési protokollok Active Directory](/previous-versions/azure/dn151124(v=azure.100))
* [Egyszeri bejelentkezés az Azure AD-beli alkalmazásokhoz](../manage-apps/what-is-single-sign-on.md)

---
**K: Hozzáadhatok olyan alkalmazásokat, amelyeken a helyszínen fut?**

**V:** Az Azure AD-alkalmazásproxy egyszerű és biztonságos hozzáférést nyújt az Ön által választott helyszíni webalkalmazásokhoz. Ezeket az alkalmazásokat ugyanúgy érheti el, ahogyan a szolgáltatott szoftverek (SaaS) alkalmazásokat az Azure AD-ben. Nincs szükség VPN-re vagy a hálózati infrastruktúra módosítására.  

További információért lásd: [Helyszíni alkalmazások biztonságos távoli elérése](../manage-apps/application-proxy.md).

---
**K: Hogyan követelhetem meg a többtényezős hitelesítést az adott alkalmazásokhoz hozzáféréssel rendelkező felhasználóknál?**

**A:** Az Azure AD feltételes hozzáférésével minden alkalmazáshoz hozzárendelhet egyedi hozzáférési szabályzatot. A házirendjében mindig megkövetelheti a többtényezős hitelesítést, vagy csak akkor, amikor a felhasználók nem csatlakoznak a helyi hálózathoz.  

További információkért lásd: [hozzáférés biztonságossá tétele Microsoft 365okhoz és más, Azure Active Directoryhoz csatlakoztatott alkalmazásokhoz](../conditional-access/overview.md).

---
**K: Mi az az automatizált felhasználói kiépítés SaaS-alkalmazásokhoz?**

**V:** Az Azure AD-vel automatizálhatja a felhasználói identitások létrehozását, karbantartását és eltávolítását számos népszerű felhőalapú SaaS alkalmazásban.

További információ: [A felhasználókiépítés és -megszüntetés automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).

---
**K: Állíthatok be biztonságos LDAP-kapcsolatot az Azure AD-vel?**

**A:**  nem. Az Azure AD nem támogatja a Lightweight Directory Access Protocol (LDAP) protokollt vagy Secure LDAP közvetlenül. Az Azure AD-bérlőn azonban lehetőség van arra, hogy a megfelelő módon konfigurált hálózati biztonsági csoportokkal engedélyezze az Azure-beli Azure AD Domain Services (Azure AD DS) példányt az LDAP-kapcsolat eléréséhez. További információ: [biztonságos LDAP konfigurálása Azure Active Directory Domain Services felügyelt tartományhoz](../../active-directory-domain-services/tutorial-configure-ldaps.md)

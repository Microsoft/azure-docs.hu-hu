---
title: Azure Identity & hozzáférési biztonsági eljárások | Microsoft Docs
description: Ez a cikk az Identitáskezelés és a hozzáférés-vezérlés ajánlott eljárásait ismerteti az Azure beépített képességeinek használatával.
services: security
documentationcenter: na
author: terrylanfear
manager: RKarlin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: b609d8389504eb10dd35a5ab74b0c198d3da57a6
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090492"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Az Azure Identity Management és a hozzáférés-vezérlés biztonsága – ajánlott eljárások

Ebben a cikkben az Azure Identity Management és a hozzáférés-vezérléssel kapcsolatos ajánlott biztonsági eljárások gyűjteményét tárgyaljuk. Ezek az ajánlott eljárások az [Azure ad](../../active-directory/fundamentals/active-directory-whatis.md) -vel és az ügyfelek, például saját felhasználói élményekkel kapcsolatos tapasztalataiból származnak.

Minden ajánlott eljáráshoz a következőket magyarázza el:

* Az ajánlott eljárás
* Miért érdemes engedélyezni az ajánlott eljárásokat
* Mi lehet az eredmény, ha nem sikerül engedélyezni az ajánlott eljárást
* Az ajánlott eljárás lehetséges alternatívái
* Hogyan sajátíthatja el az ajánlott eljárásokat

Az Azure Identity Management és a hozzáférés-vezérlés biztonsági eljárásairól szóló cikk konszenzusos véleményt és az Azure platform képességeit és funkcióit ismerteti, amelyek a cikk írásának időpontjában szerepelnek.

A cikk írásának szándéka az, hogy az üzembe helyezést követően az "[5 lépés az identitás-infrastruktúra biztonságossá](steps-secure-identity.md)tételéhez" című ellenőrzőlista általános ütemtervet nyújtson a biztonsági helyzethez.

A vélemények és technológiák idővel változnak, és ez a cikk rendszeresen frissül, hogy tükrözze ezeket a módosításokat.

Az Azure Identity Management és a hozzáférés-vezérléssel kapcsolatos ajánlott biztonsági eljárások a jelen cikkben tárgyalják a következőket:

* Identitás kezelése elsődleges biztonsági peremhálózatként
* Az Identitáskezelés központosított kezelése
* Csatlakoztatott bérlők kezelése
* Egyszeri bejelentkezés engedélyezése
* Feltételes hozzáférés bekapcsolása
* A rutin biztonsági tökéletesítésének megtervezése
* Jelszókezelés engedélyezése
* A multi-Factor-ellenőrzés érvényesítése a felhasználók számára
* Szerepköralapú hozzáférés-vezérlés alkalmazása
* A Kiemelt jogosultságú fiókok alacsonyabb kitettsége
* Az erőforrások helyének vezérlése
* Az Azure AD használata tárolási hitelesítéshez

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Identitás kezelése elsődleges biztonsági peremhálózatként

Az identitások többsége elsődleges biztonsági szempontoknak számít. Ez a hálózati biztonság hagyományos fókuszának eltolódása. A hálózati kerületek folyamatosan egyre több porózus, és a szegélyhálózati védelem nem lehet olyan hatékony, mint a [BYOD](/mem/intune/fundamentals/byod-technology-decisions) -eszközök és a Felhőbeli alkalmazások robbanása előtt.

A [Azure Active Directory (Azure ad)](../../active-directory/fundamentals/active-directory-whatis.md) az identitás-és hozzáférés-kezeléshez használható Azure-megoldás. Az Azure AD egy több-bérlős, felhőalapú címtár-és Identitáskezelő szolgáltatás a Microsofttól. Egyetlen megoldásban egyesíti az alapvető címtárszolgáltatásokat, az alkalmazáshozzáférés-kezelést és az identitásvédelmet.

Az alábbi fejezetek az identitás-és hozzáférés-biztonságra vonatkozó ajánlott eljárásokat sorolja fel az Azure AD használatával.

**Ajánlott eljárás**: a központ biztonsági ellenőrzése és észlelése a felhasználói és szolgáltatásbeli identitások köré.
**Részletek**: az Azure ad használatával rézvezetékes végezhet a vezérlők és az identitások.

## <a name="centralize-identity-management"></a>Az Identitáskezelés központosított kezelése

[Hibrid identitási](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) helyzetekben javasoljuk, hogy integrálja a helyszíni és a Felhőbeli címtárakat. Az integráció lehetővé teszi az IT-csoportnak, hogy a fiókokat egy adott helyről kezelhesse, függetlenül attól, hogy hol hoztak létre. Az integráció a Felhőbeli és a helyszíni erőforrások elérésének közös identitásával is segíti a felhasználókat.

**Ajánlott eljárás**: egyetlen Azure ad-példány létrehozása. A konzisztencia és az egyetlen mérvadó forrás is növeli az átláthatóságot, és csökkenti a biztonsági kockázatokat az emberi hibák és a konfiguráció bonyolultsága miatt.
**Részletek**: egyetlen Azure ad-címtár kijelölése a vállalati és a szervezeti fiókok mérvadó forrásaként.

**Ajánlott eljárás**: a helyszíni címtárak integrálása az Azure ad-vel.  
**Részletek**: [Azure ad Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) használatával szinkronizálja a helyszíni címtárat a felhő címtárával.

> [!Note]
> Vannak olyan [tényezők, amelyek hatással vannak a Azure ad Connect teljesítményére](../../active-directory/hybrid/plan-connect-performance-factors.md). Győződjön meg arról, Azure AD Connect elegendő kapacitással rendelkezik ahhoz, hogy a rendszerek ne akadályozzák a biztonságot és a termelékenységet. A nagyméretű vagy összetett szervezeteknek (szervezeteknek több mint 100 000 objektumot kell kiépíteniük) az [ajánlásokat](../../active-directory/hybrid/whatis-hybrid-identity.md) követve optimalizálhatja Azure ad Connect megvalósítását.

**Ajánlott eljárás**: ne szinkronizálja a fiókokat az Azure ad-be, amelyek magas szintű jogosultságokkal rendelkeznek a meglévő Active Directory-példányban.
**Részletek**: ne módosítsa az alapértelmezett [Azure ad Connect konfigurációt](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) , amely kiszűri ezeket a fiókokat. Ez a konfiguráció csökkenti a felhőből a helyszíni eszközökre irányuló ellenfelek (ami jelentős incidenst eredményezhet) kockázatát.

**Ajánlott eljárás**: a jelszó-kivonat szinkronizálásának bekapcsolása.  
**Részletek**: a jelszó-kivonatolási szinkronizálás egy helyszíni Active Directory-példányról egy FELHŐALAPÚ Azure ad-példányra történő felhasználói jelszó-kivonatok szinkronizálására szolgáló szolgáltatás. Ez a szinkronizálás segít megvédeni a korábbi támadásokból visszajátszott kiszivárgott hitelesítő adatokat.

Még ha úgy dönt, hogy összevonást használ Active Directory összevonási szolgáltatások (AD FS) (AD FS) vagy más identitás-szolgáltatókkal, beállíthatja a jelszó-kivonat szinkronizálását biztonsági mentésként, ha a helyszíni kiszolgálók meghibásodnak vagy átmenetileg elérhetetlenné válnak. Ez a szinkronizálás lehetővé teszi a felhasználók számára, hogy ugyanazzal a jelszóval jelentkezzenek be a szolgáltatásba, amelyet a helyszíni Active Directory-példányba való bejelentkezéshez használnak. Azt is lehetővé teszi, hogy az Identity Protection észlelje a feltört hitelesítő adatokat, összehasonlítva a szinkronizált jelszó-kivonatokat a biztonsággal ismert jelszavakkal, ha a felhasználó ugyanazt az e-mail címet és jelszót használta más olyan szolgáltatásokon, amelyek nem csatlakoznak az Azure AD-hez.

További információ: jelszó- [kivonatolási szinkronizálás implementálása Azure ad Connect szinkronizálással](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

**Ajánlott eljárás**: az új alkalmazások fejlesztéséhez használja az Azure ad-t a hitelesítéshez.
**Részletek**: a hitelesítés támogatásához használja a megfelelő képességeket:

  - Azure AD az alkalmazottak számára
  - [Azure ad B2B](../../active-directory/external-identities/index.yml) vendég felhasználók és külső partnerek számára
  - [Azure ad B2C](../../active-directory-b2c/index.yml) annak szabályozásához, hogy az ügyfelek hogyan regisztrálhatnak, jelentkezhetnek be és kezelhetik a profiljaikat az alkalmazások használatakor

Azok a szervezetek, amelyek nem integrálják a helyszíni identitást a Felhőbeli identitással, nagyobb terheléssel rendelkezhetnek a fiókok kezelésében. Ez a terhelés növeli a hibák és a biztonsági rések valószínűségét.

> [!Note]
> Ki kell választania, hogy mely címtárakban kritikus fiókok fognak megjelenni, és hogy a használt rendszergazdai munkaállomást az új Cloud Services vagy a meglévő folyamatok felügyelik-e. A meglévő felügyeleti és identitás-kiépítési folyamatok használata csökkentheti a kockázatokat, de a támadók kockázatát is veszélyeztethetik a helyszíni fiókok és a felhőbe való belátások. Érdemes lehet más stratégiát használni a különböző szerepkörökhöz (például rendszergazdák és üzleti egység adminisztrátorai). Itt két lehetősége van. Az első lehetőség olyan Azure AD-fiókok létrehozása, amelyek nincsenek szinkronizálva a helyszíni Active Directory-példánnyal. Csatlakoztassa a felügyeleti munkaállomást az Azure AD-hez, amelyet Microsoft Intune használatával kezelhet és javíthat. A második lehetőség a meglévő rendszergazdai fiókok használata a helyszíni Active Directory-példányra való szinkronizálással. A Active Directory tartományban meglévő munkaállomásokat kezelheti felügyeletre és biztonságra.

## <a name="manage-connected-tenants"></a>Csatlakoztatott bérlők kezelése
A biztonsági szervezetnek meg kell vizsgálnia a kockázat felmérését, és meg kell határoznia, hogy a szervezet szabályzatait és az összes szabályozási követelményt követi-e a rendszer. Győződjön meg arról, hogy a biztonsági szervezet az éles környezethez és a hálózathoz (az [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) vagy [a helyek közötti VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)-en keresztül) kapcsolódó összes előfizetéshez betekintést kapott. Az Azure AD [globális rendszergazdája](../../active-directory/roles/permissions-reference.md#global-administrator-permissions) hozzáférhet a [felhasználói hozzáférés rendszergazdai](../../role-based-access-control/built-in-roles.md#user-access-administrator) szerepköréhez, és megtekintheti a környezethez csatlakoztatott összes előfizetést és felügyelt csoportot.

Lásd: [jogosultságszint-emelési hozzáférés az összes Azure-előfizetés és-felügyeleti csoport kezeléséhez](../../role-based-access-control/elevate-access-global-admin.md) , hogy Ön és biztonsági csoportja megtekintse a környezetéhez kapcsolódó összes előfizetést vagy felügyeleti csoportot. A kockázatok felmérése után távolítsa el ezt a emelt szintű hozzáférést.

## <a name="enable-single-sign-on"></a>Egyszeri bejelentkezés engedélyezése

A mobil-első, Felhőbeli első világban az egyszeri bejelentkezést (SSO) az eszközökre, alkalmazásokra és szolgáltatásokra szeretné engedélyezni bárhonnan, így a felhasználók bárhol és bármikor dolgozhatnak. Ha több identitási megoldással rendelkezik, ez a felügyeleti probléma nem csupán az, hanem a több jelszót megjegyező felhasználók számára is.

Ha ugyanazt az identitás-megoldást használja az összes alkalmazásához és erőforrásához, az SSO-t is elérheti. És a felhasználók ugyanazt a hitelesítő adatokat használhatják a bejelentkezéshez és a szükséges erőforrásokhoz való hozzáféréshez, függetlenül attól, hogy az erőforrások a helyszínen vagy a felhőben találhatók-e.

**Ajánlott eljárás**: engedélyezze az egyszeri bejelentkezést.  
**Részletek**: az Azure ad [kiterjeszti a helyszíni Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md) a felhőre. A felhasználók saját elsődleges munkahelyi vagy iskolai fiókját használhatják a tartományhoz csatlakoztatott eszközökhöz, a vállalati erőforrásokhoz, valamint az összes olyan webes és SaaS-alkalmazáshoz, amelyre a munkájuk elvégzéséhez szükségük van. A felhasználóknak nem kell megemlékezniük több Felhasználónév és jelszó megadására, és az alkalmazásuk hozzáférése automatikusan kiépíthető (vagy kiépíthető), a szervezeti csoporttagság és az alkalmazotti állapotuk alapján. A katalógusban szereplő alkalmazások, illetve az [Azure AD-alkalmazásproxy](../../active-directory/manage-apps/application-proxy.md) használatával kifejlesztett és közzétett saját helyszíni alkalmazások hozzáférése is szabályozható.

Az egyszeri bejelentkezés lehetővé teszi, hogy a felhasználók a munkahelyi vagy iskolai fiókjuk alapján hozzáférjenek az [SaaS-alkalmazásokhoz](../../active-directory/manage-apps/what-is-single-sign-on.md) az Azure ad-ben. Ez nem csak a Microsoft SaaS-alkalmazások, hanem más alkalmazások, például a [Google Apps](../../active-directory/saas-apps/google-apps-tutorial.md) és a [Salesforce](../../active-directory/saas-apps/salesforce-tutorial.md)esetében is érvényes. Beállíthatja, hogy az alkalmazás az Azure AD-t [SAML-alapú identitás-](../../active-directory/fundamentals/active-directory-whatis.md) szolgáltatóként használja. Biztonsági ellenőrzésként az Azure AD nem ad ki jogkivonatot, amely lehetővé teszi, hogy a felhasználók bejelentkezzenek az alkalmazásba, hacsak nem kaptak hozzáférést az Azure AD-n keresztül. Közvetlenül is megadhat hozzáférést, vagy egy olyan csoporton keresztül, amely a felhasználók tagja.

Azok a szervezetek, amelyek nem hoznak létre közös identitást az SSO létrehozásához a felhasználók és az alkalmazások számára, jobban ki vannak téve olyan forgatókönyvek esetén, ahol a felhasználóknak több jelszava van. Ezek a forgatókönyvek nagyobb valószínűséggel használják a felhasználókat a jelszavak újrafelhasználására vagy a gyenge jelszavak használatára.

## <a name="turn-on-conditional-access"></a>Feltételes hozzáférés bekapcsolása

A felhasználók különböző eszközök és alkalmazások segítségével bárhonnan hozzáférhetnek a szervezet erőforrásaihoz. Rendszergazdaként meg kell győződnie arról, hogy ezek az eszközök megfelelnek a biztonsági és megfelelőségi szabványoknak. Csak arra összpontosíthat, hogy ki férhet hozzá egy erőforráshoz, már nem elegendő.

A biztonság és a termelékenység egyensúlya érdekében meg kell gondolnia, hogyan érhető el egy erőforrás, mielőtt döntést hozna a hozzáférés-vezérlésről. Az Azure AD feltételes hozzáférésével ezt a követelményt kezelheti. A feltételes hozzáférés révén automatizált hozzáférés-vezérlési döntéseket hozhat a felhőalapú alkalmazások elérésére vonatkozó feltételek alapján.

**Ajánlott eljárás**: a vállalati erőforrásokhoz való hozzáférés kezelése és szabályozása.  
**Részletek**: a közös Azure ad [feltételes hozzáférési szabályzatok](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) konfigurálása az SaaS-alkalmazások és az Azure ad-hez csatlakoztatott alkalmazások csoport, hely és alkalmazás érzékenysége alapján.

**Ajánlott eljárás**: az örökölt hitelesítési protokollok letiltása.
**Részletek**: a támadók minden nap kihasználják a régebbi protokollok gyengeségeit, különösen a jelszó-szórásos támadásokhoz. Feltételes hozzáférés konfigurálása az [örökölt protokollok blokkolásához](../../active-directory/conditional-access/howto-conditional-access-policy-block-legacy.md).

## <a name="plan-for-routine-security-improvements"></a>A rutin biztonsági tökéletesítésének megtervezése

A biztonság mindig folyamatosan fejlődik, és fontos, hogy a Felhőbeli és az Identity Management-keretrendszerbe építsen egy olyan módszert, amely rendszeresen mutatja a növekedést, és új módszereket biztosít a környezet biztonságossá tételéhez.

Az identitások biztonságos pontszáma olyan ajánlott biztonsági vezérlők összessége, amelyeket a Microsoft tesz közzé, hogy számszerű pontszámot biztosítson a biztonsági helyzet tárgyilagos méréséhez, és segítsen a jövőbeli biztonsági fejlesztések megtervezésében. A pontszámokat a többi iparágban is megtekintheti, a saját trendek pedig az idő múlásával is.

**Ajánlott eljárás**: az iparágban ajánlott eljárások alapján tervezze meg a rutin biztonsági felülvizsgálatait és fejlesztéseit.
**Részletek**: az identitások biztonságos pontszáma funkció használatával rangsorolhatja a módosításokat az idő múlásával.

## <a name="enable-password-management"></a>Jelszókezelés engedélyezése

Ha több Bérlővel rendelkezik, vagy engedélyezni szeretné a felhasználók számára a [saját jelszavának alaphelyzetbe állítását](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md), fontos, hogy a megfelelő biztonsági házirendeket használja a visszaélések elkerülése érdekében.

**Ajánlott eljárás**: az önkiszolgáló jelszó-visszaállítás (SSPR) beállítása a felhasználók számára.  
**Részletek**: használja az Azure ad önkiszolgáló [jelszó-visszaállítási](../../active-directory-b2c/user-flow-self-service-password-reset.md) funkcióját.

**Ajánlott eljárás**: figyelje, hogy a SSPR valóban használatban van-e.  
**Részletek**: figyelje meg, hogy mely felhasználók regisztrálnak az Azure ad [jelszó-visszaállítási regisztrációs tevékenység jelentés](../../active-directory/authentication/howto-sspr-reporting.md)használatával. Az Azure AD által biztosított jelentéskészítési funkció segítséget nyújt az előre elkészített jelentések használatával kapcsolatos kérdések megválaszolásához. Ha megfelelő licenccel rendelkezik, egyéni lekérdezéseket is létrehozhat.

**Ajánlott eljárás**: a felhőalapú jelszóházirendek kiterjesztése a helyszíni infrastruktúrára.
**Részletek**: a szervezet jelszavas házirendjeinek javítása a helyi jelszó módosításainak megadásával ugyanúgy végezheti el a jelszavakat, mint a felhőalapú jelszó módosításakor. Telepítse az [Azure ad-jelszavas védelmet](../../active-directory/authentication/concept-password-ban-bad.md) a Windows Server Active Directory-ügynököknek a helyszínen, hogy kiterjessze a tiltott jelszavak listáját a meglévő infrastruktúrára. Azok a felhasználók és rendszergazdák, akik a helyszíni jelszavakat módosítják, beállítják vagy alaphelyzetbe állítják, a csak felhőalapú felhasználókra vonatkozó jelszóházirend megtartásához szükségesek.

## <a name="enforce-multi-factor-verification-for-users"></a>A multi-Factor-ellenőrzés érvényesítése a felhasználók számára

Javasoljuk, hogy az összes felhasználóra vonatkozóan kétlépéses ellenőrzést igényeljen. Ebbe beletartozik a szervezet rendszergazdái és más tagjai, akik jelentős hatással lehetnek a fiókjuk biztonságára (például pénzügyi tisztviselők).

Több lehetőség is van a kétlépéses ellenőrzés megkövetelésére. A legjobb megoldás az Ön által futtatott céloktól, az Azure AD-kiadástól és a licencelési programtól függ. Tekintse meg, [hogyan kell kétlépéses ellenőrzést megkövetelni a felhasználók](../../active-directory/authentication/howto-mfa-userstates.md) számára a legjobb megoldás meghatározásához. A licencekkel és a díjszabással kapcsolatos további információkért tekintse meg az [Azure ad](https://azure.microsoft.com/pricing/details/active-directory/) és az [Azure ad multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) díjszabási oldalát.

A kétlépéses ellenőrzés engedélyezésének lehetőségei és előnyei a következők:

**1. lehetőség**: a többtényezős hitelesítés engedélyezése minden felhasználóhoz és bejelentkezési módszerhez az Azure ad biztonsági alapértelmezett **előnyökkel**: Ez a beállítás lehetővé teszi, hogy könnyen és gyorsan érvényesítse az MFA-t a környezetben lévő összes felhasználó számára, szigorú szabályzattal:

* Rendszergazdai fiókok és rendszergazdai bejelentkezési mechanizmusok
* MFA-kihívás megkövetelése Microsoft Authenticatoron keresztül az összes felhasználó számára
* Örökölt hitelesítési protokollok korlátozása.

Ez a módszer minden licencelési szinten elérhető, de nem keverhető a meglévő feltételes hozzáférési szabályzatokkal. További információt az [Azure ad biztonsági alapértelmezései](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md) című témakörben talál.

**2. lehetőség**: [a multi-Factor Authentication engedélyezése a felhasználói állapot módosításával](../../active-directory/authentication/howto-mfa-userstates.md).   
**Előnyök**: Ez a hagyományos módszer a kétlépéses ellenőrzés megkövetelésére. [Az Azure AD multi-Factor Authentication a felhőben és az azure multi-Factor Authentication-kiszolgáló](../../active-directory/authentication/concept-mfa-howitworks.md)is működik. Ennek a módszernek a használatával a felhasználóknak kétlépéses ellenőrzést kell végezniük minden alkalommal, amikor bejelentkeznek, és felülbírálják a feltételes hozzáférési szabályzatokat.

Annak megállapításához, hogy a Multi-Factor Authenticationt engedélyezni kell-e, az [Azure ad MFA melyik verziója a legmegfelelőbb a szervezetem számára?](../../active-directory/authentication/concept-mfa-howitworks.md).

**3. lehetőség**: [a multi-Factor Authentication engedélyezése feltételes hozzáférési házirenddel](../../active-directory/authentication/howto-mfa-getstarted.md).
**Előnyök**: Ez a beállítás lehetővé teszi, hogy a [feltételes hozzáférés](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)használatával megkérdezze a kétlépéses ellenőrzést az adott körülmények között. A konkrét feltételek különböző helyekről, nem megbízható eszközökről vagy kockázatos alkalmazásokból is lehetnek felhasználói bejelentkezésre. Meghatározott feltételek meghatározása, amelyekben kétlépéses ellenőrzésre van szükség, így elkerülhető a felhasználók folyamatos rákérdezése, ami kellemetlen felhasználói élmény lehet.

Ez a legrugalmasabb módszer a felhasználók kétlépéses ellenőrzésének engedélyezésére. A feltételes hozzáférési szabályzat engedélyezése csak a felhőben található Azure AD-Multi-Factor Authentication működik, és az Azure AD prémium funkciója. Ezzel a módszerrel kapcsolatban további információkat talál a [felhőalapú Azure ad-multi-Factor Authentication üzembe helyezése című](../../active-directory/authentication/howto-mfa-getstarted.md)témakörben.

**4. lehetőség**: engedélyezze a multi-Factor Authentication feltételes hozzáférési házirendekkel a [kockázatalapú feltételes hozzáférési szabályzatok](../../active-directory/conditional-access/howto-conditional-access-policy-risk.md)kiértékelésével.   
**Előnyök**: Ez a beállítás a következőket teszi lehetővé:

* A szervezet identitásait érintő lehetséges sebezhetőségek észlelése.
* Konfigurálja az automatizált válaszokat a szervezete identitásával kapcsolatos gyanús műveletekre.
* Vizsgálja meg a gyanús incidenseket, és tegye meg a megfelelő lépéseket a megoldásához.

Ez a módszer a Azure AD Identity Protection kockázatértékelés használatával határozza meg, hogy szükséges-e kétlépéses ellenőrzés a felhasználói és bejelentkezési kockázatok alapján minden felhőalapú alkalmazás esetében. Ehhez a módszerhez Azure Active Directory P2 licencelés szükséges. A metódussal kapcsolatban a [Azure Active Directory Identity Protectionban](../../active-directory/identity-protection/overview-identity-protection.md)talál további információt.

> [!Note]
> 2. lehetőség: Multi-Factor Authentication engedélyezése a felhasználói állapot módosításával, felülbírálja a feltételes hozzáférési szabályzatokat. Mivel a 3. és a 4. lehetőség feltételes hozzáférési házirendeket használ, nem használhatja a 2. lehetőséget.

Azok a szervezetek, amelyek nem vesznek fel az Identity Protection további rétegeit, például a kétlépéses ellenőrzést, hajlamosabbak a hitelesítő adatok ellopására irányuló támadásokra. A hitelesítő adatok ellopására irányuló támadás adatsérülést eredményezhet.

## <a name="use-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés alkalmazása

A felhőalapú erőforrásokhoz való hozzáférés kezelése kritikus fontosságú a felhőt használó bármely szervezet számára. Az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md) segítségével felügyelheti, hogy ki férhet hozzá az Azure-erőforrásokhoz, mit tehet az erőforrásokkal, és milyen területekhez férhet hozzá.

A csoportok vagy az egyes Azure-funkciókért felelős egyedi szerepkörök kijelölik a félreértéseket, amelyek biztonsági kockázatokat okozó emberi és automatizálási hibákhoz vezethetnek. Az adathozzáférésre vonatkozó biztonsági szabályzatokat kényszerítő szervezetek számára elengedhetetlen a hozzáférés korlátozása a [szükséges ismeret](https://en.wikipedia.org/wiki/Need_to_know) és a [legalacsonyabb jogosultsági szintű](https://en.wikipedia.org/wiki/Principle_of_least_privilege) biztonsági elvek alapján.

A kockázat felmérése és javítása érdekében a biztonsági csapatnak meg kell vizsgálnia az Azure-erőforrásokat. Ha a biztonsági csapat működési felelősséggel rendelkezik, további engedélyekre van szükségük a feladataik elvégzéséhez.

Az [Azure RBAC](../../role-based-access-control/overview.md) segítségével engedélyeket rendelhet hozzá a felhasználókhoz, csoportokhoz és alkalmazásokhoz egy adott hatókörben. A szerepkörkiosztás hatóköre előfizetés, erőforráscsoport vagy egyetlen erőforrás is lehet.

**Ajánlott eljárás**: elkülönítheti a feladatokat a csapaton belül, és csak a felhasználók számára biztosíthatja a feladatok elvégzéséhez szükséges hozzáférés mennyiségét. Ahelyett, hogy az Azure-előfizetésben vagy-erőforrásokban mindenki számára korlátlan jogosultságot adna, csak bizonyos műveleteket engedélyezzen egy adott hatókörben.
**Részletek**: az Azure [-ban beépített Azure-szerepkörök](../../role-based-access-control/built-in-roles.md) használatával jogosultságokat rendelhet a felhasználókhoz.

> [!Note]
> A konkrét engedélyek szükségtelen bonyolultságot és félreértéseket hoznak létre, és olyan "örökölt" konfigurációba halmozódnak, amely nehezen orvosolható, és nem kell mást tennie. Kerülje az erőforrás-specifikus engedélyeket. Ehelyett a vállalati szintű engedélyek és erőforráscsoportok felügyeleti csoportjait használhatja az előfizetéseken belüli engedélyekhez. Kerülje a felhasználó-specifikus engedélyeket. Inkább rendeljen hozzáférést csoportokhoz az Azure AD-ben.

**Ajánlott eljárás**: a biztonsági csapatoknak az Azure-beli felelősségi körökkel való ellátásával biztosítható, hogy az Azure-erőforrások felmérjék és kijavíthatják a kockázatot
**Részletek**: az Azure RBAC [biztonsági olvasó](../../role-based-access-control/built-in-roles.md#security-reader) szerepkörének megadása a biztonsági csapatoknak. A hatáskörök hatókörének függvényében a gyökérszintű felügyeleti csoportot vagy a szegmens felügyeleti csoportot használhatja:

* Az összes vállalati erőforrásért felelős csapatok **gyökérszintű felügyeleti csoportja**
* **Szegmens felügyeleti csoport** korlátozott hatókörű csapatok esetében (általában szabályozási vagy egyéb szervezeti határok miatt)

**Ajánlott eljárás**: adja meg a megfelelő engedélyeket a közvetlen működési feladatokkal rendelkező biztonsági csapatoknak.
**Részletek**: Tekintse át az Azure beépített szerepköreit a megfelelő szerepkör-hozzárendeléshez. Ha a beépített szerepkörök nem felelnek meg a szervezet konkrét igényeinek, létrehozhat [Egyéni Azure-szerepköröket](../../role-based-access-control/custom-roles.md)is. A beépített szerepkörökhöz hasonlóan egyéni szerepköröket rendelhet hozzá a felhasználókhoz, csoportokhoz és egyszerű szolgáltatásokhoz az előfizetés, az erőforráscsoport és az erőforrás-hatókörök alapján.

**Ajánlott eljárások**: biztosítson Azure Security Center hozzáférést a szükséges biztonsági szerepkörökhöz. Security Center lehetővé teszi a biztonsági csapatok számára a kockázatok gyors azonosítását és szervizelését.
**Részletek**: vegye fel ezeket a biztonsági csoportokat az Azure RBAC [biztonsági rendszergazdai](../../role-based-access-control/built-in-roles.md#security-admin) szerepkörbe, így megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, szerkesztheti a biztonsági házirendeket, megtekintheti a riasztásokat és javaslatokat, valamint elhagyhatja a riasztásokat Ezt a felügyeleti csoport vagy a szegmens felügyeleti csoport használatával végezheti el a felelősségi köröktől függően.

Azok a szervezetek, amelyek nem kényszerítik az adathozzáférés-vezérlést olyan funkciókkal, mint az Azure RBAC, több jogosultságot is biztosítanak, mint a felhasználók számára. Ez adatsérüléshez vezethet azáltal, hogy lehetővé teszi a felhasználók számára az adattípusok (például a nagy üzleti hatás) elérését.

## <a name="lower-exposure-of-privileged-accounts"></a>A Kiemelt jogosultságú fiókok alacsonyabb kitettsége

Az emelt szintű hozzáférés biztonságossá tétele kulcsfontosságú az üzleti eszközök védelmének első lépése. A biztonságos információkhoz vagy erőforrásokhoz hozzáférő személyek számának minimalizálása csökkenti annak az esélyét, hogy egy rosszindulatú felhasználó hozzáférjen az adatokhoz, vagy ha egy jogosult felhasználó véletlenül egy bizalmas erőforrást érint.

A Kiemelt jogosultságú fiókok olyan fiókok, amelyek az informatikai rendszereket felügyelik és kezelik. A Cyber-támadók megcélozzák ezeket a fiókokat, hogy hozzáférjenek a szervezet adateszközeihez és rendszereihez. A rendszerjogosultságú hozzáférés biztonságossá tételéhez el kell különíteni a fiókokat és a rendszereket a rosszindulatú felhasználók számára elérhető kockázattól.

Javasoljuk, hogy fejlesszen és kövessen egy olyan ütemtervet, amely biztonságos hozzáférést biztosít a Cyber-támadók ellen. További információ az Azure AD-ben, Microsoft Azure, Microsoft 365 és más felhőalapú szolgáltatásokban kezelt vagy jelentett identitások és hozzáférések védelméről, valamint az Azure AD-ben történő [hibrid és Felhőbeli üzemelő példányok védelméről](../../active-directory/roles/security-planning.md).

A következő összefoglalja az [Azure ad-ben az emelt szintű hozzáférés biztonságossá tételével kapcsolatos](../../active-directory/roles/security-planning.md)ajánlott eljárásokat:

**Ajánlott eljárás**: a Kiemelt fiókokhoz való hozzáférés kezelése, szabályozása és figyelése.   
**Részletek**: [Azure ad Privileged Identity Management](../../active-directory/roles/security-planning.md)bekapcsolása. A Privileged Identity Management bekapcsolását követően értesítési e-mail-üzeneteket fog kapni az emelt szintű hozzáférési szerepkör változásairól. Ezek az értesítések korai figyelmeztetést nyújtanak, ha további felhasználókat adnak hozzá a címtár magas jogosultsági szintű szerepköreihez.

**Ajánlott eljárás**: gondoskodjon arról, hogy minden kritikus rendszergazdai fiók felügyelve legyen az Azure ad-fiókokkal.
**Részletek**: távolítsa el a felhasználói fiókokat a kritikus rendszergazdai szerepkörökből (például Microsoft-fiókok, például hotmail.com, live.com és Outlook.com).

**Ajánlott eljárás**: gondoskodjon arról, hogy az összes kritikus rendszergazdai szerepkör külön fiókkal rendelkezzen a felügyeleti feladatokhoz, hogy elkerülje az adathalászatot és más támadásokat a rendszergazdai jogosultságokkal szemben.
**Részletek**: hozzon létre egy külön rendszergazdai fiókot, amely a rendszergazdai feladatok végrehajtásához szükséges jogosultságokat rendeli hozzá. Ezen rendszergazdai fiókok használatának letiltása a napi hatékonyságnövelő eszközökhöz, például Microsoft 365 e-mailekhez vagy tetszőleges webböngészéshez.

**Ajánlott eljárás**: a magas jogosultsági szintű szerepkörökben lévő fiókok azonosítása és kategorizálása.   
**Részletek**: Azure ad Privileged Identity Management bekapcsolása után megtekintheti a globális rendszergazda, a Kiemelt szerepkörű rendszergazda és más magas jogosultsági szintű szerepkörök felhasználóit. Távolítsa el azokat a fiókokat, amelyekre már nincs szükség ezekben a szerepkörökben, és kategorizálja a rendszergazdai szerepkörökhöz rendelt többi fiókot:

* Egyénileg rendelhető hozzá a rendszergazda felhasználókhoz, és nem rendszergazdai célokra használható (például személyes e-mail-cím)
* Egyénileg rendelhető hozzá a rendszergazda felhasználókhoz, és csak felügyeleti célokra van kijelölve
* Több felhasználó között megosztott
* Vészhelyzeti hozzáférési forgatókönyvek esetén
* Automatikus parancsfájlok esetén
* Külső felhasználók számára

**Ajánlott eljárás**: "igény szerinti" (JIT) hozzáférés megvalósítása a jogosultságok expozíciós idejének további csökkentéséhez és a Kiemelt fiókok használatának növeléséhez.   
**Részletek**: a Azure ad Privileged Identity Management a következőket teszi lehetővé:

* Korlátozza a felhasználókat arra, hogy csak a jogosultságokat JIT-re vegyék.
* A lerövidített időtartamhoz rendeljen hozzá szerepköröket úgy, hogy a jogosultságok automatikusan visszavonásra kerülnek.

**Ajánlott eljárás**: legalább két vészhelyzeti hozzáférési fiókot adjon meg.   
**Részletek**: a sürgősségi hozzáférésű fiókok segítenek a szervezeteknek a rendszerjogosultságú hozzáférés korlátozásában egy meglévő Azure Active Directory környezetben. Ezek a fiókok Kiemelt jogosultságokkal rendelkeznek, és nem adott személyekhez vannak rendelve. A sürgősségi hozzáférési fiókok olyan forgatókönyvekre korlátozódnak, amelyekben nem használhatók normál rendszergazdai fiókok. A szervezeteknek csak a szükséges időtartamra kell korlátoznia a segélyhívó fiók használatát.

Értékelje ki azokat a fiókokat, amelyek a globális rendszergazdai szerepkörhöz vannak rendelve vagy jogosultak. Ha nem látja a csak felhőalapú fiókokat a `*.onmicrosoft.com` tartomány használatával (vészhelyzeti hozzáférés céljára), hozza létre őket. További információ: [a sürgősségi hozzáférés felügyeleti fiókjainak kezelése az Azure ad-ben](../../active-directory/roles/security-emergency-access.md).

**Ajánlott** eljárás: vészhelyzet esetén a "break Glass" folyamat van érvényben.
**Részletek**: kövesse az [Azure ad-ben az emelt szintű hozzáférés biztonságossá tétele a hibrid és a felhőben történő üzembe helyezéshez](../../active-directory/roles/security-planning.md)című témakör lépéseit.

**Ajánlott eljárás**: az összes kritikus rendszergazdai fiók jelszó nélküli (előnyben részesített) vagy Multi-Factor Authentication megkövetelésének megkövetelése.
**Részletek**: a [Microsoft Authenticator alkalmazás](../../active-directory/authentication/howto-authentication-passwordless-phone.md) használatával jelszó nélkül JELENTKEZHET be bármely Azure ad-fiókba. A [vállalati Windows Hello-hez](/windows/security/identity-protection/hello-for-business/hello-identity-verification)hasonlóan a Microsoft Authenticator a kulcs-alapú hitelesítés használatával engedélyezi az eszközhöz kötött felhasználói hitelesítő adatokat, és biometrikus hitelesítést vagy PIN-kódot használ.

Az Azure AD Multi-Factor Authentication bejelentkezéskor minden olyan felhasználóhoz, aki véglegesen hozzá van rendelve egy vagy több Azure AD-rendszergazdai szerepkörhöz: globális rendszergazda, Kiemelt szerepkörű rendszergazda, Exchange Online-rendszergazda és SharePoint Online-rendszergazda. Engedélyezze [a rendszergazdai fiókok multi-Factor Authenticationét](../../active-directory/authentication/howto-mfa-userstates.md) , és győződjön meg arról, hogy a rendszergazdai fiók felhasználóinak regisztrálva vannak.

**Ajánlott eljárás**: a kritikus rendszergazdai fiókok esetében olyan rendszergazdai munkaállomás szükséges, amelyben a termelési feladatok nem engedélyezettek (például a böngészés és az e-mail-cím). Ez biztosítja a rendszergazdai fiókokat a böngészést és e-mailt használó támadási vektorokból, és jelentősen csökkenti a jelentős incidensek kockázatát.
**Részletek**: rendszergazdai munkaállomás használata. Válassza ki a munkaállomás biztonságának szintjét:

- A fokozottan biztonságos hatékonyságnövelő eszközök fokozott biztonságot nyújtanak a böngészéshez és más hatékonyságnövelő feladatokhoz.
- Az emelt [szintű hozzáférésű munkaállomások (mancsok)](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) egy dedikált operációs rendszert biztosítanak, amely az internetes támadásokkal és a bizalmas feladatokhoz kapcsolódó veszélyforrásokkal védett.

**Ajánlott eljárás**: rendszergazdai fiókok kiépítése, amikor az alkalmazottak elhagyják a szervezetét.
**Részletek**: olyan folyamattal rendelkezik, amely letiltja vagy törli a rendszergazdai fiókokat, amikor az alkalmazottak elhagyják a szervezetét.

**Ajánlott eljárás**: a rendszergazdai fiókok rendszeres tesztelése a jelenlegi támadási technikák használatával.
**Részletek**: Microsoft 365 Attack Simulator vagy egy harmadik féltől származó ajánlat használatával reális támadási forgatókönyveket futtathat a szervezetben. Ez segít a veszélyeztetett felhasználók megtalálásában a valódi támadás előtt.

**Ajánlott** eljárás: lépéseket tesz a leggyakrabban használt módszerek enyhítésére.  
**Részletek**: [azonosítsa a Microsoft-fiókokat olyan rendszergazdai szerepkörökben, amelyeket munkahelyi vagy iskolai fiókokra kell váltani](../../active-directory/roles/security-planning.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Különálló felhasználói fiókok és a levelezés továbbítása globális rendszergazdai fiókokhoz](../../active-directory/roles/security-planning.md)  

[Győződjön meg arról, hogy a rendszergazdai fiókok jelszavai nemrég módosultak](../../active-directory/roles/security-planning.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Jelszó-kivonat szinkronizálásának bekapcsolása](../../active-directory/roles/security-planning.md#turn-on-password-hash-synchronization)  

[Multi-Factor Authentication megkövetelése minden Kiemelt szerepkörrel rendelkező és a felhasználók számára elérhető felhasználók számára](../../active-directory/roles/security-planning.md#require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users)  

[A Microsoft 365 biztonságos pontszámának beszerzése (ha Microsoft 365 használ)](../../active-directory/roles/security-planning.md#obtain-your-microsoft-365-secure-score-if-using-microsoft-365)  

[Tekintse át a Microsoft 365 biztonsági útmutatást (ha Microsoft 365 használ)](../../active-directory/roles/security-planning.md#review-the-microsoft-365-security-and-compliance-guidance-if-using-microsoft-365)  

[Microsoft 365 tevékenység figyelésének konfigurálása (ha Microsoft 365 használ)](../../active-directory/roles/security-planning.md#configure-microsoft-365-activity-monitoring-if-using-microsoft-365)  

[Incidens/vészhelyzeti válasz terv tulajdonosainak létrehozása](../../active-directory/roles/security-planning.md#establish-incidentemergency-response-plan-owners)  

[Biztonságos helyszíni privilegizált rendszergazdai fiókok](../../active-directory/roles/security-planning.md#turn-on-password-hash-synchronization)

Ha nem védi a Kiemelt jogosultságú hozzáférést, előfordulhat, hogy túl sok felhasználója van magas jogosultsági szintű szerepkörben, és a támadásokkal szemben sebezhető. Rosszindulatú szereplők, beleértve a Cyber-támadókat, gyakran a rendszergazdai fiókokat és a privilegizált hozzáférés más elemeit a bizalmas adatokhoz és rendszerekhez való hozzáféréshez a hitelesítő adatok ellopásával.

## <a name="control-locations-where-resources-are-created"></a>Az erőforrások létrehozásának vezérlési helyei

A Felhőbeli operátorok számára a feladatok elvégzésének engedélyezése, miközben a szervezet erőforrásainak kezeléséhez szükséges szabályok megszegésének megakadályozása nagyon fontos. Azok a szervezetek, amelyek szabályozni szeretnék, hogy mely helyekről kell létrehozni az erőforrásokat, ezeket a webhelyeket is fel kell használni

A [Azure Resource Manager](../../azure-resource-manager/management/overview.md) használatával olyan biztonsági házirendeket hozhat létre, amelyek definíciói a kifejezetten megtagadott műveleteket vagy erőforrásokat írják le. Ezeket a házirend-definíciókat a kívánt hatókörhöz, például az előfizetéshez, az erőforráscsoporthoz vagy egy adott erőforráshoz kell rendelni.

> [!NOTE]
> A biztonsági szabályzatok nem egyeznek az Azure RBAC. Valójában az Azure RBAC használják a felhasználók számára az erőforrások létrehozásához.
>
>

Azok a szervezetek, amelyek nem szabályozzák, hogy az erőforrások hogyan jöjjenek létre, érzékenyebbek azokra a felhasználókra, akik a szükségesnél több erőforrást hoznak létre. Az erőforrás-létrehozási folyamat megerősítése fontos lépés egy több-bérlős forgatókönyv biztonságossá tételéhez.

## <a name="actively-monitor-for-suspicious-activities"></a>Gyanús tevékenységek aktív figyelése

Az aktív identitás-figyelési rendszer gyorsan képes észlelni a gyanús viselkedést, és riasztást indít a további vizsgálathoz. Az alábbi táblázat két olyan Azure AD-funkciót sorol fel, amelyek segítségével a szervezetek megtekinthetik az identitásuk körét:

**Ajánlott** eljárás: a következő módszerekkel azonosítható:

- Kísérlet történt a bejelentkezés [nyomon követése nélkül](../../active-directory/reports-monitoring/howto-find-activity-reports.md).
- [Találgatásos](../../active-directory/reports-monitoring/howto-find-activity-reports.md) támadás egy adott fiókkal szemben.
- Megpróbál több helyről bejelentkezni.
- [Fertőzött eszközökről](../../active-directory/reports-monitoring/howto-find-activity-reports.md)történő bejelentkezések.
- Gyanús IP-címek.

**Részletek**: prémium szintű Azure ad [anomália-jelentések](../../active-directory/reports-monitoring/overview-reports.md)használata. Olyan folyamatokkal és eljárásokkal kell rendelkezniük, amelyekkel a rendszergazdák napi vagy igény szerint futtathatják ezeket a jelentéseket (általában incidensek esetén).

**Ajánlott eljárás**: olyan aktív figyelési rendszerrel kell rendelkeznie, amely értesíti a kockázatokról, és az üzleti igényeknek megfelelően (magas, közepes vagy alacsony) módosíthatja a kockázati szintet.   
**Részletek**: [Azure ad Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)használata, amely a saját irányítópultján lévő aktuális kockázatokat és napi összefoglaló értesítéseket küld e-mailben. A szervezete identitásának védelméhez olyan kockázatalapú házirendeket állíthat be, amelyek automatikusan válaszolnak az észlelt problémákra, amikor elérik egy adott kockázati szintet.

Azok a szervezetek, amelyek nem figyelik aktívan a személyazonossági rendszerüket, a felhasználó hitelesítő adatai veszélyben vannak. Ha nem tudja, hogy a gyanús tevékenységek ezen hitelesítő adatokon keresztül zajlanak, a szervezetek nem tudják elhárítani az ilyen típusú fenyegetést.

## <a name="use-azure-ad-for-storage-authentication"></a>Az Azure AD használata tárolási hitelesítéshez
Az [Azure Storage](../../storage/common/storage-auth-aad.md) támogatja a hitelesítést és az engedélyezést az Azure ad-vel a blob Storage és a várólista tárolásához. Az Azure AD-hitelesítéssel az Azure szerepköralapú hozzáférés-vezérlésével konkrét engedélyeket biztosíthat a felhasználóknak, csoportoknak és alkalmazásoknak egy adott blob-tároló vagy-várólista hatóköréhez.

Javasoljuk, hogy [Az Azure ad használatával hitelesítse a tárolóhoz való hozzáférést](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/).

## <a name="next-step"></a>Következő lépés

Az Azure-beli felhőalapú megoldások tervezésekor, üzembe helyezése és kezelése során ajánlott biztonsági eljárásokat az [Azure biztonsági eljárásaival és modelljeivel](best-practices-and-patterns.md) foglalkozó témakörben talál.
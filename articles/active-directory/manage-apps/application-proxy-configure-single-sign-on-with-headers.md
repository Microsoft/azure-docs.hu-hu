---
title: Fejléc-alapú egyszeri bejelentkezés Azure AD alkalmazás proxyval rendelkező helyszíni alkalmazásokhoz
description: Ismerje meg, hogyan biztosíthat egyszeri bejelentkezést a fejléc-alapú hitelesítéssel védett helyszíni alkalmazásokhoz.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 813e360ffc6c78b52151af5b9867560ae6d58f2f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103198241"
---
# <a name="header-based-single-sign-on-for-on-premises-apps-with-azure-ad-app-proxy"></a>Fejléc-alapú egyszeri bejelentkezés Azure AD alkalmazás proxyval rendelkező helyszíni alkalmazásokhoz

Az Azure Active Directory (Azure AD) alkalmazásproxy natív módon támogatja az egyszeri bejelentkezéses hozzáférést olyan alkalmazásokhoz, amelyek fejléceket használnak a hitelesítéshez. Az Azure AD-ben az alkalmazás által igényelt fejléc-értékeket is konfigurálhatja. A fejléc értékei az Application proxyn keresztül lesznek továbbítva az alkalmazásnak. A következő előnyökkel jár, ha natív támogatást kíván használni az alkalmazásproxy-alapú hitelesítéshez az alkalmazásproxy használatával:  

* A helyszíni **alkalmazások távoli elérésének egyszerűsítése** – az App proxy lehetővé teszi a meglévő távelérési architektúra egyszerűsítését. Ezekhez az alkalmazásokhoz a VPN-hozzáférést is lecserélheti. A hitelesítéshez a helyszíni identitási megoldásokkal kapcsolatos függőségeket is el lehet távolítani. A felhasználók nem láthatnak mást, amikor bejelentkeznek a céges alkalmazásai használatára. Továbbra is bárhonnan dolgozhatnak bármely eszközről.  

* **Nincs további szoftver vagy módosítás** az alkalmazásokban – a meglévő alkalmazásproxy-összekötőket használhatja, és nincs szükség további szoftver telepítésére.  

* **Az elérhető attribútumok és átalakítások széles listája** – az összes elérhető fejléc-érték az Azure ad által kiállított standard jogcímek alapján érhető el. Az [SAML-vagy OIDC-alkalmazások jogcímeinek konfigurálásához](../develop/active-directory-saml-claims-customization.md#attributes) rendelkezésre álló összes attribútum és átalakítás fejléc-értékként is használható. 

## <a name="pre-requisites"></a>Előfeltételek
Mielőtt megkezdené az egyszeri bejelentkezést a fejléc-alapú hitelesítési alkalmazásokhoz, győződjön meg arról, hogy a környezet készen áll a következő beállításokkal és konfigurációkkal:
- Engedélyeznie kell az alkalmazásproxy-t, és telepítenie kell egy olyan összekötőt, amely a helyről az alkalmazásaihoz tartozik. A helyszíni környezet előkészítésének, az összekötők telepítésének és regisztrálásának, valamint a-összekötő tesztelésének megismeréséhez tekintse [meg az Application proxyn keresztüli távoli eléréshez szükséges helyszíni alkalmazás hozzáadása](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) című oktatóanyagot. 

## <a name="supported-capabilities"></a>Támogatott képességek

A következő táblázat az alkalmazásproxy által támogatott, a fejléc-alapú hitelesítési alkalmazásokhoz szükséges általános képességeket sorolja fel. 

|Követelmény   |Leírás|
|----------|-----------|
|Összevont egyszeri bejelentkezés |Az előre hitelesített módban minden alkalmazás Azure AD-hitelesítéssel van védve, és lehetővé teszi a felhasználók számára az egyszeri bejelentkezést. |
|Távelérés |Az alkalmazásproxy lehetővé teszi a távoli hozzáférést az alkalmazáshoz. A felhasználók a külső URL-cím használatával bármely böngészőben hozzáférhetnek az alkalmazáshoz az internetről. Az alkalmazásproxy nem vállalati hozzáférés használatára szolgál. |
|Fejléc-alapú integráció |Az alkalmazásproxy az SSO-integráció az Azure AD-vel, majd az identitás vagy más alkalmazásadatok HTTP-fejlécként való átadása az alkalmazásnak. |
|Alkalmazás engedélyezése |Az általános szabályzatok az elért alkalmazás, a felhasználó csoporttagság és egyéb szabályzatok alapján adhatók meg. Az Azure AD-ben a házirendek [feltételes hozzáférés](../conditional-access/overview.md)használatával valósíthatók meg. Az alkalmazás-engedélyezési házirendek csak a kezdeti hitelesítési kérelemre érvényesek. |
|Lépésenkénti hitelesítés |A szabályzatok meghatározhatók úgy, hogy kikényszerítsék a hozzáadott hitelesítést, például hogy hozzáférjenek a bizalmas erőforrásokhoz. |
|Részletes engedélyezés |Hozzáférés-vezérlést biztosít az URL szintjén. A hozzáadott szabályzatok az elérni kívánt URL-cím alapján kényszeríthető ki. Az alkalmazáshoz konfigurált belső URL-cím határozza meg annak az alkalmazásnak a hatókörét, amelyre a házirend vonatkozik. A rendszer kikényszeríti a legrészletesebb elérési útra konfigurált szabályzatot.  |

> [!NOTE] 
> Ebből a cikkből megtudhatja, hogyan csatlakoztathatók a fejléc-alapú hitelesítési alkalmazások az Azure AD-hez az alkalmazásproxy használatával és a javasolt minta. Alternatív megoldásként létezik egy integrációs minta is, amely a PingAccess és az Azure AD-t használja a fejléc-alapú hitelesítés engedélyezéséhez. További részletek: az [alkalmazás-proxyval és a PingAccess való egyszeri bejelentkezéshez használt fejléc-alapú hitelesítés](application-proxy-ping-access-publishing-guide.md).

## <a name="how-it-works"></a>Működés

:::image type="content" source="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png" alt-text="A fejléc-alapú egyszeri bejelentkezés működése az alkalmazásproxy esetében." lightbox="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png":::

1. A rendszergazda testreszabja az alkalmazás által az Azure AD-portálon megkövetelt attribútum-hozzárendeléseket. 
2. Amikor egy felhasználó hozzáfér az alkalmazáshoz, az alkalmazásproxy biztosítja, hogy a felhasználó hitelesítve legyen az Azure AD-ben 
3. Az alkalmazásproxy felhőalapú szolgáltatása tisztában van a szükséges attribútumokkal. Így a szolgáltatás beolvassa a megfelelő jogcímeket a hitelesítés során kapott azonosító jogkivonat alapján. A szolgáltatás ezután lefordítja az értékeket a kötelező HTTP-fejlécekre az összekötőre irányuló kérelem részeként. 
4. A rendszer ezután továbbítja a kérést az összekötőhöz, amelyet a rendszer a háttérbeli alkalmazásnak továbbít. 
5. Az alkalmazás fogadja a fejléceket, és igény szerint használhatja ezeket a fejléceket. 

## <a name="publish-the-application-with-application-proxy"></a>Az alkalmazás közzététele az Application proxyval

1. Tegye közzé az alkalmazást az Application [proxyval történő alkalmazások közzététele](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)című részben leírt utasítások szerint.  
    - A belső URL-cím értéke határozza meg az alkalmazás hatókörét. Ha a belső URL-címet az alkalmazás gyökerének elérési útján konfigurálja, akkor a gyökér alatti összes Alútvonal ugyanazt a fejléc-konfigurációt és más alkalmazás-konfigurációt fogja kapni. 
    - Hozzon létre egy új alkalmazást, amely egy másik fejléc-konfigurációt vagy felhasználói hozzárendelést állít be a megadott alkalmazásnál részletesebb elérési útra. Az új alkalmazásban konfigurálja a belső URL-címet a szükséges elérési úttal, majd konfigurálja az URL-címhez szükséges speciális fejléceket. Az alkalmazásproxy mindig megfelel a konfigurációs beállításoknak az alkalmazáshoz beállított legrészletesebb elérési útnak. 

2.  ****   Az **előhitelesítési módszerként** válassza a Azure Active Directory lehetőséget. 
3. Rendeljen hozzá egy tesztelési felhasználót a **felhasználók és csoportok** eléréséhez, és rendelje hozzá a megfelelő felhasználókat és csoportokat. 
4. Nyisson meg egy böngészőt, és navigáljon a **külső URL-címhez**   az alkalmazásproxy beállításainál. 
5. Ellenőrizze, hogy tud-e csatlakozni az alkalmazáshoz. Bár a kapcsolódás is lehetséges, az alkalmazás még nem érhető el, mivel a fejlécek nincsenek konfigurálva. 

## <a name="configure-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása 
Mielőtt megkezdené az egyszeri bejelentkezést a fejléc-alapú alkalmazásokhoz, már telepítve kell lennie egy alkalmazásproxy-összekötőnek, és az összekötő hozzáférhet a megcélzott alkalmazásokhoz. Ha nem, kövesse az [oktatóanyag: Azure ad Application proxy](application-proxy-add-on-premises-application.md)című témakör lépéseit,   majd térjen vissza ide. 

1. Miután az alkalmazás megjelenik a vállalati alkalmazások listájában, jelölje ki, majd válassza az **egyszeri bejelentkezés** lehetőséget. 
2. Az egyszeri bejelentkezési mód beállítása **fejléc-alapúra**. 
3. Az alapszintű konfigurációban **Azure Active Directory** az alapértelmezett érték lesz kiválasztva. 
4. Válassza a ceruza szerkesztése lehetőséget a fejlécekben az alkalmazásnak küldendő fejlécek konfigurálásához. 
5. Válassza az **új fejléc hozzáadása** lehetőséget. Adja meg a fejléc **nevét** , majd válassza az **attribútum** vagy az **átalakítás** lehetőséget, és válassza ki az alkalmazás által igényelt legördülő listából.  
    - Ha többet szeretne megtudni az elérhető attribútumok listájáról, tekintse meg a [jogcímek testreszabása – attribútumok](../develop/active-directory-saml-claims-customization.md#attributes)című témakört. 
    - Ha többet szeretne megtudni az elérhető átalakítási listáról, tekintse meg a [jogcímek testreszabása – jogcím-átalakítások](../develop/active-directory-saml-claims-customization.md#claim-transformations)című témakört. 
    - Hozzáadhat egy csoportfej- **fejlécet** is, amely az összes olyan csoportot elküldi, amely a felhasználó részét képezi, vagy az alkalmazáshoz a fejlécként hozzárendelt csoportokat. Ha többet szeretne megtudni a csoportok beállításáról, tekintse meg az [alkalmazások csoportos jogcímek konfigurálása](../hybrid/how-to-connect-fed-group-claims.md#add-group-claims-to-tokens-for-saml-applications-using-sso-configuration)című témakört. 
6. Kattintson a Mentés gombra. 

## <a name="test-your-app"></a>Az alkalmazás tesztelése 

Az összes lépés elvégzése után az alkalmazásnak futnia kell, és elérhetőnek kell lennie. Az alkalmazás tesztelése: 
1. Nyisson meg egy új böngészőt vagy egy privát böngészőablakot, és győződjön meg róla, hogy a korábban gyorsítótárazott fejlécek törlődnek. Ezután keresse meg a **külső URL-címet**   az alkalmazásproxy beállításainál.
2. Jelentkezzen be az alkalmazáshoz hozzárendelt tesztelési fiókkal. Ha az SSO használatával betölti és bejelentkezik az alkalmazásba, akkor Ön is jó! 

## <a name="considerations"></a>Megfontolandó szempontok

- Az alkalmazásproxy segítségével távoli hozzáférést biztosíthat a helyszíni vagy a privát felhőben lévő alkalmazásokhoz. Az alkalmazásproxy nem ajánlott a vállalati hálózatból származó belső forgalom kezelésére.
- A fejléc-alapú hitelesítési alkalmazásokhoz csak az összekötő vagy más engedélyezett fejléc-alapú hitelesítési megoldás adatforgalmára kell korlátozni. Ez általában az alkalmazás hálózati hozzáférésének korlátozásával történik az alkalmazáskiszolgáló tűzfal vagy IP-korlátozása használatával.

## <a name="next-steps"></a>Következő lépések

- [Mi az egyszeri bejelentkezés?](what-is-single-sign-on.md)
- [Mi az alkalmazásproxy?](what-is-application-proxy.md)
- [Gyorsindítás sorozat az alkalmazás-kezelésben](view-applications-portal.md)

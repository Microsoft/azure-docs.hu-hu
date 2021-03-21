---
title: Távoli hozzáférés a helyszíni alkalmazásokhoz – Azure AD Application Proxy
description: Azure Active Directory alkalmazásproxy biztonságos távoli hozzáférést biztosít a helyszíni webalkalmazásokhoz. Az Azure AD-be való egyszeri bejelentkezés után a felhasználók külső URL-címen vagy egy belső alkalmazás-portálon keresztül is hozzáférhetnek a Felhőbeli és a helyszíni alkalmazásokhoz. Az alkalmazásproxy például távoli hozzáférést és egyszeri bejelentkezést biztosíthat a Távoli asztal, a SharePoint, a Teams, a tabló, a Qlik és az üzletági (LOB) alkalmazások számára.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 40472dc2446133ec23d1d62343a93d2d69fc90cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99260146"
---
# <a name="remote-access-to-on-premises-applications-through-azure-ad-application-proxy"></a>Helyszíni alkalmazások Azure AD-alkalmazásproxyval történő távoli elérése

Azure Active Directory alkalmazásproxy biztonságos távoli hozzáférést biztosít a helyszíni webalkalmazásokhoz. Az Azure AD-be való egyszeri bejelentkezés után a felhasználók külső URL-címen vagy egy belső alkalmazás-portálon keresztül is hozzáférhetnek a Felhőbeli és a helyszíni alkalmazásokhoz. Az alkalmazásproxy például távoli hozzáférést és egyszeri bejelentkezést biztosíthat a Távoli asztal, a SharePoint, a Teams, a tabló, a Qlik és az üzletági (LOB) alkalmazások számára.

Az Azure AD-alkalmazásproxy használata:

- **Egyszerűen használható**. A felhasználók ugyanúgy férhetnek hozzá a helyszíni alkalmazásokhoz, mint az Azure AD-vel integrált Microsoft 365 és más SaaS-alkalmazásokhoz. Nem szükséges módosítani vagy frissíteni az alkalmazásokat ahhoz, hogy használhatók legyenek az alkalmazásproxyval.

- **Biztonságos**. A helyszíni alkalmazások az Azure engedélyezési vezérlőit és biztonsági elemzéseit is használhatják. A helyszíni alkalmazások például használhatják a feltételes hozzáférést és a kétlépéses ellenőrzést. Az alkalmazásproxy nem igényli a bejövő kapcsolatok megnyitását a tűzfalon keresztül.

- **Költséghatékony**. A helyszíni megoldások általában vagy demilitarizált zónák (DMZ), peremhálózati kiszolgálók vagy más összetett infrastruktúra beállításához és karbantartásához szükségesek. Az alkalmazásproxy a felhőben fut, így könnyen használható. Az alkalmazásproxy használatához nem kell módosítania a hálózati infrastruktúrát, vagy további berendezéseket kell telepítenie a helyszíni környezetben.

## <a name="what-is-application-proxy"></a>Mi az alkalmazásproxy?
Az alkalmazásproxy az Azure AD egyik funkciója, amely lehetővé teszi a felhasználók számára a helyszíni webalkalmazások távoli ügyfélről való elérését. Az alkalmazásproxy magában foglalja a felhőben futó alkalmazásproxy-szolgáltatást, valamint az alkalmazásproxy-összekötőt, amely egy helyszíni kiszolgálón fut. Az Azure AD, az alkalmazásproxy szolgáltatás és az alkalmazásproxy-összekötő együttműködve biztonságosan továbbítja a felhasználói bejelentkezési tokent az Azure AD-ből a webalkalmazásba.

Az alkalmazásproxy a következőket támogatja:

* [Integrált Windows-hitelesítést](application-proxy-configure-single-sign-on-with-kcd.md) használó webalkalmazások hitelesítéshez
* Űrlapalapú vagy [fejléc-alapú](./application-proxy-configure-single-sign-on-with-headers.md) hozzáférést használó webalkalmazások
* Webes API-k, amelyeket a különböző eszközökön lévő gazdag alkalmazások számára kíván tenni
* Egy [Távoli asztali átjáró](application-proxy-integrate-with-remote-desktop-services.md) mögött futó alkalmazások
* A Microsoft Authentication Library (MSAL) szolgáltatással integrált, gazdag alkalmazások

Az alkalmazásproxy támogatja az egyszeri bejelentkezést. További információ a támogatott módszerekről: [egyszeri bejelentkezés módszerének kiválasztása](sso-options.md#choosing-a-single-sign-on-method).

Az alkalmazásproxy használata ajánlott a távoli felhasználók számára a belső erőforrásokhoz való hozzáférés biztosítása érdekében. Az alkalmazásproxy a VPN vagy fordított proxy igényét váltja fel. Nem a vállalati hálózat belső felhasználói számára készült.  Azok a felhasználók, akik szükségtelenül használják az alkalmazásproxy-t, váratlan és nemkívánatos teljesítménnyel kapcsolatos problémákat okozhatnak.

## <a name="how-application-proxy-works"></a>Az alkalmazásproxy működése

Az alábbi ábra bemutatja, hogyan működik együtt az Azure AD és az alkalmazásproxy az egyszeri bejelentkezés biztosításához a helyszíni alkalmazásokhoz.

![AzureAD-alkalmazásproxy diagramja](./media/application-proxy/azureappproxxy.png)

1. Ha a felhasználó egy végponton keresztül fér hozzá az alkalmazáshoz, a felhasználó az Azure AD bejelentkezési oldalára lesz irányítva.
2. Sikeres bejelentkezés után az Azure AD jogkivonatot küld a felhasználó ügyfelének.
3. Az ügyfél elküldi a jogkivonatot az alkalmazásproxy szolgáltatásnak, amely lekéri az egyszerű felhasználónevet (UPN) és a rendszerbiztonsági tag nevét (SPN) a jogkivonatból. Az alkalmazásproxy ezt követően elküldi a kérést az alkalmazásproxy-összekötőnek.
4. Ha már konfigurálta az egyszeri bejelentkezést, az összekötő a felhasználó nevében további hitelesítést hajt végre.
5. Az összekötő elküldi a kérést a helyszíni alkalmazásnak.
6. A választ az összekötő és az alkalmazásproxy szolgáltatás továbbítja a felhasználónak.

> [!NOTE]
> A legtöbb Azure AD Hybrid agenthez hasonlóan az alkalmazásproxy-összekötő nem igényli a bejövő kapcsolatok megnyitását a tűzfalon keresztül. A 3. lépésben szereplő felhasználói forgalom az Application proxy szolgáltatásban (az Azure AD-ben) leáll. Az alkalmazásproxy-összekötő (helyszíni) felelős a többi kommunikációért.
>


| Összetevő | Leírás |
| --------- | ----------- |
| Végpont  | A végpont egy URL-cím vagy egy [végfelhasználói portál](end-user-experiences.md). A felhasználók a hálózaton kívül érhetik el az alkalmazásokat egy külső URL-cím elérésével. A hálózaton belüli felhasználók egy URL-címen vagy egy végfelhasználói portálon keresztül érhetik el az alkalmazást. Ha a felhasználók ezen végpontok egyikén jelentkeznek, a hitelesítés az Azure AD-ben történik, majd az összekötőn keresztül a helyszíni alkalmazáshoz irányítja őket.|
| Azure AD | Az Azure AD a felhőben tárolt bérlői könyvtár használatával végzi a hitelesítést. |
| Alkalmazásproxy szolgáltatás | Ez az alkalmazásproxy-szolgáltatás a felhőben fut az Azure AD részeként. Továbbítja a bejelentkezési jogkivonatot a felhasználótól az alkalmazásproxy-Összekötőbe. Az alkalmazásproxy továbbítja az elérhető fejléceket a kérelemben, és a protokoll szerint állítja be a fejléceket az ügyfél IP-címére. Ha a proxyhoz tartozó bejövő kérelem már tartalmazza ezt a fejlécet, a rendszer hozzáadja az ügyfél IP-címét a vesszővel elválasztott lista végéhez, amely a fejléc értéke.|
| Alkalmazásproxy-összekötő | Az összekötő egy egyszerűsített ügynök, amely egy Windows Serveren fut a hálózaton belül. Az összekötő kezeli a felhőben és a helyszíni alkalmazásban futó alkalmazásproxy szolgáltatás közötti kommunikációt. Az összekötő csak kimenő kapcsolatokat használ, így nem kell megnyitnia a bejövő portokat, vagy semmit sem kell tennie a DMZ-ben. Az összekötők állapot nélküliek, és szükség szerint lekérik az adatokat a felhőből. Az összekötők, például a terheléselosztás és a hitelesítés módjával kapcsolatos további információkért lásd: az [Azure ad Application proxy-összekötők ismertetése](application-proxy-connectors.md).|
| Active Directory (AD) | Active Directory a tartományi fiókok hitelesítésének végrehajtásához a helyszínen fut. Ha az egyszeri bejelentkezés konfigurálva van, az összekötő az AD-vel kommunikálva további hitelesítést hajt végre.
| Helyszíni alkalmazás | Végül a felhasználó hozzáférhet egy helyszíni alkalmazáshoz.

## <a name="next-steps"></a>Következő lépések
Az alkalmazásproxy használatának megkezdéséhez lásd [: oktatóanyag: helyszíni alkalmazás hozzáadása a táveléréshez alkalmazásproxy](application-proxy-add-on-premises-application.md)használatával.
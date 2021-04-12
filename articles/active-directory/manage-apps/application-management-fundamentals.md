---
title: 'Alkalmazások kezelése: ajánlott eljárások és javaslatok | Microsoft Docs'
description: Az alkalmazások Azure Active Directory-ben való kezelésével kapcsolatos ajánlott eljárások és javaslatok megismerése. Ismerje meg, hogyan használhatja az automatikus üzembe helyezést és a helyszíni alkalmazások közzétételét az alkalmazásproxy használatával.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23c688d9b2e118ef29303d435bb83ef02ad36105
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259134"
---
# <a name="application-management-best-practices"></a>Alkalmazások kezelése – ajánlott eljárások

Ez a cikk ajánlásokat és ajánlott eljárásokat tartalmaz az alkalmazások Azure Active Directory (Azure AD) való kezeléséhez, az automatikus kiépítéssel és a helyszíni alkalmazások alkalmazás-proxyval való közzétételével.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>A Cloud app és az egyszeri bejelentkezés javaslatai
| Ajánlás | Megjegyzések |
| --- | --- |
| Az Azure AD Application Gallery for Apps szolgáltatás megtekintése  | Az Azure AD olyan katalógussal rendelkezik, amely több ezer előre integrált alkalmazást tartalmaz, amelyek engedélyezve vannak a vállalati egyszeri bejelentkezéssel (SSO). Az alkalmazásspecifikus beállítási útmutatóért tekintse meg az [SaaS-alkalmazás oktatóanyagok listáját](../saas-apps/tutorial-list.md).  | 
| Összevont SAML-alapú egyszeri bejelentkezés használata  | Ha egy alkalmazás támogatja azt, akkor használjon összevont, SAML-alapú egyszeri bejelentkezést az Azure AD-vel a jelszó-alapú egyszeri bejelentkezés és az ADFS helyett.  | 
| Az SHA-256 használata tanúsítvány-aláíráshoz  | Az Azure AD alapértelmezés szerint az SHA-256 algoritmust használja az SAML-válasz aláírásához. Az SHA-256 használata, ha az alkalmazáshoz SHA-1 szükséges (lásd: [tanúsítvány-aláírási beállítások](certificate-signing-options.md) és [alkalmazás-bejelentkezési probléma](application-sign-in-problem-application-error.md).)  | 
| Felhasználói hozzárendelés megkövetelése  | Alapértelmezés szerint a felhasználók nem férhetnek hozzá a vállalati alkalmazásokhoz. Ha azonban az alkalmazás szerepköröket tesz elérhetővé, vagy ha azt szeretné, hogy az alkalmazás megjelenjen a felhasználó saját alkalmazásaiban, felhasználói hozzárendelés szükséges.  | 
| Alkalmazások üzembe helyezése a felhasználók számára | Az [alkalmazásom](end-user-experiences.md) `https://myapps.microsoft.com` egy webalapú portál, amely egyetlen belépési ponttal biztosítja a felhasználókat a hozzárendelt felhőalapú alkalmazásokhoz. Mivel a további funkciók, például a csoportok kezelése és az önkiszolgáló jelszó-visszaállítás hozzá lettek adva, a felhasználók megkereshetik azokat az alkalmazásokban. Lásd: [az alkalmazások telepítésének megtervezése](my-apps-deployment-plan.md).
| Csoport-hozzárendelés használata  | Ha belefoglalja az előfizetésbe, csoportokat rendel hozzá egy alkalmazáshoz, hogy a folyamatos hozzáférés-kezelést delegálja a csoport tulajdonosának.  | 
| A tanúsítványok kezelésére szolgáló folyamat létrehozása | Az aláíró tanúsítvány maximális élettartama három év. A tanúsítvány lejárata miatti leállások megelőzése vagy csökkentése érdekében a szerepkörök és e-mailek terjesztési listáit használva ellenőrizze, hogy a tanúsítványokkal kapcsolatos módosítási értesítések szorosan megfigyelhetők-e. |

## <a name="provisioning-recommendations"></a>Kiépítési javaslatok
| Ajánlás | Megjegyzések |
| --- | --- |
| Oktatóanyagok használata a felhőalapú alkalmazásokkal való kiépítés beállításához | Tekintse meg az [SaaS-alkalmazás oktatóanyagok listáját](../saas-apps/tutorial-list.md) , amely részletes útmutatást nyújt a hozzáadni kívánt Gallery-alkalmazás kiépítés konfigurálásához. |
| A kiépítési naplók (előzetes verzió) használata az állapot figyeléséhez | A [kiépítési naplók](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) részletezik a kiépítési szolgáltatás által végrehajtott összes műveletet, beleértve az egyes felhasználók állapotát is. |
| Terjesztési csoport társítása a kiépítési értesítő e-mailhez | A kiépítési szolgáltatás által küldött kritikus riasztások láthatóságának növeléséhez rendeljen hozzá egy terjesztési csoportot az értesítő e-mailekhez beállításhoz. |


## <a name="application-proxy-recommendations"></a>Alkalmazásproxy-javaslatok
| Ajánlás | Megjegyzések |
| --- | --- |
| Alkalmazásproxy használata a belső erőforrásokhoz való távoli hozzáféréshez | Az alkalmazásproxy használata ajánlott a távoli felhasználók számára a belső erőforrásokhoz való hozzáféréshez, a VPN-vagy fordított proxy igényének megadásához. Nem a vállalati hálózaton belüli erőforrásokhoz való hozzáférésre szolgál, mert ez késést eredményezhet.
| Egyéni tartományok használata | Egyéni tartományokat állíthat be az alkalmazásaihoz (lásd: [Egyéni tartományok konfigurálása](application-proxy-configure-custom-domain.md)), hogy a felhasználók és az alkalmazások közötti URL-címek a hálózaton belül vagy kívül is működjenek. Emellett a saját védjegyeit is szabályozhatja, és testre szabhatja az URL-címeket.  Egyéni tartománynevek használata esetén tervezze meg a nem a Microsofttól származó megbízható hitelesítésszolgáltatótól származó nyilvános tanúsítvány beolvasását. Az Azure Application proxy támogatja a standard, ([helyettesítő karakter](application-proxy-wildcard.md)) vagy a San-alapú tanúsítványokat. (Lásd: [alkalmazásproxy megtervezése](application-proxy-deployment-plan.md).) |
| Felhasználók szinkronizálása az alkalmazásproxy üzembe helyezése előtt | Az alkalmazásproxy üzembe helyezése előtt szinkronizálja a felhasználói identitásokat egy helyszíni címtárból, vagy közvetlenül az Azure AD-ben hozza létre őket. Identitásszinkronizálás lehetővé teszi az Azure AD számára, hogy előzetesen hitelesítse a felhasználókat, mielőtt hozzáférést biztosít számukra az App proxy közzétett alkalmazásaihoz. Emellett biztosítja az egyszeri bejelentkezés (SSO) elvégzéséhez szükséges felhasználói azonosító adatokat is. (Lásd: [alkalmazásproxy megtervezése](application-proxy-deployment-plan.md).) |
| Kövesse a magas rendelkezésre állást és a terheléselosztást ismertető tippjeinket | Annak megismeréséhez, hogy a felhasználók, az alkalmazásproxy-összekötők és a háttérbeli alkalmazások közötti adatforgalom Hogyan zajlik, valamint a teljesítmény és a terheléselosztás optimalizálására vonatkozó tippekért lásd: [az alkalmazásproxy-összekötők és-alkalmazások magas rendelkezésre állása és](application-proxy-high-availability-load-balancing.md)terheléselosztása. |
| Több összekötő használata | Használjon kettő vagy több alkalmazásproxy-összekötőt a rugalmasság, a rendelkezésre állás és a méretezés érdekében (lásd: [alkalmazásproxy-összekötők](application-proxy-connectors.md)). Hozzon létre összekötő csoportokat, és győződjön meg arról, hogy minden összekötő csoport legalább két összekötővel rendelkezik (a három összekötő optimális). |
| Az alkalmazás-kiszolgálókhoz közel lévő összekötő-kiszolgálók megkeresése, és annak ellenőrzése, hogy ugyanabban a tartományban vannak-e | A teljesítmény optimalizálása érdekében fizikailag keresse meg az összekötő kiszolgálót az alkalmazáskiszolgáló közelébe (lásd: [hálózati topológia szempontjai](application-proxy-network-topology.md)). Emellett az összekötő-kiszolgálónak és a webalkalmazás-kiszolgálónak ugyanahhoz a Active Directory tartományhoz kell tartoznia, vagy a megbízó tartományokat kell kiterjednie. Ez a konfiguráció szükséges az egyszeri bejelentkezéshez, integrált Windows-hitelesítéssel (IWA) és Kerberos által korlátozott delegálással (KCD). Ha a kiszolgálók különböző tartományokban találhatók, erőforrás-alapú delegálást kell használnia az egyszeri bejelentkezéshez (lásd: [KCD az egyszeri bejelentkezéshez az Application proxyval](application-proxy-configure-single-sign-on-with-kcd.md)). |
| Az összekötők automatikus frissítéseinek engedélyezése | Az összekötők automatikus frissítéseinek engedélyezése a legújabb funkciókhoz és hibajavításokhoz. A Microsoft közvetlen támogatást biztosít a legújabb összekötő-verzióhoz és egy korábbi verzióhoz. (Lásd az [alkalmazásproxy verziójának előzményeit](application-proxy-release-version-history.md).) |
| A helyszíni proxy megkerülése | A könnyebb karbantartás érdekében konfigurálja úgy az összekötőt, hogy megkerülje a helyszíni proxyt, hogy közvetlenül kapcsolódjon az Azure-szolgáltatásokhoz. (Lásd: [alkalmazásproxy-összekötők és proxykiszolgálók](application-proxy-configure-connectors-with-proxy-servers.md).) |
| Az Azure AD Application Proxy használata a webalkalmazás-proxyn keresztül | Az Azure AD Application Proxy a legtöbb helyi forgatókönyvhöz használható. A webalkalmazás-proxy használata csak olyan helyzetekben ajánlott, amelyekben proxykiszolgáló szükséges a AD FShoz, és ahol nem használhat egyéni tartományokat a Azure Active Directoryban. (Lásd: [alkalmazásproxy áttelepítése](application-proxy-migration.md).) |
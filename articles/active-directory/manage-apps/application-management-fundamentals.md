---
title: 'Alkalmazáskezelés: Ajánlott eljárások és javaslatok | Microsoft Docs'
description: Megismeri a különböző alkalmazások kezeléséhez ajánlott eljárásokat és Azure Active Directory. Ismerje meg a helyszíni alkalmazások automatikus kiépítése és közzététele a alkalmazásproxy.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9b7f312781fd4f14c5e403ad72e5978f4d01487
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379322"
---
# <a name="application-management-best-practices"></a>Ajánlott alkalmazáskezelési eljárások

Ez a cikk javaslatokat és ajánlott eljárásokat tartalmaz az alkalmazások Azure Active Directory-ben (Azure AD), automatikus kiépítés használatával és helyszíni alkalmazások közzétételével kapcsolatban a alkalmazásproxy.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Felhőalkalmazásra és egyszeri bejelentkezésre vonatkozó javaslatok
| Ajánlás | Megjegyzések |
| --- | --- |
| Tekintse meg az alkalmazások Azure AD-alkalmazásgyűjteményét  | Az Azure AD katalógusa több ezer előre integrált alkalmazást tartalmaz, amelyeken engedélyezve van a vállalati egyszeri bejelentkezés (SSO). Alkalmazásspecifikus beállítási útmutatásért tekintse meg az [SaaS-alkalmazások oktatóanyagának listáját.](../saas-apps/tutorial-list.md)  | 
| Összevont SAML-alapú SSO használata  | Ha egy alkalmazás támogatja, használjon összevont, SAML-alapú SSO-t az Azure AD-ben a jelszóalapú SSO és az ADFS helyett.  | 
| Sha-256 használata tanúsítvány-aláíráshoz  | Az SAML-válasz aláíráshoz az Azure AD alapértelmezés szerint az SHA-256 algoritmust használja. Használja az SHA-256-ot, ha az [](certificate-signing-options.md) alkalmazás nem igényel SHA-1-et (lásd a tanúsítvány-aláírási beállításokat és az alkalmazás [bejelentkezési problémáját.)](application-sign-in-problem-application-error.md)  | 
| Felhasználó-hozzárendelés megkövetelve  | Alapértelmezés szerint a felhasználók anélkül férhetnek hozzá a vállalati alkalmazásokhoz, hogy hozzájuk rendelik őket. Ha azonban az alkalmazás elérhetővé teszi a szerepköröket, vagy azt szeretné, hogy az alkalmazás megjelenjen egy felhasználó Saját alkalmazások felhasználó-hozzárendelést igényel.  | 
| Üzembe Saját alkalmazások felhasználók számára | [Saját alkalmazások](end-user-experiences.md) egy webalapú portál, amely egyetlen belépési pontot biztosít a felhasználók számára a hozzárendelt `https://myapps.microsoft.com` felhőalapú alkalmazásokhoz. A további képességek, például a csoportkezelés és az önkiszolgáló jelszóáttelepítés hozzáadása után a felhasználók a Saját alkalmazások. Lásd: [Az üzembe Saját alkalmazások meg.](my-apps-deployment-plan.md)
| Csoport-hozzárendelés használata  | Ha az előfizetés része, rendeljen csoportokat egy alkalmazáshoz, így a folyamatos hozzáférés-kezelés delegálható a csoporttulajdonosnak.  | 
| A tanúsítványok kezelésének folyamata | Az aláíró tanúsítvány maximális élettartama három év. A tanúsítvány lejárata miatti szolgáltatáskimaradás megelőzése vagy minimalizálása érdekében használjon szerepköröket és e-mail terjesztési listákat a tanúsítványokkal kapcsolatos változásértesítések szoros figyelésében. |

## <a name="provisioning-recommendations"></a>Kiépítési javaslatok
| Ajánlás | Megjegyzések |
| --- | --- |
| Oktatóanyagok használata a kiépítés felhőalkalmazásokkal való beállításhoz | A hozzáadni kívánt katalógusbeli alkalmazáshoz való kiépítés konfigurálásával kapcsolatos részletes útmutatásért tekintse meg az [SaaS-alkalmazás](../saas-apps/tutorial-list.md) oktatóanyagai listáját. |
| Kiépítési naplók (előzetes verzió) használata az állapot monitorozására | A [kiépítési naplók](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) részletes információkat tartalmaznak a kiépítési szolgáltatás által végrehajtott összes műveletről, beleértve az egyes felhasználók állapotát is. |
| Terjesztési csoport hozzárendelése a kiépítési értesítő e-mailhez | A kiépítési szolgáltatás által küldött kritikus riasztások láthatóságának növeléséhez rendeljen egy terjesztési csoportot az Értesítési e-mailek beállításhoz. |


## <a name="application-proxy-recommendations"></a>alkalmazásproxy javaslatok
| Ajánlás | Megjegyzések |
| --- | --- |
| A alkalmazásproxy használata a belső erőforrásokhoz való távoli hozzáféréshez | alkalmazásproxy ajánlott a távoli felhasználóknak hozzáférést adni a belső erőforrásokhoz, lecserélve a VPN vagy fordított proxy szükségességét. Nem célja az erőforrások elérése a vállalati hálózaton belülről, mert késést eredményezhet.
| Egyéni tartományok használata | Állítson be egyéni tartományokat az alkalmazásokhoz (lásd: Egyéni tartományok [konfigurálása),](application-proxy-configure-custom-domain.md)hogy a felhasználók és az alkalmazások közötti URL-címek a hálózaton belül vagy kívül is működni tudjanak. Emellett vezérelni tudja a márkajelzést, és testreszabhatja az URL-címeket.  Egyéni tartománynevek használata esetén tervezze meg egy nyilvános tanúsítvány megszerzését egy nem Microsoft megbízható hitelesítésszolgáltatótól. Az Azure alkalmazásproxy támogatja[](application-proxy-wildcard.md)a szabványos (helyettesítő) vagy SAN-alapú tanúsítványokat. (Lásd [a alkalmazásproxy tervezését.)](application-proxy-deployment-plan.md) |
| Felhasználók szinkronizálása a központi telepítés alkalmazásproxy | Az alkalmazásproxy üzembe helyezése előtt szinkronizálja a felhasználói identitásokat egy helyszíni címtárból, vagy hozza létre őket közvetlenül az Azure AD-ban. identitásszinkronizálás lehetővé teszi, hogy az Azure AD előzetesen hitelesítse a felhasználókat, mielőtt hozzáférést ad nekik az alkalmazásproxy által közzétett alkalmazásokhoz. Emellett az egyszeri bejelentkezés (SSO) végrehajtásához szükséges felhasználói azonosító adatokat is biztosítja. (Lásd [a alkalmazásproxy tervezését.)](application-proxy-deployment-plan.md) |
| Kövesse a magas rendelkezésre állásra és terheléselosztásra vonatkozó tippjeinket | Ha meg szeretne ismerkedni a felhasználók, az alkalmazásproxy-összekötők és a háttéralkalmazás-kiszolgálók közötti forgalommal, és tippeket szeretne kapni a teljesítmény és a terheléselosztás optimalizálásához, tekintse meg az alkalmazásproxy-összekötők és -alkalmazások magas rendelkezésre állását [és](application-proxy-high-availability-load-balancing.md)terheléselosztását. |
| Több összekötő használata | Használjon két vagy több alkalmazásproxy összekötőt a nagyobb rugalmasság, a rendelkezésre állás és a méretezés érdekében (lásd: alkalmazásproxy [összekötők).](application-proxy-connectors.md) Hozzon létre összekötőcsoportokat, és győződjön meg arról, hogy minden összekötőcsoport rendelkezik legalább két összekötővel (három összekötő az optimális). |
| Keresse meg az alkalmazáskiszolgálók közelében található összekötő-kiszolgálókat, és győződjön meg arról, hogy ugyanabban a tartományban vannak | A teljesítmény optimalizálása érdekében fizikailag keresse meg az összekötő-kiszolgálót az alkalmazáskiszolgálók közelében (lásd: [Hálózati topológiával kapcsolatos szempontok).](application-proxy-network-topology.md) Emellett az összekötő-kiszolgálónak és a webalkalmazás-kiszolgálóknak ugyanakhoz az Active Directory tartományhoz kell tartozni, vagy a megbízhatósági tartományokra kell vonatkozni. Ez a konfiguráció szükséges a integrált Windows-hitelesítés (IWA) és Kerberos által korlátozott delegálás (KCD) esetén. Ha a kiszolgálók különböző tartományokban vannak, erőforrás-alapú delegálást kell használnia az egyszeri bejelentkezéshez (lásd: [KCD](application-proxy-configure-single-sign-on-with-kcd.md)az egyszeri bejelentkezéshez a alkalmazásproxy). |
| Összekötők automatikus frissítésének engedélyezése | Engedélyezze az összekötők automatikus frissítését a legújabb funkciókhoz és hibajavításhoz. A Microsoft közvetlen támogatást nyújt az összekötő legújabb és egy korábbi verziójához. (Lásd [alkalmazásproxy kiadási verzióelőzményeket.)](application-proxy-release-version-history.md) |
| A helyszíni proxy megkerülése | A könnyebb karbantartás érdekében konfigurálja az összekötőt úgy, hogy megkerülje a helyszíni proxyt, hogy közvetlenül csatlakozzon az Azure-szolgáltatásokhoz. [(Lásd: alkalmazásproxy összekötők és proxykiszolgálók .)](application-proxy-configure-connectors-with-proxy-servers.md) |
| Az Azure AD alkalmazásproxy webalkalmazáson keresztüli alkalmazásproxy | Az Azure AD alkalmazásproxy a legtöbb helyszíni forgatókönyvhöz. A alkalmazásproxy használata csak olyan esetekben ajánlott, amelyek proxykiszolgálót igényelnek a AD FS és ahol nem használhat egyéni tartományokat a Azure Active Directory. [(Lásd: alkalmazásproxy áttelepítése.)](application-proxy-migration.md) |
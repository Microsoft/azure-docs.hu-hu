---
title: Alkalmazásproxy-alkalmazások hibakeresése – Azure Active Directory | Microsoft Docs
description: Azure Active Directory-(Azure AD-) alkalmazásproxy-alkalmazásokkal kapcsolatos hibák elhárítása.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 2066030b04feae4e50fee3442a663209801d5875
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99253391"
---
# <a name="debug-application-proxy-application-issues"></a>Alkalmazásproxy alkalmazáshibáinak hibakeresése 

Ez a cikk segítséget nyújt a Azure Active Directory (Azure AD) alkalmazásproxy alkalmazásaival kapcsolatos problémák elhárításában. Ha az alkalmazásproxy szolgáltatást a helyszíni webalkalmazáshoz való távoli hozzáféréshez használja, de az alkalmazáshoz való csatlakozás nem sikerül, használja ezt a folyamatábrát az alkalmazással kapcsolatos problémák hibakereséséhez. 

## <a name="before-you-begin"></a>Előkészületek

Az alkalmazásproxy hibáinak elhárításakor javasoljuk, hogy kezdjen el az összekötők közül. Először kövesse a hibaelhárítási folyamatot az [alkalmazásproxy-összekötő hibakeresése](application-proxy-debug-connectors.md) című témakörben, és győződjön meg arról, hogy az alkalmazásproxy-összekötők megfelelően vannak konfigurálva. Ha továbbra is problémákat tapasztal, térjen vissza ehhez a cikkhez az alkalmazás hibaelhárításához.  

További információ az alkalmazásproxy használatáról:

- [Távoli hozzáférés a helyszíni alkalmazásokhoz alkalmazásproxy használatával](application-proxy.md)
- [Alkalmazásproxy-összekötők](application-proxy-connectors.md)
- [Összekötő telepítése és regisztrálása](application-proxy-add-on-premises-application.md)
- [Alkalmazásproxyval kapcsolatos problémák és hibaüzenetek elhárítása](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Az alkalmazással kapcsolatos problémák folyamatábrája

Ez a folyamatábra végigvezeti az alkalmazáshoz való csatlakozás leggyakoribb problémáinak hibakeresési lépésein. Az egyes lépésekkel kapcsolatos részletekért tekintse meg a folyamatábrát követő táblázatot.

![Egy alkalmazás hibakeresésének lépéseit bemutató folyamatábra](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

| Lépés | Művelet | Leírás |
|---------|---------|---------|
|1 | Nyisson meg egy böngészőt, és lépjen be az alkalmazásba, és adja meg a hitelesítő adatait | A hitelesítő adataival jelentkezzen be az alkalmazásba, és keresse meg a felhasználóval kapcsolatos hibákat, például [a vállalati alkalmazás nem érhető el](application-proxy-sign-in-bad-gateway-timeout-error.md). |
|2 | A felhasználó-hozzárendelés ellenőrzése az alkalmazáshoz | Győződjön meg arról, hogy a felhasználói fiókja rendelkezik engedéllyel az alkalmazásnak a vállalati hálózaton belüli eléréséhez, majd tesztelje a bejelentkezést az [alkalmazásba az alkalmazás tesztelése](application-proxy-add-on-premises-application.md#test-the-application)lépéseit követve. Ha a bejelentkezési problémák továbbra is fennállnak, tekintse [meg a bejelentkezéssel kapcsolatos hibák elhárítása](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)című témakört.  |
|3 | Nyisson meg egy böngészőt, és próbálja meg elérni az alkalmazást | Ha egy hiba azonnal megjelenik, ellenőrizze, hogy az alkalmazásproxy megfelelően van-e konfigurálva. Az adott hibaüzenetekkel kapcsolatos részletekért lásd: [az alkalmazásproxy problémáinak elhárítása és a hibaüzenetek](application-proxy-troubleshoot.md).  |
|4 | Az egyéni tartomány beállításának megkeresése vagy a hiba hibáinak megoldása | Ha a lap egyáltalán nem jelenik meg, ellenőrizze, hogy az egyéni tartomány megfelelően van-e konfigurálva az [Egyéni tartományokkal végzett munka](application-proxy-configure-custom-domain.md)áttekintésével.<br></br>Ha a lap nem töltődik be, és hibaüzenet jelenik meg, hárítsa el a hibát az  [alkalmazásproxy-problémák és a hibaüzenetek hibaelhárítására](application-proxy-troubleshoot.md)való hivatkozással. <br></br>Ha a hibaüzenet 20 másodpercnél hosszabb időt vesz igénybe, akkor kapcsolódási probléma lehet. Nyissa meg a [Debug Application proxy-összekötők](application-proxy-debug-connectors.md) hibaelhárítási cikkét.  |
|5 | Ha a probléma továbbra is fennáll, ugorjon az összekötő hibakeresése | A proxy és az összekötő, illetve az összekötő és a háttér közötti kapcsolati probléma lehet. Nyissa meg a [Debug Application proxy-összekötők](application-proxy-debug-connectors.md) hibaelhárítási cikkét. |
|6 | Tegye közzé az összes erőforrást, keresse meg a böngésző fejlesztői eszközeit, és javítsa a hivatkozásokat. | Győződjön meg arról, hogy a közzétételi útvonal tartalmazza az alkalmazáshoz szükséges összes lemezképet, parancsfájlt és stíluslapot. Részletekért lásd: helyszíni [alkalmazás hozzáadása az Azure ad-](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)hez. <br></br>Használja a böngésző fejlesztői eszközeit (F12-eszközök az Internet Explorerben vagy a Microsoft Edge-ben), és keressen rá a közzétételi problémákra az [alkalmazás lap nem megfelelően jelenik meg](application-proxy-page-appearance-broken-problem.md). <br></br>Tekintse át [a lapon lévő hivatkozásokban lévő nem működő](application-proxy-page-links-broken-problem.md)hivatkozások feloldási beállításait. |
|7 | Hálózati késés keresése | Ha az oldal lassan töltődik be, Ismerje meg, hogyan csökkentheti a hálózati késést a [késés csökkentése](application-proxy-network-topology.md#considerations-for-reducing-latency)érdekében. | 
|8 | További hibaelhárítási Súgó | Ha a probléma továbbra is fennáll, további hibaelhárítási cikkeket talál az [alkalmazásproxy hibaelhárítási dokumentációjában](application-proxy-troubleshoot.md). |

## <a name="next-steps"></a>Következő lépések


* [Alkalmazások közzététele különálló hálózatokon és helyszíneken összekötő csoportok használatával](application-proxy-connector-groups.md)
* [Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)
* [Alkalmazásproxy-és összekötő-hibák elhárítása](application-proxy-troubleshoot.md)
* [Az Azure AD Application Proxy-összekötő csendes telepítése](application-proxy-register-connector-powershell.md)

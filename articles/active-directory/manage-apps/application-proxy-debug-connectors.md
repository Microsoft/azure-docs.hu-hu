---
title: Alkalmazásproxy-összekötők hibakeresése – Azure Active Directory | Microsoft Docs
description: Hibakeresési problémák Azure Active Directory (Azure AD) alkalmazásproxy-összekötővel.
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
ms.openlocfilehash: c656bfe7f9cc8923859a99f31210cd554aa66a41
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256966"
---
# <a name="debug-application-proxy-connector-issues"></a>Alkalmazásproxy-összekötők problémáinak hibakeresése 

Ez a cikk segítséget nyújt a Azure Active Directory (Azure AD) alkalmazásproxy-összekötők hibáinak elhárításában. Ha az alkalmazásproxy szolgáltatást a helyszíni webalkalmazáshoz való távoli hozzáféréshez használja, de az alkalmazáshoz való kapcsolódás során problémákba ütközik, használja ezt a folyamatábrát az összekötők hibáinak hibakereséséhez. 

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy telepítette az alkalmazásproxy-összekötőt, és problémába ütközött. Az alkalmazásproxy hibáinak elhárításakor javasoljuk, hogy kezdje ezzel a hibaelhárítási folyamattal annak megállapításához, hogy az alkalmazásproxy-összekötők megfelelően vannak-e konfigurálva. Ha továbbra is problémát tapasztal az alkalmazáshoz való csatlakozás során, kövesse az [alkalmazásproxy alkalmazásával kapcsolatos hibák](application-proxy-debug-apps.md)elhárítása című témakört.  


További információ az alkalmazásproxy és az összekötők használatával kapcsolatban:

- [Távoli hozzáférés a helyszíni alkalmazásokhoz alkalmazásproxy használatával](application-proxy.md)
- [Alkalmazásproxy-összekötők](application-proxy-connectors.md)
- [Összekötő telepítése és regisztrálása](application-proxy-add-on-premises-application.md)
- [Alkalmazásproxyval kapcsolatos problémák és hibaüzenetek elhárítása](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Összekötői problémák folyamatábrája

Ez a folyamatábra végigvezeti a leggyakoribb összekötő-problémák némelyikének hibakeresési lépésein. Az egyes lépésekkel kapcsolatos részletekért tekintse meg a folyamatábrát követő táblázatot.

![Az összekötők hibakeresésének lépéseit bemutató folyamatábra](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

| Lépés | Művelet | Leírás |
|---------|---------|---------|
|1 | Az alkalmazáshoz rendelt összekötő-csoport megkeresése | Valószínűleg van több kiszolgálóra telepített összekötő, amely esetben az összekötőket [hozzá kell rendelni az összekötő-csoportokhoz](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). További információ az összekötő-csoportokról: [alkalmazások közzététele különálló hálózatokon és helyszíneken összekötő csoportok használatával](application-proxy-connector-groups.md). |
|2 | Az összekötő telepítése és a csoport társítása | Ha nincs telepítve összekötő, tekintse meg [az összekötők telepítése és regisztrálása](application-proxy-add-on-premises-application.md#install-and-register-a-connector)című témakört.<br></br> Ha problémába ütközik az összekötő telepítésekor, tekintse meg [az összekötő telepítésekor jelentkező problémát](application-proxy-connector-installation-problem.md).<br></br> Ha az összekötő nincs csoporthoz rendelve, tekintse [meg az összekötő társítása csoporthoz](application-proxy-connector-groups.md#create-connector-groups)című témakört.<br></br>Ha az alkalmazás nincs összekötő-csoporthoz rendelve, tekintse [meg az alkalmazás hozzárendelése összekötő-csoporthoz](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)című témakört.|
|3 | Port teszt futtatása az összekötő-kiszolgálón | Az összekötő-kiszolgálón futtassa a port tesztet a [Telnet](/windows-server/administration/windows-commands/telnet) vagy más port tesztelési eszköz használatával annak ellenőrzéséhez, hogy [a 443-es és a 80-es portok nyitva vannak-](application-proxy-add-on-premises-application.md#open-ports)e.|
|4 | A tartományok és portok konfigurálása | [Győződjön meg arról, hogy a tartományok és a portok megfelelően vannak konfigurálva](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) Ahhoz, hogy az összekötő megfelelően működjön, bizonyos portoknak nyitva kell lenniük, és olyan URL-címekkel kell rendelkezniük, amelyeknek a kiszolgálónak hozzá kell férnie. |
|5 | Ellenőrizze, hogy van-e használatban háttérbeli proxy | Ellenőrizze, hogy az összekötők használnak-e háttér-proxykiszolgálót, vagy megkerülését. Részletekért lásd: [összekötő-proxyval kapcsolatos problémák elhárítása és a szolgáltatás kapcsolódási](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues)problémái. |
|6 | Az összekötő és a frissítő frissítése a háttér-proxy használatára | Ha a háttér-proxy használatban van, akkor győződjön meg arról, hogy az összekötő ugyanazt a proxyt használja. További információ az összekötők hibaelhárításáról és konfigurálásáról a proxykiszolgálók működéséhez: [meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Az alkalmazás belső URL-címének betöltése az összekötő-kiszolgálón | Az összekötő-kiszolgálón töltse be az alkalmazás belső URL-címét. |
|8 | Belső hálózati kapcsolat ellenőrzése | A belső hálózaton van egy csatlakozási probléma, amelyet a hibakeresési folyamat nem tud diagnosztizálni. Az alkalmazásnak belsőleg elérhetőnek kell lennie az összekötők működéséhez. Az összekötő eseménynaplóit az [alkalmazásproxy-összekötők](application-proxy-connectors.md#under-the-hood)című témakörben leírtak szerint engedélyezheti és tekintheti meg. |
|9 | Az időtúllépési érték meghosszabbítása a háttérben | Az alkalmazás **további beállításainál** módosítsa a **háttérbeli alkalmazások időtúllépési** beállítását **hosszúra**. Lásd: helyszíni [alkalmazás hozzáadása az Azure ad-](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)hez. |
|10 | Ha a probléma továbbra is fennáll, konkrét folyamatokkal kapcsolatos problémák megcélzása, alkalmazás-és egyszeri bejelentkezéses hibakeresés | A [hibakeresési alkalmazásproxy alkalmazásával kapcsolatos problémák](application-proxy-debug-apps.md) elhárítása. |

## <a name="next-steps"></a>Következő lépések


* [Alkalmazások közzététele különálló hálózatokon és helyszíneken összekötő csoportok használatával](application-proxy-connector-groups.md)
* [Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)
* [Alkalmazásproxy-és összekötő-hibák elhárítása](application-proxy-troubleshoot.md)
* [Az Azure AD Application Proxy-összekötő csendes telepítése](application-proxy-register-connector-powershell.md)
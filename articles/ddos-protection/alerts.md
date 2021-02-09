---
title: A DDoS Protection-riasztások megtekintése és konfigurálása Azure DDoS Protection standardhoz
description: Megtudhatja, hogyan tekintheti meg és konfigurálhatja a DDoS Protection-riasztásokat a Azure DDoS Protection Standard rendszerhez.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 2d72027082ed2b57b28a15a736c35801ba88188c
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832521"
---
# <a name="view-and-configure-ddos-protection-alerts"></a>DDoS Protection-riasztások megtekintése és konfigurálása

Azure DDoS Protection a standard részletes támadási elemzéseket és vizualizációkat biztosít a DDoS Attack Analytics használatával. A DDoS-támadásokkal szemben a virtuális hálózatokat védő ügyfelek részletesen megtekinthetik a támadási forgalmat, valamint a támadás enyhítésére tett lépéseket a támadásokkal kapcsolatos kockázatcsökkentő jelentésekben & a kockázatcsökkentő folyamat naplófájljait. A gazdag telemetria Azure Monitoron keresztül érhető el, beleértve a DDoS-támadások időtartama alatt részletes mérőszámokat is. A riasztás konfigurálható a DDoS Protection által elérhetővé tett Azure Monitor metrikák bármelyikéhez. A naplózás az [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), a splunk (Azure Event Hubs), a OMS log Analytics és az Azure Storage szolgáltatással is integrálható a Azure monitor diagnosztikai felületen keresztül haladó elemzésekhez.

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> * Riasztások konfigurálása Azure Monitor
> * Riasztások konfigurálása a portálon keresztül
> * Riasztások megtekintése Azure Security Center
> * A riasztások ellenőrzése és tesztelése

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- Az oktatóanyag lépéseinek elvégzése előtt először létre kell hoznia egy [Azure DDoS standard Protection-csomagot](manage-ddos-protection.md) , és a DDoS Protection szabványnak engedélyezve kell lennie egy virtuális hálózaton.
- A DDoS figyeli a virtuális hálózaton belüli erőforrásokhoz rendelt nyilvános IP-címeket. Ha nem rendelkezik nyilvános IP-címmel rendelkező erőforrásokkal a virtuális hálózaton, először létre kell hoznia egy nyilvános IP-címmel rendelkező erőforrást. Az [Azure-szolgáltatások virtuális hálózata](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (beleértve az Azure Load balancert, ahol a háttérbeli virtuális gépek a virtuális hálózaton vannak), a Resource Managerrel telepített összes erőforrás nyilvános IP-címét nyomon követheti, kivéve Azure app Service környezetekben. Az oktatóanyag folytatásához gyorsan létrehozhat egy Windows vagy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [rendszerű](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépet.     

## <a name="configure-alerts-through-azure-monitor"></a>Riasztások konfigurálása Azure Monitor

Ezekkel a sablonokkal olyan nyilvános IP-címekre vonatkozó riasztásokat konfigurálhat, amelyeken engedélyezve van a diagnosztikai bejelentkezés. Ezért ahhoz, hogy használni lehessen ezeket a riasztási sablonokat, először egy Log Analytics munkaterületre lesz szüksége, amelyen engedélyezve van a diagnosztikai beállítások. Lásd: [DDoS diagnosztikai naplózás megtekintése és konfigurálása](diagnostic-logging.md).

### <a name="azure-monitor-alert-rule"></a>Azure Monitor riasztási szabály

Ez a [Azure Monitor riasztási szabály](https://aka.ms/DDOSmitigationstatus) egy egyszerű lekérdezést futtat, amely azt vizsgálja, hogy mikor történt aktív DDoS-csökkentés. Ez potenciális támadást jelez. A műveleti csoportok használatával a riasztás eredményeként műveleteket lehet meghívni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520Monitor%2520Alert%2520-%2520DDoS%2520Mitigation%2520Started%2FDDoSMitigationStarted.json)

### <a name="azure-monitor-alert-rule-with-logic-app"></a>Riasztási szabály Azure Monitor logikai alkalmazással

Ez a [sablon](https://aka.ms/ddosalert) egy dúsított DDoS-elhárítási riasztás szükséges összetevőit telepíti: Azure Monitor riasztási szabályt, műveleti csoportot és logikai alkalmazást. A folyamat eredménye egy e-mail-riasztás, amely a támadás alatt álló IP-címekkel kapcsolatos adatokat tartalmazza, beleértve az IP-címhez kapcsolódó erőforrást is. Az erőforrás tulajdonosa az e-mail címzettje lesz, a biztonsági csapattal együtt. A rendszer az alkalmazás alapszintű rendelkezésre állási tesztjét is elvégzi, és az eredményeket belefoglalja az e-mail riasztásba.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FDDoS%2520Mitigation%2520Alert%2520Enrichment%2FEnrich-DDoSAlert.json)

## <a name="configure-alerts-through-portal"></a>Riasztások konfigurálása a portálon keresztül

A rendelkezésre álló DDoS-védelmi mérőszámok bármelyikét kiválaszthatja, ha riasztást szeretne kapni, ha a támadás során aktív megoldás van a Azure Monitor riasztás konfigurációjának használatával. 

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és keresse meg a DDoS Protection tervet.
2. A **Figyelés** területen kattintson a **Metrikák** elemre.
3. A szürke navigációs sávon válassza az **új riasztási szabály** lehetőséget. 
4. Adja meg vagy válassza ki saját értékeit, vagy adja meg a következő példákat, fogadja el a fennmaradó alapértelmezett értékeket, majd válassza a **riasztási szabály létrehozása** elemet:

    |Beállítás                  |Érték                                                                                               |
    |---------                |---------                                                                                           |
    | Hatókör                   | Válassza az **Erőforrás kiválasztása** elemet. </br> Válassza ki azt az **előfizetést** , amely a naplózni kívánt nyilvános IP-címet tartalmazza, válassza ki a **nyilvános IP-címet** az **erőforrástípus** mezőben, majd válassza ki azt a nyilvános IP-címet, amelyre a metrikákat naplózni kívánja. </br> Válassza a **Kész** lehetőséget. | 
    | Feltétel | Válassza a **feltétel kiválasztása** lehetőséget. </br> A jel neve alatt válassza **a DDoS Attack vagy a not (nem**) lehetőséget. </br> Az **operátor** területen válassza **a nagyobb vagy egyenlő** lehetőséget. </br> Az **összesítés típusa** területen válassza a **maximális** lehetőséget. </br> A **küszöbérték** mezőben adja meg az *1* értéket. A **DDoS-támadás vagy a nem** metrikus érték esetén a **0** azt jelenti, hogy nem támadás alatt áll, míg **1** azt jelenti, hogy támadás alatt áll. </br> Válassza a **Kész** lehetőséget. | 
    | Műveletek | Válassza a **műveleti csoportok hozzáadása** lehetőséget. </br> Válassza a **Műveletcsoport létrehozása** lehetőséget. </br> Az **értesítések** területen az **értesítés típusa** területen válassza az **e-mail/SMS-üzenet/leküldése/hang** lehetőséget. </br> A **név** mezőben adja meg a _MyUnderAttackEmailAlert_. </br> Kattintson a Szerkesztés gombra, majd válassza az **e-mail** lehetőséget, és adja meg az alábbi lehetőségek közül a kívántat, majd kattintson az **OK gombra**. </br> Válassza az **Áttekintés + létrehozás** lehetőséget. | 
    | Riasztási szabály részletei | A **riasztási szabály neve** alatt adja meg a _MyDdosAlert_. |

A támadások észlelése néhány percen belül egy e-mailt kap Azure Monitor metrikától, amely az alábbi képhez hasonlóan néz ki:

![Támadási riasztás](./media/manage-ddos-protection/ddos-alert.png)

További információ: [webhookok](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [logikai alkalmazások](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) konfigurálása riasztások létrehozásához.

## <a name="view-alerts-in-azure-security-center"></a>Riasztások megtekintése Azure Security Center

Azure Security Center a [biztonsági riasztások](../security-center/security-center-managing-and-responding-alerts.md)listáját tartalmazza, és információt nyújt a problémák kivizsgálásához és javításához. Ezzel a funkcióval egységes nézetet kap a riasztásokról, többek között a DDoS-támadásokkal kapcsolatos riasztásokról, valamint a támadásoknak a közeljövőben történő enyhítésére tett műveletekről.
A DDoS-támadások észlelésének és enyhítésének két konkrét riasztása jelenik meg:

- **DDoS-támadás észlelhető a nyilvános IP-címek esetében**: Ez a riasztás akkor jön létre, ha a DDoS Protection szolgáltatás észleli, hogy az egyik nyilvános IP-cím a DDoS-támadás célpontja.
- **DDoS-támadás a nyilvános IP-címek esetében**: Ez a riasztás akkor jön létre, ha a nyilvános IP-cím támadása le van tiltva.
A riasztások megtekintéséhez nyissa meg **Security Center** a Azure Portalban. A **veszélyforrások védelme** területen válassza a **biztonsági riasztások** lehetőséget. Az alábbi képernyőképen egy példa látható a DDoS-támadások riasztására.

![DDoS-riasztás a Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

A riasztások a támadás, a Geo és a veszélyforrások felderítésére vonatkozó információk, valamint a Szervizelési lépések általános információit tartalmazzák a nyilvános IP-címekről.

## <a name="validate-and-test"></a>Ellenőrzés és tesztelés

A DDOS-támadások szimulálása a riasztások ellenőrzéséhez lásd: a [DDoS-észlelés ellenőrzése](test-through-simulations.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

- Riasztások konfigurálása Azure Monitor
- Riasztások konfigurálása a portálon keresztül
- Riasztások megtekintése Azure Security Center
- A riasztások ellenőrzése és tesztelése

A DDoS-támadások teszteléséről és szimulálásáról a szimulációs tesztelési útmutatóban talál további információt:

> [!div class="nextstepaction"]
> [Tesztelés szimulációk használatával](test-through-simulations.md)

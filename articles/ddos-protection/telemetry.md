---
title: A DDoS Protection telemetria megtekintése és konfigurálása Azure DDoS Protection standard szintű használatra
description: Megtudhatja, hogyan tekintheti meg és konfigurálhatja a DDoS Protection telemetria Azure DDoS Protection standard szintű használatra.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 0be184921ff0bd6b98dd2975acb4e0d5c8b26ba0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716193"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>DDoS Protection-telemetria megtekintése és konfigurálása

Azure DDoS Protection a standard részletes támadási elemzéseket és vizualizációkat biztosít a DDoS Attack Analytics használatával. A DDoS-támadásokkal szemben a virtuális hálózatokat védő ügyfelek részletesen megtekinthetik a támadási forgalmat, valamint a támadás enyhítésére tett lépéseket a támadásokkal kapcsolatos kockázatcsökkentő jelentésekben & a kockázatcsökkentő folyamat naplófájljait. A gazdag telemetria Azure Monitoron keresztül érhető el, beleértve a DDoS-támadások időtartama alatt részletes mérőszámokat is. A DDoS Protection által közzétett Azure Monitor-metrikákhoz konfigurálhatók riasztások. A naplózás az [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), a splunk (Azure Event Hubs), a OMS log Analytics és az Azure Storage szolgáltatással is integrálható a Azure monitor diagnosztikai felületen keresztül haladó elemzésekhez.

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> * DDoS Protection-telemetria megtekintése
> * DDoS-mérséklési szabályzatok megtekintése
> * A DDoS Protection telemetria ellenőrzése és tesztelése

### <a name="metrics"></a>Mérőszámok

> [!NOTE]
> Míg Azure Portal több **összesítési** lehetőség is megjelenik, az egyes metrikák esetében csak az alábbi táblázatban felsorolt összesítési típusok támogatottak. Elnézést kérünk erre a zűrzavarra, és dolgozunk a megoldásban.

A következő [metrikák](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses) érhetők el Azure DDoS Protection standard. Ezek a metrikák a diagnosztikai beállításokon keresztül is exportálhatók (lásd: a [DDoS diagnosztikai naplózás megtekintése és konfigurálása](diagnostic-logging.md)).


| Metric | Metrika megjelenítendő neve | Unit (Egység) | Aggregáció típusa | Leírás |
| --- | --- | --- | --- | --- |
| BytesDroppedDDoS | Bejövő bájtok elvetve DDoS | BytesPerSecond | Maximum | Bejövő bájtok elvetve DDoS| 
| BytesForwardedDDoS | Bejövő bájtok továbbított DDoS | BytesPerSecond | Maximum | Bejövő bájtok továbbított DDoS |
| BytesInDDoS | Bejövő bájtok DDoS | BytesPerSecond | Maximum | Bejövő bájtok DDoS |
| DDoSTriggerSYNPackets | Bejövő SYN-csomagok a DDoS-elhárítás elindításához | CountPerSecond | Maximum | Bejövő SYN-csomagok a DDoS-elhárítás elindításához |
| DDoSTriggerTCPPackets | Bejövő TCP-csomagok a DDoS-mérséklés indításához | CountPerSecond | Maximum | Bejövő TCP-csomagok a DDoS-mérséklés indításához |
| DDoSTriggerUDPPackets | Bejövő UDP-csomagok a DDoS-elhárítás elindításához | CountPerSecond | Maximum | Bejövő UDP-csomagok a DDoS-elhárítás elindításához |
| IfUnderDDoSAttack | DDoS-támadás alatt vagy nem | Darabszám | Maximum | DDoS-támadás alatt vagy nem |
| PacketsDroppedDDoS | Bejövő csomagok eldobott DDoS | CountPerSecond | Maximum | Bejövő csomagok eldobott DDoS |
| PacketsForwardedDDoS | Bejövő csomagok továbbított DDoS | CountPerSecond | Maximum | Bejövő csomagok továbbított DDoS |
| PacketsInDDoS | Bejövő csomagok DDoS | CountPerSecond | Maximum | Bejövő csomagok DDoS |
| TCPBytesDroppedDDoS | Bejövő TCP-bájtok elvetve DDoS | BytesPerSecond | Maximum | Bejövő TCP-bájtok elvetve DDoS |
| TCPBytesForwardedDDoS | Bejövő TCP-bájtok továbbított DDoS | BytesPerSecond | Maximum | Bejövő TCP-bájtok továbbított DDoS |
| TCPBytesInDDoS | Bejövő TCP-bájtok DDoS | BytesPerSecond | Maximum | Bejövő TCP-bájtok DDoS |
| TCPPacketsDroppedDDoS | Bejövő TCP-csomagok eldobott DDoS | CountPerSecond | Maximum | Bejövő TCP-csomagok eldobott DDoS |
| TCPPacketsForwardedDDoS | Bejövő TCP-csomagok továbbított DDoS | CountPerSecond | Maximum | Bejövő TCP-csomagok továbbított DDoS |
| TCPPacketsInDDoS | Bejövő TCP-csomagok DDoS | CountPerSecond | Maximum | Bejövő TCP-csomagok DDoS |
| UDPBytesDroppedDDoS | Bejövő UDP-bájtok elvetve DDoS | BytesPerSecond | Maximum | Bejövő UDP-bájtok elvetve DDoS |
| UDPBytesForwardedDDoS | Bejövő UDP-bájtok továbbított DDoS | BytesPerSecond | Maximum | Bejövő UDP-bájtok továbbított DDoS |
| UDPBytesInDDoS | Bejövő UDP bájtok DDoS | BytesPerSecond | Maximum | Bejövő UDP bájtok DDoS |
| UDPPacketsDroppedDDoS | Bejövő UDP-csomagok eldobott DDoS | CountPerSecond | Maximum | Bejövő UDP-csomagok eldobott DDoS |
| UDPPacketsForwardedDDoS | Bejövő UDP-csomagok továbbított DDoS | CountPerSecond | Maximum | Bejövő UDP-csomagok továbbított DDoS |
| UDPPacketsInDDoS | Bejövő UDP-csomagok DDoS | CountPerSecond | Maximum | Bejövő UDP-csomagok DDoS |

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- Az oktatóanyag lépéseinek elvégzése előtt először létre kell hoznia egy [Azure DDoS standard Protection-csomagot](manage-ddos-protection.md) , és a DDoS Protection szabványnak engedélyezve kell lennie egy virtuális hálózaton.
- A DDoS figyeli a virtuális hálózaton belüli erőforrásokhoz rendelt nyilvános IP-címeket. Ha nincsenek nyilvános IP-címmel rendelkező erőforrások a virtuális hálózatban, először létre kell hoznia egy nyilvános IP-című erőforrást. Az [Azure-szolgáltatások virtuális hálózata](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (beleértve az Azure Load balancert, ahol a háttérbeli virtuális gépek a virtuális hálózaton vannak), a Resource Managerrel telepített összes erőforrás nyilvános IP-címét nyomon követheti, kivéve Azure app Service környezetekben. Az oktatóanyag folytatásához gyorsan létrehozhat egy Windows vagy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [rendszerű](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépet.  

## <a name="view-ddos-protection-telemetry"></a>DDoS Protection-telemetria megtekintése

Az Azure Monitor valós időben biztosítja a támadásokkal kapcsolatos telemetriát. A telemetria csak akkor érhető el, ha egy nyilvános IP-cím mérséklés alatt áll. 

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és keresse meg a DDoS Protection tervet.
2. A **Figyelés** területen kattintson a **Metrikák** elemre.
3. Válassza ki a **hatókört**. Válassza ki azt az **előfizetést** , amely a naplózni kívánt nyilvános IP-címet tartalmazza, válassza ki a **nyilvános IP-címet** az **erőforrástípus** mezőben, majd válassza ki azt a nyilvános IP-címet, amelyre a metrikákat naplózni kívánja, majd válassza az **alkalmaz** lehetőséget.
4. Válassza ki az **összesítési** típust **Max**.

A metrikák nevei különböző típusú csomagokat és bájtokat, illetve csomagokat, a címkék neveinek alapszintű összeállításával az egyes mérőszámokban az alábbiak szerint:

- **Eldobott címke neve** (például a **bejövő csomagok eldobott DDoS**): a DDoS Protection rendszer által eldobott/kihagyott csomagok száma.
- **Továbbított címke neve** (például **bejövő csomagok továbbított DDoS**): a DDoS rendszer által továbbított csomagok száma a célként megadott VIP – a nem szűrt forgalom.
- **Nincs címke neve** (például a **bejövő csomagok DDoS**): a kimosó rendszerbe érkezett csomagok teljes száma, amely az eldobott és továbbított csomagok összegét jelöli.

## <a name="view-ddos-mitigation-policies"></a>DDoS-mérséklési szabályzatok megtekintése

DDoS Protection a standard három automatikusan hangolt kockázatcsökkentő házirendet alkalmaz (TCP SYN, TCP & UDP) a védett erőforrás minden nyilvános IP-címéhez, a DDoS-t engedélyező virtuális hálózatban. A házirend küszöbértékeit úgy tekintheti meg, ha kiválasztja a **Bejövő TCP-csomagokat a DDoS-mérséklés** **és a** bejövő UDP-csomagok kiválasztásával, hogy a **DDoS-mérséklési** metrikákat "Max" értékkel aktiválja, ahogy az alábbi képen látható:

![Kockázatcsökkentő házirendek megtekintése](./media/manage-ddos-protection/view-mitigation-policies.png)

A házirend-küszöbértékek automatikusan konfigurálva vannak az Azure Machine learning-alapú hálózati forgalom profilkészítése révén. A támadási IP-cím esetében csak a házirend küszöbértékének megszegése esetén a DDoS-elhárítás történik.

## <a name="validate-and-test"></a>Ellenőrzés és tesztelés

DDoS-támadás szimulálása a DDoS Protection telemetria ellenőrzéséhez lásd: a [DDoS-észlelés ellenőrzése](test-through-simulations.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

- Riasztások konfigurálása DDoS Protection-mérőszámokhoz
- DDoS Protection-telemetria megtekintése
- DDoS-mérséklési szabályzatok megtekintése
- A DDoS Protection telemetria ellenőrzése és tesztelése

Ha meg szeretné tudni, hogyan konfigurálhatja a támadás-elhárítási jelentéseket és a folyamat naplóit, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [DDoS-diagnosztikai naplózás megtekintése és konfigurálása](diagnostic-logging.md)
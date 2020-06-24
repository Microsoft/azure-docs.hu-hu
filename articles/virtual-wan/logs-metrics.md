---
title: Naplók és mérőszámok
titleSuffix: Azure Virtual WAN
description: Tudnivalók az Azure-beli virtuális WAN-naplókról és-mérőszámokról
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/05/2020
ms.author: cherylmc
ms.openlocfilehash: 4ce5bc3b4e5ccd26ec3f6b35c995ebe2274042c5
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84749720"
---
# <a name="azure-virtual-wan-logs-and-metrics"></a>Azure-beli virtuális WAN-naplók és-metrikák

Azure Monitor használatával figyelheti az Azure Virtual WAN-t. A Virtual WAN egy hálózati szolgáltatás, amely számos hálózati, biztonsági és útválasztási funkciót egyesít, hogy egyetlen operatív felületet biztosítson. A virtuális WAN VPN-átjárók, a ExpressRoute-átjárók és a Azure Firewall Azure Monitoron keresztül elérhetők naplózással és metrikákkal. Azure Firewall információ: [Azure Firewall naplók és metrikák](../firewall/logs-and-metrics.md).

Ez a cikk a portálon keresztül elérhető mérőszámokat és diagnosztikai adatokat ismerteti. A metrikák egyszerűek, és a közel valós idejű forgatókönyveket támogatják, így a riasztások és a gyors problémák észlelése is hasznos lehet.

## <a name="metrics"></a>Mérőszámok

A Azure Monitor mérőszámai olyan numerikus értékek, amelyek egy adott rendszer bizonyos aspektusait írják le egy adott időpontban. A metrikák gyűjtése percenként történik, és a rendszer a riasztásokhoz is hasznos, mivel azok gyakran mintavételezésre kerülnek. A riasztások gyorsan és viszonylag egyszerű logikával is elindíthatók.

### <a name="site-to-site-vpn-gateways"></a>Helyek közötti VPN-átjárók

Az Azure-helyek közötti VPN-átjárók esetében az alábbi metrikák érhetők el:

* **Átjáró sávszélessége** – a helyek közötti átlagos összesített sávszélesség (bájt/s).
* **Alagút sávszélessége** – az alagút átlagos sávszélessége másodpercenként bájtban megadva.
* **Alagút kimenő bájtjai** – az alagút kimenő bájtjai. 
* Tunnel kimenő csomagok – az alagút kimenő **csomagjainak** száma. 
* **Bújtatási csatorna nem egyezik a csomagok eldobása** – a kimenő csomagok eldobásának száma a forgalmi választótól eltérő alagút esetén. 
* **Bújtatási** forgalom (bájt) – az alagút bejövő bájtjai. 
* **Bújtatási bejövő csomagok** – egy alagút bejövő csomagjainak száma. 
* **Bújtatási átjárók nem egyeznek a csomagok eldobásával** – a bejövő csomagok eldobásának száma nem egyezik az alagút forgalmával. 

### <a name="point-to-site-vpn-gateways"></a>Pont – hely típusú VPN-átjárók

Az Azure pont – hely típusú VPN-átjárók esetében az alábbi mérőszámok érhetők el:

* Az **ÁTJÁRÓ P2S sávszélessége** – egy átjáró átlagos pont – hely összesített sávszélessége bájt/másodpercben.
* **P2S kapcsolatok száma** – pont – hely kapcsolati szám egy átjáró számára.

### <a name="azure-expressroute-gateways"></a>Azure ExpressRoute-átjárók

Az Azure ExpressRoute-átjárók esetében az alábbi mérőszámok érhetők el:

* **BitsInPerSecond** – BITS ingressing Azure másodpercenként.
* **BitsOutPerSecond** – BITS egressing Azure másodpercenként.

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>Átjáró metrikáinak megtekintése

A következő lépések segítségével megkeresheti és megtekintheti a metrikákat:

1. A portálon Navigáljon arra a virtuális hubhoz, amelyen az átjáró található.

2. A pont – hely átjáró megkereséséhez válassza a **VPN (helyek közötti)** lehetőséget a helyek közötti átjáró, a **ExpressRoute** vagy a ExpressRoute-átjáró megkereséséhez, vagy a **felhasználói VPN (a helyhez pont)** beállításhoz. Az oldalon megtekintheti az átjáró információit. Másolja ezt az információt. Később a Azure Monitor használatával fogja megtekinteni a diagnosztikát.

3. Válassza a **Metrika** lehetőséget.

   :::image type="content" source="./media/logs-metrics/metrics.png" alt-text="mutatókat":::

4. A **metrikák** lapon megtekintheti az Önt érdeklő mérőszámokat.

   :::image type="content" source="./media/logs-metrics/metrics-page.png" alt-text="metrikák lap":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>Diagnosztikai naplók

### <a name="site-to-site-vpn-gateways"></a>Helyek közötti VPN-átjárók

Az Azure-helyek közötti VPN-átjárók esetében az alábbi diagnosztika érhető el:

* **Átjáró diagnosztikai naplói** – átjáró-specifikus diagnosztika, például állapot, konfiguráció, szolgáltatási frissítések, valamint további diagnosztika.
* **Alagutak diagnosztikai naplói** – ezek az IPSec-alagutakhoz kapcsolódó naplók, például a helyek közötti IPsec-alagút, az egyeztetett SAs, a leválasztási okok és a további diagnosztika.
* **Diagnosztikai naplók irányítása** – ezek a statikus útvonalakra, a BGP-re, az útvonalak frissítéseire, valamint a további diagnosztikare vonatkozó eseményekkel kapcsolatos naplók.
* **IKE diagnosztikai naplók** – IKE-specifikus diagnosztika az IPSec-kapcsolatokhoz.

### <a name="point-to-site-vpn-gateways"></a>Pont – hely típusú VPN-átjárók

Az Azure pont – hely típusú VPN-átjárók esetében az alábbi diagnosztika érhető el:

* **Átjáró diagnosztikai naplói** – átjáró-specifikus diagnosztika, például állapot, konfiguráció, szolgáltatási frissítések és egyéb diagnosztika.
* **IKE diagnosztikai naplók** – IKE-specifikus diagnosztika az IPSec-kapcsolatokhoz.
* **P2S diagnosztikai naplók** – ezek a felhasználók VPN (pont – hely) P2S konfigurációja és az ügyfél eseményei. Ezek közé tartozik az ügyfelek csatlakoztatása/leválasztása, a VPN-ügyfél-címek kiosztása, valamint az egyéb diagnosztika.

### <a name="view-diagnostic-logs"></a><a name="diagnostic-steps"></a>Diagnosztikai naplók megtekintése

A következő lépések segítenek a diagnosztika megkeresésében és megtekintésében:

1. A portálon navigáljon a virtuális WAN-erőforráshoz. A portál virtuális WAN lapjának **Áttekintés** szakaszában válassza az **Essentials** elemet a nézet kibontásához és az erőforráscsoport adatainak beszerzéséhez. Másolja ki az erőforráscsoport adatait.

   :::image type="content" source="./media/logs-metrics/3.png" alt-text="metrikák lap":::

2. A figyelés szakaszban navigáljon az erőforráscsoporthoz. Válassza a **diagnosztikai beállítások**lehetőséget, majd adja meg az erőforrás-információkat. Ez a cikk [korábbi részében](#metrics-steps) a 2. lépésben átmásolt erőforrás-információ.

   :::image type="content" source="./media/logs-metrics/4.png" alt-text="metrikák lap":::

3. Az eredmények lapon válassza a **+ diagnosztikai beállítás hozzáadása**lehetőséget, majd válasszon egy beállítást. Dönthet úgy is, hogy elküldi Log Analytics, streamet egy Event hub-ba, vagy egyszerűen archiválja a Storage-fiókba.

   :::image type="content" source="./media/logs-metrics/5.png" alt-text="metrikák lap":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Log Analytics minta lekérdezés

A naplók az **Azure log Analytics-munkaterületen**találhatók. Beállíthat egy lekérdezést Log Analyticsban. A következő példa egy lekérdezést tartalmaz a helyek közötti útvonal-diagnosztika beszerzéséhez.

```AzureDiagnostics | where Category == "RouteDiagnosticLog"```

Szükség esetén cserélje le az alábbi értékeket a **= =** érték után.

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog"
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

## <a name="activity-logs"></a><a name="activity-logs"></a>Tevékenységnaplók

A rendszer alapértelmezés szerint gyűjti a **tevékenység naplójának** bejegyzéseit, és megtekintheti őket a Azure Portalban. Az Azure-tevékenység naplóinak (korábbi nevén *operatív naplók* és *naplók*) használatával megtekintheti az Azure-előfizetéséhez benyújtott összes műveletet.

## <a name="next-steps"></a>További lépések

* A Azure Firewall naplók és mérőszámok figyelésének megismeréséhez tekintse meg az [oktatóanyag: Azure Firewall naplók figyelése](../firewall/tutorial-diagnostics.md)című témakört.
* Ha többet szeretne megtudni a Azure Monitor metrikákkal kapcsolatban, tekintse meg a [Azure monitor mérőszámait](../azure-monitor/platform/data-platform-metrics.md).

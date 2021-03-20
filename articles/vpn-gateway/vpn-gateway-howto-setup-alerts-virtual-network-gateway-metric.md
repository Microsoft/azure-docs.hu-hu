---
title: Riasztások beállítása az Azure VPN Gateway-metrikák
description: Megtudhatja, hogyan állíthatja be Azure Monitor riasztásokat a virtuális hálózati VPN-átjárók metrikái alapján a Azure Portal használatával.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 05fbc5675d6ee3b6720d9db9e07e7010cf1d9172
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89435657"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Riasztások beállítása VPN Gateway mérőszámokon

Ebből a cikkből megtudhatja, hogyan állíthatja be a riasztásokat az Azure VPN Gateway-metrikák használatával. A Azure Monitor lehetővé teszi a riasztások beállítását az Azure-erőforrásokhoz. Beállíthat riasztásokat a "VPN" típusú virtuális hálózati átjárók számára.


|**Metrika**   | **Unit (Egység)** | **Részletesség** | **Leírás** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bájt/s  | 5 perc| Az átjárón a helyek közötti kapcsolatok átlagos összesített sávszélesség-kihasználtsága.     |
|**P2SBandwidth**| Bájt/s  | 1 perc  | Az átjárón az összes pont – hely kapcsolat átlagos összesített sávszélesség-kihasználtsága.    |
|**P2SConnectionCount**| Darabszám  | 1 perc  | Pont – hely kapcsolatok száma az átjárón.   |
|**TunnelAverageBandwidth** | Bájt/s    | 5 perc  | Az átjárón létrehozott alagutak átlagos sávszélesség-kihasználtsága. |
|**TunnelEgressBytes** | Bájt | 5 perc | Az átjárón létrehozott alagutak kimenő forgalma.   |
|**TunnelEgressPackets** | Darabszám | 5 perc | A kimenő csomagok száma az átjárón létrehozott alagutakon.   |
|**TunnelEgressPacketDropTSMismatch** | Darabszám | 5 perc | A forgalom-választói eltérés által okozott alagutakon eldobott kimenő csomagok száma. |
|**TunnelIngressBytes** | Bájt | 5 perc | Az átjárón létrehozott alagutak bejövő forgalma.   |
|**TunnelIngressPackets** | Darabszám | 5 perc | Az átjárón létrehozott alagutakon bejövő csomagok száma.   |
|**TunnelIngressPacketDropTSMismatch** | Darabszám | 5 perc | A forgalmat kiválasztó eltérések okozta alagutakon eldobott bejövő csomagok száma. |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Azure Monitor riasztások beállítása mérőszámok alapján a Azure Portal használatával

Az alábbi példa a következő lépésekkel hoz létre egy riasztást egy átjárón:

- **Metrika:** TunnelAverageBandwidth
- **Feltétel:** Sávszélesség > 10 bájt/másodperc
- **Ablak:** 5 perc
- **Riasztási művelet:** E-mail



1. Lépjen a virtuális hálózati átjáró erőforráshoz, és válassza a **riasztások** lehetőséget a **figyelés** lapon. Ezután hozzon létre egy új riasztási szabályt, vagy szerkesszen egy meglévő riasztási szabályt.

   ![Riasztási szabály létrehozásához szükséges beállítások](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Létrehozás")

2. Válassza ki a VPN-átjárót erőforrásként.

   ![A Select (kiválasztás) gomb és a VPN-átjáró az erőforrások listájában](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Válassza ezt:")

3. Válassza ki a riasztáshoz konfigurálni kívánt metrikát.

   ![A metrikák listájában kiválasztott metrika](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Válassza ezt:")
4. Adja meg a jel logikáját. Három összetevőből áll:

    a. **Méretek**: Ha a metrika dimenziókkal rendelkezik, kiválaszthatja az egyes dimenzió értékeket, hogy a riasztás csak az adott dimenzió adatait értékelje ki. Ezeket nem kötelező megadni.

    b. **Feltétel**: Ez a művelet a metrika értékének kiértékelésére szolgál.

    c. **Idő**: a metrikai adatok részletességének és a riasztás kiértékelésének időtartamának meghatározása.

   ![A jel logikájának konfigurálásával kapcsolatos részletek](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Válassza ezt:")

5. A konfigurált szabályok megtekintéséhez válassza a **riasztási szabályok kezelése** lehetőséget.

   ![Riasztási szabályok kezelésére szolgáló gomb](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Válassza ezt:")

## <a name="next-steps"></a>Következő lépések

A riasztások bújtatási erőforrás-naplókban való konfigurálásáról további információt [a riasztások beállítása VPN Gateway erőforrás-naplókon](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)című témakörben talál.

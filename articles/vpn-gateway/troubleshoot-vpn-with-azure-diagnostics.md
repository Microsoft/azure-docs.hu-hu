---
title: Az Azure VPN Gateway hibáinak megoldása diagnosztikai naplók használatával
description: Az Azure VPN Gateway hibáinak megoldása diagnosztikai naplók használatával
services: vpn-gateway
author: stegag
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/15/2021
ms.author: stegag
ms.openlocfilehash: 232e084e44696c6aa88a9dd33092c48a96e35f85
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771999"
---
# <a name="troubleshoot-azure-vpn-gateway-using-diagnostic-logs"></a>Az Azure VPN Gateway hibáinak megoldása diagnosztikai naplók használatával

Ez a cikk segít megérteni a VPN Gateway diagnosztika számára elérhető különböző naplókat, és hogyan használhatja őket a VPN-átjárókkal kapcsolatos problémák hatékony megoldásához.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Az Azure-ban az alábbi naplók érhetők el:

|***Név** _ | _ *_Leírás_** |
|---        | ---               |
|**GatewayDiagnosticLog** | Diagnosztikai naplókat tartalmaz az átjáró konfigurációs eseményeihez, az elsődleges változásokhoz és a karbantartási eseményekhez. |
|**TunnelDiagnosticLog** | Bújtatási állapot változási eseményeit tartalmazza. Az alagút kapcsolódási/leválasztási eseményeinek összefoglaló oka van az állapot változásához, ha van ilyen. |
|**RouteDiagnosticLog** | A statikus útvonalak és az átjárón előforduló BGP-események változásainak naplózása. |
|**IKEDiagnosticLog** | Naplózza az IKE-vezérlési üzeneteket és eseményeket az átjárón. |
|**P2SDiagnosticLog** | A pont – hely típusú vezérlő üzeneteinek és eseményeinek naplózása az átjárón. |

Figyelje meg, hogy a táblázatokban több oszlop is elérhető. Ebben a cikkben csak a legfontosabb adatokat láthatjuk a naplók használatának megkönnyítésére.

## <a name="set-up-logging"></a><a name="setup"></a>Naplózás beállítása

Ha meg szeretné tudni, hogyan állíthatja be a diagnosztikai napló eseményeit az Azure VPN Gateway az Azure Log Analytics használatával, tekintse meg a [riasztások beállítása a diagnosztikai naplózási eseményekről a VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log).

## <a name="gatewaydiagnosticlog"></a><a name="GatewayDiagnosticLog"></a>GatewayDiagnosticLog

A konfigurációs változások naplózása a **GatewayDiagnosticLog** táblában történik. Néhány percet is igénybe vehet, amíg a végrehajtott módosítások megjelennek a naplókban.

Itt van egy példaként szolgáló lekérdezés a hivatkozásként.

```
AzureDiagnostics  
| where Category == "GatewayDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup  
| sort by TimeGenerated asc
```

A **GatewayDiagnosticLog** -lekérdezésben több oszlop jelenik meg.

|***Név** _ | _ *_Leírás_** |
|---        | ---               |
|**TimeGenerated** | az egyes események időbélyegzője UTC időzóna szerint.|
|**OperationName** |az esemény történt. Ez lehet *SetGatewayConfiguration, SetConnectionConfiguration, HostMaintenanceEvent, GatewayTenantPrimaryChanged, MigrateCustomerSubscription, GatewayResourceMove, ValidateGatewayConfiguration*.|
|**Üzenetet** | a művelet végrehajtásának részletei, és a sikeres/sikertelen eredmények felsorolása.|

Az alábbi példa az új konfiguráció alkalmazása során naplózott tevékenységet mutatja be:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-26-set-gateway.png" alt-text="Példa a GatewayDiagnosticLog-ben látott set Gateway műveletre.":::


Figyelje meg, hogy a rendszer minden alkalommal naplózza a SetGatewayConfiguration, amikor egy VPN Gateway vagy egy helyi hálózati átjárón módosítja a konfigurációt.
A **GatewayDiagnosticLog** táblából a **TunnelDiagnosticLog** tábla eredményeire hivatkozó kereszthivatkozások segíthetnek megállapítani, hogy az alagút csatlakozási hibája elindult-e a konfiguráció módosításának időpontjában, vagy egy karbantartás történt. Ha igen, nagyszerű mutatónk van a lehetséges kiváltó ok kialakulására.

## <a name="tunneldiagnosticlog"></a><a name="TunnelDiagnosticLog"></a>TunnelDiagnosticLog

A **TunnelDiagnosticLog** tábla nagyon hasznos az alagút korábbi kapcsolati állapotának vizsgálatához.

Itt van egy példaként szolgáló lekérdezés a hivatkozásként.

```
AzureDiagnostics
| where Category == "TunnelDiagnosticLog"
| project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup
| sort by TimeGenerated asc
```

A **TunnelDiagnosticLog** -lekérdezésben több oszlop jelenik meg.


|***Név** _ | _ *_Leírás_** |
|---        | ---               |
|**TimeGenerated** | az egyes események időbélyegzője UTC időzóna szerint.|
|**OperationName** | az esemény történt. Akár *TunnelConnected* , akár *TunnelDisconnected* is lehet.|
| **Példány \_ s** | az eseményt kiváltó átjáró szerepkör-példány. A GatewayTenantWorker \_ \_ 0 vagy GatewayTenantWorker is lehet \_ \_ , amely az átjáró két példányának neve.|
| **Erőforrás** | a VPN-átjáró nevét jelzi. |
| **ResourceGroup** | azt az erőforráscsoportot jelzi, amelyben az átjáró található.|


Példa a kimenetre:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-16-tunnel-connected.png" alt-text="Példa a TunnelDiagnosticLog-ben látott alagúthoz csatlakoztatott eseményre.":::


A **TunnelDiagnosticLog** nagyon hasznos a váratlan VPN-leválasztásokkal kapcsolatos múltbeli események megoldásához. A könnyű jellege révén számos nap alatt elemezheti a nagy időtartományokat.
Csak azt követően, hogy azonosította a leválasztás időbélyegét, átválthat a **IKEdiagnosticLog** táblázat részletesebb elemzésére, hogy a leválasztások indoklása mélyebbre kerüljön, ezek az IPSec-sel kapcsolatosak.


Néhány hibaelhárítási tipp:
- Ha egy átjáró-példányon megjelenő leválasztási esemény jelenik meg, amelyet néhány másodpercen belül a **különböző** átjáró-példányon egy kapcsolati esemény követ, egy átjáró feladatátvételt keres. Ez általában az átjáró-példányok karbantartása miatt várt viselkedés. További információ erről a viselkedésről: [About Azure VPN Gateway redundancia](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable#about-azure-vpn-gateway-redundancy).
- Ugyanez a viselkedés jelenik meg, ha szándékosan futtatja az átjárót az Azure-on, amely az aktív átjáró példányának újraindítását okozza. További információ erről a viselkedésről: [VPN Gateway alaphelyzetbe állítása](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-resetgw-classic).
- Ha a leválasztási eseményt egy adott átjárón látja, és néhány másodpercen belül egy, **ugyanazon** az átjárón lévő kapcsolati esemény követ, akkor előfordulhat, hogy a DPD időtúllépést okozó hálózati hibát keres, vagy a helyszíni eszköz hibásan küldte el a leválasztást.

## <a name="routediagnosticlog"></a><a name="RouteDiagnosticLog"></a>RouteDiagnosticLog

A **RouteDiagnosticLog** táblázat a BGP-n keresztül fogadott statikusan módosított útvonalak vagy útvonalak tevékenységeit követi nyomon.

Itt van egy példaként szolgáló lekérdezés a hivatkozásként.

```
AzureDiagnostics
| where Category == "RouteDiagnosticLog"
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

A **RouteDiagnosticLog** -lekérdezésben több oszlop jelenik meg.

|***Név** _ | _ *_Leírás_** |
|---        | ---               |
|**TimeGenerated** | az egyes események időbélyegzője UTC időzóna szerint.|
|**OperationName** | az esemény történt. A következők egyike lehet: *StaticRouteUpdate, BgpRouteUpdate, BgpConnectedEvent, BgpDisconnectedEvent*.|
| **Üzenetet** | annak részletei, hogy milyen műveletek történnek.|

A kimenetben hasznos információk jelennek meg a csatlakoztatott/leválasztott BGP-partnerekről és az útvonalakról.

Példa:


:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-31-bgp-route.png" alt-text="Példa a RouteDiagnosticLog-ben látott BGP Route Exchange-tevékenységre.":::


## <a name="ikediagnosticlog"></a><a name="IKEDiagnosticLog"></a>IKEDiagnosticLog

A **IKEDiagnosticLog** tábla részletes hibakeresési naplózást biztosít az IKE/IPSec szolgáltatáshoz. Ez nagyon hasznos, ha át szeretné tekinteni a kapcsolati hibák elhárítását, vagy nem sikerül csatlakozni a VPN-forgatókönyvekhez.

Itt van egy példaként szolgáló lekérdezés a hivatkozásként.

```
AzureDiagnostics  
| where Category == "IKEDiagnosticLog" 
| extend Message1=Message
| parse Message with * "Remote " RemoteIP ":" * "500: Local " LocalIP ":" * "500: " Message2
| extend Event = iif(Message has "SESSION_ID",Message2,Message1)
| project TimeGenerated, RemoteIP, LocalIP, Event, Level 
| sort by TimeGenerated asc
```

A **IKEDiagnosticLog** -lekérdezésben több oszlop jelenik meg.


|***Név** _ | _ *_Leírás_** |
|---        | ---               |
|**TimeGenerated** | az egyes események időbélyegzője UTC időzóna szerint.|
| **RemoteIP** | a helyszíni VPN-eszköz IP-címe. A valós forgatókönyvekben hasznos lehet a megfelelő helyszíni eszköz IP-címének szűrése, ha egynél többre van szükség. |
|**LocalIP** | a hibaelhárítási VPN Gateway IP-címe. Valós helyzetekben hasznos lehet a megfelelő VPN-átjáró IP-címének szűrése, ha az előfizetésben egynél több szerepel. |
|**Esemény** | a hibaelhárításhoz hasznos diagnosztikai üzenetet tartalmaz. Általában egy kulcsszóval kezdődnek, és az Azure Gateway által végrehajtott műveletekre hivatkoznak: a **\[ Send \]** az Azure Gateway által küldött IPSec-csomagok által okozott eseményt jelzi.  A **\[ fogadás \]** egy eseményt jelez, amely a helyszíni eszköztől kapott csomag következménye.  A **\[ helyi \]** művelet az Azure-átjáró által helyileg végrehajtott műveletet jelzi. |


Figyelje meg, hogy a RemoteIP, a LocalIP és az Event oszlopok nem szerepelnek a AzureDiagnostics-adatbázis eredeti oszlopainak listájában, de a lekérdezésbe az "üzenet" oszlop kimenetének elemzésével leegyszerűsítheti az elemzését.

Hibaelhárítási tippek:

- Az IPSec-egyeztetés kezdetének azonosításához meg kell találnia a kezdeti SA \_ init-üzenetet. Ezt az üzenetet az alagút egyik oldalán is elküldheti. Aki elküldi az első csomagot "kezdeményezőnek" az IPsec-terminológiában, míg a másik oldal a "válaszadó" lesz. Az első SA \_ init-üzenet mindig az a hely, ahol a rCookie = 0.

- Ha az IPsec-alagút létrehozása nem sikerül, az Azure néhány másodpercenként újra próbálkozik. Emiatt a "VPN Down" hibáinak elhárítása nagyon kényelmes a IKEdiagnosticLog, mert nem kell megvárnia egy adott időt a probléma újbóli előállítására. Emellett a hiba elvileg mindig ugyanaz lesz, mint amikor megpróbáljuk, így egyszerűen egyetlen "minta" sikertelen egyeztetést végezhet.

- Az SA \_ init tartalmazza azokat az IPSec-paramétereket, amelyeket a társ használni szeretne ehhez az IPsec-egyeztetéshez. A hivatalos dokumentum   
Az [alapértelmezett IPSec/IKE-paraméterek](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec) az Azure Gateway által az alapértelmezett beállításokkal támogatott IPSec-paramétereket listázza.


## <a name="p2sdiagnosticlog"></a><a name="P2SDiagnosticLog"></a>P2SDiagnosticLog

A VPN-diagnosztika utolsó elérhető táblázata a **P2SDiagnosticLog**. Ez a tábla a pont – hely közötti tevékenységet követi nyomon.

Itt van egy példaként szolgáló lekérdezés a hivatkozásként.

```
AzureDiagnostics  
| where Category == "P2SDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

A **P2SDiagnosticLog** -lekérdezésben több oszlop jelenik meg.

|***Név** _ | _ *_Leírás_** |
|---        | ---               |
|**TimeGenerated** | az egyes események időbélyegzője UTC időzóna szerint.|
|**OperationName** | az esemény történt. *P2SLogEvent* lesz.|
| **Üzenetet** | annak részletei, hogy milyen műveletek történnek.|

A kimenet megjeleníti az átjáró által alkalmazott összes pont és a hely beállításait, valamint az IPsec-házirendeket.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-28-p2s-log-event.png" alt-text="Példa arra, hogy a P2SDiagnosticLog-ben látott hely kapcsolatra mutasson.":::

Továbbá, amikor egy ügyfél a IKEv2 vagy az OpenVPN pontról a webhelyre csatlakozik, a tábla a csomag tevékenység, az EAP/RADIUS-beszélgetések és a sikeres/sikertelen eredmények felhasználó általi naplózását fogja naplózni.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-29-eap.png" alt-text="Példa a P2SDiagnosticLog-ben látott EAP-hitelesítésre.":::

## <a name="next-steps"></a>Következő lépések

A riasztások bújtatási erőforrás-naplókban való konfigurálásáról további információt [a riasztások beállítása VPN Gateway erőforrás-naplókon](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)című témakörben talál.


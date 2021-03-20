---
title: Azure Firewall-adatkapcsolatok összekötése az Azure Sentinel szolgáltatással
description: Megtudhatja, hogyan csatlakoztatható Azure Firewall-adatkapcsolat az Azure Sentinelhez.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: b21ce75bfb33b5a8869c63b7d3f71fb9f0c93768
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98621295"
---
# <a name="connect-data-from-azure-firewall"></a>Adatok összekapcsolásának Azure Firewall

Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. Ez egy teljes körűen kiépíthető, magas rendelkezésre állású és korlátlan Felhőbeli skálázhatósági szolgáltatás. 

Azure Firewall naplókat összekapcsolhatja az Azure Sentinel szolgáltatással, így megtekintheti a munkafüzetek naplófájljait, felhasználhatja egyéni riasztások létrehozására, és beépítheti azt a vizsgálat javítására.

További információ a [Azure Firewall naplók figyeléséről](../firewall/firewall-diagnostics.md).

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedélyekkel kell rendelkeznie az Azure Sentinel munkaterületen.

## <a name="connect-to-azure-firewall"></a>Kapcsolódás Azure Firewallhoz
    
1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Válassza a **Azure Firewall** lehetőséget az adatösszekötők katalógusában, majd az előnézet ablaktáblán válassza az **összekötő megnyitása lapot**  .

1. Engedélyezze a **diagnosztikai naplókat** az összes olyan tűzfalon, amelynek a naplóihoz csatlakozni szeretne:

    1. Jelölje be az **Azure Firewall erőforrás megnyitása >** hivatkozást.

    1. A **tűzfalak** navigációs menüjében válassza a **diagnosztikai beállítások** elemet.

    1. Válassza a **+ diagnosztikai beállítások hozzáadása** elemet a lista alján.

    1. A diagnosztikai **Beállítások** képernyőn adjon meg egy nevet a  **diagnosztikai beállítások neve** mezőben.
    
    1. Jelölje be a **küldés log Analyticsra** jelölőnégyzetet. Alatta két új mező jelenik meg. Válassza ki a megfelelő **előfizetést** és **log Analytics munkaterületet** (ahol az Azure Sentinel található).

    1. Megjelölheti azoknak a szabályoknak a jelölőnégyzeteit, amelyek naplóit be szeretné állítani. Javasoljuk a **AzureFirewallApplicationRule** és a **AzureFirewallNetworkRule** használatát.

    1. Válassza a **Mentés** lehetőséget a képernyő tetején.

1. Ha Azure Firewall riasztások esetében szeretné használni a Log Analytics vonatkozó sémát, keresse meg a **AzureDiagnostics**.

> [!NOTE]
>
> Ezzel az adatösszekötővel a kapcsolati állapot mutatói (az adatösszekötők katalógusában és az adattípusok neve melletti kapcsolati ikonokban lévő színcsíkok) csak akkor jelennek *meg, ha* az adatok betöltése az elmúlt két hétben egy bizonyos pontján történik. Ha két hét telt el az adatfeldolgozás nélkül, az összekötő leválasztva jelenik meg. Minél több információ érkezik, a *csatlakoztatott* állapot vissza fog térni.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakozhat Azure Firewall naplókat az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
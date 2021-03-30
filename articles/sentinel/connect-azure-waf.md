---
title: Azure webalkalmazási tűzfal (WAF)-adatkapcsolatok összekapcsolása az Azure Sentinel szolgáltatással
description: Ismerje meg, hogyan csatlakoztathatók az Azure WAF-beli adatközpontok az Azure Sentinelhez.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 05/07/2020
ms.author: yelevin
ms.openlocfilehash: c554f3582e67622a5a1739c9e410328c902d491b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655901"
---
# <a name="connect-data-from-azure-web-application-firewall-waf"></a>Adatok összekapcsolása az Azure Web Application Firewall (WAF) szolgáltatással

A webalkalmazások egyre inkább a gyakran ismert biztonsági réseket kihasználó rosszindulatú támadásokra irányulnak. Az Azure webalkalmazási tűzfal (WAF) lehetővé teszi a webalkalmazások központosított védelmét a gyakori támadásokkal és fenyegetésekkel szemben, például a kódok injektálásával és a helyek közötti parancsfájlkezeléssel. Az Azure WAF üzembe helyezhető az [azure Application Gateway](../web-application-firewall/ag/ag-overview.md) szolgáltatáson, az [Azure bejárati](../web-application-firewall/afds/afds-overview.md) szolgáltatásán, valamint egy [Azure Content Delivery Network (CDN)](../web-application-firewall/cdn/cdn-overview.md) WAF-szabályzaton keresztül (ez utóbbi jelenleg nyilvános előzetes verzióban érhető el).
Az Azure WAF-naplókat az Azure Sentinelhez is összekapcsolhatja, így megtekintheti a munkafüzetekben tárolt naplófájlokat, egyéni riasztásokat hozhat létre, és beépítheti azt a vizsgálat javítására.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedélyekkel kell rendelkeznie az Azure Sentinel munkaterületen.

## <a name="connect-to-azure-waf"></a>Kapcsolódás az Azure WAF

### <a name="instructions-tab"></a>Utasítások lap

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Válassza az **Azure webalkalmazási tűzfal (WAF)** lehetőséget az adatösszekötők gyűjteményből, majd az előnézet ablaktáblán válassza az **összekötő lap megnyitása** lehetőséget.

1. Válassza ki annak a WAF-erőforrásnak a típusát, amelynek a naplóit csatlakoztatni kívánja – **nyissa meg Application Gateway erőforrás->**, **nyissa meg az előtérben található erőforrás >** vagy **nyissa meg Content Delivery Network (CDN) WAF házirendet >** – és egyszer az erőforráslista képernyőjén válasszon egy WAF-erőforrást a listából.

    1. A WAF-erőforrás navigációs menüjében válassza a **diagnosztikai beállítások** elemet.

    1. Válassza a **+ diagnosztikai beállítások hozzáadása** elemet a lista alján.

    1. A **diagnosztikai beállítások** képernyőn írjon be egy nevet a **diagnosztikai beállítások neve** mezőbe.

    1. Kattintson a **küldés log Analyticsre** jelölőnégyzetre. Alatta két új mező jelenik meg. Válassza ki a megfelelő **előfizetést** és **log Analytics munkaterületet** (ahol az Azure Sentinel található).

    1. Kattintson annak a szabálytípusnek a jelölőnégyzetéből, amelynek a naplóit be szeretné állítani. Az egyes erőforrástípusok javaslatai:

        | Erőforrás | Betöltésre kiválasztott naplók |
        |----------|------------------------------|
        | Application Gateway | ApplicationGatewayAccessLog<br>ApplicationGatewayFirewallLog |
        | Front Door          | FrontdoorAccessLog<br>FrontdoorWebApplicationFirewallLog |
        | CDN WAF szabályzat      | WebApplicationFirewallLogs |
        |

    1. Kattintson a **Mentés** gombra.

### <a name="next-steps-tab"></a>Következő lépések lap

- Tekintse meg az Azure **webalkalmazási tűzfal** adatösszekötőjét tartalmazó ajánlott munkafüzetek, lekérdezési minták és elemzési szabályok sablonjait, hogy betekintést kapjon az Azure WAF-naplózási adataiba.

- Az Azure WAF- **Adatnaplókban** való lekérdezéséhez írja be a **AzureDiagnostics** értéket a lekérdezési ablakba.

> [!NOTE]
>
> Ezzel az adatösszekötővel a kapcsolati állapot mutatói (az adatösszekötők katalógusában és az adattípusok neve melletti kapcsolati ikonokban lévő színcsíkok) csak akkor jelennek *meg, ha* az adatok betöltése az elmúlt két hétben egy bizonyos pontján történik. Ha két hét telt el az adatfeldolgozás nélkül, az összekötő leválasztva jelenik meg. Minél több információ érkezik, a *csatlakoztatott* állapot vissza fog térni.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható az Azure WAF-naplók az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
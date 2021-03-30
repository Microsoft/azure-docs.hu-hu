---
title: Citrix WAF-adatkapcsolatok összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Citrix WAF-adatösszekötőt a naplók Azure Sentinelbe való lekéréséhez. Megtekintheti a munkafüzetekben tárolt Citrix WAF-és riasztásokat, és javíthatja a vizsgálatot.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: 475e04ad3dd8c7cc51d6345b4b51dd68cff5b597
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93102978"
---
# <a name="connect-your-citrix-waf-to-azure-sentinel"></a>A Citrix WAF és az Azure Sentinel összekötése

> [!IMPORTANT]
> A Citrix Web Application Firewall (WAF) adatösszekötő az Azure Sentinel szolgáltatásban jelenleg nyilvános előzetes verzióban érhető el. Ezt a szolgáltatást szolgáltatói szerződés nélkül biztosítjuk. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk azt ismerteti, hogyan csatlakoztatható a Citrix Web Application Firewall (WAF) készülék az Azure Sentinelhez. A Citrix WAF adatösszekötővel könnyedén csatlakoztathatja a Citrix WAF-naplókat az Azure Sentinel használatával, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. A Citrix WAF CEF-naplók az Azure Sentinelhez való csatlakoztatásával kihasználhatja a keresés és a korreláció, a riasztás és a fenyegetések felderítésének előnyeit az egyes naplókhoz.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="forward-citrix-waf-logs-to-the-syslog-agent"></a>Citrix WAF-naplók továbbítása a syslog-ügynökbe  

A Citrix WAF a syslog-üzeneteket CEF formátumban küldi el egy Linux-alapú, a rsyslog-t vagy syslog-ng-t futtató naplózási kiszolgálónak, amelyen telepítve van a Log Analytics ügynök, amely továbbítja a naplókat az Azure Sentinel-munkaterületre.

1. Ha nem rendelkezik ilyen naplózási kiszolgálóval, tekintse meg az [alábbi utasításokat](connect-cef-agent.md) .

1. Kövesse a Citrix által megadott utasításokat [a WAF konfigurálásához, a](https://support.citrix.com/article/CTX234174) [CEF naplózásának konfigurálásához](https://support.citrix.com/article/CTX136146), valamint [a naplók küldésének konfigurálásához a napló továbbítójának](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html). Ügyeljen arra, hogy a naplókat az 514-as TCP-portra küldje a naplózási továbbító számítógép IP-címére.

1. Érvényesítse a kapcsolatokat, és ellenőrizze az adatfeldolgozást [ezen utasítások](connect-cef-verify.md)alapján. Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat megjelenik a **naplók** területen az **Azure Sentinel** szakaszban, a *CommonSecurityLog* táblában.

A Log Analytics Citrix WAF-naplók lekérdezéséhez írja be a `CommonSecurityLog` parancsot a lekérdezési ablak elejére.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a Citrix WAF az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
---
title: CyberArk-EPV-adatbázis összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan használhatja a CyberArk Enterprise Password Vault (EPV) adatösszekötőt a naplók Azure Sentinel szolgáltatásba való lekéréséhez. Megtekintheti a munkafüzetek CyberArk EPV, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
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
ms.openlocfilehash: aa57963ce369e4c8f84f4aae5f99fe343181ff6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100530501"
---
# <a name="connect-cyberark-enterprise-password-vault-epv-to-azure-sentinel"></a>A CyberArk Enterprise Password Vault (EPV) és az Azure Sentinel összekötése

> [!IMPORTANT]
> A CyberArk Enterprise Password Vault (EPV) adatösszekötő az Azure Sentinel szolgáltatásban jelenleg nyilvános előzetes verzióban érhető el. Ezt a szolgáltatást szolgáltatói szerződés nélkül biztosítjuk. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A CyberArk syslog-összekötővel egyszerűen csatlakoztathatja az összes CyberArk-biztonsági megoldás naplóját az Azure Sentinelhez, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. A CyberArk és az Azure Sentinel közötti integráció a CEF-adatösszekötő használatával biztosítja a CyberArk syslog-üzenetek megfelelő elemzését és megjelenítését.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="configure-and-connect-cyberark-epv"></a>CyberArk-EPV konfigurálása és összekapcsolása

A CyberArk EPV-naplókat a rendszer a tárolóból egy Linux-alapú (rsyslog vagy syslog-ng-t futtató) naplózási kiszolgálóra küldi, amelyen telepítve van az Log Analytics-ügynök, amely a naplókat az Azure Sentinelbe exportálja. Ha nem rendelkezik ilyen naplózási kiszolgálóval, tekintse meg az [alábbi utasításokat](connect-cef-agent.md) .

1. Az Azure Sentinel-portálon kattintson az **adatösszekötők** elemre, válassza a **CyberArk Enterprise Password Vault (EPV) események (előzetes verzió)** lehetőséget, majd **nyissa meg az összekötő lapot**.

1. A [CYBERARK EPV utasításait](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm) követve konfigurálhatja a syslog-adatok küldését a naplófájl-továbbító kiszolgálóra.

1. Érvényesítse a kapcsolatokat, és ellenőrizze az adatfeldolgozást [ezen utasítások](connect-cef-verify.md)alapján. Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat megjelenik a **naplók** területen az **Azure Sentinel** szakaszban, a *CommonSecurityLog* táblában.

A Log Analytics CyberArk EPV-naplófájljainak lekérdezéséhez írja be a `CommonSecurityLog` parancsot a lekérdezési ablak elejére.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a CyberArk Enterprise Password Vault-naplók az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.

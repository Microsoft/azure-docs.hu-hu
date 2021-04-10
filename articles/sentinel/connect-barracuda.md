---
title: A Barracuda-adatkapcsolatok és az Azure Sentinel összekötése Microsoft Docs
description: Ismerje meg, hogy a Barracuda Web Application Firewall (WAF) összekötővel hogyan csatlakoztathatók a Barracuda-naplók az Azure Sentinel használatával.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: e1462246b95da67591cbdfd1f9ed819220de5764
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98633061"
---
# <a name="connect-your-barracuda-waf-appliance"></a>A Barracuda WAF-berendezés összekötése 

A Barracuda webalkalmazási tűzfal (WAF) összekötő segítségével könnyedén csatlakoztathatja a Barracuda-naplókat az Azure Sentinelhez, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. Ez nagyobb betekintést nyújt a szervezet hálózatára, és javítja a biztonsági műveletek képességeit. Az Azure Sentinel kihasználja a **Barracuda** és a log Analytics-ügynök közötti natív integrációt, hogy zökkenőmentes integrációt biztosítson. 

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="configure-and-connect-barracuda-waf"></a>A Barracuda WAF konfigurálása és összekapcsolása

A Barracuda webalkalmazási tűzfal a naplókat közvetlenül az Azure Sentinelbe integrálhatja és exportálhatja Log Analytics ügynök használatával.

1. Lépjen a [BARRACUDA WAF konfigurációs folyamathoz](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/), és kövesse az utasításokat a kapcsolódás beállításához a következő paraméterek használatával:

    - **Munkaterület azonosítója**: másolja a munkaterület-azonosító értékét az Azure Sentinel Barracuda-összekötő oldaláról.

    - **Elsődleges kulcs**: másolja az elsődleges kulcs értékét az Azure Sentinel Barracuda-összekötő oldaláról.

1. A Barracuda-események Log Analytics vonatkozó sémájának használatához keresse meg a **CommonSecurityLog** és a **barracuda_CL**.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics. 



## <a name="next-steps"></a>Következő lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatók a Barracuda-készülékek az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.



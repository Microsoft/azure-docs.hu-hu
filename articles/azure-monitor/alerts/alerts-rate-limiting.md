---
title: SMS-, e-mail-és leküldéses értesítések korlátozása
description: Ismerje meg, hogyan korlátozza az Azure a lehetséges SMS-t, e-maileket, Azure app push-vagy webhook-értesítéseket a műveleti csoportokból.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 3/12/2018
ms.subservice: alerts
ms.openlocfilehash: 717f4087dabbb3286607498ab7f04bcbcce663dd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737273"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>A hangra, SMS-re, e-mailekre, az Azure-alkalmazás leküldéses értesítéseire és a webhookra vonatkozó hozzászólások
A díjszabási korlátozás az értesítések felfüggesztése, amely akkor fordul elő, ha túl sok érkezik egy adott telefonszámra, e-mail-címre vagy eszközre. A díjszabási korlátozás biztosítja, hogy a riasztások kezelhető és végrehajthatóak legyenek.

A díjszabási küszöbértékek a következők:

- **SMS**: legfeljebb 1 SMS-t 5 percenként.
- **Hang**: legfeljebb 1 hanghívás 5 percenként.
- **E-mail**: legfeljebb 100 e-mail-cím egy órán belül.
 
  A többi művelet nem korlátozott.

## <a name="rate-limit-rules"></a>Maximális díjszabási szabályok
- Egy adott telefonszám vagy e-mail-cím korlátozott, ha több üzenetet kap, mint amennyit a küszöbérték lehetővé tesz.
- A telefonszámok vagy e-mailek számos előfizetésben lehetnek a műveleti csoportok részei. A díjszabásra vonatkozó korlátozás az összes előfizetésre vonatkozik. A küszöbérték elérésekor akkor is érvényes, ha több előfizetésből is érkeznek üzenetek.
- Ha egy e-mail-cím korlátozott, a rendszer további értesítést küld a korlátozásról. Az e-mail állapotok, ha a korlátozás lejár.

## <a name="next-steps"></a>Következő lépések ##
* További információ az [SMS-riasztás viselkedéséről](alerts-sms-behavior.md).
* [Tekintse át a tevékenységek naplójának riasztásait](./alerts-overview.md), és Ismerje meg, hogyan fogadhat riasztásokat.  
* Megtudhatja, hogyan [konfigurálhatja a riasztásokat, amikor egy szolgáltatás állapotáról értesítést küldenek](../../service-health/alerts-activity-log-service-notifications-portal.md).
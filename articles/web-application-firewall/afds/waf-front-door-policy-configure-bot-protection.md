---
title: A bot Protection konfigurálása a webalkalmazási tűzfalhoz az Azure bejárati ajtaján (előzetes verzió)
description: A webalkalmazási tűzfal (WAF) megismerése.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: f48b683044bc727cda461fb64a743c055188962a
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934652"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>A webalkalmazási tűzfal robot-védelmének konfigurálása (előzetes verzió)
Ez a cikk bemutatja, hogyan konfigurálhatja az Azure webalkalmazási tűzfal (WAF) robot Protection-szabályát Azure Portal használatával. A bot Protection-szabály parancssori felülettel, Azure PowerShell vagy Azure Resource Manager sablonnal is konfigurálható.

> [!IMPORTANT]
> A robot Protection-szabálykészlet jelenleg nyilvános előzetes verzióban érhető el, és az előzetes verziójú szolgáltatói szerződéssel van ellátva. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.  A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Hozzon létre egy alapszintű WAF-szabályzatot a bejárati ajtóhoz a [Azure Portal használatával](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Robot Protection-szabály beállításának engedélyezése

A **felügyelt szabályok** lapon a webalkalmazási tűzfal házirendjének létrehozásakor először a **felügyelt szabály beállítása** szakaszban jelölje be a jelölőnégyzetet a szabály előtt **Microsoft_BotManager_1.0** a legördülő menüből, majd válassza a **felülvizsgálat + létrehozás**elemet.

   ![Robot védelmi szabály](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [figyelheti a WAF](waf-front-door-monitor.md).

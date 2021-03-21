---
title: A Azure Firewall Service-címkék áttekintése
description: A szolgáltatáscímkék IP-címelőtagok csoportjait jelölik, így a segítségükkel csökkenthető a biztonsági szabályok létrehozásának összetettsége.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 83e9a96573bbc72e0afff61cc0f151f95b081e30
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97031579"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall szolgáltatás címkéi

A szolgáltatáscímkék IP-címelőtagok csoportjait jelölik, így a segítségükkel csökkenthető a biztonsági szabályok létrehozásának összetettsége. Nem hozhat létre saját szolgáltatáscímkéket, és nem határozhatja meg, hogy melyik IP-címeket jelöljék az egyes címkék. A szolgáltatáscímkékben lévő címelőtagokat a Microsoft kezeli, és a címek változásával automatikusan frissíti a szolgáltatáscímkéket.

Azure Firewall szolgáltatás címkéi a hálózati szabályok célhely mezőjében használhatók. Azokat meghatározott IP-címek helyett használhatja.

## <a name="supported-service-tags"></a>Támogatott szolgáltatási Címkék

Tekintse meg a [virtuális hálózati szolgáltatás címkéit](../virtual-network/service-tags-overview.md#available-service-tags) az Azure tűzfal hálózati szabályaiban használható szolgáltatási címkék listájának megtekintéséhez.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Firewall szabályokról: [Azure Firewall szabály-feldolgozási logika](rule-processing.md).

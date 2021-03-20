---
title: InvalidNetworkSecurityGroupSecurityRules-hiba – Azure HDInsight
description: A fürt létrehozása nem sikerül a ErrorCode InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: 7e0b984b3ec4a203f8a1118c0e6a166c5a9e1125
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944381"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Forgatókönyv: InvalidNetworkSecurityGroupSecurityRules – a fürt létrehozása nem sikerül az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az `InvalidNetworkSecurityGroupSecurityRules` alhálózattal konfigurált hálózati biztonsági csoport biztonsági szabályaihoz hasonló leírással kapott hibakód nem teszi lehetővé a szükséges bejövő és/vagy kimenő kapcsolatokat.

## <a name="cause"></a>Ok

Valószínűleg probléma van a fürthöz konfigurált bejövő [hálózati biztonsági csoport](../../virtual-network/virtual-network-vnet-plan-design-arm.md) szabályaival.

## <a name="resolution"></a>Feloldás

Nyissa meg a Azure Portal, és keresse meg azt az alhálózathoz társított NSG, amelyben a fürtöt üzembe helyezi. A **bejövő biztonsági szabályok** szakaszban győződjön meg arról, hogy a szabályok engedélyezik a 443-es port bejövő hozzáférését az [itt](../control-network-traffic.md)említett IP-címekhez.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
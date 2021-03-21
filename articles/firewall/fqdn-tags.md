---
title: Az Azure Firewall FQDN-címkék áttekintése
description: Az FQDN címke a jól ismert Microsoft-szolgáltatásokhoz társított teljes tartománynevek (FQDN-EK) csoportját jelöli.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: c0bd2f6a021baae80dafcc3d544e1062d8e022e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94653334"
---
# <a name="fqdn-tags-overview"></a>FQDN-címkék – áttekintés

Az FQDN címke a jól ismert Microsoft-szolgáltatásokhoz társított teljes tartománynevek (FQDN-EK) csoportját jelöli. Az alkalmazási szabályokban az FQDN címke használatával engedélyezheti a szükséges kimenő hálózati forgalmat a tűzfalon keresztül.

Ha például manuálisan szeretné engedélyezni Windows Update hálózati forgalmat a tűzfalon keresztül, létre kell hoznia több alkalmazási szabályt a Microsoft dokumentációjában. A FQDN-címkék használatával létrehozhat egy alkalmazási szabályt, belefoglalhatja a **Windows Updates** címkét, és most a Microsoft Windows Update-végpontok felé irányuló hálózati forgalmat a tűzfalon keresztül is elvégezheti.

Nem hozhat létre saját FQDN-címkéket, és nem adhatja meg, hogy mely teljes tartománynevek szerepeljenek a címkén belül. A Microsoft kezeli a teljes tartománynév címke által felölelt teljes tartományneveket, és frissíti a címkét FQDN-ként. 

<!--- screenshot of application rule with a FQDN tag.-->

A következő táblázat a jelenleg használható FQDN-címkéket mutatja. A Microsoft fenntartja ezeket a címkéket, és rendszeres időközönként további címkéket is hozzáadhat.

## <a name="current-fqdn-tags"></a>Jelenlegi FQDN-Címkék

|FQDN címke  |Description  |
|---------|---------|
|Windows Update     |A Microsoft Update kimenő hozzáférésének engedélyezése a [tűzfal konfigurálása a szoftverfrissítések számára](/mem/configmgr/sum/get-started/install-a-software-update-point)című témakörben leírtak szerint.|
|Windows diagnosztika|Engedélyezze a kimenő hozzáférést az összes [Windows diagnosztikai végponthoz](/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Microsoft Active Protection Service (MAPS)|A [Maps](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/)-hez való kimenő hozzáférés engedélyezése.|
|App Service Environment (bemutató)|Kimenő hozzáférés engedélyezése az ASE-platform forgalmához. Ez a címke nem fedi le a beszállító által létrehozott ügyfél-specifikus tárterületet és SQL-végpontokat. Ezeket a [szolgáltatás-végpontokon](../virtual-network/tutorial-restrict-network-access-to-resources.md) keresztül kell engedélyezni, vagy manuálisan kell hozzáadni.<br><br>További információ a Azure Firewall beépítéséről a bevonással: [app Service Environment zárolása](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Azure Backup|Engedélyezi a kimenő hozzáférést a Azure Backup szolgáltatásokhoz.|
|Azure HDInsight|Engedélyezi a kimenő hozzáférést a HDInsight-platform forgalmához. Ez a címke nem fedi le az ügyfél-specifikus tárterületet vagy az HDInsight-ből származó SQL-forgalmat. Engedélyezze ezeket a [szolgáltatás-végpontok](../virtual-network/tutorial-restrict-network-access-to-resources.md) használatával, vagy manuálisan adja hozzá őket.|
|WindowsVirtualDesktop (WVD)|Engedélyezi a kimenő Windowsos virtuális asztali platform forgalmát. Ez a címke nem fedi le a WVD által létrehozott központi telepítésre vonatkozó tárolási és Service Bus végpontokat. Emellett a DNS-és a KMS-hálózati szabályok megadása kötelező. A Azure Firewall és a WVD integrálásával kapcsolatos további információkért lásd: a [Azure Firewall használata a Windows rendszerű virtuális asztali környezetek elleni védelemhez](protect-windows-virtual-desktop.md).|
|Azure Kubernetes Service (AKS)|Engedélyezi a kimenő hozzáférést az AK-hoz. További információ: [a Azure Firewall használata az Azure Kubernetes-szolgáltatás (ak) üzembe helyezéséhez](protect-azure-kubernetes-service.md).|

> [!NOTE]
> Ha egy alkalmazási szabályban kijelöli a teljes tartománynevet, a port mezőt a **https** értékre kell beállítani.

## <a name="next-steps"></a>Következő lépések

A Azure Firewall telepítésének megismeréséhez tekintse meg [az oktatóanyag: Azure Firewall telepítése és konfigurálása a Azure Portal használatával](tutorial-firewall-deploy-portal.md)című témakört.
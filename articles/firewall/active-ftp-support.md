---
title: Azure Firewall aktív FTP-támogatás
description: Alapértelmezés szerint az aktív FTP le van tiltva Azure Firewallon. A PowerShell, a CLI és az ARM sablon használatával engedélyezheti.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: victorh
ms.openlocfilehash: 47bced3c3c4f01212af6919d51adc5dfc2e6e1b5
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200062"
---
# <a name="azure-firewall-active-ftp-support"></a>Azure Firewall aktív FTP-támogatás

Az aktív FTP szolgáltatással az FTP-kiszolgáló kezdeményezi az adatkapcsolatot a kijelölt FTP-ügyfél adatportjával. Az ügyféloldali hálózat tűzfala általában blokkolja a belső ügyfél portján kívüli kapcsolódási kéréseket. További információ: [aktív FTP és passzív FTP, végleges magyarázat](https://slacksite.com/other/ftp.html).

Alapértelmezés szerint az aktív FTP-támogatás le van tiltva a Azure Firewallon az FTP-parancs használatával történő FTP-visszafordulási támadásokkal szembeni védelem érdekében `PORT` . A Azure PowerShell, az Azure CLI vagy egy Azure ARM-sablon használatával történő üzembe helyezéskor azonban engedélyezheti az aktív FTP-t.

> [!NOTE]
> Az aktív FTP jelenleg csak a virtuális hálózatban üzembe helyezett tűzfalak esetében támogatott. A virtuális WAN-támogatás később lesz hozzáadva.

## <a name="azure-powershell"></a>Azure PowerShell

A Azure PowerShell használatával történő üzembe helyezéshez használja a `AllowActiveFTP` paramétert. További információ: [tűzfal létrehozása az Active FTP engedélyezésével](/powershell/module/az.network/new-azfirewall?view=azps-5.4.0#16---create-a-firewall-with-allow-active-ftp-).

## <a name="azure-cli"></a>Azure CLI

Az Azure CLI használatával történő üzembe helyezéshez használja a `--allow-active-ftp` paramétert. További információ: [az Network Firewall Create](/cli/azure/ext/azure-firewall/network/firewall#ext_azure_firewall_az_network_firewall_create-optional-parameters). 

## <a name="azure-resource-manager-arm-template"></a>Azure Resource Manager- (ARM-) sablon

ARM-sablon használatával történő üzembe helyezéshez használja a következő `AdditionalProperties` mezőt:

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
További információ: [Microsoft. Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="next-steps"></a>Következő lépések

A Azure Firewall telepítésének megismeréséhez tekintse meg a [Azure Firewall telepítése és konfigurálása a Azure PowerShell használatával](deploy-ps.md)című témakört.
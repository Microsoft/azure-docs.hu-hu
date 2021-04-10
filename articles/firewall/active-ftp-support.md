---
title: Azure Firewall aktív FTP-támogatás
description: Alapértelmezés szerint az aktív FTP le van tiltva Azure Firewallon. A PowerShell, a CLI és az ARM sablon használatával engedélyezheti.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: victorh
ms.openlocfilehash: adbc2a9eb6cd3b054df84911604143ddb711ad20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499135"
---
# <a name="azure-firewall-active-ftp-support"></a>Azure Firewall aktív FTP-támogatás

Az aktív FTP szolgáltatással az FTP-kiszolgáló kezdeményezi az adatkapcsolatot a kijelölt FTP-ügyfél adatportjával. Az ügyféloldali hálózat tűzfala általában blokkolja a belső ügyfél portján kívüli kapcsolódási kéréseket. További információ: [aktív FTP és passzív FTP, végleges magyarázat](https://slacksite.com/other/ftp.html).

Alapértelmezés szerint az aktív FTP-támogatás le van tiltva a Azure Firewallon az FTP-parancs használatával történő FTP-visszafordulási támadásokkal szembeni védelem érdekében `PORT` . A Azure PowerShell, az Azure CLI vagy egy Azure ARM-sablon használatával történő üzembe helyezéskor azonban engedélyezheti az aktív FTP-t.


## <a name="azure-powershell"></a>Azure PowerShell

A Azure PowerShell használatával történő üzembe helyezéshez használja a `AllowActiveFTP` paramétert. További információ: [tűzfal létrehozása az Active FTP engedélyezésével](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-).

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
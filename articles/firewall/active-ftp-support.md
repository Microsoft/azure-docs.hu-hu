---
title: Azure Firewall aktív FTP-támogatás
description: Alapértelmezés szerint az aktív FTP le van tiltva Azure Firewallon. A PowerShell, a CLI és az ARM sablon használatával engedélyezheti.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: victorh
ms.openlocfilehash: e08be08f2d898b017bb34ed38c9c3a69ee0582fa
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312977"
---
# <a name="azure-firewall-active-ftp-support"></a>Azure Firewall aktív FTP-támogatás

Az aktív FTP szolgáltatással az FTP-kiszolgáló kezdeményezi az adatkapcsolatot a kijelölt FTP-ügyfél adatportjával. Az ügyféloldali hálózat tűzfala általában blokkolja a belső ügyfél portján kívüli kapcsolódási kéréseket. További információ: [aktív FTP és passzív FTP, végleges magyarázat](https://slacksite.com/other/ftp.html).

Alapértelmezés szerint az aktív FTP-támogatás le van tiltva a Azure Firewallon az FTP-parancs használatával történő FTP-visszafordulási támadásokkal szembeni védelem érdekében `PORT` . A Azure PowerShell, az Azure CLI vagy egy Azure ARM-sablon használatával történő üzembe helyezéskor azonban engedélyezheti az aktív FTP-t.

Az aktív módú FTP támogatásához a következő TCP-portokat kell megnyitni:

- FTP-kiszolgáló (21-es port) bárhonnan (az ügyfél kezdeményezi a csatlakozást)
- Az FTP-kiszolgáló 21-es portja > 1023-as portra (a kiszolgáló válaszol az ügyfél vezérlési portjára)
- Az FTP-kiszolgáló 20-ös portja a 1023-es portokra > az ügyfeleken (a kiszolgáló adatkapcsolatot kezdeményez az ügyfél adatportjával)
- Az FTP-kiszolgáló 20. portja a 1023-as portokon > az ügyfeleken (az ügyfél a nyugták küldi a kiszolgáló adatportjára)

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
---
title: Azure Firewall FTP támogatása
description: Alapértelmezés szerint az aktív FTP le van tiltva a Azure Firewall. Ezt a PowerShell, a parancssori felület és az ARM-sablon használatával engedélyezheti.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: victorh
ms.openlocfilehash: 18b3680e47fe808413998144259e033a4cbcaa27
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864468"
---
# <a name="azure-firewall-active-ftp-support"></a>Azure Firewall FTP támogatása

Aktív FTP esetén az FTP-kiszolgáló kezdeményezi az adatkapcsolatot a kijelölt FTP-ügyfél adatportjához. Az ügyféloldali hálózaton található tűzfalak általában blokkolnak egy belső ügyfélportra vonatkozó külső kapcsolódási kérelmet. További információ: [Active FTP vs. Passive FTP, a Definitive Explanation](https://slacksite.com/other/ftp.html)(Határozott magyarázat).

Alapértelmezés szerint az aktív FTP-támogatás le van tiltva a Azure Firewall FTP-visszadobásos támadások ellen az `PORT` FTP-paranccsal. Azonban engedélyezheti az aktív FTP-t, ha az üzembe helyezést Azure PowerShell, az Azure CLI-vel vagy egy Azure ARM-sablonnal.

Az aktív módú FTP támogatásához a következő TCP-portokat kell megnyitni:

- FTP-kiszolgáló 21-es portja bárhonnan (az ügyfél kapcsolatot kezdeményez)
- FTP-kiszolgáló 21-es portja az 1023-as > portokra (a kiszolgáló válaszol az ügyfél vezérlőportjának)
- FTP-kiszolgáló 20-as portja > 1023-as porthoz az ügyfeleken (a kiszolgáló adatkapcsolatot kezdeményez az ügyfél adatportjához)
- Ftp-kiszolgáló 20-as portja > 1023-as portról az ügyfeleken (az ügyfél ACK-ket küld a kiszolgáló adatportjának)

## <a name="azure-powershell"></a>Azure PowerShell

Az üzembe helyezéshez Azure PowerShell `AllowActiveFTP` paramétert. További információ: Tűzfal létrehozása aktív [FTP engedélyezése használatával.](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-)

## <a name="azure-cli"></a>Azure CLI

Az Azure CLI használatával való üzembe helyezéshez használja a `--allow-active-ftp` paramétert. További információ: [az network firewall create.](/cli/azure/network/firewall#az_network_firewall_create-optional-parameters) 

## <a name="azure-resource-manager-arm-template"></a>Azure Resource Manager- (ARM-) sablon

Arm-sablonnal való üzembe helyezéshez használja a `AdditionalProperties` mezőt:

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
További információ: [Microsoft.Network azureFirewalls.](/azure/templates/microsoft.network/azurefirewalls)

## <a name="next-steps"></a>Következő lépések

A virtuális gép üzembe helyezésének Azure Firewall [lásd: Deploy and configure Azure Firewall using Azure PowerShell](deploy-ps.md).
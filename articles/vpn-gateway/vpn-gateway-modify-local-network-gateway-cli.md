---
title: A helyi hálózati átjáró IP-címelőtagokat, és a VPN-átjáró IP-cím módosítása |} Azure |} PARANCSSORI FELÜLET |} A Microsoft Docs
description: Ez a cikk végigvezeti az Azure CLI használatával helyi hálózati átjáró IP-címelőtagjainak módosítása.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: 20e8269f0ee7463dcff9a5f66e46ebd81cb3df84
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60652854"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Az Azure CLI használatával a helyi hálózati átjáró beállításainak módosítása

Egyes esetekben a helyi hálózati átjáró címelőtagja vagy az átjáró IP-cím beállításait módosíthatja. Ez a cikk bemutatja, hogyan lehet a helyi hálózati átjáró beállításainak módosítása. Ezeket a beállításokat egy másik lehetőség kiválasztásával az alábbi listából egy másik módszer a is módosíthatja:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Előkészületek

Telepítse a CLI-parancsok (2.0-s vagy újabb) legújabb verzióját. Információk a CLI-parancsok telepítéséről: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="ipaddprefix"></a>IP-címelőtagjainak módosítása

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="gwip"></a>Átjáró IP-címének módosítása

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>További lépések

Ellenőrizheti, hogy az átjárókapcsolat. Lásd: [átjárókapcsolat ellenőrzése](vpn-gateway-verify-connection-resource-manager.md).


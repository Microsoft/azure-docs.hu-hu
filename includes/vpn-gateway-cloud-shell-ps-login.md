---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 24d146da7946176c92902698d0f52ae01baf79ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93061618"
---
Ha a PowerShellt helyileg futtatja, nyissa meg emelt szintű jogosultságokkal a PowerShell-konzolt, és kapcsolódjon az Azure-fiókjához. A *AzAccount* parancsmag hitelesítő adatokat kér. A hitelesítés után letölti a fiók beállításait, hogy elérhetők legyenek Azure PowerShell számára.

Ha a PowerShell helyi futtatása helyett Azure Cloud Shell használ, láthatja, hogy nem kell futtatnia a *AzAccount*. Ha a **kipróbálás** lehetőséget választja, a Azure Cloud Shell automatikusan csatlakozik az Azure-fiókjához.

1. Ha helyileg futtatja a PowerShellt, jelentkezzen be.

   ```azurepowershell
   Connect-AzAccount
   ```

1. Ha egynél több előfizetéssel rendelkezik, szerezze be az Azure-előfizetések listáját.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. Válassza ki a használni kívánt előfizetést.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```

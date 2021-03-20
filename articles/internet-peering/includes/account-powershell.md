---
title: fájl belefoglalása
titleSuffix: Azure
description: fájl belefoglalása
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: beffb2babefd86c2807e21e9337cba66f42fcfc2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "81678493"
---
A konfigurálás megkezdése előtt telepítse és importálja a szükséges modulokat. Rendszergazdai jogosultságokkal kell rendelkeznie a modulok PowerShellben való telepítéséhez.

1. Telepítse és importálja az az modult.
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Telepítse és importálja az az. peering modult.
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. A következő parancs használatával ellenőrizze, hogy a modulok megfelelően lettek-e importálva:
    ```powershell
    Get-Module
    ```
1. Jelentkezzen be az Azure-fiókjába a következő parancs használatával:
    ```powershell
    Connect-AzAccount
    ```
1. Tekintse át a fiók előfizetéseit, és válassza ki azt az előfizetést, amelyben létre szeretné hozni a társítást.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Ha még nem rendelkezik erőforráscsoporthoz, létre kell hoznia egyet a társítás létrehozása előtt. Ezt a következő parancs futtatásával teheti meg:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Ha még nem társította az ASN-t és az előfizetést, kövesse a [peer ASN társítása](../howto-subscription-association-powershell.md)című témakör lépéseit. Ez a művelet a társítás igényléséhez szükséges.

> [!NOTE]
> Az erőforráscsoport helye független attól a helytől, ahol a társítást beállítja.
&nbsp;

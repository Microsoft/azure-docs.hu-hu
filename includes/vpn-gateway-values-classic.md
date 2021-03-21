---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 11d2172d085fe9b47587f4084908f99d7b54437e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92103237"
---
Ha a Azure Portal klasszikus virtuális hálózatok hoz létre, a megtekintett név nem a PowerShellhez használt teljes név. Előfordulhat például, hogy a portálon a **TestVNet1** nevű VNet sokkal több nevet tartalmaz a hálózati konfigurációs fájlban. A "ClassicRG" nevű erőforráscsoport VNet a következőhöz hasonló lehet: **Group ClassicRG TestVNet1**. A kapcsolatok létrehozásakor fontos, hogy a hálózati konfigurációs fájlban látható értékeket használja.

A következő lépésekben csatlakozni fog az Azure-fiókjához, és letölti és megtekinti a hálózati konfigurációs fájlt a kapcsolatokhoz szükséges értékek beszerzéséhez.

1. Töltse le és telepítse az Azure Service Management (SM) PowerShell-parancsmagok legújabb verzióját. A legtöbb felhasználó helyileg telepített Resource Manager-modulokat, de nem rendelkezik Service Management modulokkal. A Service Management modulok örökölt, és külön kell telepíteni őket. További információ: a [Service Management-parancsmagok telepítése](/powershell/azure/servicemanagement/install-azure-ps).

1. Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. Az alábbi példák segítséget nyújtanak a kapcsolódáshoz. Ezeket a parancsokat helyileg kell futtatni a PowerShell Service Management modul használatával. Csatlakozás a fiókhoz. A következő példa segít a kapcsolódásban:

   ```powershell
   Add-AzureAccount
   ```
1. Keresse meg a fiókot az előfizetésekben.

   ```powershell
   Get-AzureSubscription
   ```
1. Ha egynél több előfizetéssel rendelkezik, akkor válassza ki azt, amelyiket használni szeretné.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
1. Hozzon létre egy könyvtárat a számítógépén. Például: C:\AzureVNet
1. Exportálja a hálózati konfigurációs fájlt a könyvtárba. Ebben a példában a hálózati konfigurációs fájlt a rendszer a **C:\AzureNet** exportálja.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
1. Nyissa meg a fájlt egy szövegszerkesztővel, és tekintse meg a virtuális hálózatok és a webhelyek nevét. Ezek a nevek lesznek a kapcsolatok létrehozásakor használt nevek.<br>A **VNet** nevei a **VirtualNetworkSite neve =**<br>A **helyek** nevei a **LocalNetworkSiteRef neve =**
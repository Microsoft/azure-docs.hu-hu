---
title: Mindkét útválasztási beállítás konfigurálása virtuális géphez – Azure Portal
description: Megtudhatja, hogyan engedélyezheti mindkét útválasztási beállítás lehetőségét
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: d9bb00077b1d96d13e15861cac477f913a002030
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101679657"
---
# <a name="configure-both-routing-preference-options-for-a-virtual-machine"></a>A virtuális gép útválasztási beállításainak mindkét beállításának konfigurálása

Ez a cikk bemutatja, hogyan konfigurálhatja a virtuális gép (VM) [útválasztási](./routing-preference-overview.md) beállításait (Internet és Microsoft Global Network). Ez két virtuális hálózati adapter használatával érhető el, egy nyilvános IP-címmel rendelkező hálózati adaptert a Microsoft globális hálózatán keresztül, a másikat pedig egy ISP-hálózaton keresztül irányított nyilvános IP-címmel.

## <a name="prerequisites"></a>Előfeltételek

Hozzon létre egy nyilvános IP-címmel rendelkező virtuális gépet a következő témakörben ismertetett módon: a [virtuális gép létrehozása statikus nyilvános IP-címmel a Azure Portal](./virtual-network-deploy-static-pip-arm-portal.md) használatával a Azure Portal segítségével. A nyilvános IP-cím alapértelmezett útválasztási beállításai a **Microsoft globális hálózatán** keresztül választhatók. Miután létrehozott egy nyilvános IP-címmel rendelkező virtuális gépet, adjon hozzá egy második nyilvános IP-címet a virtuális GÉPHEZ, amely átirányítja a forgalmat a tranzit ISP-hálózaton keresztül, és az útválasztási beállításokat az **internethez** konfigurálta.

## <a name="create-a-public-ip-address-with-a-routing-preference-choice-internet"></a>Nyilvános IP-cím létrehozása útválasztási preferencia választása internettel
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza az **Erőforrás létrehozása** lehetőséget. 
3. A keresőmezőbe írja be a *nyilvános IP-cím* kifejezést.
4. A keresési eredmények között válassza a **nyilvános IP-cím** elemet. Ezután a **nyilvános IP-cím** lapon válassza a **Létrehozás** lehetőséget.
5. Az **útválasztási** beállítások lehetőségnél válassza az **Internet** lehetőséget.

      ![Nyilvános IP-cím létrehozása](./media/routing-preference-portal/public-ip-new.png)

    > [!NOTE]
    > A nyilvános IP-címek IPv4- vagy IPv6-címekkel jönnek létre. Az útválasztási beállítások azonban jelenleg csak az IPV4-t támogatják.

## <a name="create-a-network-interface-and-associate-the-public-ip"></a>Hálózati adapter létrehozása és a nyilvános IP-cím hozzárendelése

1. Hozzon létre egy [hálózati adaptert](./routing-preference-overview.md) az alapértelmezett beállításokkal a Azure Portal használatával. 
1. [Rendelje hozzá a nyilvános IP-címet az előző szakaszban létrehozott hálózati adapterhez](./associate-public-ip-address-vm.md) . Előfordulhat, hogy egy IP-cím megjelenése eltarthat néhány másodpercig. Tekintse meg az IP-konfigurációhoz rendelt nyilvános IP-címet az alábbiak szerint:

    ![A nyilvános IP-cím hozzárendelése](./media/routing-preference-mixed-network-adapter-portal/public-ip.png)

## <a name="attach-network-interface-to-the-vm"></a>Hálózati adapter csatolása a virtuális géphez

1. [Csatlakoztassa az előző szakaszban létrehozott hálózati adaptert a virtuális géphez](./virtual-network-network-interface-vm.md).
2. Most megtekintheti a virtuális géphez társított két virtuális hálózati adaptert, egyet egy nyilvános IP-címmel, amely a Microsoft globális hálózatán keresztül irányítható át, a másik pedig az INTERNETSZOLGÁLTATÓ hálózatán keresztül, a következő módon:  ![ hálózati adapter csatlakoztatása virtuális géphez](./media/routing-preference-mixed-network-adapter-portal/mixed-network-adapter.png) 

## <a name="next-steps"></a>Következő lépések
- További információ a [nyilvános IP-címekről az útválasztási beállításokkal](routing-preference-overview.md).
- [Konfigurálja a virtuális gép útválasztási beállításait](tutorial-routing-preference-virtual-machine-portal.md).
- [Konfigurálja a nyilvános IP-cím útválasztási beállításait a PowerShell használatával](routing-preference-powershell.md).
- További információ az Azure [-beli nyilvános IP-címekről](./public-ip-addresses.md#public-ip-addresses) .
- További információ az összes [nyilvános IP-cím beállításról](virtual-network-public-ip-address.md#create-a-public-ip-address).
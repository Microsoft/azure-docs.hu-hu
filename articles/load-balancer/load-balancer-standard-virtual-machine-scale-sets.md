---
title: Szabályok hozzáadása az Azure standard Load Balancer és a virtuálisgép-méretezési csoportokhoz
titleSuffix: Add rules for Azure Standard Load Balancer and virtual machine scale sets
description: Ezzel a képzési útvonallal megismerheti az Azure standard Load Balancer és a virtuálisgép-méretezési csoportok használatának első lépéseit.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: 7a2e0531427343a2ec267de54cee05b5eb25889f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592279"
---
# <a name="add-rules-for-azure-load-balancer-with-virtual-machine-scale-sets"></a>A virtuálisgép-méretezési csoportokkal rendelkező Azure Load Balancer szabályainak hozzáadása

Ha a virtuálisgép-méretezési csoportokkal és a Azure Load Balancerekkel dolgozik, vegye figyelembe a következő irányelveket.

## <a name="port-forwarding-and-inbound-nat-rules"></a>A port továbbítása és a bejövő NAT-szabályok

A méretezési csoport létrehozása után a rendszer nem módosíthatja a háttér-portot olyan terheléselosztási szabályhoz, amelyet a terheléselosztó állapotának mintavétele használ. A port módosításához távolítsa el az állapot-mintavételt úgy, hogy frissíti a virtuálisgép-méretezési csoportját, és frissíti a portot. Ezután konfigurálja újra az állapot-mintavételt.

Ha a terheléselosztó háttér-készletében a virtuálisgép-méretezési csoportját használja, a rendszer automatikusan létrehozza az alapértelmezett bejövő NAT-szabályokat.
  
## <a name="inbound-nat-pool"></a>Bejövő NAT-készlet

A virtuálisgép-méretezési csoportoknak legalább egy bejövő NAT-készlettel kell rendelkezniük. A bejövő NAT-készlet a bejövő NAT-szabályok gyűjteménye. Egy bejövő NAT-készlet nem támogatja több virtuálisgép-méretezési csoport használatát.

## <a name="load-balancing-rules"></a>Terheléselosztási szabályok

Ha a terheléselosztó háttér-készletében a virtuálisgép-méretezési csoportját használja, a rendszer automatikusan létrehozza az alapértelmezett terheléselosztási szabályt.
  
## <a name="outbound-rules"></a>Kimenő szabályok

Ha egy terheléselosztási szabály által már hivatkozott háttér-készlethez szeretne kimenő szabályt létrehozni, válassza a **nem** lehetőséget a Azure Portal **implicit kimenő szabályok létrehozása** a bejövő terheléselosztási szabály létrehozásakor szakaszban.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="A terheléselosztási szabályok létrehozását bemutató képernyőkép." border="true":::

A következő módszerekkel telepítheti a virtuálisgép-méretezési csoportját Load Balancer meglévő példányával:

* [Virtuálisgép-méretezési csoport konfigurálása Azure Load Balancer meglévő példányával a Azure Portal használatával](./configure-vm-scale-set-portal.md)
* [Virtuálisgép-méretezési csoport konfigurálása a Azure Load Balancer meglévő példányával Azure PowerShell használatával](./configure-vm-scale-set-powershell.md)
* [Virtuálisgép-méretezési csoport konfigurálása Azure Load Balancer meglévő példányával az Azure CLI használatával](./configure-vm-scale-set-cli.md)
* [Virtuálisgép-méretezési csoport által használt Azure Load Balancer meglévő példányának frissítése vagy törlése](./update-load-balancer-with-vm-scale-set.md)

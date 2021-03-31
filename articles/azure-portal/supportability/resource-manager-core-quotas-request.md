---
title: Azure Resource Manager vCPU-kvóta növelésére vonatkozó kérelmek
description: Azure Resource Manager vCPU-kvóta növelésére vonatkozó kérelmek
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 2580da2a4ac7b943dee3e5e6ff8bdbd49664505b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745247"
---
# <a name="quota-increase-requests"></a>Kvótanövelési kérések

A virtuális gépekhez és a virtuálisgép-méretezési csoportokhoz tartozó Resource Manager-vCPU-kvóták minden egyes régióban az egyes előfizetések esetében két szinten érvényesülnek.

Az első szint a teljes regionális vCPU korlátja az összes virtuálisgép-sorozaton belül. A második réteg a virtuálisgép-sorozat vCPU korlátja, például a D sorozat vCPU. Az új virtuális gép üzembe helyezésének időpontjában az adott virtuálisgép-sorozat új és meglévő vCPU-használatának összege nem haladhatja meg az adott VM-sorozathoz jóváhagyott vCPU-kvótát. Továbbá az összes virtuálisgép-sorozat összes új és meglévő vCPU száma nem haladhatja meg az előfizetéshez jóváhagyott teljes regionális vCPU-kvótát. Ha túllépi a kvóták valamelyikét, a virtuális gép üzembe helyezése nem engedélyezett.
A virtuálisgép-sorozat vCPU-kvótájának növelését Azure Portal-ból kérheti. A virtuálisgép-sorozat kvótájának növekedése automatikusan megnöveli a teljes regionális vCPU-korlátot ugyanazzal az összeggel.

Új előfizetés létrehozásakor előfordulhat, hogy az alapértelmezett teljes regionális vCPU nem egyenlő az egyes virtuálisgép-sorozatok alapértelmezett vCPU-kvótáinak összegével. Ez a tény olyan előfizetést eredményezhet, amely elegendő kvótával rendelkezik minden egyes telepíteni kívánt virtuálisgép-sorozathoz. Nem elegendő kvóta az összes üzemelő példány regionális vCPU. Ebben az esetben kérelmet kell benyújtania a teljes regionális vCPU-korlát explicit módon történő növeléséhez. A regionális vCPU-határértékek teljes száma nem haladhatja meg a jóváhagyott kvóta összegét a régió összes virtuálisgép-sorozatában.

> [!NOTE]
> Ha az alapértelmezett korlátnál magasabb korlátot vagy kvótát szeretne növelni, [Nyisson meg egy online ügyfélszolgálati kérést díjmentesen](../../azure-resource-manager/templates/error-resource-quota.md#solution).

A kvótákkal kapcsolatos további információkért lásd a [virtuális gépek vCPU kvótáit](../../virtual-machines/windows/quotas.md) és az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és korlátozásait](../../azure-resource-manager/management/azure-subscription-service-limits.md).
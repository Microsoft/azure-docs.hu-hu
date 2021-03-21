---
title: Az EA és a CSP Azure VMware megoldás-előfizetések áthelyezése
description: Megtudhatja, hogyan helyezheti át a privát felhőt az egyik előfizetésből a másikba. A mozgalom különféle okokból elvégezhető, például számlázással.
ms.topic: how-to
ms.date: 03/15/2021
ms.openlocfilehash: 608f46dbd84d6bb899a3e7fcd1f8a63b3a5e85fb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555388"
---
# <a name="move-ea-and-csp-azure-vmware-solution-subscriptions"></a>Az EA és a CSP Azure VMware megoldás-előfizetések áthelyezése

Ebből a cikkből megtudhatja, hogyan helyezheti át a privát felhőt az egyik előfizetésből a másikba. A mozgalom különféle okokból elvégezhető, például számlázással. 

>[!IMPORTANT]
>Legalább közreműködői jogosultsággal kell rendelkeznie a forrás-és a cél előfizetésekhez. A VNet és a VNet átjáró nem helyezhető át egyik előfizetésből a másikba. Emellett az előfizetések áthelyezése nem befolyásolja a felügyeletet és a munkaterheléseket, például a vCenter, a NSX és a munkaterhelés virtuális gépeket.

1. Jelentkezzen be a Azure Portalba, és válassza ki az áthelyezni kívánt privát felhőt.

1. Válassza ki az **előfizetés (módosítás)** hivatkozást.

   :::image type="content" source="media/private-cloud-overview-subscription-id.png" alt-text="A titkos felhő részleteit ábrázoló képernyőkép.":::

1. Adja meg a **cél** előfizetésének adatait, és kattintson a **Tovább gombra**.

   :::image type="content" source="media/move-resources-subscription-target.png" alt-text="Képernyőkép a cél erőforrásról." lightbox="media/move-resources-subscription-target.png":::

1. Erősítse meg az áthelyezéshez kiválasztott erőforrások érvényesítését, és válassza a **tovább** lehetőséget. 

   :::image type="content" source="media/confirm-move-resources-subscription-target.png" alt-text="Az áthelyezni kívánt erőforrást ábrázoló képernyőkép." lightbox="media/confirm-move-resources-subscription-target.png":::

1. Jelölje be a jelölőnégyzetet, amely azt jelzi, hogy a társított eszközök és parancsfájlok nem fognak működni, amíg nem frissíti őket az új erőforrás-azonosítók használatára. Ezután válassza az **Áthelyezés** lehetőséget.

   :::image type="content" source="media/review-move-resources-subscription-target.png" alt-text="Képernyőfelvétel: az áthelyezett kiválasztott erőforrás összefoglalása. " lightbox="media/review-move-resources-subscription-target.png":::

   Megjelenik egy értesítés, ha az erőforrás áthelyezése befejeződött. Az új előfizetés megjelenik a privát felhő áttekintésében.

   :::image type="content" source="media/moved-subscription-target.png" alt-text="Képernyőfelvétel: új előfizetés." lightbox="media/moved-subscription-target.png":::


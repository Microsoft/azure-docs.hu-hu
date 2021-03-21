---
title: Csatlakozás a szinapszis studióhoz privát hivatkozások használatával
description: Ez a cikk bemutatja, hogyan csatlakozhat az Azure szinapszis studióhoz privát hivatkozások használatával
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: d39beca60264023c8eb7c1bc78cd1ac15c3b45dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586624"
---
# <a name="connect-to-azure-synapse-studio-using-azure-private-link-hubs"></a>Csatlakozás az Azure szinapszis studióhoz az Azure Private link hubok használatával 

Ez a cikk azt ismerteti, hogyan használható az Azure szinapszis Analytics Private link hubok privát hivatkozásai a szinapszis studióhoz való biztonságos csatlakozáshoz. 

## <a name="azure-private-link-hubs"></a>Azure Private link hubok 
Az Azure-beli szinapszis studióhoz privát hivatkozások használatával biztonságosan csatlakozhat az Azure-beli virtuális hálózatból. Az Azure szinapszis Analytics privát kapcsolati hubok olyan Azure-erőforrások, amelyek összekötőként működnek a biztonságos hálózat és a szinapszis Studio webes felülete között. 

A szinapszis studióhoz a privát hivatkozások használatával két lépésben csatlakozhat. Először létre kell hoznia egy magánhálózati kapcsolati hubok erőforrást. Másodszor, létre kell hoznia egy privát végpontot az Azure-beli virtuális hálózatról ehhez a privát kapcsolati hubhoz. Ezután privát végpontokat használhat a szinapszis Studióval való biztonságos kommunikációhoz. A magánhálózati végpontokat integrálnia kell a DNS-megoldással, vagy a helyszíni megoldással vagy az Azure saját DNSával. 

## <a name="azure-private-links-hubs-and-azure-synapse-studio"></a>Az Azure Private Links hubok és az Azure szinapszis Studio
Egyetlen Azure szinapszis Private link hub-erőforrást is használhat, hogy az Azure szinapszis Studio használatával privát módon kapcsolódjon az Azure szinapszis Analytics-munkaterületekhez. A munkaterületeknek nem kell ugyanabban a régióban lennie, mint az Azure szinapszis Private link hub. Az Azure szinapszis Private link hub erőforrás a különböző előfizetésekben vagy az Azure AD-bérlőben található szinapszis-munkaterületek kapcsolataihoz is használható.

A privát kapcsolati hubot úgy is létrehozhatja, *hogy* megkeresi a Azure Portal és az **Azure szinapszis Analytics (Private link hubok)** szolgáltatást a szolgáltatások közül. A részletekért kövesse a útmutató a munkaterület- [erőforrásokhoz való kapcsolódás korlátozott hálózatról](./how-to-connect-to-workspace-from-restricted-network.md) című témakör lépéseit.

>[!NOTE]
>A magánhálózati kapcsolati hubok biztonságos betöltést biztosítanak a szinapszis Studiójának a privát hivatkozásokon keresztül. **Külön, privát végpontokat** kell létrehoznia a munkaterületen belül csatlakozni kívánó erőforrásokhoz, például kiépített/dedikált SQL-készletekhez vagy Spark-készletekhez. 

## <a name="azure-private-links-hubs-and-azure-virtual-network"></a>Azure Private Links hubok és Azure Virtual Network
Csatlakoztatnia kell az Azure-beli virtuális hálózatot a szinapszis Private link hub-erőforráshoz, hogy biztonságossá tegye a szinapszis studióhoz való végpontok közötti kapcsolatot. Ehhez létre kell hoznia egy magánhálózati végpontot a virtuális hálózatról a létrehozott privát kapcsolati hubhoz. Használhatja az Azure Portal a privát kapcsolati hubhoz, és megtekintheti a privát végpont szakaszt. Válassza a "+ privát végpont" lehetőséget, és hozzon létre egy új privát végpontot, amely a privát kapcsolati hubhoz csatlakozik.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-private-endpoint.png" alt-text="A privát Endpoint Connections oldalt bemutató képernyőkép.":::

Győződjön meg arról, hogy a "Resource" lapon a "Microsoft. szinapszis/privateLinkHubs" erőforrástípus van kiválasztva.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-resource-type.png" alt-text="Képernyőkép, amely a &quot;privát végpont létrehozása&quot; oldalt jeleníti meg az &quot;erőforrás típusa&quot; kiemelésével.":::

A "konfiguráció" lapon válassza a "privatelink.azuresynapse.net" lehetőséget saját DNS zónákhoz a virtuális hálózat és a magánhálózati DNS-zóna integrálásakor.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-dns-zones.png" alt-text="Privát végpont létrehozása a privát kapcsolati hubhoz":::

## <a name="next-steps"></a>Következő lépések

[Kapcsolódás a munkaterület erőforrásaihoz egy korlátozott hálózatról](./how-to-connect-to-workspace-from-restricted-network.md)

További információ a [felügyelt munkaterületről Virtual Network](./synapse-workspace-managed-vnet.md)

További információ a [felügyelt privát végpontokról](./synapse-workspace-managed-private-endpoints.md)

[Felügyelt privát végpontok létrehozása az adatforrásokhoz](./how-to-create-managed-private-endpoints.md)

[Kapcsolódás a szinapszis munkaterülethez privát végpontok használatával](./how-to-connect-to-workspace-with-private-links.md)


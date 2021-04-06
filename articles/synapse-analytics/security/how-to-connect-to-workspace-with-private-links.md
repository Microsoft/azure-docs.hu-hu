---
title: Csatlakozás egy szinapszis-munkaterülethez privát hivatkozások használatával
description: Ez a cikk bemutatja, hogyan csatlakozhat az Azure szinapszis-munkaterülethez privát hivatkozások használatával
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9782cce4165487b612c0295dc893d120ed043225
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98218263"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links"></a>Csatlakozás saját Azure Synapse-munkaterülethez privát hivatkozással

Ebből a cikkből megtudhatja, hogyan hozhat létre egy privát végpontot az Azure szinapszis-munkaterülethez. További információért tekintse meg a [privát hivatkozások és a privát végpontok](../../private-link/index.yml) című témakört.

## <a name="step-1-register-network-resource-provider"></a>1. lépés: a hálózati erőforrás-szolgáltató regisztrálása

Ha még nem tette meg, regisztrálja a hálózati erőforrás-szolgáltatót. Az erőforrás-szolgáltató regisztrálása konfigurálja az előfizetést az erőforrás-szolgáltatóval való együttműködésre. A [regisztráláskor](../../azure-resource-manager/management/resource-providers-and-types.md)válassza a *Microsoft. Network* elemet az erőforrás-szolgáltatók listájából. Ha a hálózati erőforrás-szolgáltató már regisztrálva van, folytassa a 2. lépéssel.

## <a name="step-2-open-your-azure-synapse-workspace-in-azure-portal"></a>2. lépés: az Azure szinapszis-munkaterület megnyitása Azure Portal

Válassza a **privát végponti kapcsolatok** lehetőséget a **Biztonság** területen. 
![Az Azure szinapszis munkaterületének megnyitása Azure Portal](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

A következő képernyőn válassza a **+ privát végpont** elemet.

![Privát végpont megnyitása Azure Portal](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1a.png)

## <a name="step-3-select-your-subscription-and-region-details"></a>3. lépés: válassza ki az előfizetését és a régió részleteit

A **privát végpont létrehozása** ablak **alapismeretek** lapján válassza ki az **előfizetést** és az **erőforráscsoportot**. Adjon **nevet** a létrehozni kívánt privát végpontnak. Válassza ki azt a **régiót** , ahol létre szeretné hozni a privát végpontot.

A magánhálózati végpontok egy alhálózaton jönnek létre. A kiválasztott előfizetés, erőforráscsoport és régió szűri a magánhálózati végpontok alhálózatait. Válassza a **tovább lehetőséget: erőforrás->** , ha elkészült.
![Előfizetés és régió részleteinek kiválasztása 1](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-4-select-your-azure-synapse-workspace-details"></a>4. lépés: az Azure szinapszis-munkaterület részleteinek kiválasztása

Válassza a **Kapcsolódás egy Azure-erőforráshoz a saját címtárban** az **erőforrás** lapon. Válassza ki az Azure szinapszis-munkaterületet tartalmazó **előfizetést** . A privát végpontok Azure-beli szinapszis-munkaterületre való létrehozására szolgáló **erőforrástípus** *Microsoft. szinapszis/munkaterület*.

Válassza ki az Azure szinapszis-munkaterületet **erőforrásként**. Minden Azure szinapszis-munkaterület három **cél alerőforrással** rendelkezik, amelyekhez létrehozhat egy privát végpontot az SQL, a SqlOnDemand és a dev szolgáltatáshoz.

Válassza a Next (tovább) lehetőséget **: a konfiguráció>** a telepítés következő részére való továbblépés előtt.
![Előfizetés és régió részleteinek kiválasztása 2](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

A **konfiguráció** lapon válassza ki azt a **virtuális hálózatot** és **alhálózatot** , amelyben létre szeretné hozni a magánhálózati végpontot. Létre kell hoznia egy DNS-rekordot is, amely a privát végponthoz van leképezve.

Válassza az **Igen** lehetőséget a privát **DNS-zónával való integráláshoz** , hogy a privát VÉGPONTOT egy privát DNS-zónába integrálja. Ha nem rendelkezik a Microsoft Azure Virtual Networkhoz tartozó saját DNS-zónával, a rendszer létrehoz egy új privát DNS-zónát. Válassza a **felülvizsgálat + létrehozás** lehetőséget, ha elkészült.

![Előfizetés és régió részleteinek kiválasztása 3](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Ha a telepítés befejeződött, nyissa meg az Azure szinapszis munkaterületét Azure Portal, és válassza a **privát végponti kapcsolatok** lehetőséget. Megjelenik a privát végponthoz társított új privát végpont és privát végpont-kapcsolódási név.

![Az előfizetés és a régió részleteinek kiválasztása 4](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Következő lépések

További információ a [felügyelt munkaterületről Virtual Network](./synapse-workspace-managed-vnet.md)

További információ a [felügyelt privát végpontokról](./synapse-workspace-managed-private-endpoints.md)

[Felügyelt privát végpontok létrehozása az adatforrásokhoz](./how-to-create-managed-private-endpoints.md)
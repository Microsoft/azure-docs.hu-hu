---
title: Hozzon létre egy felügyelt magánhálózati végpontot az adatforrás eredményeihez való kapcsolódáshoz.
description: Ebből a cikkből megtudhatja, hogyan hozhat létre felügyelt magánhálózati végpontokat az adatforrásokhoz egy Azure szinapszis-munkaterületről.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: d6a0a84050cb8f1b3ad0c04aa66c5b3797282f2e
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983227"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>Felügyelt privát végpont létrehozása az adatforráshoz (előzetes verzió)

Ez a cikk bemutatja, hogyan hozhat létre felügyelt privát végpontot az Azure-beli adatforráshoz. További információért lásd: [felügyelt privát végpontok](./synapse-workspace-managed-private-endpoints.md) .

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>1. lépés: az Azure szinapszis-munkaterület megnyitása Azure Portal

Az Azure szinapszis studióból létrehozhat egy felügyelt privát végpontot az adatforráshoz. Válassza az **Áttekintés** lapot Azure Portal és válassza a **szinapszis Studio elindítása**lehetőséget.
![Az Azure szinapszis Studio elindítása](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>2. lépés: navigáljon a Virtual Networks (felügyelt virtuális hálózatok) lapra a szinapszis Studióban

Az Azure szinapszis Studióban válassza a **kezelés** fület a bal oldali navigációs sávon. Válassza a **felügyelt virtuális hálózatok** lehetőséget, majd válassza az **+ új**lehetőséget.
![Új felügyelt privát végpont létrehozása](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>3. lépés: az adatforrás típusának kiválasztása

Válassza ki az adatforrás típusát. Ebben az esetben a célként megadott adatforrás egy ADLS Gen2-fiók. Válassza a **Folytatás** elemet.
![Válassza ki a célként megadott adatforrás típusát](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>4. lépés: az adatforrással kapcsolatos adatok megadása

A következő ablakban adja meg az adatforrással kapcsolatos adatokat. Ebben a példában egy felügyelt magánhálózati végpontot hozunk létre egy ADLS Gen2-fiókhoz. Adja meg a felügyelt magánhálózati végpont **nevét** . Adja meg az **Azure-előfizetést** és a **Storage-fiók nevét**. Kattintson a **Létrehozás** gombra.
![Adja meg a célként megadott adatforrás részleteit](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>5. lépés: annak ellenőrzése, hogy sikerült-e létrehozni a felügyelt magánhálózati végpontot

A kérelem elküldése után megjelenik az állapota. Annak ellenőrzéséhez, hogy létrejött-e a felügyelt privát végpont sikeres létrehozása, ellenőrizze annak *kiépítési állapotát*. Előfordulhat, hogy várnia kell 1 percet, majd a **frissítés** elemre kell kattintania a kiépítési állapot frissítéséhez. Láthatja, hogy a felügyelt magánhálózati végpont sikeresen létrejött a ADLS Gen2 fiókban.

Azt is láthatja, hogy a *jóváhagyási állapot* *függőben*van. A célként megadott erőforrás tulajdonosa jóváhagyhatja vagy megtagadhatja a magánhálózati végponti kapcsolatok kérelmét. Ha a tulajdonos jóváhagyja a magánhálózati végponti kapcsolódási kérelmet, akkor létrejön egy privát kapcsolat. Ha meg van tagadva, a magánhálózati kapcsolat nincs létrehozva.
![Felügyelt magánhálózati végpont-létrehozási kérelem állapota](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>További lépések

További információ a [felügyelt privát végpontokról](./synapse-workspace-managed-private-endpoints.md)
---
title: Felügyelt privát végpont létrehozása az adatforrás eredményeihez való kapcsolódáshoz
description: Ebből a cikkből megtudhatja, hogyan hozhat létre felügyelt magánhálózati végpontokat az adatforrásokhoz egy Azure szinapszis-munkaterületről.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: e0309b4c96b2ae25eb568e390717ba76cfd84fa5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96461312"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source"></a>Felügyelt privát végpont létrehozása az adatforráshoz

Ez a cikk bemutatja, hogyan hozhat létre felügyelt privát végpontot az Azure-beli adatforráshoz. További információért lásd: [felügyelt privát végpontok](./synapse-workspace-managed-private-endpoints.md) .

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>1. lépés: az Azure szinapszis-munkaterület megnyitása Azure Portal

Az Azure szinapszis studióból létrehozhat egy felügyelt privát végpontot az adatforráshoz. Válassza az **Áttekintés** fület Azure Portal, majd a kezdeti lépések szakaszban **válassza a Megnyitás lehetőséget a** szinapszis Studio-kártyán.

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>2. lépés: navigáljon a Virtual Networks (felügyelt virtuális hálózatok) lapra a szinapszis Studióban

Az Azure szinapszis Studióban válassza a **kezelés** fület a bal oldali navigációs sávon. Válassza a **felügyelt privát végpontok** lehetőséget, majd válassza az **+ új** lehetőséget.
![Új felügyelt privát végpont létrehozása](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>3. lépés: az adatforrás típusának kiválasztása

Válassza ki az adatforrás típusát. Ebben az esetben a célként megadott adatforrás egy ADLS Gen2-fiók. Válassza a **Folytatás** lehetőséget.
![Válassza ki a célként megadott adatforrás típusát](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>4. lépés: az adatforrással kapcsolatos adatok megadása

A következő ablakban adja meg az adatforrással kapcsolatos adatokat. Ebben a példában egy felügyelt magánhálózati végpontot hozunk létre egy ADLS Gen2-fiókhoz. Adja meg a felügyelt magánhálózati végpont **nevét** . Adja meg az **Azure-előfizetést** és a **Storage-fiók nevét**. Válassza a **Létrehozás** lehetőséget.
![Adja meg a célként megadott adatforrás részleteit](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>5. lépés: annak ellenőrzése, hogy sikerült-e létrehozni a felügyelt magánhálózati végpontot

A kérelem elküldése után megjelenik az állapota. Annak ellenőrzéséhez, hogy létrejött-e a felügyelt privát végpont sikeres létrehozása, ellenőrizze annak *kiépítési állapotát*. Előfordulhat, hogy várnia kell 1 percet, majd a **frissítés** elemre kell kattintania a kiépítési állapot frissítéséhez. Láthatja, hogy a felügyelt magánhálózati végpont sikeresen létrejött a ADLS Gen2 fiókban.

Azt is láthatja, hogy a *jóváhagyási állapot* *függőben* van. A célként megadott erőforrás tulajdonosa jóváhagyhatja vagy megtagadhatja a magánhálózati végponti kapcsolatok kérelmét. Ha a tulajdonos jóváhagyja a magánhálózati végponti kapcsolódási kérelmet, akkor létrejön egy privát kapcsolat. Ha meg van tagadva, a magánhálózati kapcsolat nincs létrehozva.
![Felügyelt magánhálózati végpont-létrehozási kérelem állapota](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>Következő lépések

További információ a [felügyelt privát végpontokról](./synapse-workspace-managed-private-endpoints.md)
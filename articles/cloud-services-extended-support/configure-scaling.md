---
title: Az Azure Cloud Services skálázásának konfigurálása (bővített támogatás)
description: Az Azure Cloud Services skálázási beállításainak engedélyezése (bővített támogatás)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: cfa5be01a0d36764086c6c9adf97e6cb166d2bb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728161"
---
# <a name="configure-scaling-options-with-azure-cloud-services-extended-support"></a>Skálázási beállítások konfigurálása az Azure Cloud Services (bővített támogatás) 

A feltételek beállíthatók úgy, hogy Cloud Services (kiterjesztett támogatás) központi telepítéseket engedélyezzen a be-és kiskálázáshoz. Ezek a feltételek a CPU-használat, a lemez terhelése és a hálózati terhelés alapján állíthatók be. 

A Cloud Service-környezetek skálázásának konfigurálásakor vegye figyelembe a következő információkat:
- A skálázás az alapvető használatot befolyásolja. A nagyobb szerepkörű példányok több magot használnak, és csak az előfizetés alapvető korlátján belül méretezhetők. További információk: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md).
- A várólista-üzenetkezelési küszöbérték alapján történő skálázás támogatott. További információ: Ismerkedés [Az Azure üzenetsor-tárolóval](../storage/queues/storage-dotnet-how-to-use-queues.md).
- A felhőalapú szolgáltatás (kiterjesztett támogatás) alkalmazásai magas rendelkezésre állásának biztosítása érdekében győződjön meg arról, hogy két vagy több szerepkör-példánnyal kell telepíteni.
- Az egyéni autoskálázás csak akkor fordulhat elő, ha az összes szerepkör **üzemkész** állapotban van.

## <a name="configure-and-manage-scaling"></a>Skálázás konfigurálása és kezelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Válassza ki a felhőalapú szolgáltatás (bővített támogatás) központi telepítését, amelyen be szeretné állítani a méretezést. 
3. Válassza ki a **skála** panelt. 

    :::image type="content" source="media/enable-scaling-1.png" alt-text="A képen a Távoli asztal lehetőség kiválasztását mutatja a Azure Portal":::

4. Egy oldal megjeleníti az összes olyan szerepkör listáját, amelyben a skálázás konfigurálható. Válassza ki a konfigurálni kívánt szerepkört. 
5. Válassza ki a konfigurálni kívánt méretezés típusát
    - A **manuális skálázás** a példányok abszolút számát állítja be.
        1. Válassza a **manuális méretezés** lehetőséget.
        2. Adja meg a méretezni kívánt példányok számát.
        3. Kattintson a **Mentés** gombra.

        :::image type="content" source="media/enable-scaling-2.png" alt-text="A rendszerkép a Azure Portal manuális skálázásának beállítását mutatja be":::

        4. A skálázási művelet azonnal elindul. 
        
    - Az **Egyéni autoscale** segítségével olyan szabályokat állíthat be, amelyek meghatározzák, hogy mennyit vagy milyen mértékben méretezhető. 
        1. **Egyéni méretezés** kiválasztása
        2. A mérőszám vagy a példányszám alapján méretezheti a méretezést.

        :::image type="content" source="media/enable-scaling-3.png" alt-text="A képen látható az egyéni autoskálázás beállítása a Azure Portal":::

        3. Ha metrika alapján skálázást használ, adja hozzá a szükséges szabályokat a kívánt skálázási eredmények eléréséhez.

        :::image type="content" source="media/enable-scaling-4.png" alt-text="A képen a Azure Portal egyéni autoskálázási szabályainak beállítása látható":::

        4. Kattintson a **Mentés** gombra.
        5. A skálázási műveletek akkor kezdődnek, amikor egy szabály elindul.
        
6. A **Méretezés** lapon megtekintheti vagy módosíthatja az üzemelő példányokra alkalmazott meglévő méretezési szabályokat.

    :::image type="content" source="media/enable-scaling-5.png" alt-text="A rendszerkép a Azure Portal meglévő méretezési szabályának módosítását mutatja be":::

## <a name="next-steps"></a>Következő lépések 
- Tekintse át a Cloud Services [üzembe helyezésének előfeltételeit](deploy-prerequisite.md) (kiterjesztett támogatás).
- Tekintse át a Cloud Servicesra vonatkozó [gyakori kérdéseket](faq.md) (kiterjesztett támogatás).
- A [Azure Portal](deploy-portal.md), a [PowerShell](deploy-powershell.md), a [sablon](deploy-template.md) vagy a [Visual Studio](deploy-visual-studio.md)használatával üzembe helyezhet egy felhőalapú szolgáltatást (kiterjesztett támogatás).
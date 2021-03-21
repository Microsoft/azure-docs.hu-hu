---
title: Frissítés az Azure Defenderre – Azure Security Center
description: Ez a rövid útmutató bemutatja, hogyan frissíthet a Security Center Azure Defender-ra további biztonság érdekében.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 2f37a59d5db3883754b602b2b2525e07b57206b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099352"
---
# <a name="quickstart-set-up-azure-security-center"></a>Gyors útmutató: Azure Security Center beállítása

A Azure Security Center egységes biztonsági felügyeletet és veszélyforrások elleni védelmet biztosít a hibrid és a többfelhős munkaterhelések között. Habár az ingyenes funkciók csak korlátozott biztonságot biztosítanak az Azure-erőforrások számára, az Azure Defender lehetővé teszi, hogy a helyszíni és egyéb felhőkre is kiterjessze ezeket a képességeket. Az Azure Defender segítségével megtalálhatja és elháríthatja a biztonsági réseket, a hozzáférés-vezérlést és az alkalmazás-vezérlőket a kártékony tevékenységek blokkolására, az elemzések és az intelligencia használatával észlelt fenyegetések észlelésére, valamint a támadás Az Azure Defender szolgáltatás díjmentesen kipróbálható. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).

Ez a rövid útmutató részletesen ismerteti a Azure Security Center és az Azure Defender használatának javasolt lépéseit. Ha befejezte az összes rövid útmutató lépését, a következőkre lesz szüksége:

> [!div class="checklist"]
> * Security Center engedélyezve az Azure-előfizetésekben
> * Azure Defender engedélyezve az Azure-előfizetéseken
> * Automatikus adatgyűjtés beállítása
> * Biztonsági riasztásokhoz beállított e-mail-értesítések
> * Az Azure-hoz csatlakoztatott hibrid és több felhőalapú gépek

## <a name="prerequisites"></a>Előfeltételek
A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).

Az Azure Defender előfizetésre való engedélyezéséhez hozzá kell rendelnie az előfizetés tulajdonosa, az előfizetés közreműködője vagy a biztonsági rendszergazda szerepkört.

## <a name="enable-security-center-on-your-azure-subscription"></a>Security Center engedélyezése az Azure-előfizetésben

> [!TIP]
> Ha a felügyeleti csoportban lévő összes előfizetéshez Security Center szeretne engedélyezni, tekintse [meg a Security Center engedélyezése több Azure-előfizetésben](onboard-management-group.md)című témakört.

1. Jelentkezzen be az [Azure Portalra](https://azure.microsoft.com/features/azure-portal/).

1. A portál menüjében válassza a **Security Center** lehetőséget. 

    Megnyílik a Security Center áttekintése oldal.

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="Security Center áttekintése irányítópult" lightbox="./media/security-center-get-started/overview.png":::

    A **Security Center – Áttekintés** egységes képet ad a hibrid felhőbeli számítási feladatok biztonsági rendszeréről, lehetővé téve a számítási feladatok biztonsági állapotának felderítését és felmérését, valamint a kockázatok észlelését és csökkentését. Security Center automatikusan, díjmentesen lehetővé teszi, hogy az Ön vagy egy másik előfizetés-felhasználó által korábban nem előfizetett Azure-előfizetéseket.

Megtekintheti és szűrheti az előfizetések listáját az **előfizetések** menüpont kiválasztásával. A Security Center úgy állítja be a kijelzőt, hogy tükrözze a kiválasztott előfizetések biztonsági állapotát. 

A Security Center első indítását követően néhány percen belül megjelenhet a következő:

- **Javaslatok** a csatlakoztatott erőforrások biztonságának javítására.
- A Security Center által értékelt erőforrások leltára, valamint az egyes eszközök biztonsági helyzete.

A Security Center teljes körű kihasználása érdekében folytassa a gyors üzembe helyezési szakasz következő lépéseivel.



## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban engedélyezte Azure Security Center. A következő lépés az Azure Defender engedélyezése az egységes biztonsági felügyelethez és a fenyegetések elleni védelemhez a hibrid Felhőbeli számítási feladatokban.

> [!div class="nextstepaction"]
> [Gyors útmutató: az Azure Defender engedélyezése](enable-azure-defender.md)
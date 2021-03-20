---
title: Azure Bastion-munkamenet figyelése és kezelése | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan választhat ki egy folyamatban lévő munkamenetet, és hogyan kényszerítheti le vagy törölheti azt.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 05/21/2020
ms.author: charwen
ms.openlocfilehash: beb4f8fc5b9e22bc3e2bdd4732c8321dfcd70780
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90980728"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Munkamenet-figyelés és-felügyelet az Azure Bastion szolgáltatásban

A megerősített szolgáltatás a virtuális hálózatban való üzembe helyezése és telepítése után zökkenőmentesen csatlakozhat a virtuális hálózat bármely virtuális GÉPHEZ. Ahogy a felhasználók munkaterhelésekhez csatlakoznak, az Azure Bastion használatával figyelheti a távoli munkameneteket, és gyors felügyeleti műveleteket hajthat végre. Az Azure Bastion munkamenet-figyelési funkciójával láthatja, hogy mely felhasználók csatlakoznak mely virtuális gépekhez. Azt az IP-címet jeleníti meg, amelyhez a felhasználó csatlakozik, mennyi ideig csatlakoztatva van, és mikor kapcsolódtak. A munkamenet-kezelési élmény lehetővé teszi, hogy egy folyamatban lévő munkamenetet válasszon ki, és kényszerítse a kapcsolat bontását, vagy törölje a munkamenetet, hogy leválassza a felhasználót a folyamatban lévő munkamenetből.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>Távoli munkamenetek figyelése

1. A [Azure Portal](https://portal.azure.com)navigáljon az Azure Bastion-erőforráshoz, és válassza a **munkamenetek** lehetőséget az Azure Bastion oldalon.

   ![A képernyőképen a kiválasztott munkamenetek Azure Portal menüjének beállításai láthatók.](./media/session-monitoring/sessions.png)
2. A **munkamenetek** lapon a jobb oldalon láthatja a folyamatban lévő távoli munkameneteket.

   ![munkamenet megtekintése](./media/session-monitoring/view-session.png)
3. Válassza a **frissítés** lehetőséget a távoli munkamenetek frissített listájának megtekintéséhez. Ha a frissítés lehetőséget választja, az Azure Bastion beolvassa a legfrissebb figyelési információkat, és frissíti azt a portálon.

   ![A képernyőképen egy olyan munkamenet látható, amely az Azure Bastion-t használja a frissítés lehetőség kiemelésével.](./media/session-monitoring/refresh.png)


## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>Folyamatban lévő távoli munkamenet törlése vagy kényszerített leválasztása

Kiválaszthatja a munkamenet (ek) készletét, és kényszerítheti a leválasztást. A következő lépések bemutatják, hogyan törölhet távoli munkameneteket:

1. Navigáljon az Azure Bastion-erőforráshoz, és válassza a **munkamenetek** lehetőséget az Azure Bastion oldalon.

   ![A képernyőképen a beállítások alatt kiválasztott munkamenetek Azure Portal látható.](./media/session-monitoring/navigate.png)
2. A munkamenetek kiválasztása után megtekintheti a távoli munkamenetek listáját.

   ![munkamenetek listázása](./media/session-monitoring/list.png)
3. Válasszon ki egy adott távoli munkamenetet, majd válassza ki a három ellipszist a munkamenet-sor jobb oldali végén, majd válassza a **Törlés** lehetőséget.

   ![Képernyőfelvétel: az a Azure Portal, amely a Delete (Törlés) ikonnal jelölt munkamenetet jeleníti meg.](./media/session-monitoring/delete.png)
4. Ha a Törlés lehetőséget választja, a távoli munkamenet le lesz választva, és a felhasználó "megszakadt" üzenet jelenik meg a távoli munkamenetben.

   ![A képernyőképen egy üzenet jelenik meg, amely ellenőrzi, hogy megszakadt-e a kapcsolat.](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Következő lépések

Olvassa el a [megerősített GYIK](bastion-faq.md)-t.

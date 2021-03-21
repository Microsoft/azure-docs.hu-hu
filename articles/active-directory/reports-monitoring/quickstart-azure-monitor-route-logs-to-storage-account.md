---
title: Oktatóanyag – a címtár naplófájljainak archiválása egy Storage-fiókba | Microsoft Docs
description: Megtudhatja, hogyan állíthat be Azure Diagnostics a Azure Active Directory naplók Storage-fiókba való leküldéséhez
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa2cac7c89a2cb1bd99675727bcd51e82c4a18ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "74007865"
---
# <a name="tutorial-archive-azure-ad-logs-to-an-azure-storage-account"></a>Oktatóanyag: Azure AD-naplók archiválása Azure Storage-fiókba

Az oktatóanyagból megtudhatja, hogyan konfigurálhatja úgy az Azure Monitort, hogy az Azure Active Directory- (Azure AD-) naplókat egy Azure-tárfiókba irányítsa.

## <a name="prerequisites"></a>Előfeltételek 

A szolgáltatás használatához a következőkre lesz szüksége:

* Egy Azure-előfizetés egy Azure-tárfiókkal. Ha nincs Azure-előfizetése, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/free/).
* Egy Azure AD-bérlő.
* Egy felhasználó, aki az adott Azure AD-bérlő *globális* vagy *biztonsági rendszergazdája*.

## <a name="archive-logs-to-an-azure-storage-account"></a>Tevékenységnaplók archiválása egy Azure-tárfiókba

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. Válassza ki **Azure Active Directory**  >  **tevékenység**-  >  **naplózási naplókat**. 

3. Válassza a **Beállítások exportálása** lehetőséget. 

4. A **Diagnosztikai beállítások** ablaktáblán a következő lehetőségek közül választhat:
   * Kattintson a **Beállítás szerkesztése** parancsra, ha a meglévő beállításokat szeretné szerkeszteni,
   * vagy a **Diagnosztikai beállítás hozzáadása** parancsra, ha egy új beállítást szeretne hozzáadni.  
     Legfeljebb három beállítás adható meg. 

     ![Beállítások exportálása](./media/quickstart-azure-monitor-route-logs-to-storage-account/ExportSettings.png)

5. Adjon egy rövid nevet a beállításnak, ami emlékezteti annak funkciójára (például: *Küldés az Azure-tárfiókba*). 

6. Jelölje be az **Archiválás tárfiókba** jelölőnégyzetet, majd válassza a **Tárfiók** elemet. 

7. Válassza ki, melyik Azure-előfizetésbe és tárfiókba kívánja átirányítani a naplókat.
 
8. Az **OK** gombra kattintva lépjen ki a konfigurálásból.

9. A következő lehetőségek egyikét vagy mindkettőt választhatja:
    * Jelölje be az **AuditLogs** (Naplófájlok) jelölőnégyzetet, ha az auditnaplókat szeretné továbbítani a tárfiókba. 
    * Jelölje be a **SignInLogs** (Bejelentkezési naplók) jelölőnégyzetet, ha a bejelentkezési naplókat szeretné továbbítani a tárfiókba.

10. A naplóadatok megőrzési idejét a csúszkával állíthatja be. Ez az érték alapértelmezés szerint *0*, ami azt jelenti, hogy a naplók korlátlan ideig megmaradnak a tárfiókban. Ha más értéket ad meg, a rendszer automatikusan törli a megadott számú napnál régebbi eseményeket.

11. A beállítás mentéséhez kattintson a **Mentés** gombra.

    ![Diagnosztikai beállítások](./media/quickstart-azure-monitor-route-logs-to-storage-account/DiagnosticSettings.png)

12. Körülbelül 15 perc elteltével ellenőrizze, hogy a rendszer leküldi-e a naplókat a tárfiókba. Lépjen az [Azure Portalra](https://portal.azure.com), kattintson a **Tárfiókok** elemre, válassza ki a korábban használt tárfiókot, és kattintson a **Blobok** elemre. Az **Auditnaplók** esetében kattintson az **insights-log-audit** elemre. A **Bejelentkezési naplók** esetében kattintson az **insights-logs-signin** elemre.

    ![A(z)](./media/quickstart-azure-monitor-route-logs-to-storage-account/StorageAccount.png)

## <a name="next-steps"></a>Következő lépések

* [A naplózási naplók sémájának értelmezése Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [A bejelentkezési naplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-sign-ins-log-schema.md)
* [Gyakori kérdések és ismert hibák](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
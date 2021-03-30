---
title: A Microsoft Endpoint Configuration Manager konfigurálása – Azure
description: A Microsoft Endpoint Configuration Manager konfigurálása a szoftverfrissítések Windows 10 Enterprise multi-session a Windows rendszerű virtuális asztalon való központi telepítéséhez.
author: Heidilohr
ms.topic: how-to
ms.date: 06/12/2020
ms.author: helohr
ms.reviewer: v-cawood; clemr
manager: lizross
ms.openlocfilehash: bfc797e0c11a1804d27c6c97fea8cb2ba62dca7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88010123"
---
# <a name="configure-microsoft-endpoint-configuration-manager"></a>A Microsoft Endpoint Configuration Manager konfigurálása

Ez a cikk azt ismerteti, hogyan konfigurálható a Microsoft Endpoint Configuration Manager, hogy automatikusan alkalmazza a frissítéseket egy Windows 10 Enterprise multi-session hostt futtató Windows rendszerű virtuális asztali gazdagépre.

## <a name="prerequisites"></a>Előfeltételek

A beállítás konfigurálásához a következőkre lesz szüksége:

   - Győződjön meg arról, hogy telepítette a Microsoft Endpoint Configuration Manager ügynököt a virtuális gépeken.
   - Győződjön meg arról, hogy a Microsoft Endpoint Configuration Manager verziója legalább a 1906-as ág szintjén van. A legjobb eredmény érdekében használja a 1910-as vagy újabb ág-szintet.

## <a name="configure-the-software-update-point"></a>A szoftverfrissítési pont konfigurálása

A Windows 10 Enterprise multi-session-munkamenet frissítéseinek fogadásához engedélyeznie kell a Windows Server 1903-es vagy újabb verzióját a Microsoft Endpoint Configuration Manager terméken belül. A termék beállítása akkor is érvényes, ha a Windows Server Update szolgáltatást használja a rendszerek frissítéseinek központi telepítéséhez.

Frissítések fogadása:

1. Nyissa meg a Microsoft Endpoint Configuration Managert, és válassza a **helyek** lehetőséget.
2. Válassza a **hely összetevőinek konfigurálása** lehetőséget.
3. Válassza a **szoftverfrissítési pont** lehetőséget a legördülő menüben.
4. Válassza a **Products** (Termékek) lapot.
5. Jelölje be a **Windows Server 1903-es és újabb verziójának** bejelölését.
6. Nyissa meg a **szoftver-függvénytár**  >  **Áttekintés**  >  **szoftverfrissítéseket**  >   , és válassza a **szoftverfrissítések szinkronizálása** lehetőséget.
7. Ellenőrizze a Wsyncmgr. log fájlt a **Program Files**  >  **Microsoft Configuration Manager**  >  **naplóban** , hogy megbizonyosodjon róla, hogy mentette a módosításokat. A frissítések szinkronizálása néhány percet is igénybe vehet.

## <a name="create-a-query-based-collection"></a>Lekérdezésen alapuló gyűjtemény létrehozása

Windows 10 Enterprise rendszerű, több munkamenetből álló virtuális gépek gyűjteményének létrehozásához lekérdezésen alapuló gyűjtemény használható az adott operációs rendszer SKU-jának azonosítására.

Gyűjtemény létrehozása:

1. Válassza **az eszközök és megfelelőség** lehetőséget.
2. Nyissa meg az **áttekintő**  >  **eszközök gyűjteményeit** , és kattintson a jobb gombbal az **eszközök** gyűjteménye elemre, majd válassza a legördülő menü **eszköz gyűjtemény létrehozása** elemét
3. A megnyíló menü **általános** lapján adjon meg egy nevet, amely leírja a gyűjteményt a **név** mezőben. A **Megjegyzés** mezőben további információkat adhat meg, amely leírja, hogy mi a gyűjtemény. A **gyűjtemény korlátozása** területen határozza meg, hogy mely gépeket kívánja megadni a gyűjtemény lekérdezésében.
4. A **tagsági szabályok** lapon adjon hozzá egy szabályt a lekérdezéshez Ehhez válassza a **szabály hozzáadása**, majd a **lekérdezési szabály** lehetőséget.
5. A **lekérdezési szabály tulajdonságainál** adja meg a szabály nevét, majd a **lekérdezési utasítás szerkesztése** lehetőség kiválasztásával adja meg a szabály paramétereit.
6. Válassza a **lekérdezési utasítás megjelenítése** lehetőséget.
7. Az utasításban adja meg a következő karakterláncot:

    ```syntax
    select
    SMS_R_SYSTEM.ResourceID,SMS_R_SYSTEM.ResourceType,SMS_R_SYSTEM.Name,SMS_R_SYSTEM.SMSUniqueIdentifier,SMS_R_SYSTEM.ResourceDomainORWorkgroup,SMS_R_SYSTEM.Client
    from SMS_R_System inner join SMS_G_System_OPERATING_SYSTEM on
    SMS_G_System_OPERATING_SYSTEM.ResourceId = SMS_R_System.ResourceId where
    SMS_G_System_OPERATING_SYSTEM.OperatingSystemSKU = 175
    ```

8. A gyűjtemény létrehozásához kattintson **az OK gombra** .
9. Annak ellenőrzéséhez, hogy sikeresen létrehozta-e a gyűjteményt, lépjen az **eszközök és megfelelőség**  >  **áttekintése**  >  **eszköz gyűjteményéhez**.

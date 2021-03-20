---
title: VIRTUÁLIS gépek automatikus indítási beállításainak konfigurálása Azure DevTest Labsban | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a virtuális gépek automatikus indítási beállításait a laborban. Ez a beállítás lehetővé teszi, hogy a laborban lévő virtuális gépek automatikusan el legyenek indítva egy adott időpontban.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 83e7b0836273a59eaaf66471bd0cb42d63ccf1c3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91328479"
---
# <a name="auto-startup-lab-virtual-machines"></a>Automatikus indítási Tesztkörnyezet virtuális gépei  
A Azure DevTest Labs lehetővé teszi a laborban lévő virtuális gépek automatikus indítását és leállítását ütemterv alapján. Az automatikus leállítási beállítások konfigurálásával kapcsolatos információkért lásd: [Azure DevTest Labs-tesztkörnyezet automatikus leállítási házirendjeinek kezelése](devtest-lab-auto-shutdown.md). 

Az automatikus leállítástól eltérően, ahol a házirend bekapcsolásakor a rendszer az összes virtuális gépet tartalmazza, az autostart-házirendhez a tesztkörnyezet felhasználójának explicit módon ki kell választania egy virtuális gépet, és be kell jelentkeznie erre az ütemtervre. Így nem fog könnyen futni a helyzet, amikor a nemkívánatos virtuális gépek véletlenül automatikusan elindulnak, és váratlan kiadásokat okoznak.

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy laborhoz tartozó autostart-házirendet.

## <a name="configure-autostart-settings-for-a-lab"></a>Tesztkörnyezet automatikus indítási beállításainak konfigurálása 
1. Navigáljon a labor kezdőlapjának oldalára. 
2. Válassza a **konfiguráció és szabályzatok** lehetőséget a bal oldali menüben. 

    ![Képernyőkép, amely a "konfiguráció és szabályzatok" menüt mutatja a DevTest laborban.](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. A **konfiguráció és házirendek** lapon hajtsa végre a következő lépéseket:
    
    1. Válassza **a** be lehetőséget, ha engedélyezni szeretné, hogy a **virtuális gépek automatikus indításra legyenek ütemezve** , hogy engedélyezze az autostart funkciót ehhez a laborhoz. 
    2. Válassza ki a kezdési időpontot (például: 8:00:00 AM) az **ütemezett kezdés** mezőhöz. 
    3. Válassza ki a használni kívánt **időzónát** . 
    4. Válassza ki **a hét azon napjait,** amelyeken a virtuális gépeket automatikusan el kell indítani. 
    5. Ezután válassza a **Mentés** lehetőséget az eszköztáron a beállítások mentéséhez. 

        ![Automatikus indítás beállításai](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > Ez a szabályzat nem alkalmazza automatikusan a laborban található virtuális gépek automatikus indítását. Ha **az egyéni virtuális** gépeket szeretné használni, lépjen a virtuális gép oldalra, és engedélyezze az **automatikus indítást** az adott virtuális géphez.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>A laborban lévő virtuális gépek automatikus indításának engedélyezése
Az alábbi eljárás lépéseit ismerteti a virtuális gépeknek a labor automatikus indítási házirendjébe való leválasztásához. 

1. A labor kezdőlapján válassza **ki a** **virtuális gépet a saját virtuális gépek** listájában. 

    ![Konfiguráció és házirendek menü](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. A **virtuális gép** lapon válassza az **Automatikus indítás** lehetőséget a bal oldali menüben vagy az **ütemtervek** listán. 

    ![Automatikus indítás menü kiválasztása](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. Az **autostart** lapon válassza a **be** lehetőséget a **virtuális gép automatikus indítási beállításra való ütemezésének engedélyezése** lehetőségnél.

    ![A virtuális gép automatikus indításának engedélyezése](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. Ezután válassza a **Mentés** lehetőséget az eszköztáron a beállítás mentéséhez. 


## <a name="next-steps"></a>Következő lépések
A laborok automatikus leállítási házirendjének megismeréséhez lásd: [Azure DevTest Labs](devtest-lab-auto-shutdown.md)

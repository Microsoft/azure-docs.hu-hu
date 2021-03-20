---
title: Hozzáférés egy osztályterem-tesztkörnyezethez az Azure Lab Services szolgáltatásban | Microsoft Docs
description: Ebben az oktatóanyagban egy oktató által beállított tantermi laborban érheti el a virtuális gépeket.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: dacfa34c0d3ab637ef513342bc5ce5fe81038e11
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85443468"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Oktatóanyag: Hozzáférés egy osztályterem-tesztkörnyezethez az Azure Lab Services szolgáltatásban
Ebben az oktatóanyagban a diákok megtanulnak csatlakozni egy osztályterem-tesztkörnyezet virtuális gépéhez (VM). 

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Regisztráció a laborba
> * A virtuális gép elindítása
> * Kapcsolódás a virtuális géphez

## <a name="register-to-the-lab"></a>Regisztráció a laborba

1. Navigáljon a pedagógustól kapott **regisztrációs URL-címre** . A regisztráció befejezése után nem szükséges a regisztrációs URL-cím használata. Ehelyett használja az URL-címet: [https://labs.azure.com](https://labs.azure.com) . Az Internet Explorer 11 még nem támogatott. 

    ![Regisztráció a laborba](./media/tutorial-connect-vm-in-classroom-lab/register-lab.png)
1. A regisztráció elvégzéséhez jelentkezzen be a szolgáltatásba az iskolai fiókjával. 

    > [!NOTE]
    > A Azure Lab Services használatához Microsoft-fiók szükséges. Ha nem Microsoft-fiók, például a Yahoo vagy a Google fiókok használatával próbál bejelentkezni a portálra, kövesse az utasításokat, és hozzon létre egy Microsoft-fiók, amely a nem Microsoft-fiókhoz lesz társítva. Ezután kövesse a lépéseket a regisztrációs folyamat befejezéséhez. 
1. A regisztrációt követően ellenőrizze, hogy látja-e a virtuális gépet abban a tesztkörnyezetben, amelyhez hozzáférése van. 

    ![Elérhető virtuális gépek](./media/tutorial-connect-vm-in-classroom-lab/accessible-vms.png)
1. Várjon, amíg a virtuális gép készen áll. A virtuális gép csempén figyelje meg a következő mezőket:
    1. A csempe tetején megjelenik a **labor neve**.
    1. A jobb oldalon megjelenik a virtuális gép **operációs rendszerét** jelképező ikon. Ebben a példában ez a Windows operációs rendszer. 
    1. A csempe folyamatjelzője megjeleníti az órák számát, amelyet a rendszer az Ön által hozzárendelt [kvóta-órák](how-to-configure-student-usage.md#set-quotas-for-users) száma alapján használ. Ez az idő az a további időpont, amelyet a tesztkörnyezet ütemezett ideje mellett is kiosztottak. 
    1. A csempe alján található ikonokat/gombokat a virtuális gép elindításához/leállításához és a virtuális géphez való kapcsolódáshoz használhatja. 
    1. A gombok jobb oldalán megtekintheti a virtuális gép állapotát. Győződjön meg arról, hogy a virtuális gép állapota **leállt**. 

        ![Leállított állapotú virtuális gép](./media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>A virtuális gép elindítása
1. **Indítsa el** a virtuális gépet az alábbi képen látható első gomb kiválasztásával. Ez a folyamat hosszabb időt vesz igénybe.  

    ![A virtuális gép elindítása](./media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Győződjön meg arról, hogy a virtuális gép állapota **fut** értékre van állítva. 

    ![Futó állapotú virtuális gép](./media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Figyelje meg, hogy az első gomb ikonja egy **leállítási** műveletet jelöl. Ezzel a gombbal állíthatja le a virtuális gépet. 

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

1. A labor virtuális géphez való **kapcsolódáshoz** kattintson a következő képen látható második gombra. 

    ![Kapcsolódás egy virtuális géphez](./media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Hajtsa végre az alábbi lépések egyikét: 
    1. **Windows rendszerű** virtuális gépek esetén mentse az **RDP** -fájlt a merevlemezre. Nyissa meg az RDP-fájlt a virtuális géphez való kapcsolódáshoz. Használja a pedagógustól kapott **felhasználónevet** és **jelszót** , hogy bejelentkezzen a gépre. 
    3. **Linux** rendszerű virtuális gépek esetén az **SSH** vagy az **RDP** (ha engedélyezve van) használatával csatlakozhat hozzájuk. További információ: [Távoli asztali kapcsolat engedélyezése Linux rendszerű gépekhez](how-to-enable-remote-desktop-linux.md). 

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy osztályterem laborhoz fér hozzá, amely a pedagógustól kapott regisztrációs hivatkozással érhető el.

A labor tulajdonosaként szeretné megtekinteni, hogy ki regisztrált a laborban, és nyomon követheti a virtuális gépek használatát. Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan követheti nyomon a labor használatát:

> [!div class="nextstepaction"]
> [Tesztkörnyezet használatának nyomon követése](tutorial-track-usage.md) 

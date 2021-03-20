---
title: Virtuális gépek jelszavának beállítása Azure Lab Servicesban | Microsoft Docs
description: Megtudhatja, hogyan állíthatja be és állíthatja alaphelyzetbe a virtuális gépek (VM) jelszavát a Azure Lab Services Labs szolgáltatásban.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6ae577ee4c0c7e31760e0fb12afeaeac1ef8b7e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434226"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Virtuális gépek készleteinek létrehozása és kezelése 
Ez a cikk bemutatja, hogyan végezheti el a következő feladatokat:

- A virtuális gépek (VM-EK) számának növeléséhez a tesztkörnyezetben
- Minden virtuális gép vagy kiválasztott virtuális gép indítása 
- Virtuális gépek alaphelyzetbe állítása

## <a name="update-the-lab-capacity"></a>A tesztkörnyezet kapacitásának frissítése
A labor kapacitásának növeléséhez vagy csökkentéséhez (a laborban található virtuális gépek száma) hajtsa végre a következő lépéseket:

1. A **virtuális gép készlete** lapon válassza a **labor kapacitása: &lt; számú &gt; gép** lehetőséget.
2. Adja meg a laborban használni kívánt **virtuális gépek új számát** . Ennek a számnak nagyobbnak vagy egyenlőnek kell lennie a laborban regisztrált felhasználók számával. 
3. Ezután válassza a **Mentés** lehetőséget. 

    ![Képernyőfelvétel: a "labor Capacity" ablak a "maximális gépek a laborban" és a "Mentés" gomb kiválasztásával.](./media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Ha növelte a kapacitást, megtekintheti a létrehozott virtuális gépet vagy virtuális gépeket. Ha nem látja az új virtuális gépet a listában, frissítse a lapot. 

    ![Létrehozott virtuális gép](./media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Virtuális gépek indítása

### <a name="start-ot-stop-all-vms"></a>Az összes virtuális gép leállításának elindítása
1. Váltson a **virtuálisgép-készlet** lapra. 
2. Válassza az **összes elindítása** lehetőséget az eszköztáron. 

    ![Összes elindítása gomb](./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Az összes virtuális gép elindítása után leállíthatja az összes virtuális gépet az eszköztáron az **összes leállítása** gomb kiválasztásával. 

    ![Összes leállítása gomb](./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Kijelölt virtuális gépek indítása
A kiválasztott virtuális gépek kétféleképpen indíthatók el (egy vagy több). Első lépésként válassza ki a virtuális gépet vagy virtuális gépeket a listából, majd válassza az **Indítás** lehetőséget az eszköztáron. 

A második módszer egy vagy több virtuális gép kijelölése a listában, majd az **állapot** oszlopban lévő gomb bekapcsolása. 

![Kijelölt virtuális gépek indítása](./media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Hasonlóképpen leállíthat egy vagy több virtuális gépet is, ha az **állapot** oszlopban lévő gombra kattint, vagy kiválasztja az eszköztár **Leállítás** elemét. 

> [!NOTE]
> Ha egy oktató bekapcsol egy tanulói virtuális gépet, az nem érinti a tanulóra vonatkozó kvótát. A felhasználóhoz tartozó kvóta meghatározza, hogy a felhasználó számára hány labor óra legyen elérhető az ütemezett osztály időpontján kívül. A kvótákkal kapcsolatos további információkért lásd: [kvóták beállítása a felhasználók](how-to-configure-student-usage.md?#set-quotas-for-users)számára.

## <a name="reset-vms"></a>Virtuális gépek alaphelyzetbe állítása

Egy vagy több virtuális gép alaphelyzetbe állításához jelölje ki azokat a listában, majd válassza az **Alaphelyzetbe állítás** lehetőséget az eszköztáron. 

![Kijelölt virtuális gépek alaphelyzetbe állítása](./media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

A **virtuális gép (ek) alaphelyzetbe állítása** párbeszédpanelen válassza az **Alaphelyzetbe állítás** lehetőséget. 

![Virtuális gép alaphelyzetbe állítása párbeszédpanel](./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)

## <a name="set-password-for-vms"></a>Virtuális gépek jelszavának beállítása
A labor tulajdonosa (pedagógus) beállíthatja/alaphelyzetbe állíthatja a virtuális gépek jelszavát a labor létrehozásakor (labor létrehozása varázsló) vagy a labor létrehozása után a **sablon** lapon. 

### <a name="set-password-at-the-time-of-lab-creation"></a>Jelszó beállítása a labor létrehozásakor
A labor tulajdonosa (pedagógus) beállíthatja a virtuális gépek jelszavát a labor létrehozási varázsló **virtuális gép hitelesítő adatok** lapján.

![Új tesztkörnyezet ablak](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Ha engedélyezi vagy letiltja az **azonos jelszó használata az összes virtuális gép számára** beállítást ezen az oldalon, a pedagógus dönthet úgy, hogy ugyanazt a jelszót használja a tesztkörnyezetben lévő összes virtuális géphez, vagy lehetővé teszi a tanulók számára a virtuális gépek jelszavának beállítását. Alapértelmezés szerint ez a beállítás az Ubuntu kivételével minden Windows-és Linux operációsrendszer-lemezkép esetében engedélyezve van. Ha ez a beállítás le van tiltva, a diákoknak meg kell adnia egy jelszót, amikor első alkalommal próbálnak csatlakozni a virtuális géphez. 

### <a name="reset-password-later"></a>Jelszó alaphelyzetbe állítása később

1. A labor **sablon** lapján válassza a **jelszó alaphelyzetbe állítása** lehetőséget az eszköztáron. 
1. A **jelszó alaphelyzetbe állítása** párbeszédpanelen adja meg a jelszót, és válassza a **jelszó alaphelyzetbe állítása** lehetőséget.
    
    ![Jelszó beállítása párbeszédpanel](./media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Kapcsolódás tanulói virtuális gépekhez
Ha a következő feltételek teljesülnek, a tesztkörnyezet létrehozója (pedagógus) tud csatlakozni a tanuló virtuális géphez: 

- A tesztkörnyezet létrehozásakor a **virtuális gépekhez ugyanazt a jelszót kell használni**
- A virtuális gép fut 

 A tanuló virtuális géphez való kapcsolódáshoz vigye az egeret a listában a virtuális gépre, és válassza a számítógép gombot.  

![Kapcsolódás tanulói virtuális géphez gomb](./media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> Ha a pedagógus elindítja a virtuális gépet, és csatlakozik hozzá, a tanulói kvóta nincs hatással. 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>Virtuális gépek listájának exportálása CSV-fájlba

1. Váltson a **virtuálisgép-készlet** lapra.
2. Válassza a **...** (három pont) lehetőséget az eszköztáron, majd válassza a **CSV exportálása** lehetőséget. 

    ![Virtuális gépek listájának exportálása](./media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>Következő lépések
Ha szeretne többet megtudni a tanulók által beállítható egyéb használati lehetőségekről, tekintse meg a következő cikket: a [tanulói használat konfigurálása](how-to-configure-student-usage.md).

A virtuális gépek jelszavának alaphelyzetbe állításával kapcsolatos további információkért lásd: a [Virtual Machines szolgáltatáshoz tartozó jelszó beállítása vagy alaphelyzetbe állítása a laborban (diákok)](how-to-set-virtual-machine-passwords-student.md).
---
title: Labs-ütemterv létrehozása a Azure Lab Servicesban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre ütemterveket a laborokhoz a Azure Lab Servicesban, hogy a laborokban lévő virtuális gépek egy adott időpontban induljon el és legyenek leállítva.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2a827c3d9f3022cb7d27ee43c9c95227c44f97e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434025"
---
# <a name="create-and-manage-schedules-for-labs-in-azure-lab-services"></a>Labs-ütemtervek létrehozása és kezelése Azure Lab Services 
Az ütemtervek lehetővé teszik a tantermi laborok konfigurálását, hogy a laborban lévő virtuális gépek automatikusan elindulnak és leálljanak egy adott időpontban. Meghatározhat egy egyszeri vagy ismétlődő ütemtervet. Az alábbi eljárások egy osztályterem laborhoz tartozó ütemtervek létrehozását és kezelését ismertetik: 

> [!IMPORTANT]
> A virtuális gépek ütemezett futási ideje nem számít bele a [felhasználó számára kiosztott kvótába](how-to-configure-student-usage.md#set-quotas-for-users). A kvóta az ütemezett órákon kívüli időre szól, amelyet a tanuló a virtuális gépeken tölt. 

## <a name="set-a-schedule-for-the-lab"></a>A laborhoz tartozó ütemterv beállítása
Hozzon létre egy ütemezett eseményt a laborhoz, hogy a laborban lévő virtuális gépek meghatározott időpontokban automatikusan elindulnak/leállnak. A korábban megadott felhasználói kvóta az egyes felhasználók számára az ütemezett időponton kívül hozzárendelt további idő. 

> [!NOTE]
> Az első lépések elkezdése előtt itt látható, hogyan befolyásolják az ütemezett Tesztkörnyezet virtuális gépei: 
>- A sablon virtuális gépe nem szerepel az ütemtervekben. 
>- Csak a hozzárendelt virtuális gépek vannak elindítva. Ez azt jelenti, hogy ha egy gépet nem igényel a végfelhasználó (tanuló), akkor a gép nem indul el az ütemezett órákon. 
>- Az összes virtuális gép (akár felhasználó által igényelt, akár nem) leáll a labor-ütemterv alapján. 

1. Váltson az **ütemezések** lapra, és válassza az eszköztár **ütemezett esemény hozzáadása** elemét. 

    ![Képernyőfelvétel: a Azure Lab Services "Schedule" (ütemterv hozzáadása) lap, amelyen be van jelölve az ütemezett Hozzáadás gomb.](./media/how-to-create-schedules/add-schedule-button.png)
2. Ellenőrizze, hogy a **standard** érték van-e kiválasztva az **esemény típusára**. A **csak indítás** gombra kattintva adhatja meg a virtuális gépek kezdési idejét. Ha csak a virtuális gépek leállítási idejét szeretné megadni, válassza a **Leállítás** lehetőséget. 
7. Az **ismétlés** szakaszban válassza ki az aktuális ütemtervet. 

    ![Ütemterv hozzáadása gomb az ütemtervek lapon](./media/how-to-create-schedules/select-current-schedule.png)
5. A **REPEAT (ismétlés** ) párbeszédpanelen hajtsa végre a következő lépéseket:
    1. Győződjön meg arról, hogy **minden héten** be van állítva az **ismétlés** mező. 
    3. A **kezdő dátum** meghatározása.
    4. Itt adhatja meg azt a **kezdési időpontot** , amikor a virtuális gépeket el szeretné indítani.
    5. Itt adhatja meg a **leállítási időt** , amikor a virtuális gépeket le kell állítani. 
    6. Adja meg az **időzónát** a megadott kezdési és befejezési időpontnál. 
    2. Válassza ki azokat a napokat, amelyeknek érvénybe szeretné venni az ütemtervet. A következő példában Monday-Thursday van kiválasztva. 
    8. Kattintson a **Mentés** gombra. 

        ![Ismétlődő ütemterv beállítása](./media/how-to-create-schedules/set-repeat-schedule.png)

3. Az **ütemezett esemény hozzáadása** lapon a **Megjegyzések (nem kötelező)** mezőben adja meg az ütemezés leírását vagy megjegyzéseit. 
4. Az **ütemezett esemény hozzáadása** lapon válassza a **Mentés** lehetőséget. 

    ![Heti ütemterv](./media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Ütemtervek megtekintése a naptárban
A naptár nézetben Kiemelt dátumok és időpontok a következő képen látható módon jeleníthetők meg:

![Ütemtervek a naptár nézetben](./media/how-to-create-schedules/schedules-calendar.png)

Kattintson a jobb felső sarokban található **ma** gombra, hogy az aktuális dátumra váltson a naptárban. Kattintson a **balra mutató nyílra** az előző hétre és a **jobbra mutató nyílra** való váltáshoz a naptár következő hetében. 

## <a name="edit-a-schedule"></a>Ütemterv szerkesztése
Amikor kijelöl egy kiemelt ütemtervet a naptárban, a gombokkal **szerkesztheti** vagy **törölheti** az ütemtervet. 

![Ütemterv szerkesztése lap](./media/how-to-create-schedules/schedule-edit-button.png)

Az **ütemezett esemény szerkesztése** lapon frissítheti az ütemezést, és a **Mentés** gombra kattinthat. 

## <a name="delete-a-schedule"></a>Ütemezés törlése

1. Az ütemterv törléséhez jelöljön ki egy kiemelt ütemtervet a naptárban, és válassza a Kuka ikon (Törlés) gombot:

    ![Törlés gomb az eszköztáron](./media/how-to-create-schedules/schedule-delete-button.png)
2. Az **ütemezett esemény törlése** párbeszédpanelen válassza az **Igen** lehetőséget a törlés megerősítéséhez. 



## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Rendszergazdaként, labor-fiókok létrehozása és kezelése](how-to-manage-lab-accounts.md)
- [Labor tulajdonosaként Labs létrehozása és kezelése](how-to-manage-classroom-labs.md)
- [Tesztkörnyezet tulajdonosaként konfigurálhatja és szabályozhatja a labor használatát](how-to-configure-student-usage.md)
- [Labor-felhasználóként a hozzáférés Labs](how-to-use-classroom-lab.md)

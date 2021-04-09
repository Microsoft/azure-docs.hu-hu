---
title: SAP ide EHP7 SP3 telepítése az SAP ERP 6,0-hez az Azure-on | Microsoft Docs
description: SAP ide EHP7 SP3 telepítése SAP ERP 6,0-hez az Azure-ban
author: hermanndms
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 5184df38b6a6b2757a5d5f7c0970258657cf5c0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101671005"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>SAP ide EHP7 SP3 telepítése SAP ERP 6,0-hez az Azure-ban
Ez a cikk bemutatja, hogyan helyezhet üzembe egy SQL Server és az Azure-beli Windows operációs rendszert futtató SAP ide-rendszert az SAP Cloud Appliance Library (SAP CAL) 3,0 használatával. A képernyőképen a lépésenkénti folyamat látható. Egy másik megoldás üzembe helyezéséhez kövesse ugyanezen lépéseket.

Az SAP CAL elindításához lépjen az [SAP Cloud Appliance Library](https://cal.sap.com/) webhelyére. Az SAP az új [SAP Cloud Appliance Library 3,0-es könyvtáráról](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience)is tartalmaz blogot. 

> [!NOTE]
> 2017. május 29-én az SAP CAL üzembe helyezéséhez a kevésbé előnyben részesített klasszikus üzemi modell mellett használhatja a Azure Resource Manager üzemi modellt is. Javasoljuk, hogy az új Resource Manager-alapú üzemi modellt használja, és hagyja figyelmen kívül a klasszikus üzemi modellt.

Ha már létrehozott egy, a klasszikus modellt használó SAP CAL-fiókot, *létre kell hoznia egy másik SAP Cal-fiókot*. Ennek a fióknak kizárólag az Azure-ba kell telepítenie a Resource Manager-modell használatával.

Az SAP CAL-be való bejelentkezés után az első oldal általában a **megoldások** lapjára vezet. Az SAP CAL-on kínált megoldások folyamatosan növekednek, ezért előfordulhat, hogy egy kicsit kell görgetni a kívánt megoldás megtalálásához. A kizárólag az Azure-on elérhető, Kiemelt Windows-alapú SAP ide-megoldás az üzembe helyezés folyamatát mutatja be:

![SAP CAL-megoldások](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Fiók létrehozása az SAP CAL-ben
1. Ha első alkalommal szeretne bejelentkezni az SAP CAL-be, használja az SAP S-User vagy más, az SAP-ban regisztrált felhasználót. Ezután Definiáljon egy SAP CAL-fiókot, amelyet az SAP CAL használ a berendezések Azure-beli üzembe helyezéséhez. A fiók definíciójában a következőket kell tennie:

    a. Válassza ki az Azure-beli üzembe helyezési modellt (Resource Manager vagy klasszikus).

    b. Adja meg az Azure-előfizetését. Egy SAP CAL-fiók csak egy előfizetéshez rendelhető hozzá. Ha egynél több előfizetésre van szüksége, létre kell hoznia egy másik SAP CAL-fiókot.
    
    c. Adja meg az SAP CAL engedélyt az Azure-előfizetésében való üzembe helyezéshez.

   > [!NOTE]
   >  A következő lépések bemutatják, hogyan hozhat létre SAP CAL-fiókot a Resource Manager-alapú üzemelő példányokhoz. Ha már rendelkezik egy, a klasszikus üzemi modellel társított SAP CAL-fiókkal *, akkor az* alábbi lépéseket követve hozzon létre egy új SAP Cal-fiókot. Az új SAP CAL-fióknak üzembe kell helyeznie a Resource Manager-modellben.

1. Új SAP CAL-fiók létrehozásához a **fiókok** lapon két lehetőség látható az Azure-hoz: 

    a. A **Microsoft Azure (klasszikus)** a klasszikus üzemi modell, és már nem ajánlott.

    b. **Microsoft Azure** az új Resource Manager-alapú üzemi modell.

    ![Képernyőfelvétel: a P CAL-fiókok Microsoft Azure felhívtak.](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    A Resource Manager-modellben való üzembe helyezéshez válassza a **Microsoft Azure** lehetőséget.

    ![A képernyőképen A P CAL-fiókok láthatók.](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Adja meg a Azure Portal található Azure- **előfizetés azonosítóját** . 

    ![SAP CAL-előfizetés azonosítója](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Ha engedélyezni szeretné az SAP CAL üzembe helyezését az Ön által megadott Azure-előfizetésben, kattintson az **Engedélyezés** elemre. A böngésző lapon a következő oldal jelenik meg:

    ![Internet Explorer Cloud Services-bejelentkezés](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. Ha több felhasználó is szerepel a listában, válassza ki azt a Microsoft-fiók, amely kapcsolódik a kiválasztott Azure-előfizetés rendszergazdájának. A böngésző lapon a következő oldal jelenik meg:

    ![Az Internet Explorer Cloud Services megerősítése](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. Kattintson az **Elfogadás** lehetőségre. Ha az engedélyezés sikeres, az SAP CAL-fiók definíciója ismét megjelenik. Rövid idő elteltével egy üzenet megerősíti, hogy az engedélyezési folyamat sikeres volt.

1. Ha az újonnan létrehozott SAP CAL-fiókot szeretné hozzárendelni a felhasználóhoz, adja meg a **felhasználói azonosítóját** a jobb oldali szövegmezőben, majd kattintson a **Hozzáadás** gombra. 

    ![Fiók a felhasználói társításhoz](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. A fiók az SAP CAL-be való bejelentkezéshez használt felhasználóhoz való hozzárendeléséhez kattintson a **felülvizsgálat** gombra. 

1. A felhasználó és az újonnan létrehozott SAP CAL-fiók közötti társítás létrehozásához kattintson a **Létrehozás** gombra.

    ![Felhasználói fiók társítása](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Sikeresen létrehozott egy SAP CAL-fiókot, amely a következőket teszi lehetővé:

- Használja a Resource Manager-alapú üzemi modellt.
- SAP-rendszerek üzembe helyezése az Azure-előfizetésében.

> [!NOTE]
> Ahhoz, hogy a Windows és SQL Server alapján üzembe lehessen helyezni az SAP ide-megoldást, előfordulhat, hogy regisztrálnia kell egy SAP CAL-előfizetésre. Ellenkező esetben előfordulhat, hogy a megoldás **zárolva** jelenik meg az Áttekintés oldalon.

### <a name="deploy-a-solution"></a>Megoldás üzembe helyezése
1. Miután beállította az SAP CAL-fiókot, válassza ki **a Windows és SQL Server megoldás SAP ide-megoldását** . Kattintson a **példány létrehozása** lehetőségre, és erősítse meg a használati és használati feltételeket. 

1. Az **alapszintű mód: példány létrehozása** lapon a következőket kell tennie:

    a. Adja meg a példány **nevét**.

    b. Válasszon ki egy Azure- **régiót**. Előfordulhat, hogy egy SAP CAL-előfizetésre van szüksége, hogy több Azure-régió is elérhető legyen.

    c.  Adja meg a megoldás fő **jelszavát** , az alábbiak szerint:

    ![SAP CAL alapszintű mód: példány létrehozása](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. Kattintson a **Létrehozás** lehetőségre. Némi várakozás után a megoldás méretétől és összetettségének függvényében (az SAP CAL becslést biztosít) az állapot aktívként jelenik meg, és készen áll a használatra: 

    ![SAP CAL-példányok](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. Az SAP CAL által létrehozott erőforráscsoport és az összes objektum megkereséséhez lépjen a Azure Portal. A virtuális gép az SAP CAL-ban megadottal azonos nevű példánnyal is megtalálhatók.

    ![Erőforráscsoport-objektumok](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. Az SAP CAL portálon lépjen az üzembe helyezett példányok pontra, és kattintson a **Kapcsolódás** lehetőségre. Megjelenik a következő előugró ablak: 

    ![Kapcsolódás a példányhoz](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. Mielőtt az egyik lehetőséget használja az üzembe helyezett rendszerekhez való kapcsolódásra, kattintson **első lépések útmutató**. A dokumentáció minden kapcsolódási módszerhez megnevezi a felhasználókat. A felhasználók jelszavai a telepítési folyamat elején megadott fő jelszóra vannak beállítva. A dokumentációban a többi funkcionális felhasználó is megjelenik a jelszavuk alapján, amelyek segítségével bejelentkezhet a telepített rendszerbe.

    ![SAP üdvözlő dokumentáció](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Az Azure-ban néhány órán belül üzembe helyezhető egy kifogástalan SAP ide-rendszer.

Ha SAP CAL-előfizetést vásárolt, az SAP teljes mértékben támogatja az Azure-beli SAP CAL-n keresztül történő üzembe helyezést. A támogatási várólista a BC-VCM-CAL.


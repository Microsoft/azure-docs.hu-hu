---
title: SAP S/4HANA vagy BW/4HANA üzembe helyezése Azure-beli virtuális gépen | Microsoft Docs
description: SAP S/4HANA vagy BW/4HANA üzembe helyezése Azure-beli virtuális gépen
author: hermanndms
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 09/15/2016
ms.author: hermannd
ms.reviewer: cynthn
ms.openlocfilehash: 4b739efcf2695a5385351f531efb6201bbea1cd5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671933"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>SAP S/4HANA vagy BW/4HANA üzembe helyezése az Azure-ban
Ez a cikk bemutatja, hogyan helyezhet üzembe S/4HANA az Azure-ban az SAP Cloud Appliance Library (SAP CAL) 3,0 használatával. Más SAP HANA-alapú megoldások, például a BW/4HANA üzembe helyezéséhez kövesse ugyanezeket a lépéseket.

> [!NOTE]
> Az SAP CAL-ról az [SAP Cloud Appliance Library](https://cal.sap.com/) webhelyén talál további információt. Az SAP a 3,0-es [SAP Cloud Appliance Library könyvtáráról](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience)is tartalmaz blogot.
> 
> [!NOTE]
> 2017. május 29-én az SAP CAL üzembe helyezéséhez a kevésbé előnyben részesített klasszikus üzemi modell mellett használhatja a Azure Resource Manager üzemi modellt is. Javasoljuk, hogy az új Resource Manager-alapú üzemi modellt használja, és hagyja figyelmen kívül a klasszikus üzemi modellt.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Lépésenkénti folyamat a megoldás üzembe helyezéséhez

A következő képernyőképek azt mutatják be, hogyan helyezhet üzembe S/4HANA az Azure-ban az SAP CAL használatával. A folyamat ugyanúgy működik, mint az egyéb megoldások, például a BW/4HANA.

A **megoldások** lapon az Azure-on elérhető SAP Cal HANA-alapú megoldások láthatók. **SAP S/4HANA 1610 FPS01, Fully-Activated készülék** középső sorban van:

![SAP CAL-megoldások](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Fiók létrehozása az SAP CAL-ben
1. Ha első alkalommal szeretne bejelentkezni az SAP CAL-be, használja az SAP S-User vagy más, az SAP-ban regisztrált felhasználót. Ezután Definiáljon egy SAP CAL-fiókot, amelyet az SAP CAL használ a berendezések Azure-beli üzembe helyezéséhez. A fiók definíciójában a következőket kell tennie:

    a. Válassza ki az Azure-beli üzembe helyezési modellt (Resource Manager vagy klasszikus).

    b. Adja meg az Azure-előfizetését. Egy SAP CAL-fiók csak egy előfizetéshez rendelhető hozzá. Ha egynél több előfizetésre van szüksége, létre kell hoznia egy másik SAP CAL-fiókot.

    c. Adja meg az SAP CAL engedélyt az Azure-előfizetésében való üzembe helyezéshez.

   > [!NOTE]
   >  A következő lépések bemutatják, hogyan hozhat létre SAP CAL-fiókot a Resource Manager-alapú üzemelő példányokhoz. Ha már rendelkezik egy, a klasszikus üzemi modellel társított SAP CAL-fiókkal *, akkor az* alábbi lépéseket követve hozzon létre egy új SAP Cal-fiókot. Az új SAP CAL-fióknak üzembe kell helyeznie a Resource Manager-modellben.

1. Hozzon létre egy új SAP CAL-fiókot. A **fiókok** lapon három választási lehetőség látható az Azure-hoz: 

    a. A **Microsoft Azure (klasszikus)** a klasszikus üzemi modell, és már nem ajánlott.

    b. **Microsoft Azure** az új Resource Manager-alapú üzemi modell.

    c. A **21Vianet által üzemeltetett Windows Azure** egy olyan lehetőség Kínában, amely a klasszikus üzemi modellt használja.

    A Resource Manager-modellben való üzembe helyezéshez válassza a **Microsoft Azure** lehetőséget.

    ![SAP CAL-fiók részletei](./media/cal-s4h/s4h-pic-2a.png)

1. Adja meg a Azure Portal található Azure- **előfizetés azonosítóját** .

   ![SAP CAL-fiókok](./media/cal-s4h/s4h-pic3c.png)

1. Ha engedélyezni szeretné az SAP CAL üzembe helyezését az Ön által megadott Azure-előfizetésben, kattintson az **Engedélyezés** elemre. A böngésző lapon a következő oldal jelenik meg:

   ![Internet Explorer Cloud Services-bejelentkezés](./media/cal-s4h/s4h-pic4c.png)

1. Ha több felhasználó is szerepel a listában, válassza ki azt a Microsoft-fiók, amely kapcsolódik a kiválasztott Azure-előfizetés rendszergazdájának. A böngésző lapon a következő oldal jelenik meg:

   ![Az Internet Explorer Cloud Services megerősítése](./media/cal-s4h/s4h-pic5a.png)

1. Kattintson az **Elfogadás** lehetőségre. Ha az engedélyezés sikeres, az SAP CAL-fiók definíciója ismét megjelenik. Rövid idő elteltével egy üzenet megerősíti, hogy az engedélyezési folyamat sikeres volt.

1. Ha az újonnan létrehozott SAP CAL-fiókot szeretné hozzárendelni a felhasználóhoz, adja meg a **felhasználói azonosítóját** a jobb oldali szövegmezőben, majd kattintson a **Hozzáadás** gombra.

   ![Fiók a felhasználói társításhoz](./media/cal-s4h/s4h-pic8a.png)

1. A fiók az SAP CAL-be való bejelentkezéshez használt felhasználóhoz való hozzárendeléséhez kattintson a **felülvizsgálat** gombra. 
 
1. A felhasználó és az újonnan létrehozott SAP CAL-fiók közötti társítás létrehozásához kattintson a **Létrehozás** gombra.

   ![Felhasználó – SAP CAL-fiók társítása](./media/cal-s4h/s4h-pic9b.png)

Sikeresen létrehozott egy SAP CAL-fiókot, amely a következőket teszi lehetővé:

- Használja a Resource Manager-alapú üzemi modellt.
- SAP-rendszerek üzembe helyezése az Azure-előfizetésében.

Most már elkezdheti az S/4HANA üzembe helyezését a felhasználói előfizetésben az Azure-ban.

> [!NOTE]
> Mielőtt továbblépne, állapítsa meg, hogy rendelkezik-e Azure vCPU-kvótákkal az Azure H-sorozatú virtuális gépekhez. Az SAP CAL jelenleg a H-sorozatú virtuális gépeket használja az Azure-ban a SAP HANA-alapú megoldások üzembe helyezéséhez. Előfordulhat, hogy az Azure-előfizetése nem rendelkezik H-sorozatú vCPU-kvótával a H-sorozathoz. Ha igen, előfordulhat, hogy kapcsolatba kell lépnie az Azure támogatási szolgálatával legalább 16 H-sorozatú vCPU kvótájának lekéréséhez.
> 
> [!NOTE]
> Ha az SAP CAL-ben üzembe helyez egy megoldást az Azure-ban, akkor előfordulhat, hogy csak egy Azure-régiót választ ki. Az SAP CAL által javasolt Azure-régiókban való üzembe helyezéshez az SAP-Cal-előfizetést kell vásárolnia. Előfordulhat, hogy meg kell nyitnia egy SAP-üzenetet, hogy az CAL-fiókja engedélyezve legyen az eredetileg javasolt Azure-régiókba való továbbításhoz.

### <a name="deploy-a-solution"></a>Megoldás üzembe helyezése

Hozzon üzembe egy megoldást az SAP CAL **Solutions** oldaláról. Az SAP CAL két sorozatot tartalmaz a üzembe helyezéshez:

- Egy alapszintű sorozatot, amely egy oldalt használ a telepítendő rendszer definiálásához
- Egy speciális folyamat, amely a virtuálisgép-méretekhez biztosít bizonyos döntéseket 

Itt mutatjuk be az üzembe helyezés alapvető elérési útját.

1. A **fiók részletei** lapon a következőket kell tennie:

    a. Válasszon ki egy SAP CAL-fiókot. (Használjon olyan fiókot, amely a Resource Manager-alapú üzemi modellel való üzembe helyezéshez van társítva.)

    b. Adja meg a példány **nevét**.

    c. Válasszon ki egy Azure- **régiót**. Az SAP CAL a régiót javasolja. Ha egy másik Azure-régióra van szüksége, és nem rendelkezik SAP CAL-előfizetéssel, az SAP-val kell rendelnie egy CAL-előfizetést.

    d. Adja meg a nyolc vagy kilenc karakterből álló megoldás fő **jelszavát** . A jelszót a különböző összetevők rendszergazdái használják.

   ![SAP CAL alapszintű mód: példány létrehozása](./media/cal-s4h/s4h-pic10a.png)

1. Kattintson a **Létrehozás** gombra, és a megjelenő üzenetablakban kattintson **az OK** gombra.

   ![SAP CAL támogatott VM-méretek](./media/cal-s4h/s4h-pic10b.png)

1. A **titkos kulcs** párbeszédpanelen kattintson a **tároló** elemre a titkos kulcs az SAP Cal-ben való tárolásához. Ha a titkos kulcs jelszavas védelmét szeretné használni, kattintson a **Letöltés** gombra. 

   ![SAP CAL titkos kulcsa](./media/cal-s4h/s4h-pic10c.png)

1. Olvassa el az SAP CAL **figyelmeztető** üzenetet, és kattintson **az OK** gombra.

   ![SAP CAL figyelmeztetés](./media/cal-s4h/s4h-pic10d.png)

    Az üzembe helyezés most megtörténik. Némi várakozás után a megoldás méretétől és összetettségének függvényében (az SAP CAL becslést biztosít) az állapot aktívként jelenik meg, és használatra kész.

1. Egy erőforráscsoport más kapcsolódó erőforrásaival gyűjtött virtuális gépek megkereséséhez nyissa meg a Azure Portal: 

   ![Az új portálon üzembe helyezett SAP CAL-objektumok](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. Az SAP CAL portálon az állapot **aktívként** jelenik meg. A megoldáshoz való kapcsolódáshoz kattintson a **Kapcsolódás** gombra. A különböző összetevőkhöz való kapcsolódás különböző beállításai a megoldáson belül vannak telepítve.

   ![SAP CAL-példányok](./media/cal-s4h/active_solution.png)

1. Mielőtt az egyik lehetőséget használja az üzembe helyezett rendszerekhez való kapcsolódásra, kattintson **első lépések útmutató**. 

   ![Kapcsolódás a példányhoz](./media/cal-s4h/connect_to_solution.png)

    A dokumentáció minden kapcsolódási módszerhez megnevezi a felhasználókat. A felhasználók jelszavai a telepítési folyamat elején megadott fő jelszóra vannak beállítva. A dokumentációban a többi funkcionális felhasználó is megjelenik a jelszavuk alapján, amelyek segítségével bejelentkezhet a telepített rendszerbe. 

    Ha például a Windows Távoli asztal gépen előre telepített SAP GUI-t használja, az S/4 rendszer a következőhöz hasonló lehet:

   ![SM50 az előre telepített SAP grafikus felhasználói felületén](./media/cal-s4h/gui_sm50.png)

    Ha a DBACockpit használja, a példány a következőhöz hasonló lehet:

   ![SM50 a DBACockpit SAP grafikus felhasználói felületén](./media/cal-s4h/dbacockpit.png)

Néhány órán belül egy kifogástalan SAP S/4 készülék üzembe helyezése az Azure-ban történik.

Ha SAP CAL-előfizetést vásárolt, az SAP teljes mértékben támogatja az Azure-beli SAP CAL-n keresztül történő üzembe helyezést. A támogatási várólista a BC-VCM-CAL.








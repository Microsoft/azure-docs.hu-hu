---
title: Logikai alkalmazások kezelése a Azure Portalban
description: Logikai alkalmazások szerkesztése, engedélyezése, letiltása vagy törlése a Azure Portal használatával.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 07/20/2020
ms.openlocfilehash: d50f577a7170982be004cc8957114f79675fbc6e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006098"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Logikai alkalmazások kezelése a Azure Portalban

A Logic apps a [Azure Portal](https://portal.azure.com) vagy a [Visual Studio](manage-logic-apps-with-visual-studio.md)használatával kezelhető. Ez a cikk bemutatja, hogyan szerkesztheti, tilthatja le, engedélyezheti vagy törölhet logikai alkalmazásokat a Azure Portal. Ha most ismerkedik a Azure Logic Appsval, tekintse meg a [Mi az a Azure Logic apps](logic-apps-overview.md)? című témakört.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy meglévő logikai alkalmazás. Ha meg szeretné tudni, hogyan hozhat létre logikai alkalmazásokat a Azure Portalban, tekintse meg a rövid útmutató [: az első munkafolyamat létrehozása Azure Logic apps-Azure Portal használatával](quickstart-create-first-logic-app-workflow.md)című témakört.

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>Logikai alkalmazások keresése

A logikai alkalmazás megkereséséhez és megnyitásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

1. Az Azure Search sávban írja be a kifejezést `logic apps` , majd válassza a **Logic apps** lehetőséget.

   ![Képernyőkép a Azure Portal menüjéről, Logic Apps kereséssel és kijelöléssel a keresősávban.](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. A **Logic apps** lapon keresse meg és válassza ki a kezelni kívánt logikai alkalmazást.

   A logikai alkalmazás **Áttekintés** paneljének megnyitása után az alábbi módokon szűrheti a **Logic apps** lapon megjelenő listát:

   * Logikai alkalmazások keresése név szerint
   * Logikai alkalmazások szűrése előfizetés, Erőforráscsoport, hely és címkék alapján
   * Logikai alkalmazások csoportosítása erőforráscsoport, típus, előfizetés és hely szerint

## <a name="view-logic-app-properties"></a>Logikai alkalmazás tulajdonságainak megtekintése

1. A Azure Portal keresse meg [és nyissa meg a logikai alkalmazást](#find-logic-app).

1. A logikai alkalmazás menüjében, a **Beállítások** területen válassza a **Tulajdonságok** elemet.

1. A **Tulajdonságok** panelen megtekintheti és átmásolhatja a logikai alkalmazással kapcsolatos alábbi adatokat:

   * **Név**
   * **Erőforrás-azonosító**
   * **Erőforráscsoport**
   * **Hely**
   * **Típus** 
   * **Előfizetés neve**
   * **Előfizetés azonosítója**
   * **Hozzáférési végpont**
   * **Futásidejű kimenő IP-címek**
   * **Hozzáférési végpont IP-címei**
   * **Összekötő kimenő IP-címei**

## <a name="disable-or-enable-logic-apps"></a>Logikai alkalmazások letiltása vagy engedélyezése

A Azure Portal egyszerre engedélyezheti vagy letilthatja [egyetlen logikai alkalmazást](#disable-enable-single-logic-app) vagy [több logikai](#disable-or-enable-multiple-logic-apps) alkalmazást. [A Visual Studióban is engedélyezheti vagy letilthatja a Logic apps](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app)szolgáltatást.

A logikai alkalmazás letiltása a munkafolyamat-példányokat érinti, és az alábbi módokon fut:

* A folyamatban lévő és a függőben lévő futtatások addig folytatódnak, amíg be nem fejeződik. A futtatások számától függően ez a folyamat hosszabb időt is igénybe vehet.

* A Logic Apps motor nem hoz létre vagy nem futtat új munkafolyamat-példányokat.

* Az trigger nem fog elindulni a feltételek legközelebb való teljesülése esetén.

* Az trigger állapota arra a pontra emlékszik, amelyen a logikai alkalmazás le lett állítva. Tehát ha újra engedélyezi a logikai alkalmazást, az eseményindító az utolsó Futtatás óta az összes feldolgozatlan elemnél elindul.

  Ha le szeretné állítani a logikai alkalmazást az utolsó Futtatás óta feldolgozatlan elemek égetéséről, törölje az aktiválás állapotát a logikai alkalmazás újbóli engedélyezése előtt:

  1. A Azure Portal keresse meg [és nyissa meg a logikai alkalmazást](#find-logic-app).

  1. Szerkessze a logikai alkalmazás triggerének bármely részét.

  1. Mentse a módosításokat. Ez a lépés alaphelyzetbe állítja az trigger aktuális állapotát.

  1. [Engedélyezze újra a logikai alkalmazást](#disable-enable-single-logic-app).

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>Egyetlen logikai alkalmazás letiltása vagy engedélyezése

1. A Azure Portal keresse meg [és nyissa meg a logikai alkalmazást](#find-logic-app).

1. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget. Válasszon a következő lehetőségek közül:

   * Az eszköztáron válassza a **Letiltás** lehetőséget.

     ![Képernyőkép a logikai alkalmazás eszköztáráról, amely a Letiltás gomb kiválasztásával jelenik meg.](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     Ha a logikai alkalmazás már le van tiltva, csak az **enable (Engedélyezés** ) lehetőség jelenik meg.

   * Az eszköztáron válassza az **Engedélyezés** lehetőséget.

     ![Képernyőkép a logikai alkalmazás eszköztáráról, amely az Engedélyezés gombot választja.](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     Ha a logikai alkalmazás már engedélyezve van, csak a **Letiltás** lehetőség jelenik meg. 

   A Azure Portal egy értesítést jelenít meg a fő Azure-eszköztáron, amely megerősíti, hogy a művelet sikeres vagy sikertelen volt-e.

   ![A Azure Portal képernyőképe, amely a művelet állapotának megerősítésére szolgáló értesítést jeleníti meg](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>Több logikai alkalmazás letiltása vagy engedélyezése

1. A Azure Portal keresse meg a letiltani vagy engedélyezni kívánt [logikai alkalmazásokat](#find-logic-app) .

1. Annak ellenőrzéséhez, hogy egy logikai alkalmazás jelenleg engedélyezve vagy le van-e tiltva, a **Logic apps** lapon tekintse át az adott logikai alkalmazás **állapot** oszlopát. 

   ![Képernyőkép a Azure Portal Logic Apps oldalról, amely megjeleníti a logikai alkalmazások állapotát oszlop szerint rendezve.](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   Ha az **állapot** oszlop nem látható, akkor a **Logic apps** eszköztáron válassza az **előnézet kipróbálása** lehetőséget.

   ![Képernyőkép a Azure Portal Logic Apps oldalról, amely az előzetes előnézet gomb kiválasztásával jelenik meg.](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. A jelölőnégyzet oszlopban válassza ki a letiltani vagy engedélyezni kívánt logikai alkalmazásokat. Az eszköztáron válassza a **Letiltás** vagy az **Engedélyezés** lehetőséget.

   ![Képernyőkép a Azure Portal Logic Apps oldalról, amely a több logikai alkalmazás számára engedélyezi és letiltja a gombokat.](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. Ha megjelenik a megerősítő mező, kattintson az **Igen** gombra a folytatáshoz.

   A Azure Portal egy értesítést jelenít meg a fő Azure-eszköztáron, amely megerősíti, hogy a művelet sikeres vagy sikertelen volt-e.

## <a name="delete-logic-apps"></a>Logikai alkalmazások törlése

[Törölhet egyetlen logikai alkalmazást](#delete-single-logic-app) , vagy egyszerre [több logikai alkalmazást is törölhet](#delete-multiple-logic-apps) a Azure Portalban. [A logikai alkalmazást a Visual Studióban is törölheti](manage-logic-apps-with-visual-studio.md#delete-your-logic-app).

A logikai alkalmazás törlése a következő módokon befolyásolja a munkafolyamat-példányokat:

* A folyamatban lévő és a függőben lévő futtatások addig folytatódnak, amíg be nem fejeződik. A futtatások számától függően ez a folyamat hosszabb időt is igénybe vehet.

* A Logic Apps motor nem hoz létre vagy nem futtat új munkafolyamat-példányokat.

> [!NOTE]
> Ha töröl és újból létrehoz egy alárendelt logikai alkalmazást, újra kell mentenie a szülő logikai alkalmazást. Az újból létrehozott alárendelt alkalmazás eltérő metaadatokkal fog rendelkezni.
> Ha nem kívánja újra a szülő logikai alkalmazást a gyermek újbóli létrehozása után, a gyermek logikai alkalmazás hívásai sikertelenek lesznek, a "nem engedélyezett" hibaüzenettel. Ez a viselkedés a szülő-gyermek Logic apps-alkalmazásokra vonatkozik, például azokra, amelyek az integrációs fiókokban vagy az Azure functions szolgáltatásban használnak összetevőket.

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>Egyetlen logikai alkalmazás törlése

1. A Azure Portal keresse meg [és nyissa meg a logikai alkalmazást](#find-logic-app).

1. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget. A logikai alkalmazás eszköztárán válassza a **Törlés** lehetőséget.

   ![Képernyőkép a logikai alkalmazás eszköztáráról, a törlés gomb kiválasztásával.](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. Amikor megjelenik a megerősítő mező, adja meg a logikai alkalmazás nevét, és válassza a **Törlés** lehetőséget.

   ![Képernyőkép: Logic Apps Rákérdezés az egyetlen logikai alkalmazás törlésének megerősítéséhez.](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   A Azure Portal egy értesítést jelenít meg a fő Azure-eszköztáron, amely megerősíti, hogy a művelet sikeres vagy sikertelen volt-e.

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>Több logikai alkalmazás törlése

1. A Azure Portal keresse meg [a törölni kívánt logikai alkalmazásokat](#find-logic-app).

1. A jelölőnégyzet oszlopban válassza ki a törölni kívánt logikai alkalmazásokat. Az eszköztáron válassza a **Törlés** lehetőséget.

   ![Képernyőkép Logic Apps oldalról, amely a törlésre kijelölt listában több logikai alkalmazást mutat be.](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. Amikor megjelenik a megerősítő mező, írja be a szöveget `yes` , majd válassza a **Törlés** lehetőséget.

   ![Képernyőkép: Logic Apps Rákérdezés a több logikai alkalmazás törlésének megerősítéséhez.](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   A Azure Portal egy értesítést jelenít meg a fő Azure-eszköztáron, amely megerősíti, hogy a művelet sikeres vagy sikertelen volt-e.

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>Logic app-verziók kezelése

A Logic apps verziójának vezérlésére a Azure Portal használhatja. Megtalálhatja a logikai alkalmazás korábbi verzióinak előzményeit, és előléptetheti az előző verziókat.

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>Korábbi verziók keresése és megtekintése

1. A Azure Portal keresse meg [a kezelni kívánt logikai alkalmazást](#find-logic-app).

1. A logikai alkalmazás menüjében, a **fejlesztői eszközök** területen válassza a **verziók** elemet.

   ![Képernyőkép a logikai alkalmazásról Azure Portalban, a verziók kiválasztása oldal a fejlesztői eszközök területen.](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. A listából válassza ki a kezelni kívánt logikai alkalmazás **verzióját** . A lista szűréséhez megadhatja a **verziószámot** a keresősávban.

1. Az **Előzmények verziója** lapon az előző verzió részleteit láthatja csak olvasható módban. A Logic Apps **Designer** és a **kód nézet** módok közül választhat.

   ![Képernyőkép a Logic Apps verziótörténete lapon, amely a kód nézet és a Tervező nézet beállításait mutatja.](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>Korábbi verziók előléptetése

1. A logikai alkalmazás korábbi verzióiban [Keresse meg és válassza ki az előléptetni kívánt verziót](#find-version-history).

1. Az **Előzmények verziója** lapon válassza az **előléptetés** lehetőséget.

   ![Képernyőkép a Logic app korábbi verzióiról, és megjeleníti a gombot az előző verzió előléptetéséhez.](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. A megnyíló **Logic apps Designer** oldalon szerkessze a szükséges verziót. Válthat a **tervező** és a **kód nézet** módok között. A **paramétereket**, **sablonokat** és **összekötőket** is frissítheti.

   ![Képernyőkép a Logic Apps Designerről, amely a logikai alkalmazás korábbi verziójának előléptetésére szolgáló gombot mutatja.](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. Az előző verzió előléptetésének és befejezésének megtakarításához válassza a **Mentés** lehetőséget. (Vagy a módosítások megszakításához válassza az **Elvetés** lehetőséget.) 

   Ha újra [megtekinti a logikai alkalmazás verziójának előzményeit](#find-version-history) , akkor a lista tetején megjelenik az előléptetett verzió, és új azonosítóval rendelkezik.

## <a name="next-steps"></a>Következő lépések

* [Logikai alkalmazások figyelése](monitor-logic-apps.md)

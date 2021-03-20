---
title: Logikai alkalmazások szerkesztése és kezelése a Visual Studio és a Cloud Explorer használatával
description: Szerkesztés, frissítés, kezelés, Hozzáadás a forrás vezérlőelemhez és logikai alkalmazások üzembe helyezése a Visual Studio és a Cloud Explorer használatával
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/29/2020
ms.openlocfilehash: cde8db5310e3ede2721ba327b28c789ccd0b7dd0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87280766"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Logikai alkalmazások kezelése a Visual Studióval

Habár logikai alkalmazásokat hozhat létre, szerkeszthet, kezelhet és helyezhet üzembe a [Azure Portalban](https://portal.azure.com), a Visual studiót is használhatja, ha a logikai alkalmazásokat hozzá kívánja adni a forrás vezérlőelemhez, különböző verziókat tesz közzé, és különböző üzembe helyezési környezetekhez [Azure Resource Manager](../azure-resource-manager/management/overview.md) sablonokat hoz létre. A Visual Studio Cloud Explorer segítségével megkeresheti és kezelheti a logikai alkalmazásait más Azure-erőforrásokkal együtt. Például megnyithatja, letöltheti, szerkesztheti, futtathatja, megtekintheti a futtatási előzményeket, letilthatja és engedélyezheti a Azure Portalban már üzembe helyezett Logic apps-alkalmazásokat. Ha még nem ismeri a Azure Logic Apps használatát a Visual Studióban, Ismerje meg, [hogyan hozhat létre logikai alkalmazásokat a Visual Studióval](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

[A logikai alkalmazásokat a Azure Portal is kezelheti](manage-logic-apps-with-azure-portal.md).

> [!IMPORTANT]
> A Logic apps üzembe helyezése vagy közzététele a Visual Studióban felülírja az alkalmazás verzióját a Azure Portalban. Ha tehát a megtartani kívánt Azure Portal módosításokat hajt végre, akkor a Visual studióból a következő üzembe helyezéskor vagy a Visual studióból való közzététel előtt [frissítse a logikai alkalmazást](#refresh) a Azure Portal.

<a name="requirements"></a>

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Ha még nincsenek telepítve, töltse le és telepítse az alábbi eszközöket:

  * [Visual Studio 2019, 2017 vagy 2015 – Community Edition vagy újabb](https://aka.ms/download-visual-studio). Ez a rövid útmutató a Visual Studio Community 2017 kiadást használja, amely ingyenes.

    > [!IMPORTANT]
    > A Visual Studio 2019-es vagy 2017-es vagy-es telepítésekor ügyeljen arra, hogy kiválassza az **Azure-fejlesztési** számítási feladatot.
    > További információ: [Az Azure-fiókokhoz társított erőforrások kezelése a Visual Studio Cloud Explorerben](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer).

    A Cloud Explorer for Visual Studio 2015 telepítéséhez [töltse le a Cloud Explorer alkalmazást a Visual Studio piactérről](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). További információ: [Az Azure-fiókokhoz társított erőforrások kezelése a Visual Studio Cloud Explorerben (2015)](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015).

  * [Azure SDK (2.9.1 vagy újabb)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * A Visual Studio bővítmény legújabb Azure Logic Apps eszközei a kívánt verzióhoz:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Letöltheti és telepítheti az Azure Logic Apps alkalmazást közvetlenül a Visual Studio piacteréről, vagy nézzen utána, [hogyan telepítheti a bővítményt a Visual Studio rendszerén belül](/visualstudio/ide/finding-and-using-visual-studio-extensions). Ne feledje, hogy a telepítés után újra kell indítani a Visual Studiót.

  * Ha Azure Government-előfizetéseket szeretné használni a Visual Studióval, tekintse meg a következő témaköröket a további telepítéshez:

    * Visual Studio 2019: gyors útmutató [: Kapcsolódás a Azure Governmenthoz a Visual Studióval](../azure-government/documentation-government-connect-vs.md)

    * Visual Studio 2017: [Az Azure Environment választó Visual Studio bővítmény bemutatása](https://devblogs.microsoft.com/azuregov/introducing-the-azure-environment-selector-visual-studio-extension/), amelyet a [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=SteveMichelotti.AzureEnvironmentSelector)-en tölthet le és telepíthet.

* Hozzáférés a világhálóhoz a Embedded Logic Apps Designer használatával

  A tervezőnek internetkapcsolatra van szüksége, hogy létre tudja hozni az erőforrásokat az Azure-ban, és be tudja olvasni a tulajdonságokat és adatokat a logikai alkalmazásban lévő összekötőkből.

<a name="find-logic-apps-vs"></a>

## <a name="find-logic-apps"></a>Logikai alkalmazások keresése

A Visual Studióban megtalálhatja az Azure-előfizetéshez társított összes logikai alkalmazást, és a Azure Portal a Cloud Explorer használatával telepítheti azokat.

1. Nyissa meg a Visual Studiót. A **nézet** menüben válassza a **Cloud Explorer** lehetőséget.

1. A Cloud Explorerben válassza a **Fiókkezelés** ikont. Válassza ki a logikai alkalmazásokhoz társított Azure-előfizetést, majd válassza az **alkalmaz** lehetőséget. Például:

   !["Fiókkezelés" kiválasztása](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. A **Fiókkezelés** ikon mellett válassza az **erőforrástípusok** lehetőséget. Az Azure-előfizetése alatt bontsa ki a **Logic apps** , így megtekintheti az előfizetéséhez társított összes telepített logikai alkalmazást.

Ezután nyissa meg a logikai alkalmazást a Logic app Editorban.

<a name="open-designer"></a>

## <a name="open-logic-apps-in-visual-studio"></a>Logikai alkalmazások megnyitása a Visual Studióban

A Visual Studióban megnyithatja a korábban létrehozott és üzembe helyezett Logic apps-alkalmazásokat közvetlenül a Azure Portal vagy Azure Resource Group-projektekkel a Visual Studióval.

1. [Nyissa meg a Cloud Explorert, és keresse meg a logikai alkalmazást](#find-logic-apps-vs).

1. A logikai alkalmazás helyi menüjében válassza a **Megnyitás a Logic app Editor** használatával lehetőséget.

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a legújabb Visual Studio-frissítésekkel.

   ![Üzembe helyezett logikai alkalmazás megnyitása Azure Portal](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Miután a logikai alkalmazás megnyílik Logic Apps Designerben, a tervező alján kiválaszthatja a **kód nézetet** , hogy áttekintse az alapul szolgáló logikai alkalmazás definíciós struktúráját. Ha a logikai alkalmazáshoz központi telepítési sablont szeretne létrehozni, Ismerje meg, [hogyan tölthet le egy Azure Resource Manager sablont](#download-logic-app) a logikai alkalmazáshoz. További információ a [Resource Manager-sablonokról](../azure-resource-manager/templates/overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Letöltés az Azure-ból

A logikai alkalmazásokat [letöltheti](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource) a [Azure Portalból](https://portal.azure.com) , és mentheti őket [Azure Resource Manager](../azure-resource-manager/management/overview.md) sablonként. Ezután helyileg szerkesztheti a sablonokat a Visual Studióval, és testreszabhatja a logikai alkalmazásokat a különböző üzembe helyezési környezetekhez.  A Logic apps letöltése automatikusan *felparaméterezi* a definíciókat a [Resource Manager-sablonokban](../azure-resource-manager/templates/overview.md), amely a JavaScript Object Notation (JSON) is használja.

1. [Nyissa meg az Azure-ból letölteni kívánt logikai alkalmazást](#open-designer)a Visual Studióban a Cloud Explorer használatával.

1. A logikai alkalmazás helyi menüjében válassza a **Megnyitás a Logic app Editor** használatával lehetőséget.

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a legújabb Visual Studio-frissítésekkel.

   A logikai alkalmazás megnyílik a Logic app Designerben.

1. A tervező eszköztárán válassza a **Letöltés** lehetőséget.

   ![Logikai alkalmazás letöltése Azure Portalról](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. Amikor a rendszer megkéri a helyet, keresse meg a helyet, és mentse a logikai alkalmazás definíciójának Resource Manager-sablonját JSON-formátumban (. JSON).

   A logikai alkalmazás definíciója a `resources` Resource Manager-sablon alszakaszában jelenik meg. Most már szerkesztheti a Logic app-definíciót és a Resource Manager-sablont a Visual Studióval. A sablont [Azure erőforráscsoport-projektként](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) is hozzáadhatja egy Visual Studio-megoldáshoz. Ismerkedjen meg a [Visual Studióban található Logic apps-projektekkel kapcsolatos Azure Resource Group-projektekkel](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Integrációs fiókra mutató hivatkozás

A vállalatok közötti (B2B) vállalati integrációs forgatókönyvek logikai alkalmazásainak létrehozásához összekapcsolhatja a logikai alkalmazást egy korábban létrehozott [integrációs fiókkal](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , amely ugyanabban a régióban található, mint a logikai alkalmazás. Az integrációs fiók B2B-összetevőket, például kereskedelmi partnereket, szerződéseket, sémákat és térképeket tartalmaz, és lehetővé teszi, hogy a logikai alkalmazás VÁLLALATKÖZI összekötőket használjon az XML-érvényesítéshez és a sima fájl kódolásához vagy dekódolásához. Bár [ezt a hivatkozást a Azure Portal használatával is létrehozhatja](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account), a Visual studiót az [Előfeltételek](#requirements)teljesítése után is használhatja, és a logikai alkalmazás JSON-(. JSON-) fájlként is létezik egy [Azure erőforráscsoport-projektben](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). Ismerkedjen meg a [Visual Studióban található Logic apps-projektekkel kapcsolatos Azure Resource Group-projektekkel](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project).

1. A Visual Studióban nyissa meg az Azure erőforráscsoport-projektet, amely tartalmazza a logikai alkalmazást.

1. Megoldáskezelő a fájl helyi menüjében nyissa meg a **<Logic-app-Name # C1.js** fájlt, és válassza a **Megnyitás a Logic app designerrel** lehetőséget. (Billentyűzet: CTRL + L)

   ![Logikai alkalmazás. JSON fájljának megnyitása a Logic app Designerrel](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a legújabb frissítésekkel a Visual Studióban és a Azure Logic Apps Tools bővítményben.

1. Győződjön meg arról, hogy a Logic app Designer fókuszban van a tervező fülének vagy felületének kiválasztásával, így a Tulajdonságok ablak megjeleníti a logikai alkalmazás **integrációs fiókjának** tulajdonságát.

   ![Tulajdonságok ablak – "integrációs fiók" tulajdonság](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > Ha a Tulajdonságok ablak még nincs megnyitva, a **nézet** menüben válassza a **Tulajdonságok ablak** elemet. (Billentyűzet: nyomja meg az F4 billentyűt)

1. Nyissa meg az **integrációs fiók** tulajdonságai listát, és válassza ki a logikai alkalmazáshoz csatolni kívánt integrációs fiókot, például:

   ![Az "integrációs fiók" tulajdonságok listájának megnyitása](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Ha elkészült, ne felejtse el menteni a Visual Studio-megoldást.

Ha az **integrációs fiók** tulajdonságot a Visual Studióban állítja be, és Azure Resource Manager sablonként menti a logikai alkalmazást, a sablon a kiválasztott integrációs fiókhoz tartozó paraméter-deklarációt is tartalmazza. A sablon paramétereinek és a Logic apps szolgáltatással kapcsolatos további információkért lásd [: Áttekintés: a logikai alkalmazások telepítésének automatizálása](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="change-location"></a>

## <a name="change-deployment-location"></a>Telepítési hely módosítása

A Visual Studióban, ha a logikai alkalmazás JSON-(. JSON-) fájlként létezik egy [Azure erőforráscsoport-projektben](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) , amelyet az üzembe helyezés automatizálására használ, a logikai alkalmazás a hely típusára és egy adott helyre van beállítva. Ez a hely vagy egy Azure-régió vagy egy meglévő [integrációs szolgáltatási környezet (ISE)](connect-virtual-network-vnet-isolated-environment.md).

Ha módosítani szeretné a logikai alkalmazás helyének típusát vagy helyét, a Logic app Designer használatával meg kell nyitnia a logikai alkalmazás munkafolyamat-definíciós (. JSON) fájlját Megoldáskezelő. Ezek a tulajdonságok nem módosíthatók a Cloud Explorer használatával.

> [!IMPORTANT]
> Ha megváltoztatja a hely típusát a **régióról** [**integrációs szolgáltatási környezetre**](connect-virtual-network-vnet-isolated-environment-overview.md) , a logikai alkalmazás [díjszabási modelljét](logic-apps-pricing.md#fixed-pricing) a számlázáshoz, a [korlátozásokhoz](logic-apps-limits-and-config.md#integration-account-limits), az [integrációs fiók támogatásához](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)stb. A másik hely típusának kiválasztása előtt győződjön meg arról, hogy tisztában van a logikai alkalmazással kapcsolatos hatással.

1. A Visual Studióban nyissa meg az Azure erőforráscsoport-projektet, amely tartalmazza a logikai alkalmazást.

1. A Megoldáskezelőban nyissa meg a `<logic-app-name>.json` fájl helyi menüjét, és válassza a **Megnyitás a Logic app Designerben** lehetőséget. (Billentyűzet: CTRL + L)

   ![Logikai alkalmazás. JSON fájljának megnyitása a Logic app Designerrel](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a legújabb frissítésekkel a Visual Studióban és a Azure Logic Apps Tools bővítményben.

1. Győződjön meg arról, hogy a Logic app Designer fókuszban van a tervező fülének vagy felületének kiválasztásával, így a Tulajdonságok ablak a logikai alkalmazás **hely típusának** és **helyének** kiválasztása tulajdonságot jeleníti meg. A projekt helyének típusa az egyik **régióra** vagy **integrációs szolgáltatási környezetra** van beállítva.

   ![Tulajdonságok ablak – "a hely típusának kiválasztása" & "location" tulajdonságok](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > Ha a Tulajdonságok ablak még nincs megnyitva, a **nézet** menüben válassza a **Tulajdonságok ablak** elemet. (Billentyűzet: nyomja meg az F4 billentyűt)

1. A hely típusának módosításához nyissa meg a **hely típusának kiválasztása** tulajdonságot, és válassza ki a kívánt hely típusát.

   Ha például a hely típusa **integrációs szolgáltatási környezet**, akkor kiválaszthatja a **régiót**.

   !["Hely típusának kiválasztása" tulajdonság – hely típusának módosítása](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. Az adott hely módosításához nyissa meg a **Location (hely** ) tulajdonság listáját. A hely típusa alapján válassza ki a kívánt helyet, például:

   * Válasszon másik Azure-régiót:

     ![Nyissa meg a "location" (hely) tulajdonságot, válasszon másik Azure-régiót](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * Válasszon másik ISE-t:

     ![Nyissa meg a "location" (hely) tulajdonságot, válasszon egy másik ISE elemet](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. Ha elkészült, ne felejtse el menteni a Visual Studio-megoldást.

Ha megváltoztatja a hely típusát vagy helyét a Visual Studióban, és Azure Resource Manager sablonként menti a logikai alkalmazást, a sablon a hely típusára és helyére vonatkozó paramétereket is tartalmaz. A sablon paramétereinek és a Logic apps szolgáltatással kapcsolatos további információkért lásd [: Áttekintés: a logikai alkalmazások telepítésének automatizálása](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Frissítés az Azure-ból

Ha a Azure Portal módosítja a logikai alkalmazást, és szeretné megtartani ezeket a módosításokat, akkor győződjön meg arról, hogy a módosításokkal frissíti az alkalmazás verzióját a Visual Studióban.

* A Visual Studióban, a Logic app Designer eszköztárán válassza a **frissítés** lehetőséget.

  -vagy-

* A Visual Studio Cloud Explorerben nyissa meg a logikai alkalmazás helyi menüjét, és válassza a **frissítés** lehetőséget.

![Logikai alkalmazás frissítése a frissítésekkel](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>A logikai alkalmazások frissítéseinek közzététele

Ha készen áll a logikai alkalmazás frissítéseinek üzembe helyezésére a Visual studióból az Azure-ba, akkor a Logic app Designer eszköztárán válassza a **Közzététel** lehetőséget.

![Frissített logikai alkalmazás közzététele Azure Portal](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>A logikai alkalmazás manuális futtatása

Az Azure-ban üzembe helyezett Logic apps-t manuálisan is aktiválhatja a Visual studióból. A Logic app Designer eszköztárán válassza az **trigger futtatása** lehetőséget.

![Trigger manuális futtatása a logikai alkalmazáshoz](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Futtatási előzmények áttekintése

A Logic app-futtatások állapotának ellenőrzéséhez és a problémák diagnosztizálásához tekintse át a Visual Studióban futó alkalmazások részleteit, például bemeneteit és kimeneteit.

1. A Cloud Explorerben nyissa meg a logikai alkalmazás helyi menüjét, és válassza a **futtatási előzmények megnyitása** lehetőséget.

   ![A logikai alkalmazás futtatási előzményeinek megnyitása](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. Egy adott Futtatás részleteinek megtekintéséhez kattintson duplán egy futtatásra. Például:

   ![Adott futtatással kapcsolatos információk megtekintése](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > A tábla tulajdonság szerinti rendezéséhez válassza ki az oszlop fejlécét az adott tulajdonsághoz.

1. Bontsa ki azokat a lépéseket, amelyek bemeneteit és kimeneteit szeretné áttekinteni, például:

   ![Az egyes lépésekhez tartozó bemenetek és kimenetek megtekintése](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Logikai alkalmazás letiltása vagy engedélyezése

A logikai alkalmazás törlése nélkül leállíthatja az indítást a következő alkalommal, amikor a trigger feltétele teljesül. A logikai alkalmazás letiltásával megakadályozható, hogy a Logic Apps motor a logikai alkalmazás jövőbeli munkafolyamat-példányait hozza létre és futtassa. A Cloud Explorerben nyissa meg a logikai alkalmazás helyi menüjét, és válassza a **Letiltás** lehetőséget.

![Logikai alkalmazás letiltása a Cloud Explorerben](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> Ha letilt egy logikai alkalmazást, a rendszer nem hoz létre új futtatásokat. Az összes folyamatban lévő és függőben lévő Futtatás a befejezésig folytatódni fog, ami hosszabb időt is igénybe vehet.

A logikai alkalmazás újraaktiválásához a Cloud Explorerben nyissa meg a logikai alkalmazás helyi menüjét, és válassza az **Engedélyezés** lehetőséget.

![Logikai alkalmazás engedélyezése a Cloud Explorerben](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>A logikai alkalmazás törlése

Ha törölni szeretné a logikai alkalmazást a Azure Portal, a Cloud Explorerben nyissa meg a logikai alkalmazás helyi menüjét, és válassza a **Törlés** lehetőséget.

![Logikai alkalmazás törlése Azure Portal](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> Amikor törli a logikai alkalmazást, a rendszer nem kezdeményez új futtatásokat. A rendszer minden folyamatban lévő és függő futtatást megszakít. Ha több ezer futtatása van, a megszakítás jelentős ideig eltarthat.

> [!NOTE]
> Ha töröl és újból létrehoz egy alárendelt logikai alkalmazást, újra kell mentenie a szülő logikai alkalmazást. Az újból létrehozott alárendelt alkalmazás eltérő metaadatokkal fog rendelkezni.
> Ha nem kívánja újra a szülő logikai alkalmazást a gyermek újbóli létrehozása után, a gyermek logikai alkalmazás hívásai sikertelenek lesznek, a "nem engedélyezett" hibaüzenettel. Ez a viselkedés a szülő-gyermek Logic apps-alkalmazásokra vonatkozik, például azokra, amelyek az integrációs fiókokban vagy az Azure functions szolgáltatásban használnak összetevőket.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a Logic app-projektet a Logic Apps Designerben nyitja meg, előfordulhat, hogy nem kapja meg az Azure-előfizetés kiválasztásának lehetőségét. Ehelyett a logikai alkalmazás egy olyan Azure-előfizetéssel nyílik meg, amelyet nem a használni kívánt. Ez azért fordulhat elő, mert a Logic apps. JSON fájljának megnyitása után a Visual Studio gyorsítótárazza az első kiválasztott előfizetést későbbi használatra. A probléma megoldásához próbálja meg a következő lépések egyikét:

* Nevezze át a logikai alkalmazás. JSON fájlját. Az előfizetés gyorsítótára a fájl nevétől függ.

* Ha el szeretné távolítani a korábban kiválasztott előfizetéseket a megoldásban szereplő *összes* logikai alkalmazáshoz, törölje a rejtett Visual Studio Settings mappát (. vs) a megoldás könyvtárában. Ez a hely tárolja az előfizetési adatokat.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan kezelheti a telepített Logic apps-alkalmazásokat a Visual Studióval. Következő lépésként megismerheti a Logic app-definíciók az üzembe helyezéshez való testreszabását:

> [!div class="nextstepaction"]
> [Logic app-definíciók készítése a JSON-ban](../logic-apps/logic-apps-author-definitions.md)

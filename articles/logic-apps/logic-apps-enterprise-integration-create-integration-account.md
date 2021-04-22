---
title: B2B integrációs fiókok létrehozása vagy kezelése
description: Integrációs fiókok létrehozása, összekapcsolása és kezelése a vállalati integrációhoz a Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: ae5ca6ac822dabd32b6463c3a742901f32b34323
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862254"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Integrációs fiókok létrehozása és felügyelete B2B vállalati integrációkhoz az Azure Logic Appsben

Mielőtt [vállalati integrációs és B2B-megoldásokat](../logic-apps/logic-apps-enterprise-integration-overview.md) hozhatna létre az [Azure Logic Appsszel](../logic-apps/logic-apps-overview.md), létre kell hoznia egy integrációs fiókot, amely egy különálló Azure-erőforrás, és biztonságos, skálázható, felügyelhető tárolót biztosít a logikaialkalmazás-munkafolyamatokkal meghatározott és használt integrációs összetevők számára.

Létrehozhat, tárolhat és kezelhet például B2B-összetevőket, például kereskedelmi partnereket, szerződéseket, térképeket, sémákat, tanúsítványokat és kötegelt konfigurációkat. Emellett ahhoz, hogy a logikai alkalmazás használni tudja ezeket az összetevőket, és használni tudja Logic Apps B2B összekötőket, össze kell kapcsolnunk az integrációs fiókot a logikai alkalmazással. [](#link-account) Az integrációs fióknak és a logikai alkalmazásnak is ugyanazon a helyen *vagy* régióban kell lennie.

> [!IMPORTANT]
> A kiválasztott integrációs fiók típusától függően az integrációs fiók létrehozása költségekkel jár. További információt a díjszabási [és Logic Apps és](logic-apps-pricing.md#integration-accounts) díjszabási Logic Apps [tartalmaz.](https://azure.microsoft.com/pricing/details/logic-apps/)

Ez a témakör bemutatja, hogyan hajthat végre ilyen feladatokat:

* Hozza létre az integrációs fiókot.

  > [!TIP]
  > Integrációs szolgáltatási környezeten belüli integrációs fiók [létrehozásához](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)lásd: [Integrációs fiókok létrehozása ISE-környezetben.](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)

* Az integrációs fiók összekapcsolása egy logikai alkalmazással.

* Módosítsa az integrációs fiók tarifacsomagját.

* Az integrációs fiók leválasztása egy logikai alkalmazásról.

* Helyezze át az integrációs fiókot egy másik Azure-erőforráscsoportba vagy előfizetésbe.

* Törölje az integrációs fiókot.

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Integrációs fiók létrehozása

### <a name="portal"></a>[Portál](#tab/azure-portal)

Ehhez a feladathoz használhatja a Azure Portal az ebben a szakaszban található lépések, [a Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)vagy az Azure [CLI használatával.](/cli/azure/resource#az_resource_create)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. Az Azure főmenüjében válassza az **Erőforrás létrehozása** lehetőséget. A keresőmezőbe írja be szűrőként az "integration account" (integrációs fiók) lehetőséget, majd válassza **az Integration Account (Integrációs fiók) lehetőséget.**

   ![Új integrációs fiók létrehozása](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. Az **Integrációs fiók alatt válassza** a Létrehozás **lehetőséget.**

   ![Az integrációs fiók létrehozásához válassza a "Hozzáadás" lehetőséget](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Adja meg az integrációs fiókra vonatkozó következő információkat:

   ![Adja meg az integrációs fiók adatait](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Név** | Yes | <*integration-account-name*> | Az integrációs fiók neve, amely csak betűket, számokat, kötőjeleket ( ), aláhúzásjeleket ( ), zárójeleket ( , ) és `-` `_` `(` `)` pontokat ( ) `.` tartalmazhat. Ez a példa a "Fabrikam-Integration" et használja. |
   | **Előfizetés** | Yes | <*Azure-előfizetés neve*> | Az Azure-előfizetés neve |
   | **Erőforráscsoport** | Yes | <*Azure-erőforráscsoport neve*> | A kapcsolódó [](../azure-resource-manager/management/overview.md) erőforrások rendszerezéséhez használt Azure-erőforráscsoport neve. Ebben a példában hozzon létre egy új erőforráscsoportot "FabrikamIntegration-RG" néven. |
   | **Tarifacsomag** | Yes | <*díjszabási szint*> | Az integrációs fiók tarifacsomagja, amelyet később módosíthat. Ebben a példában válassza az **Ingyenes lehetőséget.** További információt az alábbi témakörökben talál: <p>- [Logic Apps díjszabási modell](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Logic Apps korlátozások és konfiguráció](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Logic Apps díjszabás](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Hely** | Yes | <*Azure-régió*> | Az a régió, ahol az integrációs fiók metaadatait tárolni kell. Válassza ki ugyanazt a helyet, mint a logikai alkalmazás, vagy hozza létre a logikai alkalmazásokat az integrációs fiókkal azonos helyen. Ebben a példában használja az "USA nyugati régiója" régiót. <p>**Megjegyzés:** Ha integrációs szolgáltatási környezetben [(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)belül hoz létre integrációs fiókot, válassza ki az ISE-t helyként. További információ: [Integrációs fiókok létrehozása ISE-ban.](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment) |
   | **Naplóelemzés** | No | Ki, Be | Ebben a **példában tartsa** meg a Ki beállítást. |
   |||||

1. Ha elkészült, válassza a Létrehozás **lehetőséget.**

   Az üzembe helyezés befejezése után az Azure megnyitja az integrációs fiókját.

   ![Az Azure megnyitja az integrációs fiókot](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Mielőtt a logikai alkalmazás használni tudja az integrációs fiókot, kövesse a következő lépéseket az integrációs fiók és a logikai alkalmazás összekapcsolása érdekében.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Integrációs fiókot az ebben a szakaszban található Azure CLI-parancsokkal hozhat létre.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-an-integration-account"></a>Integrációs fiók létrehozása

Ezekkel a parancsokkal hozhat létre integrációs fiókot.

1. Az [az logic integration-account bővítmény hozzáadásához](/cli/azure/logic/integration-account) használja [az az extension add](/cli/azure/extension#az_extension_add) parancsot:

   ```azurecli
   az extension add –-name logic
   ```

1. Egy erőforráscsoport létrehozásához vagy egy meglévő erőforráscsoport létrehozásához futtassa az [az group create](/cli/azure/group#az_group_create) parancsot:

   ```azurecli
   az group create --name myresourcegroup --location westus
   ```

   Egy erőforráscsoport integrációs fiókjainak listához használja az [az logic integration-account list parancsot:](/cli/azure/logic/integration-account#az_logic_integration_account_list)

   ```azurecli
   az logic integration-account list --resource-group myresourcegroup
   ```

1. Integrációs fiók létrehozásához futtassa az [az logic integration-account create](/cli/azure/logic/integration-account#az_logic_integration_account_create) parancsot:

   ```azurecli
   az logic integration-account create --resource-group myresourcegroup \
       --name integration_account_01 --location westus --sku name=Standard
   ```

   Az integrációs fiók neve csak betűket, számokat, kötőjeleket (-), aláhúzásjeleket (_), zárójeleket ((, )) és pontokat (.) tartalmazhat.

   > [!TIP]
   > Integrációs szolgáltatási környezetben [(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)belüli integrációs fiók létrehozásához válassza ki az ISE helyet. További információ: [Integrációs fiókok létrehozása ISE-ban.](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)

   Egy adott integrációs fiók megtekintéséhez használja [az az logic integration-account show parancsot:](/cli/azure/logic/integration-account#az_logic_integration_account_show)

   ```azurecli
   az logic integration-account show --name integration_account_01 --resource-group myresourcegroup
   ```

   A termékváltozatot vagy a tarifacsomagot az [az logic integration-account update](/cli/azure/logic/integration-account#az_logic_integration_account_update) paranccsal módosíthatja:

   ```azurecli
   az logic integration-account update --sku name=Basic --name integration_account_01 \
       --resource-group myresourcegroup
   ```

   A díjszabással kapcsolatos további információkért tekintse meg az alábbi forrásforrásokat:

   * [Logic Apps díjszabási modell](../logic-apps/logic-apps-pricing.md#integration-accounts)
   * [Logic Apps és konfigurálás](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)
   * [A Logic Apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/)

Integrációs fiók JSON-fájl használatával történő importáláshoz használja [az az logic integration-account import](/cli/azure/logic/integration-account#az_logic_integration_account_import) parancsot:

```azurecli
az logic integration-account import --name integration_account_01 \
    --resource-group myresourcegroup --input-path integration.json
```

Integrációs fiókot az az [logic integration-account delete](/cli/azure/logic/integration-account#az_logic_integration_account_delete) paranccsal törölhet:

```azurecli
az logic integration-account delete --name integration_account_01 --resource-group myresourcegroup
```

Mielőtt a logikai alkalmazás használni tudja az integrációs fiókját, összekapcsolja az integrációs fiókot és a logikai alkalmazást. A következő szakasz a hivatkozásokat ismerteti.

---
<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Kapcsolódás a logikai alkalmazáshoz

Ahhoz, hogy a logikai alkalmazások hozzáférjenek a B2B-összetevőket tartalmazó integrációs fiókhoz, először össze kell kapcsolnunk az integrációs fiókot a logikai alkalmazással. A logikai alkalmazásnak és az integrációs fióknak is ugyanabban a régióban kell lennie. A feladat végrehajtásához használhatja a Azure Portal. Ha a Visual Studio logikai alkalmazás egy Azure-erőforráscsoport-projektben található, a logikai alkalmazást a következő használatával kapcsolhatja össze [egy integrációs Visual Studio.](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account) [](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)

1. A Azure Portal keresse meg és nyissa meg a logikai alkalmazást.

1. A [Azure Portal](https://portal.azure.com)nyisson meg egy meglévő logikai alkalmazást, vagy hozzon létre egy újat.

1. A logikai alkalmazás menüjének Beállítások menüjében **válassza** a **Munkafolyamat-beállítások lehetőséget.** Az **Integrációs fiók alatt** nyissa meg az **Integrációs fiók kiválasztása** listát. Válassza ki az integrációs fiókot a logikai alkalmazáshoz való kapcsolódáshoz.

   ![Az integrációs fiók kiválasztása](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. A hivatkozás befejezéséhez válassza a **Mentés lehetőséget.**

   ![Képernyőkép az integrációs fiók kiválasztásához a Mentés kiválasztásának helyével.](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Az integrációs fiók sikeres összekapcsolása után az Azure megerősítő üzenetet küld.

   ![Az Azure megerősíti a sikeres hivatkozást](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

A logikai alkalmazás mostantól használhatja az integrációs fiókban és a B2B-összekötőkben található összetevőket, például az XML-érvényesítést és az egyszerű fájlkódolást vagy -dekódolást.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>A tarifacsomag módosítása

Az [integrációs fiókok](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) korlátainak növeléséhez magasabb tarifacsomagra frissíthet, [](#upgrade-pricing-tier)ha elérhető. Frissíthet például az Ingyenes szintről az Alapszintű vagy a Standard szintre. Ha [elérhető, alacsonyabb szintre](#downgrade-pricing-tier)is lehet visszalépést. A díjszabásról az alábbi témakörökben található további információ:

* [A Logic Apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Logic Apps díjszabási modell](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Tarifacsomag frissítése

A módosításhoz használhatja a következőt: Azure Portal Azure CLI.

#### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. A fő Azure-keresőmezőbe írja be szűrőként az "integrációs fiókok" lehetőséget, majd válassza az **Integrációs fiókok lehetőséget.**

   ![Integrációs fiók megkeresása](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Az Azure megjeleníti az Összes integrációs fiókot az Azure-előfizetésében.

1. Az **Integrációs fiókok alatt** válassza ki az áthelyezni kívánt integrációs fiókot. Az integrációs fiók menüjében válassza az Áttekintés **lehetőséget.**

   ![Az integrációs fiók menüjében válassza az "Áttekintés" lehetőséget](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Az Áttekintés panelen válassza a Tarifacsomag **frissítése** lehetőséget, amely felsorolja az elérhető magasabb szinteket. Amikor kiválaszt egy szintet, a módosítás azonnal életbe lép.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Ha még nem tette meg, telepítse [az Azure CLI előfeltételeit.](/cli/azure/get-started-with-azure-cli)

1. A Azure Portal nyissa meg a [Azure Cloud Shell](../cloud-shell/overview.md) környezetet.

   ![Az Azure Cloud Shell megnyitása](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. A parancssorba írja be az [ **az resource** parancsot,](/cli/azure/resource#az_resource_update)és állítsa a kívánt `skuName` magasabb szintre.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Ha például alapszintű csomagja van, beállíthatja a `skuName` `Standard` következőt:

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

---

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Visszalépés tarifacsomagra

A módosítás hoz használja az [Azure CLI-t.](/cli/azure/get-started-with-azure-cli)

1. Ha még nem tette meg, telepítse [az Azure CLI előfeltételeit.](/cli/azure/get-started-with-azure-cli)

1. A Azure Portal nyissa meg a [Azure Cloud Shell](../cloud-shell/overview.md) környezetet.

   ![Az Azure Cloud Shell megnyitása](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. A parancssorba írja be az [ **az resource** parancsot,](/cli/azure/resource#az_resource_update) és állítsa a kívánt `skuName` alsó szintre.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Ha például a Standard szint van beállítva, beállíthatja a `skuName` `Basic` következőt:

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Leválasztás a logikai alkalmazásról

Ha a logikai alkalmazást egy másik integrációs fiókkal szeretné összekapcsolni, vagy már nem szeretne integrációs fiókot használni a logikai alkalmazással, törölje a hivatkozást az Azure Erőforrás-kezelő.

1. Nyissa meg a böngészőablakot, és nyissa meg [a Azure Erőforrás-kezelő ( ) gombra. https://resources.azure.com) ](https://resources.azure.com) Jelentkezzen be ugyanazokkal az Azure-fiók hitelesítő adataival.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. A keresőmezőbe írja be a logikai alkalmazás nevét, hogy megtalálja és kiválasztja a logikai alkalmazást.

   ![Logikai alkalmazás megkerese és kiválasztása](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Az Explorer címsorában válassza az **Olvasás/Írás lehetőséget.**

   ![Az "Olvasás/Írás" mód bekapcsolása](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Az Adatok **lapon** válassza a Szerkesztés **lehetőséget.**

   ![Az "Adatok" lapon válassza a "Szerkesztés" lehetőséget](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. A szerkesztőben keresse meg az objektumot, és törölje azt a `integrationAccount` tulajdonságot, amelynek formátuma a következő:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Például:

   ![Az "integrationAccount" objektum megkeresása](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. A módosítások **mentéséhez** az Adatok lapon válassza a **Put** (Elszúrás) lehetőséget.

   ![A módosítások mentéshez válassza a "Put" (Put) lehetőséget.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. A Azure Portal keresse meg és válassza ki a logikai alkalmazást. Az alkalmazás **Munkafolyamat-beállításai alatt ellenőrizze,** hogy az **Integrációs fiók** tulajdonság üresen jelenik-e meg.

   ![Ellenőrizze, hogy az integrációs fiók nincs-e összekapcsolva](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Integrációs fiók áthelyezése

Az integrációs fiókot áthelyezheti egy másik Azure-erőforráscsoportba vagy Azure-előfizetésbe. Az erőforrások áthelyezésekor az Azure új erőforrás-eszközökhöz hoz létre, ezért győződjön meg arról, hogy ehelyett az új sablonokat használja, és frissítse az áthelyezett erőforrásokhoz társított szkripteket vagy eszközöket. Ha módosítani szeretné az előfizetést, meg kell adnia egy meglévő vagy új erőforráscsoportot is.

Ehhez a feladathoz használhatja a Azure Portal az ebben a szakaszban található lépéseket vagy az [Azure CLI-t.](/cli/azure/resource#az_resource_move)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. A fő Azure-keresőmezőbe írja be szűrőként az "integrációs fiókok" lehetőséget, majd válassza az **Integrációs fiókok lehetőséget.**

   ![Integrációs fiók megkeresása](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Az Azure megjeleníti az Összes integrációs fiókot az Azure-előfizetésében.

1. Az **Integrációs fiókok alatt** válassza ki az áthelyezni kívánt integrációs fiókot. Az integrációs fiók menüjében válassza az Áttekintés **lehetőséget.**

   ![Az integrációs fiók menüjében válassza az "Áttekintés" lehetőséget](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Az Erőforráscsoport **vagy** az **Előfizetés neve mellett válassza** a módosítás **lehetőséget.**

   ![Erőforráscsoport vagy előfizetés módosítása](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Válassza ki az áthelyezni kívánt kapcsolódó erőforrásokat.

1. A kiválasztott beállítások alapján az alábbi lépésekkel módosíthatja az erőforráscsoportot vagy az előfizetést:

   * Erőforráscsoport: Az **Erőforráscsoport listából** válassza ki a célként kiválasztott erőforráscsoportot. Másik erőforráscsoport létrehozásához válassza az **Új erőforráscsoport létrehozása lehetőséget.**

   * Előfizetés: Az Előfizetés **listából** válassza ki a cél-előfizetést. Az **Erőforráscsoport listából** válassza ki a célként kiválasztott erőforráscsoportot. Másik erőforráscsoport létrehozásához válassza az **Új erőforráscsoport létrehozása lehetőséget.**

1. Ha tudomásul veszi, hogy az áthelyezett erőforrásokhoz társított parancsfájlok vagy eszközök nem fognak működni, amíg nem frissíti őket az új erőforrás-eszközökhöz, jelölje be a megerősítési jelölőnégyzetet, majd kattintson az **OK gombra.**

1. Miután végzett, győződjön meg arról, hogy minden szkriptet az áthelyezett erőforrások új erőforrás-ID-ekkel frissít.  

## <a name="delete-integration-account"></a>Integrációs fiók törlése

Ehhez a feladathoz használhatja a Azure Portal az ebben a szakaszban található [Azure CLI-](/cli/azure/resource#az_resource_delete)vagy [Azure PowerShell.](/powershell/module/az.logicapp/remove-azintegrationaccount)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. A fő Azure-keresőmezőbe írja be szűrőként az "integrációs fiókok" lehetőséget, majd válassza az **Integrációs fiókok lehetőséget.**

   ![Integrációs fiók megkeresása](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Az Azure megjeleníti az Azure-előfizetések összes integrációs fiókjait.

1. Az **Integrációs fiókok alatt** válassza ki a törölni kívánt integrációs fiókot. Az integrációs fiók menüjében válassza az Áttekintés **lehetőséget.**

   ![Az integrációs fiók menüjében válassza az "Áttekintés" lehetőséget](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Az Áttekintés panelen válassza a **Törlés lehetőséget.**

   ![Az "Áttekintés" panelen válassza a "Törlés" lehetőséget](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Az integrációs fiók törlésének megerősítéséhez válassza az **Igen lehetőséget.**

   ![A törlés megerősítéséhez válassza az "Igen" lehetőséget.](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Következő lépések

* [Kereskedelmi partnerek létrehozása az integrációs fiókban](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Partnerek közötti szerződések létrehozása az integrációs fiókban](../logic-apps/logic-apps-enterprise-integration-agreements.md)

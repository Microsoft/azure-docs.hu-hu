---
title: Erőforrások üzembe helyezése Azure Portal
description: A Azure Portal és az Azure Erőforrás-kezelés használatával az erőforrásokat üzembe helyezheti az előfizetésében található erőforráscsoporthoz.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: d8467bb4e51fc4e6ba89a84f1260a8d2743758d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028675"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>Erőforrások üzembe helyezése ARM-sablonokkal és Azure Portal

Ismerje meg, hogyan helyezhet üzembe az Azure-erőforrásokat a [Azure Portal](https://portal.azure.com) és a [Azure Resource Manager-sablonok (ARM-sablonok)](overview.md) használatával. Az erőforrások kezelésével kapcsolatos további információkért lásd: [Az Azure-erőforrások kezelése a Azure Portal használatával](../management/manage-resources-portal.md).

Az Azure-erőforrások a Azure Portal használatával történő üzembe helyezése általában két lépést foglal magában:

- Hozzon létre egy erőforráscsoportot.
- Erőforrások üzembe helyezése az erőforráscsoporthoz.

Emellett létrehozhat egy testreszabott ARM-sablont is az Azure-erőforrások üzembe helyezéséhez.

Ez a cikk mindkét módszert mutatja be.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

1. Új erőforráscsoport létrehozásához válassza az **erőforráscsoportok** lehetőséget a [Azure Portal](https://portal.azure.com).

   ![Erőforráscsoportok kiválasztása](./media/deploy-portal/select-resource-groups.png)

1. Az erőforráscsoportok területen válassza a **Hozzáadás** lehetőséget.

   ![Erőforráscsoport hozzáadása](./media/deploy-portal/add-resource-group.png)

1. Válassza ki vagy adja meg a következő tulajdonságértékek értékét:

    - **Előfizetés**: válasszon ki egy Azure-előfizetést.
    - **Erőforráscsoport**: adja meg az erőforráscsoport nevét.
    - **Régió**: válasszon egy Azure-helyet. Ez a hely, ahol az erőforráscsoport az erőforrásokkal kapcsolatos metaadatokat tárolja. Megfelelőségi okokból érdemes megadnia a metaadatok tárolási helyét. Általánosságban elmondható, hogy olyan helyet kell megadnia, ahol az erőforrások többsége lesz. Ha ugyanazt a helyet használja, egyszerűbbé teheti a sablon használatát.

   ![Csoportok értékeinek beállítása](./media/deploy-portal/set-group-properties.png)

1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. Tekintse át az értékeket, majd válassza a **Létrehozás** lehetőséget.
1. Válassza a **frissítés** lehetőséget, mielőtt megtekinti az új erőforráscsoportot a listában.

## <a name="deploy-resources-to-a-resource-group"></a>Erőforrások üzembe helyezése erőforráscsoporthoz

Az erőforráscsoport létrehozása után erőforrásokat telepíthet a csoportba a piactéren. A piactér előre definiált megoldásokat kínál a gyakori forgatókönyvekhez.

1. A központi telepítés elindításához válassza az **erőforrás létrehozása** lehetőséget a [Azure Portal](https://portal.azure.com).

   ![Új erőforrás](./media/deploy-portal/new-resources.png)

1. Keresse meg a telepíteni kívánt erőforrás típusát. Az erőforrások kategóriákba vannak rendezve. Ha nem látja a központilag telepíteni kívánt megoldást, megkeresheti a piactéren. Az alábbi képernyőfelvételen látható, hogy az Ubuntu-kiszolgáló van kiválasztva.

   ![Erőforrás típusának kiválasztása](./media/deploy-portal/select-resource-type.png)

1. A kiválasztott erőforrás típusától függően az üzembe helyezés előtt meg kell adni a megfelelő tulajdonságok gyűjteményét. Minden típushoz ki kell választania egy cél erőforráscsoportot. Az alábbi képen megtudhatja, hogyan hozhat létre Linux rendszerű virtuális gépet, és hogyan telepítheti azt a létrehozott erőforráscsoporthoz.

   ![Erőforráscsoport létrehozása](./media/deploy-portal/select-existing-group.png)

   Az erőforrások telepítésekor dönthet úgy, hogy létrehoz egy erőforráscsoportot. Válassza az **új létrehozása** elemet, és adja meg az erőforráscsoport nevét.

1. A telepítés megkezdődik. Az üzembe helyezés több percet is igénybe vehet. Egyes erőforrások hosszabb időt vehetnek igénybe, mint a többi erőforrás. Ha a telepítés befejeződött, egy értesítés jelenik meg. Válassza **az erőforrás** megnyitása a megnyitáshoz lehetőséget.

   ![Értesítés megtekintése](./media/deploy-portal/view-notification.png)

1. Az erőforrások üzembe helyezése után további erőforrásokat adhat hozzá az erőforráscsoporthoz a **Hozzáadás gombra** kattintva.

   ![Erőforrás hozzáadása](./media/deploy-portal/add-resource.png)

Bár nem látja, a portál egy ARM-sablont használt a kiválasztott erőforrások üzembe helyezéséhez. A sablont az üzembe helyezési előzmények közül lehet megtalálni. További információ: [sablon exportálása az üzembe helyezés után](export-template-portal.md#export-template-after-deployment).

## <a name="deploy-resources-from-custom-template"></a>Erőforrások üzembe helyezése egyéni sablonból

Ha egy központi telepítést kíván végrehajtani, de nem használja a piactér egyik sablonját sem, létrehozhat egy testreszabott sablont, amely meghatározza a megoldás infrastruktúráját. A sablonok létrehozásával kapcsolatos további tudnivalókért lásd [az ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető témakört.

> [!NOTE]
> A portál felülete nem támogatja a [titkos kulcsra hivatkozó Key Vault](key-vault-parameter.md). Ehelyett a [PowerShell](deploy-powershell.md) vagy az [Azure CLI](deploy-cli.md) használatával helyezheti üzembe a SABLONT helyileg vagy külső URI-ból.

1. Testreszabott sablon a portálon keresztüli üzembe helyezéséhez válassza az **erőforrás létrehozása**, majd a **sablon** keresése lehetőséget. majd válassza a **template Deployment** lehetőséget.

   ![Sablon központi telepítésének keresése](./media/deploy-portal/search-template.png)

1. Válassza a **Létrehozás** lehetőséget.
1. A sablonok létrehozásához több lehetőség is megjelenik:

    - **Saját sablon készítése a szerkesztőben**: saját sablon létrehozása a portál sablon szerkesztőjében.
    - **Gyakori sablonok**: válasszon a gyakori megoldások közül.
    - **GitHub-gyorsindítási sablon betöltése**: válasszon a [Gyorsindítás sablonok](https://azure.microsoft.com/resources/templates/)közül.

   ![Beállítások megtekintése](./media/deploy-portal/see-options.png)

    Ez az oktatóanyag a gyors üzembe helyezési sablon betöltéséhez nyújt útmutatást.

1. A GitHub gyors üzembe helyezése **sablon betöltéséhez** írja be vagy válassza a **101-Storage-Account-Create** lehetőséget.

    Erre két lehetősége van:

    - **Sablon kiválasztása**: a sablon üzembe helyezése.
    - **Sablon szerkesztése**: az üzembe helyezés előtt szerkessze a rövid útmutató sablont.

1. Válassza a **Sablon szerkesztése** lehetőséget a portál sablon szerkesztőjének megismeréséhez. A sablon betöltődik a szerkesztőbe. Figyelje meg, hogy két paraméter van: `storageAccountType` és `location` .

   ![Sablon létrehozása](./media/deploy-portal/show-json.png)

1. Végezze el a sablon kisebb módosítását. Frissítse például a változót a következőre `storageAccountName` :

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Kattintson a **Mentés** gombra. Ekkor megjelenik a portál sablonjának telepítési felülete. Figyelje meg a sablonban megadott két paramétert.
1. Adja meg vagy válassza ki a tulajdonságértékek értékét:

    - **Előfizetés**: válasszon ki egy Azure-előfizetést.
    - **Erőforráscsoport**: válassza az **új létrehozása** lehetőséget, és adjon meg egy nevet.
    - **Hely**: válasszon ki egy Azure-helyet.
    - **Storage-fiók típusa**: használja az alapértelmezett értéket.
    - **Hely**: használja az alapértelmezett értéket.
    - **Elfogadom a fenti feltételeket és kikötéseket**: (kiválasztás)

1. Válassza a **Beszerzés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- A naplók megtekintéséhez lásd: [műveletek naplózása a Resource Managerrel](../management/view-activity-logs.md).
- Az üzembe helyezési hibák elhárításáról lásd: [telepítési műveletek megtekintése](deployment-history.md).
- Ha a sablont egy központi telepítésből vagy erőforráscsoporthoz szeretné exportálni, tekintse meg az [ARM-sablonok exportálása](export-template-portal.md)című témakört.
- A szolgáltatás több régióban való biztonságos kihelyezéséhez lásd: [Azure Deployment Manager](deployment-manager-overview.md).

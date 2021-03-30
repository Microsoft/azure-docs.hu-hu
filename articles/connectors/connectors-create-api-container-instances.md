---
title: '& felügyeletének központi telepítése Azure Container Instances'
description: Feladatok és munkafolyamatok automatizálása, amelyek tároló-telepítéseket hoznak létre és kezelhetnek Azure Container Instances a Azure Logic Apps használatával
services: logic-apps, container-instances
ms.service: logic-apps
ms.suite: integration
author: dlepow
ms.author: danlep
ms.manager: gwallace
ms.reviewer: estfan, macolso
ms.topic: article
tags: connectors
ms.date: 01/14/2020
ms.openlocfilehash: ecb1049d64197f2a60438df7eedfb244907f7327
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "76046291"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>Azure Container Instances üzembe helyezése és kezelése Azure Logic Apps használatával

A Azure Logic Apps és az Azure Container instance Connector használatával olyan automatizált feladatokat és munkafolyamatokat állíthat be, amelyek [tároló csoportokat](../container-instances/container-instances-container-groups.md)telepítenek és kezelhetnek. A Container instance Connector a következő műveleteket támogatja:

* Tároló-csoport létrehozása vagy törlése
* Egy tároló csoport tulajdonságainak beolvasása
* A tároló-csoportok listájának beolvasása
* Tároló-példány naplóinak beolvasása

Ezeket a műveleteket a logikai alkalmazásokban olyan feladatokhoz használhatja, mint például a tároló munkaterhelésének futtatása egy Logic Apps triggerre válaszul. Más műveletek is használhatók a tároló-példányok műveleteinek kimenetére. 

Ez az összekötő csak műveleteket biztosít, így a logikai alkalmazás elindításához használjon egy külön eseményindítót, például egy **ismétlődési** eseményindítót, amely rendszeres időközönként futtatja a tároló számítási feladatait. Vagy előfordulhat, hogy a tároló csoport központi telepítését egy olyan esemény után kell elindítania, mint például egy Outlook e-mail érkezésekor. 

Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Alapvető ismeretek a [logikai alkalmazások létrehozásával](../logic-apps/quickstart-create-first-logic-app-workflow.md) és [a tároló-példányok létrehozásával és kezelésével](../container-instances/container-instances-quickstart.md) kapcsolatban

* Az a logikai alkalmazás, ahová el szeretné érni a tároló példányait. Ha műveletet szeretne használni, indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **ismétlődési** eseményindítóval.

## <a name="add-a-container-instance-action"></a>Container instance művelet hozzáadása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Válasszon egy elérési utat: 

   * Az utolsó lépésben, amelyben hozzá szeretne adni egy műveletet, válassza az **új lépés** lehetőséget. 

     -vagy-

   * A művelethez hozzáadni kívánt lépések között vigye az egérmutatót a lépések közötti nyíl fölé. 
   Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.

1. A keresőmezőbe írja be szűrőként a "Container instance" kifejezést. A műveletek listán válassza ki az Azure Container instance Connector kívánt műveletét.

1. Adja meg a kapcsolat nevét. 

1. Adja meg a kiválasztott művelethez szükséges adatokat, és folytassa a logikai alkalmazás munkafolyamatának összeállítását.

  Válassza például a **tároló csoport létrehozása** elemet, és adja meg egy tároló csoport tulajdonságait, valamint a csoport egy vagy több tároló példányát, ahogy az alábbi képen látható (részleges részletek):

  ![Tárolócsoport létrehozása](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos technikai részletekért, amelyeket az összekötő OpenAPI (korábban hencegés) leírása ismertet, tekintse át az összekötő [hivatkozási oldalát](/connectors/aci/) vagy a [YAML-referenciát](../container-instances/container-instances-reference-yaml.md).

## <a name="next-steps"></a>Következő lépések

* Az e-mail vagy a Twitter-szöveg hangulatának elemzéséhez tekintse meg a Azure Container Instances tárolót futtató [minta logikai alkalmazást](https://github.com/Azure-Samples/aci-logicapps-integration) .

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
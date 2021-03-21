---
title: Azure API Management-példány létrehozása a Visual Studio Code használatával | Microsoft Docs
description: Visual Studio Code Azure API Management-példány létrehozásához.
ms.service: api-management
ms.workload: integration
author: vladvino
ms.author: apimpm
ms.topic: quickstart
ms.date: 09/14/2020
ms.openlocfilehash: 3105b6f34d7ece81e8145fdd9e89568e66360ddb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649512"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-visual-studio-code"></a>Rövid útmutató: új Azure API Management Service-példány létrehozása a Visual Studio Code használatával

Az Azure API Management (APIM) segít közzétenni az API-kat a külső, a partner- és a belső fejlesztők számára, hogy ki tudják használni az adataikban és szolgáltatásaikban rejlő lehetőségeket. Az API Management a fejlesztők bevonásán, az üzleti elemzéseken, a biztonságon és a védelmen keresztül biztosítja az alapvető kompetenciákat az API-program sikeressé tételéhez. Az APIM segítségével modern API-átjárókat hozhat létre meglévő háttérrendszerekhez, és az üzemeltetés helyétől függetlenül kezelheti azokat. További információt az [Áttekintés](api-management-key-concepts.md) témakörben talál.

Ez a rövid útmutató ismerteti, hogyan hozhat létre új API Management példányt a Visual Studio Code-hoz készült *Azure API Management bővítménnyel* . A bővítmény használatával a API Management-példányon is elvégezheti a gyakori felügyeleti műveleteket.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Továbbá győződjön meg arról, hogy telepítette a következőt:

- [Visual Studio Code](https://code.visualstudio.com/)

- [Azure API Management-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Indítsa el a Visual Studio Code alkalmazást, és nyissa meg az Azure-bővítményt. (Ha nem látja az Azure ikont a tevékenység sávján, győződjön meg arról, hogy az *azure API Management* -bővítmény engedélyezve van.)

Válassza a bejelentkezés az Azure-ba **...** lehetőséget a böngészőablak elindításához, és jelentkezzen be a Microsoft-fiókba.

![Jelentkezzen be az Azure-ba a VS Code API Management-bővítményével](./media/vscode-create-service-instance/vscode-apim-login.png)

## <a name="create-an-api-management-service"></a>API Management szolgáltatás létrehozása

Miután bejelentkezett a Microsoft-fiókba, az *Azure: API Management* Explorer ablaktábláján megjelennek az Azure-előfizetések.

Kattintson a jobb gombbal a használni kívánt előfizetésre, és válassza az **API Management létrehozása az Azure-ban** lehetőséget.

![API Management varázsló létrehozása a VS Code-ban](./media/vscode-create-service-instance/vscode-apim-create.png)

A megnyíló ablaktáblán adja meg az új API Management példány nevét. Globálisan egyedinek kell lennie az Azure-on belül, és 1-50 alfanumerikus karakterből és/vagy kötőjelből kell állnia, és betűvel kell kezdődnie, és egy alfanumerikus karaktert kell végződnie.

A rendszer új API Management példányt (és szülő erőforráscsoportot) hoz létre a megadott névvel. A példány alapértelmezés szerint az *USA nyugati* régiójában jön létre, a *felhasználási* SKU-val.

> [!TIP]
> Ha az *Azure API Management bővítmény beállításaiban* engedélyezi a **speciális létrehozást** , megadhat egy [API Management SKU](https://azure.microsoft.com/pricing/details/api-management/)-t, [Azure-régiót](https://status.azure.com/en-us/status)és egy [erőforráscsoportot](../azure-resource-manager/management/overview.md) is a API Management példány üzembe helyezéséhez.
>
> Míg a *fogyasztási* SKU kevesebb mint egy percet vesz igénybe, a többi sku általában 30-40 percet vesz igénybe.

Most már készen áll az első API-k importálására és közzétételére. Ezt megteheti, és a Visual Studio Code-ban a bővítményen belül közös API Management műveleteket is végrehajthat. További információkért tekintse meg [az oktatóanyagot](visual-studio-code-tutorial.md) .

![Az újonnan létrehozott API Management példány a VS Code API Management bővítmény panelen](./media/vscode-create-service-instance/vscode-apim-instance.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, távolítsa el a API Management példányt a [API Management szolgáltatás](get-started-create-service-instance.md#clean-up-resources) és az erőforráscsoport törléséhez kattintson a jobb gombbal, és válassza a **Megnyitás a portálon** lehetőséget.

Másik lehetőségként kiválaszthatja a **delete API Management** elemet a API Management példány törléséhez (ez a művelet nem törli az erőforráscsoportot).

![API Management példány törlése a VS Code-ból](./media/vscode-create-service-instance/vscode-apim-delete.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [API-k importálása és kezelése az API Management bővítmény használatával](visual-studio-code-tutorial.md)

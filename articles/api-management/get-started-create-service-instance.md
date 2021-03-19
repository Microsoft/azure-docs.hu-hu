---
title: Rövid útmutató – Azure API Management-példány létrehozása
description: Hozzon létre egy új Azure API Management Service-példányt a Azure Portal használatával.
author: vladvino
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/08/2020
ms.author: apimpm
ms.openlocfilehash: 66d0874536b358b720b43780d6ecce879ac866ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90708206"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-portal"></a>Rövid útmutató: új Azure API Management Service-példány létrehozása a Azure Portal használatával

Az Azure API Management (APIM) segít közzétenni az API-kat a külső, a partner- és a belső fejlesztők számára, hogy ki tudják használni az adataikban és szolgáltatásaikban rejlő lehetőségeket. Az API Management a fejlesztők bevonásán, az üzleti elemzéseken, a biztonságon és a védelmen keresztül biztosítja az alapvető kompetenciákat az API-program sikeressé tételéhez. A APIM lehetővé teszi modern API Gateway-átjárók létrehozását és felügyeletét a bárhol üzemeltetett meglévő háttér-szolgáltatásokhoz. További információ: [Áttekintés](api-management-key-concepts.md).

Ez a gyors útmutató az új API Management-példányok Azure Portalon történő létrehozásának lépéseit mutatja be.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

:::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-created.png" alt-text="API Management-példány":::

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-new-service"></a>Új szolgáltatás létrehozása

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet. Kiválaszthatja az **erőforrás létrehozása** lehetőséget is az Azure **kezdőlapján** . 
   
   :::image type="content" source="media/get-started-create-service-instance/00-CreateResource-01.png" alt-text="Válassza az erőforrás létrehozása lehetőséget.":::

   
1. Az **új** lapon válassza az **integráció**  >  **API Management** elemet.

   :::image type="content" source="media/get-started-create-service-instance/00-CreateResource-02.png" alt-text="Új Azure API Management-példány":::
   
1. A **API Management szolgáltatás** lapon adja meg a beállításokat.

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-create-new.png" alt-text="Új példány":::
   
   | Beállítás                 | Leírás   |                                                                     
   |-------------------------|-----------------------------------------------|
   | **Név**                | A API Management szolgáltatás egyedi neve. A név később nem módosítható. A szolgáltatás neve a szolgáltatásra és a hozzá tartozó Azure-erőforrásra is vonatkozik. <br/> A szolgáltatás neve egy alapértelmezett tartománynév létrehozásához használható: *\<name\> . Azure-API.net.* Ha egyéni tartománynevet kíván használni, tekintse meg az [Egyéni tartománynév konfigurálása](configure-custom-domain.md) témakört. |
   | **Előfizetés**          | Az új szolgáltatáspéldány létrehozásához használt előfizetés.   |
   | **Erőforráscsoport**      |  Válasszon ki egy új vagy egy meglévő erőforráscsoportot. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. |
   | **Hely**          | Válasszon ki egy földrajzi régiót az elérhető API Management szolgáltatási helyekről. | 
   | **Szervezet neve**   | A szervezet neve. Ezt a nevet sok helyen használja a rendszer, többek között a fejlesztői portál címében és az értesítő e-mailek küldőjeként. |                                                         
   | **Rendszergazda e-mail-címe** | Az e-mail-cím, amelyre a **API Management** összes értesítése el lesz küldve.   |  
   | **Tarifacsomag**        | Válassza ki a **fejlesztői** szintet a szolgáltatás kiértékeléséhez. Ez a rétegek nem használhatók éles környezetben. További információt az API Management szintjeinek skálázásáról a [frissítés és skálázás](upgrade-and-scale.md) oldalon talál. |

3. Válassza a **Létrehozás** lehetőséget.

    > [!TIP]
    > Ezen a szinten 30 és 40 percet is igénybe vehet egy API Management szolgáltatás létrehozása és aktiválása. A **Rögzítés az irányítópulton** lehetőség használatával könnyebben megtalálhatja az újonnan létrehozott szolgáltatást.

[!INCLUDE [api-management-navigate-to-instance](../../includes/api-management-navigate-to-instance.md)]

Tekintse át a szolgáltatás tulajdonságait az **Áttekintés** oldalon.

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-created.png" alt-text="API Management-példány":::

Ha a API Management Service-példány online állapotban van, készen áll a használatára. Kezdje az Oktatóanyaggal az [első API importálásához és közzétételéhez](import-and-publish.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, eltávolíthatja az erőforráscsoportot és az összes kapcsolódó erőforrást az alábbi lépések végrehajtásával:

1. A Azure Portal keresse meg és válassza ki az **erőforráscsoportok** elemet. A **kezdőlapon** kiválaszthatja az **erőforráscsoportok** lehetőséget is. 

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-01.png" alt-text="Erőforráscsoport navigációja":::

1. Az **erőforráscsoportok** lapon válassza ki az erőforráscsoportot.

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-02.png" alt-text="Erőforráscsoport kiválasztása":::

1. Az erőforráscsoport lapon válassza az **erőforráscsoport törlése** elemet. 
   
1. Írja be az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-03.png" alt-text="Erőforráscsoport törlése":::

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az első API importálása és közzététele](import-and-publish.md)

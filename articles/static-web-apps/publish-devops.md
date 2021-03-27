---
title: 'Oktatóanyag: Azure statikus Web Apps közzététele az Azure DevOps'
description: Ismerje meg, hogyan teheti közzé az Azure statikus Web Apps az Azure DevOps használatával.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: 701f999427d743c18f5dbcadb00cf303f97a8f53
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627289"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>Oktatóanyag: Azure statikus Web Apps közzététele az Azure DevOps

Ez a cikk bemutatja, hogyan helyezhető üzembe az [Azure statikus Web Apps](./overview.md) az [Azure DevOps](https://dev.azure.com/)használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

- Azure statikus Web Apps-hely beállítása
- Azure DevOps-folyamat létrehozása statikus webalkalmazások létrehozásához és közzétételéhez

## <a name="prerequisites"></a>Előfeltételek

- **Aktív Azure-fiók:** Ha még nem rendelkezik ilyennel, [ingyenes fiókot hozhat létre](https://azure.microsoft.com/free/).
- **Azure DevOps-projekt:** Ha még nem rendelkezik ilyennel, [létrehozhat egy projektet ingyenesen](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/).
- **Azure DevOps-folyamat:** Ha segítségre van szüksége a kezdéshez, tekintse [meg az első folyamat létrehozása](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline?view=azure-devops&preserve-view=true)című témakört.

## <a name="create-a-static-web-app-in-an-azure-devops-repository"></a>Statikus Webalkalmazás létrehozása Azure DevOps-tárházban

  > [!NOTE]
  > Ha rendelkezik egy meglévő alkalmazással a tárházban, ugorjon a következő szakaszra.

1. Navigáljon az Azure DevOps-tárházhoz.

1. Válassza az **Importálás** lehetőséget egy minta alkalmazás importálásának megkezdéséhez.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps-tárház":::

1. A **klónozási URL-cím** mezőbe írja be a értéket `https://github.com/staticwebdev/vanilla-api.git` .

1. Válassza az **Importálás** lehetőséget.

## <a name="create-a-static-web-app"></a>Statikus webalkalmazás létrehozása

1. Navigáljon a [Azure Portal](https://portal.azure.com).

1. Válassza az **Erőforrás létrehozása** lehetőséget.

1. **Statikus Web Apps** keresése.

1. Válassza a **statikus Web Apps (előzetes verzió)** lehetőséget.

1. Válassza a **Létrehozás** lehetőséget.

1. Az _üzembe helyezés részletei_ területen válassza a **más** lehetőséget. Ez lehetővé teszi a kód használatát az Azure DevOps-tárházban.

    > [!NOTE]
    > A _más_ kiválasztására szolgáló funkciók jelenleg nem érhetők el, és az összes Azure-előfizetésben még nem érhető el.

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Központi telepítés részletei – egyéb":::

1. Miután az üzembe helyezés sikeres volt, navigáljon az új statikus Web Apps erőforráshoz.

1. Válassza a **központi telepítési jogkivonat kezelése** lehetőséget.

1. Másolja a **telepítési jogkivonatot** , és illessze be egy szövegszerkesztőbe egy másik képernyőn való használatra.

    > [!NOTE]
    > Ez az érték most nincs elkülönítve, mert több értéket fog másolni és beilleszteni a következő lépésekbe.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Üzembe helyezési jogkivonat":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Folyamat létrehozása az Azure DevOps

1. Navigáljon a korábban létrehozott Azure DevOps adattárhoz.

1. Válassza a **Build beállítása** lehetőséget.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Buildelési folyamat":::

1. A *folyamat konfigurálása* képernyőn válassza a **kezdő folyamat** lehetőséget.

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="Folyamat konfigurálása":::

1. Másolja és illessze be a következő YAML a folyamatba.

    ```yaml
    trigger:
      - main
    
    pool:
      vmImage: ubuntu-latest
    
    steps:
      - task: AzureStaticWebApp@0
        inputs:
          app_location: "/" 
          api_location: "api"
          output_location: ""
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > Ha nem használja a minta alkalmazást, a, a és a értékének meg `app_location` `api_location` `output_location` kell változtatnia az alkalmazás értékeinek megfelelően.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    Az `azure_static_web_apps_api_token` érték önállóan felügyelt, és manuálisan van konfigurálva.

1. Válasszon **változókat**.

1. Hozzon létre egy új változót.

1. Nevezze el a változót **deployment_token** (a munkafolyamatban szereplő névvel egyező).

1. Másolja a korábban beillesztett központi telepítési tokent egy szövegszerkesztőbe.

1. Illessze be a központi telepítési tokent az _érték_ mezőbe.

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Változó token":::

1. Válassza **az érték megtartása titkot**.

1. Válassza az **OK** lehetőséget.

1. Válassza a **Mentés** lehetőséget a folyamat YAML való visszatéréshez.

1. A _Mentés és Futtatás párbeszédpanel_ megnyitásához kattintson a **Mentés és Futtatás** gombra.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Folyamat":::

1. Válassza a **Mentés és Futtatás** lehetőséget a folyamat futtatásához.

1. Ha az üzembe helyezés sikeres volt, navigáljon az Azure statikus Web Apps **áttekintő oldalra** , amely a telepítési konfigurációra mutató hivatkozásokat tartalmaz. Figyelje meg, hogy a _forrás_ hivatkozás most az Azure DevOps adattárának ágra és helyére mutat.

1. Válassza ki az **URL-címet** az újonnan üzembe helyezett webhely megtekintéséhez.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Központi telepítés helye":::

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure statikus Web Apps konfigurálása](./configuration.md)

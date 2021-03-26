---
title: 'Oktatóanyag: Azure statikus Web Apps közzététele az Azure DevOps'
description: Ismerje meg, hogyan teheti közzé az Azure statikus Web Apps az Azure DevOps használatával.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: af359734ff5bfe90dedbb7f8389aecdc6e056654
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543558"
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

1. Használjon meglévő tárházat, vagy _importáljon egy tárházat_ az alább látható módon.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps-tárház":::

1. Hozzon létre egy új fájlt az előtér-webalkalmazáshoz.

1. Másolja és illessze be az alábbi HTML-kódot az új fájlba:

    ```html
    <!DOCTYPE html>
    <html lang="en">
  
    <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <link rel="stylesheet" href="styles.css">
      <title>Hello World!</title>
    </head>
  
    <body>
      <main>
        <h1>Hello World!</h1>
      </main>
    </body>
  
    </html>
    ```

1. Mentse a fájlt.

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

1. Ha az üzembe helyezés sikeres volt, válassza a **központi telepítési jogkivonat kezelése** elemet.

1. Másolja a **telepítési jogkivonatot** , és illessze be egy szövegszerkesztőbe egy másik képernyőn való használatra.

    > [!NOTE]
    > Ez az érték most nincs elkülönítve, mert több értéket fog másolni és beilleszteni a következő lépésekbe.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Üzembe helyezési jogkivonat":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Folyamat létrehozása az Azure DevOps

1. Navigáljon a korábban létrehozott Azure DevOps-projekthez.

2. Hozzon létre egy új **létrehozási** folyamatot, és válassza a **Build beállítása** lehetőséget.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Buildelési folyamat":::

3. Másolja és illessze be a következő YAML a folyamatba.

    > [!NOTE]
    > A _app_location_,_api_location_ és _output_location_ megadott értékeket módosítani kell az alkalmazáshoz.  

    ```yaml
    trigger:
      - main
    
    pool:
      vmImage: ubuntu-latest
    
    steps:
      - task: AzureStaticWebApp@0
        inputs:
          app_location: frontend 
          api_location: api
          output_location: build
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    Konfigurálja az Azure-beli statikus webalkalmazások bemeneteit az alkalmazás mappa szerkezete alapján.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    Az `azure_static_web_apps_api_token` érték önállóan felügyelt, és manuálisan van konfigurálva.

4. Válasszon **változókat**.

5. Hozzon létre egy új változót.

6. Nevezze el a változót **deployment_token** (a munkafolyamatban szereplő névvel egyező).

7. Másolja a korábban beillesztett központi telepítési tokent egy szövegszerkesztőbe.

8. Illessze be a központi telepítési tokent az _érték_ mezőbe.

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Változó token":::

9. Válassza az **OK** lehetőséget.

10. Válassza a **Mentés lehetőséget, és futtassa** a folyamatot.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Folyamat":::

11. Ha az üzembe helyezés sikeres volt, navigáljon az Azure statikus Web Apps **áttekintő oldalra** , amely a telepítési konfigurációra mutató hivatkozásokat tartalmaz.

12. Válassza ki az **URL-címet** az újonnan üzembe helyezett webhely megtekintéséhez. Figyelje meg, hogy a _forrás_ hivatkozás most az Azure DevOps adattárának ágra és helyére mutat.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Központi telepítés helye":::

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure statikus Web Apps konfigurálása](./configuration.md)

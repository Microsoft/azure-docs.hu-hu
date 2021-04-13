---
title: 'Oktatóanyag: Azure Static Web Apps közzététele az Azure DevOps használatával'
description: Megtudhatja, hogyan használhatja az Azure DevOpsot a Azure Static Web Apps.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: f82ae60ab7f57b20a727deefa6e286d698ee5b6c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365756"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>Oktatóanyag: Azure Static Web Apps közzététele az Azure DevOps használatával

Ez a cikk bemutatja, hogyan helyezhet üzembe [Azure Static Web Apps](./overview.md) az [Azure DevOps használatával.](https://dev.azure.com/)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

- Hely Azure Static Web Apps beállítása
- Azure DevOps-folyamat létrehozása statikus webalkalmazás létrehozásához és közzétételéhez

## <a name="prerequisites"></a>Előfeltételek

- **Aktív Azure-fiók:** Ha még nincs fiókja, ingyenesen [létrehozhat egy fiókot.](https://azure.microsoft.com/free/)
- **Azure DevOps Project:** Ha még nem használhatja, ingyenesen létrehozhat [egy projektet.](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/)
- **Azure DevOps-folyamat:** Ha segítségre van szüksége az első lépésekhez, lásd: [Az első folyamat létrehozása.](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline?view=azure-devops&preserve-view=true)

## <a name="create-a-static-web-app-in-an-azure-devops-repository"></a>Statikus webalkalmazás létrehozása Azure DevOps-adattárban

  > [!NOTE]
  > Ha már van alkalmazása az adattárban, a következő szakaszra ugorhat.

1. Lépjen az Azure DevOps-adattárra.

1. Válassza **az Importálás** lehetőséget egy mintaalkalmazás importálásának megkezdéséhez.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps-repo":::

1. A **Clone URL (Klón URL-címe)** mezőben adja meg a következőt: `https://github.com/staticwebdev/vanilla-api.git` .

1. Válassza az **Importálás** lehetőséget.

## <a name="create-a-static-web-app"></a>Statikus webalkalmazás létrehozása

1. Lépjen a [Azure Portal.](https://portal.azure.com)

1. Válassza az **Erőforrás létrehozása** lehetőséget.

1. Keressen rá a **Static Web Apps.**

1. Válassza **Static Web Apps (előzetes verzió) lehetőséget.**

1. Válassza a **Létrehozás** lehetőséget.

1. Az Üzembe _helyezés részletei alatt_ válassza az Egyéb **lehetőséget.** Ez lehetővé teszi a kód használatát az Azure DevOps-adattárban.

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Üzembe helyezés részletei – egyéb":::

1. Ha az üzembe helyezés sikeres volt, lépjen az új Static Web Apps erőforráshoz.

1. Válassza az **Üzembe helyezési jogkivonat kezelése lehetőséget.**

1. Másolja ki **az üzembe helyezési jogkivonatot,** és illessze be egy szövegszerkesztőbe egy másik képernyőn való használathoz.

    > [!NOTE]
    > Ezt az értéket most félretesszük, mert a következő lépésekben további értékeket fog másolni és beilleszteni.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Üzembehelyi jogkivonat":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>A folyamatfeladat létrehozása az Azure DevOpsban

1. Keresse meg a korábban létrehozott Azure DevOps-adattárat.

1. Válassza a **Build beállítása lehetőséget.**

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Buildelési folyamat":::

1. A Folyamat *konfigurálása képernyőn* válassza a Kezdő **folyamat lehetőséget.**

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="Folyamat konfigurálása":::

1. Másolja és illessze be az alábbi YAML-t a folyamatba.

    ```yaml
    trigger:
      - main
    
    pool:
      vmImage: ubuntu-latest
    
    steps:
      - checkout: self
        submodules: true

      - task: AzureStaticWebApp@0
        inputs:
          app_location: "/" 
          api_location: "api"
          output_location: ""
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > Ha nem használja a mintaalkalmazást, a , és értékét módosítania kell, hogy megegyeznek az `app_location` `api_location` alkalmazásban használt `output_location` értékekkel.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    Az `azure_static_web_apps_api_token` érték önálló, és manuálisan van konfigurálva.

1. Válassza **a Változók lehetőséget.**

1. Hozzon létre egy új változót.

1. A **változónak** deployment_token (a munkafolyamatban egyező névvel).

1. Másolja ki a korábban egy szövegszerkesztőbe bemásolt üzembe helyezési jogkivonatot.

1. Illessze be az üzembe helyezési jogkivonatot az _Érték mezőbe._

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Változó jogkivonat":::

1. Válassza **a Keep this value secret (Ez az érték maradjon titkos) lehetőséget.**

1. Válassza az **OK** lehetőséget.

1. Válassza **a Mentés lehetőséget** a folyamat YAML-hez való visszatéréshez.

1. Válassza **a Mentés és futtatás** lehetőséget a Mentés és _futtatás párbeszédpanel megnyitásához._

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Folyamat":::

1. A **folyamat futtatásához válassza** a Mentés és futtatás lehetőséget.

1. Ha az üzembe helyezés sikeres volt, lépjen a Azure Static Web Apps **áttekintési lapra,** amely az üzembe helyezési konfigurációra mutató hivatkozásokat tartalmaz. Figyelje _meg, hogy a Forrás_ hivatkozás most az Azure DevOps-adattár ágra és helyre mutat.

1. Az **újonnan üzembe helyezett webhelyet** az URL-cím kiválasztásával láthatja.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Üzembe helyezés helye":::

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure Static Web Apps](./configuration.md)

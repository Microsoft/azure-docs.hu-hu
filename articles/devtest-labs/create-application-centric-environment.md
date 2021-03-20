---
title: Alkalmazás-központú környezet létrehozása – Azure
description: Ez a cikk bemutatja, hogyan hozhat létre egy alkalmazás-központú környezetet Cloud Shell kolóniával és Microsoft Azureával.
ms.topic: how-to
ms.date: 11/26/2020
ms.openlocfilehash: 88244f268d5ed038e9bb7082d3d5cc1179e5ec4e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94918153"
---
# <a name="create-an-application-centric-environment"></a>Alkalmazás-központú környezet létrehozása

A [cloudshellben-kolónia](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) egy SaaS-platform, amely az infrastruktúra-automatizálást az összetett, alkalmazás-központú környezetekben, például az Azure-ban és a Kubernetes-ben biztosítja. A Cloudshellben Colony kiegészíti a Azure DevTest Labs, hogy a fejlesztői csapatok az egész értékre kiterjedő összetett alkalmazásokat telepítsenek, egészen az éles környezetig.

Ez a cikk bemutatja, hogyan hozhat létre egy alkalmazás-központú környezetet a Cloudshellben Colony és a Microsoft Azure használatával.

## <a name="set-up-the-environment-with-cloudshell-colony-and-microsoft-azure"></a>A környezet beállítása a Cloudshellben Colony és a Microsoft Azure

1. Regisztráljon a [kolónia](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview)ingyenes próbaidőszakára.

    :::image type="content" source="./media/create-application-centric-environment/free-trial.png" alt-text="Regisztráljon az ingyenes próbaverzióra":::    
1. Az Azure-fiók összekapcsolása ([itt tekintheti meg a lépéseket](https://colonysupport.quali.com/hc/articles/360008222234)).

    :::image type="content" source="./media/create-application-centric-environment/welcome.png" alt-text="Üdvözli a Colony!":::     
1. Meghívhatja a felhasználókat a tárhelyre.
1. Hozza létre az első tervezetét egy YAML-fájl használatával ([itt tekintheti meg a lépéseket](https://colonysupport.quali.com/hc/articles/360001680807-Steps-to-Developing-a-Blueprint)).
    1. Csatolja a Blueprint-tárházat a GitHubon vagy a BitBucket a Colony-ben.
    1. Használja a Colony Sample tervrajzot az alapjaként, és módosítsa a megfelelő módon.

        :::image type="content" source="./media/create-application-centric-environment/performance-stress-tests.png" alt-text="Stressztesztek":::    
    1. Tegye közzé a tervét mások számára a használatra.
1. Indítsa el az alkalmazási környezetet egy Sandboxban a Colony használatával.

    :::image type="content" source="./media/create-application-centric-environment/blueprints.png" alt-text="Alkalmazás-környezet elindítása egy homokozóban a Colony használatával":::    

> [!NOTE]
> A tervet a CI/CD-munkafolyamatok részeként is integrálhatja az Azure DevOps ([itt tekintheti meg a lépéseket](https://colonysupport.quali.com/hc/articles/360008464234)).

:::image type="content" source="./media/create-application-centric-environment/devops-pipeline.png" alt-text="Kapcsolódás az Azure DevOps-folyamathoz":::    

## <a name="next-steps"></a>Következő lépések

[A Colony bemutatójának kérése](https://info.quali.com/cloudshell-colony-demo-request)

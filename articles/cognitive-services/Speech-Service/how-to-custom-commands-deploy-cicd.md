---
title: Folyamatos üzembe helyezés az Azure DevOps (előzetes verzió)
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan állíthatja be a folyamatos üzembe helyezést az egyéni parancsok alkalmazásaihoz. Létrehozza a parancsfájlokat a folyamatos üzembe helyezési munkafolyamatok támogatásához.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 686e342eb68e2e5c97bdbae9cd11bdff5819b5d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98927604"
---
# <a name="continuous-deployment-with-azure-devops"></a>Folyamatos üzembe helyezés az Azure DevOps használatával

Ebből a cikkből megtudhatja, hogyan állíthatja be a folyamatos üzembe helyezést az egyéni parancsok alkalmazásaihoz. A CI/CD-munkafolyamatot támogató parancsfájlok elérhetők Önnek.

## <a name="prerequisite"></a>Előfeltétel
> [!div class = "checklist"]
> * Egyéni parancsokat fejlesztőknek (DEV)
> * Egyéni parancsok alkalmazása éles környezethez (PROD)
> * Regisztráció az [Azure-folyamatokra](/azure/devops/pipelines/get-started/pipelines-sign-up)

## <a name="exportimportpublish"></a>Exportálás/Importálás/közzététel

A szkriptek a [Cognitive Services Voice Assistant – Custom parancsokban](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands)vannak tárolva. A bash-címtárban lévő parancsfájlok klónozása a tárházba. Győződjön meg arról, hogy ugyanazt az útvonalat tartja karban.

### <a name="set-up-a-pipeline"></a>Folyamat beállítása 

1. Nyissa meg az **Azure DevOps-folyamatokat** , és kattintson az új folyamat elemre.
1. A **kapcsolat** szakaszban válassza ki a tárház helyét, ahol a parancsfájlok találhatók
1. A **Select (kijelölés** ) szakaszban válassza ki a tárházat
1. A **Konfigurálás** szakaszban válassza az "indító folyamat" lehetőséget.
1. Ezután egy YAML-fájlt tartalmazó szerkesztőt kap, a "Steps" szakaszt cserélje le ezzel a szkripttel.

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Export source app'
      inputs:
        targetType: filePath
        filePath: ./bash/export.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(SourceAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Import to target app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish target app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```
    
1. Vegye figyelembe, hogy ezek a parancsfájlok feltételezik, hogy a régiót használja `westus2` , ha ez nem így van, akkor a feladatok argumentumait ennek megfelelően frissíti.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép, amely kiemeli a régió értékét az argumentumokban.](media/custom-commands/cicd-new-pipeline-yaml.png)

1. A "Mentés és Futtatás" gombon nyissa meg a legördülő menüt, és kattintson a "Mentés" gombra.

### <a name="hook-up-the-pipeline-with-your-application"></a>A folyamat összekapcsolása az alkalmazással

1. Navigáljon a folyamat főoldalára.
1. A jobb felső sarokban legördülő menüben válassza a **folyamat szerkesztése** lehetőséget. Egy YAML-szerkesztőbe kerül. 
1. A "Futtatás" gomb melletti jobb felső sarokban válassza a **változók** lehetőséget. Kattintson az **új változó** elemre.
1. Adja hozzá ezeket a változókat:
    
    | Változó | Leírás |
    | ------- | --------------- | ----------- |
    | SourceAppId | A fejlesztői alkalmazás azonosítója |
    | TargetAppId | A GYÁRTÁSIRENDELÉS-alkalmazás azonosítója |
    | SubscriptionKey | Mindkét alkalmazáshoz használt előfizetési kulcs |
    | Kultúra | Az alkalmazások kultúrája (pl. en-US) |

    > [!div class="mx-imgBorder"]
    > ![Tevékenység-adattartalom küldése](media/custom-commands/cicd-edit-pipeline-variables.png)

1. Kattintson a Futtatás elemre, majd kattintson a "Job" (Futtatás) elemre. 

    A következőt tartalmazó feladatok listája jelenik meg: "export Source app", "Importálás a célalkalmazás számára" & "betanítás és közzététel célalkalmazás"

## <a name="deploy-from-source-code"></a>Üzembe helyezés forráskódból

Ha meg szeretné őrizni az alkalmazás definícióját egy adattárban, a forráskódból történő üzembe helyezéshez biztosítunk parancsfájlokat. Mivel a szkriptek a bashben vannak, a Windows használata esetén telepítenie kell a [Linux alrendszert](/windows/wsl/install-win10).

A szkriptek a [Cognitive Services Voice Assistant – Custom parancsokban](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands)vannak tárolva. A bash-címtárban lévő parancsfájlok klónozása a tárházba. Győződjön meg arról, hogy ugyanazt az útvonalat tartja karban.

### <a name="prepare-your-repository"></a>A tárház előkészítése

1. Hozzon létre egy könyvtárat az alkalmazáshoz, a példánkban hozzon létre egy "alkalmazások" nevű mappát.
1. Frissítse az alábbi bash-szkript argumentumait, és futtassa a parancsot. Importálja az alkalmazás párbeszédpanel-modelljét a következő fájlba myapp.js
    ```BASH
    bash/export.sh -r <region> -s <subscriptionkey> -c en-us -a <appid> -f apps/myapp.json
    ```
    | Argumentumok | Leírás |
    | ------- | --------------- | ----------- |
    | régió | az alkalmazás régiója, pl. westus2. |
    | subscriptionkey | a beszédfelismerési erőforrás előfizetési kulcsa. |
    | AppID | az exportálni kívánt egyéni parancsok alkalmazás-azonosítója. |

1. Küldje el ezeket a módosításokat a tárházba.

### <a name="set-up-a-pipeline"></a>Folyamat beállítása 

1. Nyissa meg az **Azure DevOps-folyamatokat** , és kattintson az új folyamat elemre.
1. A **kapcsolat** szakaszban válassza ki a tárház helyét, ahol a parancsfájlok találhatók
1. A **Select (kijelölés** ) szakaszban válassza ki a tárházat
1. A **Konfigurálás** szakaszban válassza az "indító folyamat" lehetőséget.
1. Ezután egy YAML-fájlt tartalmazó szerkesztőt kap, a "Steps" szakaszt cserélje le ezzel a szkripttel.

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Import app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ../apps/myapp.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```

    > [!NOTE]
    > Ezek a parancsfájlok feltételezik, hogy a westus2 régiót használja, ha nem ebben az esetben frissíti a feladatok argumentumait ennek megfelelően

1. A "Mentés és Futtatás" gombon nyissa meg a legördülő menüt, és kattintson a "Mentés" gombra.

### <a name="hook-up-the-pipeline-with-your-target-applications"></a>A folyamat összekapcsolása a megcélzott alkalmazásokkal

1. Navigáljon a folyamat főoldalára.
1. A jobb felső sarokban legördülő menüben válassza a **folyamat szerkesztése** lehetőséget. Egy YAML-szerkesztőbe kerül. 
1. A "Futtatás" gomb melletti jobb felső sarokban válassza a **változók** lehetőséget. Kattintson az **új változó** elemre.
1. Adja hozzá ezeket a változókat:

    | Változó | Leírás |
    | ------- | --------------- | ----------- |
    | TargetAppId | A GYÁRTÁSIRENDELÉS-alkalmazás azonosítója |
    | SubscriptionKey | Mindkét alkalmazáshoz használt előfizetési kulcs |
    | Kultúra | Az alkalmazások kultúrája (pl. en-US) |

1. Kattintson a Futtatás elemre, majd kattintson a "Job" (Futtatás) elemre.
    A következőt tartalmazó feladatok listája jelenik meg: "alkalmazás importálása" & "betanítás és alkalmazás közzététele"

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Minták megtekintése a GitHubon](https://aka.ms/speech/cc-samples)

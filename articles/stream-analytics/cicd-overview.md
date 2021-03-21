---
title: A Azure Stream Analytics folyamatos integrációja és üzembe helyezése
description: Ez a cikk áttekintést nyújt a Azure Stream Analytics folyamatos integrációs és üzembe helyezési (CI/CD) folyamatáról.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 940813f12d542715db47781731144a75e854a98e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019567"
---
# <a name="continuous-integration-and-deployment-cicd-for-azure-stream-analytics"></a>Folyamatos integráció és üzembe helyezés (CI/CD) a Azure Stream Analyticshoz

A Azure Stream Analytics-feladatot folyamatosan üzembe helyezheti a verziókövetés integrációjának használatával. A verziókövetés integrációja lehetővé teszi egy olyan munkafolyamat használatát, amelyben a kód frissítése elindítja az erőforrás-telepítést az Azure-ban. Ez a cikk a folyamatos integrációs és üzembe helyezési (CI/CD) folyamat létrehozásának alapvető lépéseit ismerteti.

Ha még nem ismeri a Azure Stream Analyticst, ismerkedjen meg a [Azure stream Analytics](stream-analytics-quick-create-portal.md)rövid útmutatóval.

## <a name="create-a-cicd-pipeline"></a>CI/CD-folyamat létrehozása

Kövesse a jelen útmutató lépéseit egy CI/CD-folyamat létrehozásához a Stream Analyticshoz.

1. Azure Stream Analytics-lekérdezés fejlesztése.

   A [Visual Studio Code](./quick-create-visual-studio-code.md) vagy a [Visual Studio](stream-analytics-quick-create-vs.md) Azure stream Analytics Tools használatával [helyileg fejlesztheti és tesztelheti a lekérdezéseket](develop-locally.md). [Meglévő feladatot is exportálhat](visual-studio-code-explore-jobs.md#export-a-job-to-a-local-project) egy helyi projektbe.

2. Véglegesítse Azure Stream Analytics projektjeit a verziókövetés rendszerébe, például egy git-tárházra.

3. A [Azure stream Analytics CI/CD Tools](cicd-tools.md) használatával hozza létre a projekteket, és hozzon létre Azure Resource Management-sablonokat az üzembe helyezéshez.

4. [Automatikus parancsfájl-tesztek](cicd-tools.md#automated-test) futtatása a minőségi regresszióhoz.

5. [Telepítse a feladatot az](cicd-tools.md#deploy-to-azure) Azure-ba automatikusan.

## <a name="auto-build-test-and-deploy"></a>Automatikus létrehozás, tesztelés és üzembe helyezés

A parancssorból és [Azure stream Analytics CI/CD-eszközökről](cicd-tools.md) is használhatja az automatikus kiépítést, tesztelést és üzembe helyezést. A CI/CD-folyamat is beállítható az [Azure-folyamatokban](set-up-cicd-pipeline.md). Az Azure-folyamatok a fejlettebb képességek, például a folyamatok kezelése, a vizualizációk és a triggerek engedélyezésére szolgálnak.

## <a name="next-steps"></a>Következő lépések

* [Egy Azure Stream Analytics-feladatokhoz tartozó buildek, tesztek és központi telepítések automatizálása CI/CD-eszközök használatával](cicd-tools.md)
* [CI/CD-folyamat beállítása Stream Analytics feladatokhoz az Azure-folyamatok használatával](set-up-cicd-pipeline.md)
---
title: Azure Stream Analytics feladatok másolása vagy biztonsági mentése
description: Ez a cikk a Azure Stream Analytics feladatok másolását és biztonsági mentését ismerteti.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/11/2019
ms.openlocfilehash: 864c5ffc9ed88f438a5be5a1fcb55d0b78df5e07
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016611"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Azure Stream Analytics feladatok másolása vagy biztonsági mentése

A Visual Studio Code vagy a Visual Studio használatával másolhatja vagy biztonsági másolatot készíthet az üzembe helyezett Azure Stream Analytics-feladatokról. A feladatok másik régióba való másolása nem másolja az utolsó kimeneti időpontot. Ezért a másolt feladatok indításakor nem használható a [**legutóbbi leállítási**](./start-job.md#start-options) beállítás.

## <a name="before-you-begin"></a>Előkészületek
* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

* Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

* Telepítse [Azure stream Analytics bővítményt a Visual Studio Code](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension) vagy a [Azure stream Analytics Tools for Visual Studio alkalmazáshoz](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension).  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Kattintson az **Azure** ikonra a Visual Studio Code tevékenység sávján, majd bontsa ki **stream Analytics** csomópontot. A feladatoknak az előfizetések alatt kell megjelenniük.

   ![Stream Analytics Explorer megnyitása](./media/vscode-explore-jobs/open-explorer.png)

2. Ha a feladatot egy helyi projektbe szeretné exportálni, keresse meg az exportálni kívánt feladatot a Visual Studio Code **stream Analytics Explorerben** . Ezután válasszon ki egy mappát a projekt számára.

    ![ASA-feladatok keresése a Visual Studio Code-ban](./media/vscode-explore-jobs/export-job.png)

    A projektet a rendszer a kiválasztott mappába exportálja, és hozzáadja az aktuális munkaterülethez.

3. Ha más néven szeretné közzétenni a feladatot egy másik régióban vagy biztonsági másolatban, válassza a **kiválasztás az előfizetések közül** lehetőséget a lekérdezés-szerkesztőben (. asaql) való közzétételhez, \* és kövesse az utasításokat.

    ![Közzététel az Azure-ban a Visual Studio Code-ban](./media/quick-create-visual-studio-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Kövesse az [üzembe helyezett Azure stream Analyticsi feladat exportálása projekt utasításait](./stream-analytics-vs-tools.md#export-jobs-to-a-project).

2. Nyissa meg a \* . asaql fájlt a Lekérdezéstervezőben, válassza az **elküldés az Azure** -ba lehetőséget a parancsfájl-szerkesztőben, és kövesse az utasításokat a feladat egy másik régióba vagy biztonsági másolatba való közzétételéhez egy új néven.

## <a name="next-steps"></a>Következő lépések

* [Rövid útmutató: Stream Analytics-feladatok létrehozása a Visual Studio Code használatával](quick-create-visual-studio-code.md)
* [Rövid útmutató: Stream Analytics-feladatok létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)
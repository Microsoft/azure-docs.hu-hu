---
title: 'Oktatóanyag: az Azure szinapszis Analytics Cognitive Services használatának előfeltételei'
description: Ismerje meg, hogyan konfigurálhatja a Cognitive Services Azure Szinapszisban való használatához szükséges előfeltételeket.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 3ab861caca0ef6f58c2c1bc722412774deb725ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936681"
---
# <a name="tutorial-prerequisites-for-using-cognitive-services-in-azure-synapse-analytics"></a>Oktatóanyag: az Azure szinapszis Analytics Cognitive Services használatának előfeltételei

Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be az Azure-Cognitive Services az Azure szinapszis Analytics szolgáltatásban való biztonságos használatára vonatkozó előfeltételeket.

Ez az oktatóanyag az alábbiakkal foglalkozik:
> [!div class="checklist"]
> - Hozzon létre egy Cognitive Services erőforrást, például Text Analytics vagy anomália detektort.
> - A hitelesítési kulcs tárolásával Cognitive Servicesheti az erőforrásokat a Azure Key Vaultban titkos kódokként, és konfigurálhatja az Azure szinapszis Analytics-munkaterülethez való hozzáférést.
> - Hozzon létre egy Azure Key Vault társított szolgáltatást az Azure szinapszis Analytics-munkaterületen.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Az [Azure szinapszis Analytics-munkaterület](../get-started-create-workspace.md) egy Azure Data Lake Storage Gen2 Storage-fiókkal, amely alapértelmezett tárolóként van konfigurálva. A Azure Data Lake Storage Gen2 fájlrendszer *tárolási blob-Adatközreműködőinek* kell lennie.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services-erőforrás létrehozása

Az [Azure Cognitive Services](../../cognitive-services/index.yml) számos különböző típusú szolgáltatást tartalmaz. Az Text Analytics és a anomália detektor két példát mutat be az Azure szinapszis oktatóanyagokban.

A Azure Portal [text Analytics](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) erőforrást is létrehozhat:

![Képernyőkép a portálon Text Analytics megjelenítése a létrehozás gombbal.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

A Azure Portalban létrehozhat egy [rendellenesség-Kiderítő](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) erőforrást:

![Képernyőkép, amely a Create (létrehozás) gombra kattintva megjeleníti a rendellenességeket jelző detektort a portálon.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

## <a name="create-a-key-vault-and-configure-secrets-and-access"></a>Key Vault létrehozása és a titkok és hozzáférés konfigurálása

1. Hozzon létre egy [Key vaultot](https://ms.portal.azure.com/#create/Microsoft.KeyVault) a Azure Portal.
2. Lépjen **Key Vault**  >  **hozzáférési házirendek** elemre, és adja meg az [Azure szinapszis-munkaterület MSI](../security/synapse-workspace-managed-identity.md) -engedélyeit a titkok olvasásához Azure Key Vaultból.

   > [!NOTE]
   > Győződjön meg arról, hogy a házirend módosításai mentve lettek. Ez a lépés könnyen kihagyható.

   ![A hozzáférési szabályzatok hozzáadására szolgáló beállításokat bemutató képernyőkép.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Lépjen a Cognitive Services-erőforráshoz. Például lépjen az **anomália detektor**  >  **kulcsaihoz és a végponthoz**. Ezután másolja a két kulcs egyikét a vágólapra.

4. Új titok létrehozásához lépjen a **Key Vault**  >  **Secret (titok** ) gombra. Adja meg a titok nevét, majd illessze be a kulcsot az előző lépésben az **érték** mezőbe. Végül válassza a **Létrehozás** lehetőséget.

   ![Képernyőkép, amely a titkos kód létrehozására szolgáló beállításokat jeleníti meg.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

   > [!IMPORTANT]
   > Ügyeljen rá, hogy jegyezze fel vagy jegyezze fel ezt a titkos nevet. Később akkor fogja használni, amikor az Azure szinapszis studióból csatlakozik Cognitive Serviceshoz.

## <a name="create-an-azure-key-vault-linked-service-in-azure-synapse"></a>Azure Key Vault társított szolgáltatás létrehozása az Azure Szinapszisban

1. Nyissa meg a munkaterületet az Azure szinapszis Studióban. 
2. Válassza a   >  **társított szolgáltatások** kezelése lehetőséget. Hozzon létre egy **Azure Key Vault** társított szolgáltatást úgy, hogy az imént létrehozott kulcstartóra mutat. 
3. A kapcsolatok **tesztelése** gomb kiválasztásával ellenőrizze a kapcsolatokat. Ha a kapcsolódás zöld színű, válassza a **Létrehozás** lehetőséget, majd válassza az **összes közzététele** lehetőséget a módosítás mentéséhez.

![Képernyőkép, amely a Azure Key Vault új társított szolgáltatásként jeleníti meg.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

Most már készen áll arra, hogy folytassa az Azure Cognitive Services-élmény az Azure szinapszis Studióban való használatának egyik oktatóanyagával.

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: érzelmek elemzése az Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Oktatóanyag: anomáliák észlelése az Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Oktatóanyag: gépi tanulási modellek pontozása az Azure szinapszis DEDIKÁLT SQL-készletekben](tutorial-sql-pool-model-scoring-wizard.md).
- [Az Azure Synapse Analytics gépi tanulási képességei](what-is-machine-learning.md)
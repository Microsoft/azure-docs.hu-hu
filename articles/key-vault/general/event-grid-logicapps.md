---
title: E-Key Vault a titkos tok állapotának megváltozásáról
description: Útmutató a titkos Logic Apps változásaira való Key Vault való válaszadáshoz
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: e6ec06e3df87e0ad0e50efca24439435d655f6a1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752101"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>A Logic Apps e-mail fogadása a Key Vault titkos kulcsának állapotváltozásáról

Ebből az útmutatóból megtudhatja, hogyan válaszolhat a Azure Key Vault [](../../event-grid/index.yml) keresztül fogadott Azure Event Grid a [Azure Logic Apps.](../../logic-apps/index.yml) A végére egy Azure-beli logikai alkalmazás lesz beállítva, amely minden alkalommal értesítő e-mailt küld, amikor létrejön egy titkos Azure Key Vault.

A Azure Key Vault/Azure Event Grid áttekintését lásd: [Monitoring Key Vault with Azure Event Grid](event-grid-overview.md).

## <a name="prerequisites"></a>Előfeltételek

- E-mail-fiók bármely e-mail-szolgáltatótól, amelyet a Azure Logic Apps támogat (például Az Office 365 Outlook). Ez az e-mail-fiók küldi majd az eseményértesítéseket. A támogatott Logic Apps-összekötők teljes listáját az [összekötők áttekintésében](/connectors) találja.
- Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy kulcstartó az Azure-előfizetésben. Gyorsan létrehozhat egy új kulcstartót a Titkos kulcs beállítása és lekérése a kulcstartóból az [Azure CLI használatával Azure Key Vault lépéseit követve.](../secrets/quick-create-cli.md)
- Regisztrált Event Grid erőforrás-szolgáltatóként, lásd: [Erőforrás-szolgáltatók regisztrációi](../../azure-resource-manager/management/resource-providers-and-types.md)

## <a name="create-a-logic-app-via-event-grid"></a>Logikai alkalmazás létrehozása Event Grid

Először hozzon létre egy logikai alkalmazást az Event Grid-kezelővel, és iratkozzon fel Azure Key Vault SecretNewVersionCreated" eseményekre.

Új előfizetés Azure Event Grid kövesse az alábbi lépéseket:

1. A Azure Portal a kulcstartóhoz, válassza az **Események > első lépések** lehetőséget, majd kattintson a **Logic Apps**

    
    ![Key Vault – események lap](../media/eventgrid-logicapps-kvsubs.png)

1. A **Logic Apps Designerben ellenőrizze** a kapcsolatot, majd kattintson a Folytatás **gombra.** 
 
    ![Logic App Designer – kapcsolat](../media/eventgrid-logicappdesigner1.png)

1. Az **Erőforrás-esemény bekövetkezése esetén képernyőn** tegye a következőket:
    - Hagyja **meg az Előfizetés** és az Erőforrás neve **beállítást** az alapértelmezett értéken.
    - Válassza **a Microsoft.KeyVault.vaults** **erőforrástípust.**
    - Az 1 eseménytípushoz válassza a **Microsoft.KeyVault.SecretNewVersionCreated** **elemet.**

    ![Logic App Designer – eseménykezelő](../media/eventgrid-logicappdesigner2.png)

1. Válassza **az + Új lépés** Ez megnyit egy ablakot a Művelet kiválasztása lehetőséghez.
1. Keresse meg az **E-mail** elemet. Az e-mail-szolgáltató alapján keresse meg és válassza ki a megfelelő összekötőt. Ez az oktatóanyag az **Office 365 Outlookot használja.** Az egyéb e-mail-szolgáltatókra vonatkozó lépések is hasonlók.
1. Válassza az **E-mail küldése (V2)** műveletet.

   ![Logic App Designer – e-mail küldése](../media/eventgrid-logicappdesigner3.png)

1. Készítse el az e-mail-sablont:
    - **A következőre:** Adja meg az e-mail-címet az értesítő e-mailek fogadásához. Ehhez az oktatóanyaghoz olyan e-mail-fiókot használjon, amelyhez hozzáfér majd a tesztelés során.
    - **Tárgy** és **törzs**: Írja be az e-mail szövegét. A választó eszközről választott JSON-tulajdonságokkal az esemény adataira alapuló dinamikus tartalmat illeszthet be. Az esemény adatait a használatával lehet `@{triggerBody()?['Data']}` lekérni.

    Az e-mail-sablon az alábbi példához hasonló lehet.

    ![Logikaialkalmazás-tervező – e-mail törzse](../media/eventgrid-logicappdesigner4.png)

8. Kattintson a **Mentés másként gombra.**
9. Adja meg az **új** logikai alkalmazás nevét, majd kattintson a **Létrehozás gombra.**
    
    ![Logikaialkalmazás-tervező – létrehozás](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Tesztelés és ellenőrzés

1.  Az esemény-előfizetések eseményeinek Azure Portal kulcstartóban válassza **> lehetőséget.**  Új előfizetés létrejöttének ellenőrzése
    
    ![Logikaialkalmazás-tervező – tesztelés és ellenőrzés](../media/eventgrid-logicapps-kvnewsubs.png)

1.  A Kulcstartóban válassza a Titkos **kulcsok,** majd a **+ Generálás/Importálás lehetőséget.** Hozzon létre egy új titkos adatokat tesztelési célokra– nevezze el a kulcsot, és a többi paramétert tartsa meg az alapértelmezett beállításokban.

    ![Key Vault – Titkos adat létrehozása](../media/eventgrid-logicapps-kv-create-secret.png)

1. A Titkos **adat létrehozása képernyőn** adjon meg bármilyen nevet és értéket, majd válassza a Létrehozás **lehetőséget.**

A titkos tok létrehozása után a rendszer e-mailt kap a konfigurált címekről.

## <a name="next-steps"></a>Következő lépések

- Áttekintés: [Monitorozási Key Vault monitorozása Azure Event Grid](event-grid-overview.md)
- How to: [Route key vault notifications to Azure Automation.](event-grid-tutorial.md)
- [Azure Event Grid eseménysémát a Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- További tudnivalók az [Azure Event Grid](../../event-grid/index.yml) szolgáltatásról.
- További tudnivalók az [Azure App Service szolgáltatás Logic Apps funkciójáról](../../logic-apps/index.yml).
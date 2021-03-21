---
title: E-mail-cím, ha a titkos módosítások Key Vault állapota
description: Útmutató a Logic Apps a titkok változására való reagáláshoz Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 9c522d870a25b3df34ab6a0cf1c1e944a6462685
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013989"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>A Key Vault-titkok állapotának változásairól szóló e-mailek fogadása Logic Apps használatával

Ebből az útmutatóból megtudhatja, hogyan válaszolhat a [Azure Event Grid](../../event-grid/index.yml) által a [Azure Logic Apps](../../logic-apps/index.yml)használatával fogadott Azure Key Vault eseményekre. A befejezést követően egy Azure Logic App-alkalmazással kell elküldenie egy értesítő e-mailt minden alkalommal, amikor létrehoznak egy titkos kulcsot Azure Key Vault.

A Azure Key Vault/Azure Event Grid integrációjának áttekintését lásd: [Key Vault Azure Event Grid figyelése](event-grid-overview.md).

## <a name="prerequisites"></a>Előfeltételek

- A Azure Logic Apps által támogatott e-mail-szolgáltatótól származó e-mail-fiók (például Office 365 Outlook). Ez az e-mail-fiók küldi majd az eseményértesítéseket. A támogatott Logic Apps-összekötők teljes listáját az [összekötők áttekintésében](/connectors) találja.
- Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy kulcstartó az Azure-előfizetésében. Az Azure CLI használatával gyorsan létrehozhat egy új kulcstartót az [Azure Key Vault titkos kód beállítása és lekérése](../secrets/quick-create-cli.md)a következő lépésekkel.
- Regisztrált Event Grid erőforrás-szolgáltatóként: erőforrás- [szolgáltatók regisztrációja](../../azure-resource-manager/management/resource-providers-and-types.md)

## <a name="create-a-logic-app-via-event-grid"></a>Logikai alkalmazás létrehozása Event Grid használatával

Először hozzon létre logikai alkalmazást az Event Grid-kezelővel, és fizessen elő Azure Key Vault "SecretNewVersionCreated" eseményekre.

Azure Event Grid előfizetés létrehozásához kövesse az alábbi lépéseket:

1. A Azure Portal nyissa meg a Key vaultot, válassza az **események > első lépések** lehetőséget, majd kattintson a **Logic apps**

    
    ![Key Vault – események lap](../media/eventgrid-logicapps-kvsubs.png)

1. **Logic apps Designerben** ellenőrizze a kapcsolatokat, és kattintson a **Folytatás** gombra. 
 
    ![Logic app Designer – kapcsolatok](../media/eventgrid-logicappdesigner1.png)

1. A **Ha egy erőforrás esemény bekövetkezik** képernyőn, tegye a következőket:
    - Adja meg az **előfizetés** és az **erőforrás nevét** alapértelmezett értékként.
    - Válassza ki a **Microsoft. kulcstartó.** tárolókat az **erőforrás típushoz**.
    - Válassza a **Microsoft. kulcstartó. SecretNewVersionCreated** **elemet a-1 eseménytípus elemnél**.

    ![Logic app Designer – eseménykezelő](../media/eventgrid-logicappdesigner2.png)

1. Válassza az **+ új lépés** lehetőséget, majd megnyílik egy ablak, amely kiválasztja a műveletet.
1. Keresse meg az **E-mail** elemet. Az e-mail-szolgáltató alapján keresse meg és válassza ki a megfelelő összekötőt. Ez az oktatóanyag az **Office 365 Outlookot** használja. Az egyéb e-mail-szolgáltatókra vonatkozó lépések is hasonlók.
1. Válassza az **E-mail küldése (v2)** műveletet.

   ![Logic app Designer – e-mail küldése](../media/eventgrid-logicappdesigner3.png)

1. Hozza létre az e-mail sablonját:
    - Ide **:** Adja meg az e-mail-címet az értesítő e-mailek fogadásához. Ehhez az oktatóanyaghoz olyan e-mail-fiókot használjon, amelyhez hozzáfér majd a tesztelés során.
    - **Tárgy** és **törzs**: Írja be az e-mail szövegét. A választó eszközről választott JSON-tulajdonságokkal az esemény adataira alapuló dinamikus tartalmat illeszthet be. Az esemény adatai a használatával kérhetők le `@{triggerBody()?['Data']}` .

    Az e-mail-sablon az alábbi példához hasonló lehet.

    ![Logic app Designer – e-mail törzs](../media/eventgrid-logicappdesigner4.png)

8. Kattintson **a Mentés másként** elemre.
9. Adja meg az új logikai alkalmazás **nevét** , majd kattintson a **Létrehozás** gombra.
    
    ![Logic app Designer – létrehozás](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Tesztelés és ellenőrzés

1.  Nyissa meg a Key vaultot a Azure Portalon, és válassza az **események > esemény-előfizetések** lehetőséget.  Új előfizetés létrehozásának ellenőrzése
    
    ![Logic app Designer – tesztelés és ellenőrzés](../media/eventgrid-logicapps-kvnewsubs.png)

1.  Nyissa meg a Key vaultot, válassza a **titkok** lehetőséget, majd válassza a **+ Létrehozás/importálás** lehetőséget. Hozzon létre egy új titkot tesztelési célra, nevezze el a kulcsot, és tartsa meg a fennmaradó paramétereket az alapértelmezett beállításokban.

    ![Key Vault – titkos kód létrehozása](../media/eventgrid-logicapps-kv-create-secret.png)

1. A **titkos kulcs létrehozása** képernyőn adjon meg bármilyen nevet, bármilyen értéket, és válassza a **Létrehozás** lehetőséget.

A titkos kulcs létrehozásakor a rendszer egy e-mailt fog kapni a konfigurált címeken.

## <a name="next-steps"></a>Következő lépések

- Áttekintés: [Key Vault figyelése Azure Event Grid](event-grid-overview.md)
- Útmutató: [Key Vault-értesítések átirányítása a Azure Automationba](event-grid-tutorial.md).
- [Azure Key Vault Azure Event Gridi esemény sémája](../../event-grid/event-schema-key-vault.md)
- További tudnivalók az [Azure Event Grid](../../event-grid/index.yml) szolgáltatásról.
- További tudnivalók az [Azure App Service szolgáltatás Logic Apps funkciójáról](../../logic-apps/index.yml).
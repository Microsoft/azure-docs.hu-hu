---
title: App Service terv kezelése – Azure | Microsoft Docs
description: Megtudhatja, hogyan hajthat végre különböző feladatokat egy App Service-csomag kezeléséhez.
keywords: App Service, Azure app Service, skálázás, app Service-csomag, módosítás, létrehozás, felügyelet, felügyelet
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/24/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e8bdc749ee354e75a6043dbd6dac3f93a606f79e
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72898952"
---
# <a name="manage-an-app-service-plan-in-azure"></a>App Service-csomag kezelése az Azure-ban

Egy [Azure app Service csomag](overview-hosting-plans.md) biztosítja azokat az erőforrásokat, amelyeket egy app Service alkalmazásnak futtatnia kell. Ez az útmutató bemutatja, hogyan kezelhető egy App Service-csomag.

## <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

> [!TIP]
> Ha App Service Environment rendelkezik, tekintse meg a [app Service terv létrehozása app Service Environment](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Létrehozhat egy üres App Service-csomagot, vagy létrehozhat egy csomagot az alkalmazások létrehozásának részeként.

1. A [Azure Portal](https://portal.azure.com)válassza az **új** > **webalkalmazás** vagy más típusú app Service alkalmazás lehetőséget.

   ![Hozzon létre egy alkalmazást a Azure Portalban.][createWebApp]

2. Adja meg a **példány részletei** szakaszt a app Service terv konfigurálása előtt. Az olyan beállítások, mint a **közzétételi** és az **operációs rendszerek** , megváltoztathatják a app Service csomag elérhető díjszabási szintjeit. A **régió** határozza meg, hogy a app Service terv hol lett létrehozva.
   
3. A **app Service terv** szakaszban válasszon ki egy meglévő csomagot, vagy hozzon létre egy csomagot az **új létrehozása**lehetőség kiválasztásával.

   ![Hozzon létre egy App Service tervet.][createASP] 

4. A csomag létrehozásakor kiválaszthatja az új csomag díjszabási szintjét. Az **SKU és a size**értéknél válassza a **méret módosítása** lehetőséget az árképzési szintek módosításához. 

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Alkalmazás áthelyezése egy másik App Service tervbe

Az alkalmazások áthelyezhetők egy másik App Service csomagba, ha a forrás-és a megcélzott terv _ugyanabban az erőforráscsoport-és földrajzi régióban_található.

> [!NOTE]
> Az Azure minden új App Service tervet üzembe helyez egy üzembe helyezési egységben, amelyet egy webtárhelynek nevezünk. Az egyes régiók több webtárhelyet is tartalmazhatnak, de az alkalmazás csak az ugyanazon a webtérben létrehozott csomagok között tud mozogni. Az App Service Environment egy elkülönített webtárhely, így az alkalmazások áthelyezhetők a csomagok között ugyanazon a App Service Environment, de nem a különböző App Service környezetekben lévő csomagok között.
>
> A terv létrehozásakor nem adhatja meg a használni kívánt webtárhelyet, de lehetséges, hogy a terv ugyanabban a webtérben jön létre, mint egy meglévő csomag. Röviden, az azonos erőforráscsoport és régió kombinációval létrehozott összes csomag ugyanarra a webtárhelyre van telepítve. Ha például létrehozta az A és A B régióba tartozó csomagot, akkor az A és B régióba később létrehozott összes csomag ugyanarra a webtárhelyre lesz telepítve. Vegye figyelembe, hogy a csomagok létrehozása után nem helyezhetők át a webtárhelyek, így nem helyezhet át egy tervet "ugyanazon a webtárhelyre", mert egy másik erőforráscsoporthoz helyezi át.
> 

1. A [Azure Portal](https://portal.azure.com)navigáljon az áthelyezni kívánt alkalmazáshoz.

2. A bal oldali menüben válassza az **app Service terv módosítása**lehetőséget.

3. A **app Service terv** legördülő menüben válasszon ki egy meglévő csomagot az alkalmazás áthelyezéséhez. A legördülő lista csak azokat a csomagokat mutatja be, amelyek ugyanabban az erőforráscsoporthoz és földrajzi régióban találhatók, mint az aktuális App Service terv. Ha nincs ilyen csomag, lehetővé teszi, hogy alapértelmezés szerint hozzon létre egy csomagot. Új tervet manuálisan is létrehozhat az **új létrehozása**lehetőség kiválasztásával.

4. Ha létrehoz egy csomagot, akkor kiválaszthatja az új csomag díjszabási szintjét. Az **árképzési**szinten válassza ki a meglévő szintet a módosításhoz. 
   
   > [!IMPORTANT]
   > Ha egy magasabb rétegű csomagból egy alacsonyabb rétegű csomagra helyez át egy alkalmazást, például a **D1** -ről az **F1**-re, akkor az alkalmazás elveszítheti a megcélzott csomag bizonyos funkcióit. Ha például az alkalmazás SSL-tanúsítványokat használ, a következő hibaüzenet jelenhet meg:
   >
   > `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

5. Ha elkészült, kattintson **az OK gombra**.
   
   ![App Service csomag kiválasztása.][change] 

## <a name="move-an-app-to-a-different-region"></a>Alkalmazás áthelyezése másik régióba

Az a régió, amelyben az alkalmazás fut, a App Service megtervezni. Azonban nem módosítható App Service csomag régiója. Ha az alkalmazást egy másik régióban szeretné futtatni, az egyik alternatíva az alkalmazások klónozása. A klónozás az alkalmazás egy példányát egy új vagy meglévő App Service tervbe hozza bármely régióban.

A **klónozási alkalmazást** a menü **fejlesztői eszközök** szakaszában találja.

> [!IMPORTANT]
> A klónozás bizonyos korlátozásokkal rendelkezik. Ezekről [Azure app Service alkalmazások klónozásával](app-service-web-app-cloning.md)kapcsolatos információkat olvashat.

## <a name="scale-an-app-service-plan"></a>App Service terv méretezése

Az App Service csomag díjszabási szintjeinek vertikális felskálázásához tekintse meg az [alkalmazás vertikális Felskálázása az Azure-ban](manage-scale-up.md)című témakört.

Az alkalmazások példányszámának felskálázásához tekintse meg a [Példányszám manuális vagy automatikus skálázása](../monitoring-and-diagnostics/insights-how-to-scale.md)című témakört.

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>App Service csomag törlése

Ha el szeretné kerülni a nem várt díjakat, akkor a App Service csomag utolsó alkalmazásának törlésekor App Service a csomagot is alapértelmezés szerint törli. Ha úgy dönt, hogy megtartja a tervet, érdemes módosítania az **ingyenes** szintet, hogy ne kelljen fizetnie.

> [!IMPORTANT]
> Azok a App Service csomagok, amelyeknek nincsenek hozzárendelt alkalmazásai, díjkötelesek, mert továbbra is fenntartják a konfigurált virtuálisgép-példányokat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Alkalmazások vertikális felskálázása az Azure-ban](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png

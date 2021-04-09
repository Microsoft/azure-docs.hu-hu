---
author: baanders
description: fájl belefoglalása az Azure-függvények Visual studióból való közzétételének folyamatához
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 9f8ca9ab6dc184abb8d9dcd25ebe87cbd7761722
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102630285"
---
Ha közzé szeretné tenni a projektet egy Azure-beli Function alkalmazásban, kattintson a jobb gombbal *megoldáskezelő* a projektre, és válassza a **Közzététel** lehetőséget.

> [!IMPORTANT] 
> Az Azure-beli Function-alkalmazásokban az előfizetése további díjakat is felszámít az Azure Digital ikrektől függetlenül.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="A Visual Studio képernyőképe, amely a jobb gombbal válassza a megoldás menüt. A közzététel ki van emelve a menüben.":::

Az alábbi *közzétételi* oldalon hagyja meg az **Azure** alapértelmezett céljának kijelölését, és válassza a *tovább* lehetőséget. 

Egy adott célpont esetében válassza az **Azure függvényalkalmazás (Windows)** lehetőséget, és kattintson a *Tovább gombra*.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="A Visual Studio képernyőképe az Azure-függvény közzététele párbeszédpanelen. Az Azure függvényalkalmazás (Windows) az adott cél oldalon van kiválasztva.":::

A *functions példány* lapon válassza ki az előfizetését. Ezután válassza ki az *+* ikont egy új Azure-függvény létrehozásához.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="A Visual Studio képernyőképe az Azure-függvény közzététele párbeszédpanelen. Új függvény létrehozásához a + gomb jelenik meg a functions példány lapon.":::

A *függvényalkalmazás (Windows) – új ablak létrehozása* ablakban töltse ki a mezőket a következő módon:
* A **Name (név** ) annak a használati tervnek a neve, amelyet az Azure a Azure functions alkalmazás üzemeltetéséhez fog használni. Ez lesz a függvény alkalmazásának neve is, amely a tényleges függvényt tárolja. Megadhatja a saját egyedi értékét, vagy meghagyhatja az alapértelmezett javaslatot.
* Győződjön meg arról, hogy az **előfizetés** megfelel a használni kívánt előfizetésnek 
* Győződjön meg arról, hogy az **erőforráscsoport** a használni kívánt erőforráscsoporthoz van
* A **csomag típusának** elhagyása *felhasználásként*
* Válassza ki azt a **helyet** , amely megfelel az erőforráscsoport helyének.
* Hozzon létre egy új **Azure Storage** -erőforrást az *új.* .. hivatkozás használatával. Állítsa be úgy a helyet, hogy egyezzen az erőforráscsoporthoz, használja a többi alapértelmezett értéket, majd válassza az OK gombot.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="A Visual Studio képernyőképe az Azure-függvény közzététele párbeszédpanelen. Az új Function-alkalmazás részletei kitöltése folyamatban van, beleértve a nevet, az előfizetést, az erőforráscsoportot, a csomag típusát, a helyet és az Azure Storage-t.":::

Ezután kattintson a **Létrehozás** elemre.

Rövid várakozás után, amíg az App Service létrejött, a párbeszédpanelnek vissza kell térnie a functions *instance* lapra, és az új Function alkalmazás jelenik meg az erőforráscsoport alá ágyazott **Function apps** területen. Válassza a *Befejezés* gombot.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Azure-függvény közzététele a Visual Studióban: functions instance (a Function app után)":::

A Visual Studio fő ablakában megnyíló *Közzététel* ablaktáblán győződjön meg arról, hogy az összes információ helyesnek tűnik, majd válassza a **Közzététel** lehetőséget.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="A Visual Studio képernyőképe az Azure-függvény közzététele párbeszédpanelen. Megjelenik az új Function alkalmazás a Function apps listájában, és van egy Befejezés gomb.":::

> [!NOTE]
> Ha a következőhöz hasonló előugró ablak jelenik meg: :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="képernyőkép a Visual Studio előugró ablakban a közzétételi hitelesítő adatok nevű ablakban. Tartalmaz egy felhasználónevet és egy jelszót, valamint egy gombot, amely megkísérli beolvasni a hitelesítő adatokat az Azure-ból." border="false":::
> Válassza a **kísérlet a hitelesítő adatok lekérése az Azure-ból és a** **Mentés** lehetőséget.
>
> Ha a *functions verziójának frissítése az Azure* -ban vagy a *functions Runtime verziója nem egyezik az Azure-ban futó verzióval*:
>
> Az utasításokat követve frissítsen a legújabb Azure Functions futtatókörnyezet-verzióra. Ez a probléma akkor fordulhat elő, ha a Visual Studio egy régebbi verzióját használja.

A Function alkalmazás most már közzé van téve az Azure-ban.
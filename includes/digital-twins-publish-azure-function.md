---
author: baanders
description: fájlba foglalja bele az Azure-függvények közzétételi folyamatához a Visual Studio
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: ddc56ab05a087c9e86d67a13aebcfb8e65fbd78f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480754"
---
A projekt Azure-beli függvényalkalmazásban való közzétételéhez kezdje a Megoldáskezelő. Kattintson a jobb gombbal a projektre, majd válassza a **Publish (Közzététel) lehetőséget.**

> [!IMPORTANT] 
> Az Azure-beli függvényalkalmazásban való közzététel további díjakkal kapcsolatos, az előfizetéstől Azure Digital Twins.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Képernyőkép a Visual Studio jobb gombbal a megoldás menüjéről. A menüben a Közzététel ki van emelve.":::

A **megnyíló Közzététel** oldalon hagyja meg az Azure alapértelmezett célkijelölését.  Ezután kattintson a **Tovább** gombra. 

Egy adott célhoz válassza az **Azure-függvényalkalmazás (Windows),** majd a Tovább **lehetőséget.**

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Képernyőkép a Visual Studio, amely az Azure-függvény közzététele párbeszédpanelt mutatja. Az Adott cél lapon a kijelölés az Azure-függvényalkalmazás (Windows) lesz.":::

A **Functions-példány lapon** válassza ki az előfizetését. Ezután válassza a plusz (+) ikont egy új függvény létrehozásához.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Képernyőkép a Visual Studio, amely az Azure-függvény közzététele párbeszédpanelt mutatja. A plusz ikon ki van emelve.":::

A **Függvényalkalmazás (Windows) – Új** létrehozása ablakban töltse ki a következő mezőket:
* **A** név annak a használat alapú csomagnak a neve, amely az Azure-ban a Azure Functions lesz. Ez a név a tényleges függvényt is tartalmazza a függvényalkalmazásra. Kiválaszthat egy egyedi értéket, vagy meghagyhatja az alapértelmezett javaslatot.
* Győződjön meg **arról, hogy az Előfizetés** megfelel a használni kívánt előfizetésnek. 
* Győződjön meg **arról, hogy** az Erőforráscsoportot szeretné használni.
* A Csomag **típusa beállításnál hagyja** meg a **Felhasználás beállítását.**
* Válassza ki **az** erőforráscsoport helyét.
* Hozzon létre egy **új Azure Storage-erőforrást** az Új **hivatkozásra kattintva.** Állítsa be a helyet az erőforráscsoportnak megfelelőre, használja a többi alapértelmezett értéket, majd kattintson az **OK gombra.**

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Képernyőkép a Visual Studio, amely az Azure-függvény közzététele párbeszédpanelt mutatja. Egy új függvényalkalmazás részletei vannak kitöltve, beleértve a nevet, az előfizetést, az erőforráscsoportot, a csomag típusát, a helyet és az Azure Storage-et.":::

Ezután kattintson a **Létrehozás** elemre.

Az App Service létrehozása után megnyílik a **Functions-példány** lap. Az új függvényalkalmazás megjelenik a **Függvényalkalmazások területen** az erőforráscsoport alatt. Válassza a **Befejezés** gombot.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Képernyőkép a Visual Studio, amely az Azure-függvény közzététele párbeszédpanelt mutatja. A Függvénypéldány lap van kiválasztva. Az új függvényalkalmazás megjelenik az erőforráscsoport alatt.":::

A **fő** ablakban megnyíló Közzététel Visual Studio ellenőrizze, hogy minden információ helyesnek tűnik-e. Ezután válassza a **Publish** (Közzététel) lehetőséget.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Képernyőkép a Visual Studio a Közzététel panelről. A Közzététel gomb ki van emelve.":::

> [!NOTE]
> Ha az alábbi példához hasonló előugró ablak jelenik meg, válassza a Hitelesítő adatok lekérése az **Azure-ból** lehetőséget, majd válassza a **Mentés lehetőséget.**
> :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="Képernyőkép Visual Studio a Hitelesítő adatok közzététele előugró ablakkal. A Felhasználónév és a Jelszó mezőket tartalmazza. Egy gombot is tartalmaz, amely megpróbálja lekérni a hitelesítő adatokat az Azure-ból." border="false":::
>
> Ha a következő figyelmeztetések valamelyikét látja, kövesse az utasításokat a Azure Functions verziójára való frissítéshez:
> * "A Functions verziójának frissítése az Azure-ban."
> * "A függvény futtatókörnyezetének verziója nem egyezik az Azure-ban futó verzióval."
>
> Ezek a figyelmeztetések akkor jelennek meg, ha az alkalmazás egy régebbi verzióját Visual Studio.

A függvényalkalmazás most már közzé vantéve az Azure-ban.

---
title: A Távoli asztal alkalmazása Cloud Services (bővített támogatás)
description: Cloud Services Távoli asztal engedélyezése (bővített támogatás)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 53f873013a6f16ce5a28ee5d915afa556057f643
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744421"
---
# <a name="apply-the-remote-desktop-extension-to-azure-cloud-services-extended-support"></a>A Távoli asztal bővítmény alkalmazása az Azure Cloud Servicesra (bővített támogatás)

A Azure Portal a távoli asztal bővítmény használatával engedélyezheti a Távoli asztalt, még az alkalmazás telepítése után is. A felhőalapú szolgáltatás távoli asztal beállításai lehetővé teszik a távoli asztal engedélyezését, a helyi rendszergazdai fiók frissítését, a hitelesítéshez használt tanúsítványok kijelölését és a tanúsítványok lejárati dátumának beállítását. 

## <a name="apply-remote-desktop--extension"></a>Távoli asztal-bővítmény alkalmazása
1. Navigáljon ahhoz a felhőalapú szolgáltatáshoz, amelyhez engedélyezni szeretné a Távoli asztalt, majd a bal oldali navigációs panelen válassza a **"távoli asztal"** lehetőséget.

    :::image type="content" source="media/remote-desktop-1.png" alt-text="A képen a Távoli asztal lehetőség kiválasztását mutatja a Azure Portal":::

2. Válassza a **Hozzáadás** elemet.
3. Válassza ki azokat a szerepköröket, amelyekkel engedélyezni szeretné a Távoli asztalt.
4. Adja meg a Felhasználónév, a jelszó, a lejárat és a tanúsítvány kötelező mezőit (nem kötelező).

    :::image type="content" source="media/remote-desktop-2.png" alt-text="A képen a távoli asztalhoz való kapcsolódáshoz szükséges információk bevitele látható.":::

5. Ha elkészült, válassza a **Mentés** lehetőséget. Néhány percet is igénybe vehet, mielőtt a szerepkör-példányok megkapják a kapcsolatokat.

## <a name="connect-to-role-instances-with-remote-desktop-enabled"></a>Kapcsolódás a szerepkör-példányokhoz Távoli asztal engedélyezve
Ha a távoli asztal engedélyezve van a szerepkörökön, akkor közvetlenül a Azure Portal kezdeményezheti a kapcsolatokat.

1. A példányok beállításainak megnyitásához kattintson a **szerepkörök és példányok** elemre.

    :::image type="content" source="media/remote-desktop-3.png" alt-text="A rendszerkép a konfiguráció panelen a szerepkörök és példányok lehetőség kiválasztásával jelenik meg.":::

2. Válassza ki azt a szerepkör-példányt, amelyhez a távoli asztal konfigurálva van.
3. A távoli asztali kapcsolat fájl letöltéséhez kattintson a **Kapcsolódás** lehetőségre.

    :::image type="content" source="media/remote-desktop-4.png" alt-text="A rendszerkép a Azure Portal feldolgozói szerepkör példányának kiválasztását mutatja.":::
    
4. Nyissa meg a fájlt a szerepkör-példányhoz való kapcsolódáshoz.


## <a name="next-steps"></a>További lépések 
- Tekintse át a Cloud Services [üzembe helyezésének előfeltételeit](deploy-prerequisite.md) (kiterjesztett támogatás).
- Tekintse át a Cloud Servicesra vonatkozó [gyakori kérdéseket](faq.md) (kiterjesztett támogatás).
- A [Azure Portal](deploy-portal.md), a [PowerShell](deploy-powershell.md), a [sablon](deploy-template.md) vagy a [Visual Studio](deploy-visual-studio.md)használatával üzembe helyezhet egy felhőalapú szolgáltatást (kiterjesztett támogatás).
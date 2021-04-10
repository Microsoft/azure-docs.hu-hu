---
title: Az Azure VMware-megoldás Jump Box létrehozása
description: Az Azure VMware-megoldás Jump Box létrehozásának lépései.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: f746e11763e1df1686f3134960dea167bf1c9908
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462251"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-access-private-cloud.md -->

1. Az erőforráscsoport területen válassza a **+ Hozzáadás** lehetőséget, majd válassza a **Microsoft Windows 10** lehetőséget, majd válassza a **Létrehozás** lehetőséget.

   :::image type="content" source="../media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Új Windows 10-es virtuális gép hozzáadása egy Jump Box-hoz." border="true":::

1. Adja meg a szükséges adatokat a mezőkben, majd válassza a **felülvizsgálat + létrehozás** elemet. 

   A mezőkkel kapcsolatos további információkért tekintse meg a következő táblázatot.

   | Mező | Érték |
   | --- | --- |
   | **Előfizetés** | Az érték előre fel van töltve az erőforráscsoporthoz tartozó előfizetéssel. |
   | **Erőforráscsoport** | Az érték előre ki van töltve az aktuális erőforráscsoporthoz, amelyet az előző oktatóanyagban hozott létre.  |
   | **Virtuális gép neve** | Adjon meg egy egyedi nevet a virtuális gép számára. |
   | **Régió** | Válassza ki a virtuális gép földrajzi helyét. |
   | **Rendelkezésre állási beállítások** | Hagyja bejelölve az alapértelmezett értéket. |
   | **Kép** | Válassza ki a virtuális gép rendszerképét. |
   | **Méret** | Hagyja meg az alapértelmezett méret értéket. |
   | **Hitelesítés típusa**  | Válassza a **jelszó** lehetőséget. |
   | **Felhasználónév** | Adja meg a virtuális gépre való bejelentkezéshez használt felhasználónevet. |
   | **Jelszó** | Adja meg a virtuális gépre való bejelentkezéshez használt jelszót. |
   | **Jelszó megerősítése** | Adja meg a virtuális gépre való bejelentkezéshez használt jelszót. |
   | **Nyilvános bejövő portok** | Válassza a **Nincs** lehetőséget. Ha a nincs lehetőséget választja, a [JIT-hozzáférés](../../security-center/security-center-just-in-time.md#jit-configure) használatával szabályozhatja a virtuális gép elérését, ha azt szeretné elérni. Másik lehetőségként használhatja az [Azure](../../bastion/tutorial-create-host-portal.md) -t is, ha a Jump Box-kiszolgálót biztonságosan szeretné elérni az internetről anélkül, hogy hálózati portot kellene kitenni.  |


1. Az ellenőrzés után válassza a **Létrehozás** lehetőséget a virtuális gép létrehozási folyamatának elindításához.


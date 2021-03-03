---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 7bf71f55e1b49a9280b25cfcc01090afbd0c42db
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750991"
---
## <a name="create-an-image-gallery"></a>Rendszerkép-gyűjtemény létrehozása

A képgyűjtemény a képmegosztás engedélyezéséhez használt elsődleges erőforrás. A katalógus nevének megengedett karaktere nagybetűs vagy kisbetűk, számjegyek, pontok és időszakok. A gyűjtemény neve nem tartalmazhat kötőjeleket.  A katalógus nevének egyedinek kell lennie az előfizetésen belül. 

A következő példában létrehozunk egy *MyGallery* nevű katalógust a *myGalleryRG* -erőforráscsoporthoz.

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
1. A keresőmezőbe írja be a **megosztott képtárat** , majd a találatok között válassza a **megosztott képtára** lehetőséget.
1. A **megosztott rendszerkép** -katalógus lapon kattintson a **Hozzáadás** gombra.
1. A **megosztott rendszerkép létrehozása** lapon válassza ki a megfelelő előfizetést.
1. Az **erőforráscsoport** területen válassza az **új létrehozása** lehetőséget, és írja be a *myGalleryRG* nevet.
1. A **név** mezőbe írja be a *MyGallery* nevet a gyűjtemény neveként.
1. Hagyja meg az alapértelmezett **régiót**.
1. Beírhatja a katalógus rövid leírását, például a *saját képtárat a teszteléshez.* majd kattintson a **felülvizsgálat + létrehozás** gombra.
1. Az érvényesítési fázisok után válassza a **Létrehozás** lehetőséget.
1. Ha a telepítés befejeződött, válassza az **Ugrás az erőforráshoz** lehetőséget.


## <a name="create-an-image-definition"></a>Rendszerkép-definíció létrehozása 

A rendszerkép-definíciók logikai csoportosítást hoznak létre a képekhez. Ezek az adatok a bennük létrehozott rendszerkép-verziókra vonatkozó információk kezelésére szolgálnak. A képdefiníciók nevei nagybetűket, kisbetűket, számokat, pontokat, kötőjeleket és pontokat tartalmazhatnak. További információ a képdefiníciók által megadható értékekről: [képdefiníciók](../articles/virtual-machines/shared-image-galleries.md#image-definitions).

Hozza létre a katalógus képének definícióját a katalógusban. Ebben a példában a katalógus rendszerképének neve *myImageDefinition*.

1. Az új rendszerkép-katalógus lapján válassza az **új rendszerkép-definíció hozzáadása** lehetőséget az oldal tetején. 
1. Az **új rendszerkép-definíció hozzáadása a közös képtárhoz** területen válassza az *USA keleti* **régiója** lehetőséget.
1. A **rendszerkép-definíció neve** mezőbe írja be a következőt: *myImageDefinition*.
1. **Operációs rendszer** esetén válassza ki a megfelelő beállítást a forrás virtuális gép alapján.  
1. Virtuálisgép- **generáció** esetén válassza a forrás virtuális gép alapján lehetőséget. A legtöbb esetben ez az 1. *Gen* lesz. További információ: 2. [generációs virtuális gépek támogatása](../articles/virtual-machines/generation-2.md).
1. Az **operációs rendszer állapota** területen válassza a forrás virtuális gép alapján lehetőséget. További információ: [általánosított és specializált](../articles/virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).
1. A **Publisherben** írja be a következőt: *myPublisher*. 
1. Az **ajánlat** mezőbe írja be a következőt: *myOffer*.
1. Az **SKU** mezőbe írja be a következőt: *mySKU*.
1. Ha elkészült, válassza a **felülvizsgálat + létrehozás** elemet.
1. Miután a rendszerkép definíciója áthaladt az ellenőrzésen, válassza a **Létrehozás** lehetőséget.
1. Ha a telepítés befejeződött, válassza az **Ugrás az erőforráshoz** lehetőséget.


## <a name="create-an-image-version"></a>Rendszerképverzió létrehozása

Rendszerkép-verzió létrehozása felügyelt rendszerképből. Ebben a példában a rendszerkép verziója a *1.0.0* , és a rendszer replikálja az *USA nyugati középső* régiójában és az *USA déli középső* régiójában. A célcsoportok replikáláshoz való kiválasztásakor ne feledje, hogy a *forrás* régiót is meg kell adnia a replikálás céljának.

A képverzió megengedett karaktereinek száma számok és időszakok. A számoknak egy 32 bites egész számon belüli tartományba kell esniük. Formátum: *MajorVersion*. *MinorVersion*. *Javítás*.

A rendszerkép-verziók létrehozásának lépései némileg eltérőek, attól függően, hogy a forrás általánosított rendszerkép-e, vagy egy speciális virtuális gép pillanatképe. 

### <a name="option-generalized"></a>Lehetőség: általánosított

1. A rendszerkép definíciójának lapján válassza a **verzió hozzáadása** lehetőséget az oldal tetején.
1. A **régió** területen válassza ki azt a régiót, ahol a felügyelt képet tárolja. A képverziókat ugyanabban a régióban kell létrehozni, mint a létrehozott felügyelt rendszerképből.
1. A **név** mezőbe írja be a *1.0.0* nevet. A rendszerkép verziószámának a következőnek kell lennie: *Major*. *kisebb*. a *javítási* formátum egész számok használatával. 
1. A **forrás rendszerképben** válassza ki a forrásként kezelt rendszerképet a legördülő menüből.
1. A **legutóbbi kizárásnál** hagyja *meg az alapértelmezett értéket.*
1. Az **élettartam végén** válasszon ki egy dátumot a naptárból, amely a jövőben néhány hónap.
1. A **replikáció** területen hagyja meg az **alapértelmezett replika 1 értéket** . Replikálni kell a forrás régióba, ezért hagyja meg az első replikát alapértelmezettként, majd válasszon egy második replikát az *USA keleti* régiójában.
1. Ha elkészült, válassza a **felülvizsgálat + létrehozás** elemet. Az Azure ellenőrzi a konfigurációt.
1. Ha a rendszerkép verziója megfelel az ellenőrzésnek, válassza a **Létrehozás** lehetőséget.
1. Ha a telepítés befejeződött, válassza az **Ugrás az erőforráshoz** lehetőséget.

Eltarthat egy ideig, amíg a rendszer replikálja a rendszerképet az összes megcélzott régióba.

### <a name="option-specialized"></a>Beállítás: speciális

1. A rendszerkép definíciójának lapján válassza a **verzió hozzáadása** lehetőséget az oldal tetején.
1. A **régió** területen válassza ki azt a régiót, ahol a pillanatképet tárolja. A képverziókat ugyanabban a régióban kell létrehozni, mint a-ból létrehozott forrást.
1. A **név** mezőbe írja be a *1.0.0* nevet. A rendszerkép verziószámának a következőnek kell lennie: *Major*. *kisebb*. a *javítási* formátum egész számok használatával. 
1. Az **operációsrendszer-lemez pillanatképe** lapon válassza ki a pillanatképet a forrás virtuális gépről a legördülő menüből. Ha a forrás virtuális gépe tartalmaz egy adatlemezt, amelyet szeretne felvenni, válassza ki a megfelelő **LUN** -számot a legördülő listából, majd válassza ki az adatlemez **pillanatképének** pillanatképét. 
1. A **legutóbbi kizárásnál** hagyja *meg az alapértelmezett értéket.*
1. Az **élettartam végén** válasszon ki egy dátumot a naptárból, amely a jövőben néhány hónap.
1. A **replikáció** területen hagyja meg az **alapértelmezett replika 1 értéket** . Replikálni kell a forrás régióba, ezért hagyja meg az első replikát alapértelmezettként, majd válasszon egy második replikát az *USA keleti* régiójában.
1. Ha elkészült, válassza a **felülvizsgálat + létrehozás** elemet. Az Azure ellenőrzi a konfigurációt.
1. Ha a rendszerkép verziója megfelel az ellenőrzésnek, válassza a **Létrehozás** lehetőséget.
1. Ha a telepítés befejeződött, válassza az **Ugrás az erőforráshoz** lehetőséget.

## <a name="share-the-gallery"></a>A katalógus megosztása

Javasoljuk, hogy a Képtár szintjén ossza meg a hozzáférést. A következő végigvezeti az imént létrehozott katalógus megosztásán.

1. Az új rendszerkép-katalógus lapjának bal oldali menüjében válassza a **hozzáférés-vezérlés (iam)** lehetőséget. 
1. **A szerepkör-hozzárendelés hozzáadása** területen válassza a **Hozzáadás** lehetőséget. Ekkor megnyílik a **szerepkör-hozzárendelés hozzáadása** ablaktábla. 
1. A **szerepkör** területen válassza az **olvasó** lehetőséget.
1. A **hozzáférés társítása** alatt hagyja meg az alapértelmezett **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév** beállítást.
1. A **Select (kijelölés**) területen írja be a meghívni kívánt személy e-mail-címét.
1. Ha a felhasználó a szervezeten kívül esik, megjelenik az üzenet, **amely lehetővé teszi, hogy a felhasználó a Microsofttal való együttműködéshez küldjön egy e-mailt.** Válassza ki az e-mail-címmel rendelkező felhasználót, majd kattintson a **Mentés** gombra.

Ha a felhasználó a szervezeten kívül esik, e-mailben meghívót kap a szervezethez való csatlakozáshoz. A felhasználónak el kell fogadnia a meghívást, és látni fogja a katalógust és az összes rendszerkép-definíciót és verziót az erőforrások listájában.

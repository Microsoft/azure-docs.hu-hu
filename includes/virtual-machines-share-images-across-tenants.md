---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f74d4cbc17e49345534a37e9e6612a36e19be295
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "73903709"
---
Ha azonban az Azure-bérlőn kívül szeretné megosztani a lemezképeket, akkor a megosztás megkönnyítéséhez létre kell hoznia egy alkalmazás-regisztrálást.  Az alkalmazások regisztrálásával összetettebb megosztási forgatókönyvek is engedélyezhetők, például a következő esetekben: 

* Megosztott lemezképek kezelése, amikor egy vállalat megvásárol egy másikat, az Azure-infrastruktúra pedig külön bérlők között oszlik meg. 
* Az Azure-partnerek az Azure-infrastruktúrát az ügyfeleik nevében kezelhetik. A képek testreszabása a partnerek bérlőn belül történik, de az infrastruktúra központi telepítései az ügyfél bérlője számára történnek. 


## <a name="create-the-app-registration"></a>Az alkalmazás regisztrációjának létrehozása

Hozzon létre egy alkalmazás-regisztrációt, amelyet mindkét bérlő használni fog a képkatalógus erőforrásainak megosztásához.
1. Nyissa meg a [Azure Portal Alkalmazásregisztrációk (előzetes verzió)](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. Az oldal tetején található menüben válassza az **új regisztráció** lehetőséget.
1. A **név** mezőbe írja be a következőt: *myGalleryApp*.
1. A **támogatott fióktípus** területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
1. Az **átirányítási URI** mezőbe írja be a nevet, *https://www.microsoft.com* majd válassza a **regisztráció** lehetőséget. Az alkalmazás regisztrációjának létrehozása után megnyílik az áttekintő oldal.
1. Az Áttekintés lapon másolja az **alkalmazás (ügyfél) azonosítóját** , és mentse a alkalmazást később.   
1. Válassza a **tanúsítványok & titkok** lehetőséget, majd válassza az **új ügyfél titka** lehetőséget.
1. A **Leírás** mezőbe írja be a *megosztott rendszerkép-katalógus több-bérlős alkalmazás titkos kulcsát*.
1. A **lejáratnál** hagyja meg az alapértelmezett **1 év** értéket, majd válassza a **Hozzáadás** lehetőséget.
1. Másolja a titkos kulcs értékét, és mentse egy biztonságos helyre. Az oldal elhagyása után nem kérhető le.


Adja meg az alkalmazás regisztrációjának engedélyét a megosztott képtárat használva.
1. A Azure Portal válassza ki azt a megosztott képtárat, amelyet másik Bérlővel szeretne megosztani.
1. Válassza a **hozzáférés-vezérlés kiválasztása (iam)** lehetőséget, majd a **szerepkör-hozzárendelés hozzáadása** területen válassza a *Hozzáadás* lehetőséget. 
1. A **szerepkör** területen válassza az **olvasó** lehetőséget.
1. A **hozzáférés társítása a** következőhöz területen hagyja ezt az **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév** beállításnál.
1. A **Select (kijelölés**) területen írja be a *myGalleryApp* , és válassza ki azt, amikor megjelenik a listában. Amikor elkészült, válassza a **Mentés** gombot.


## <a name="give-tenant-2-access"></a>2. bérlői hozzáférés biztosítása

Adja meg a bérlő 2 hozzáférését az alkalmazáshoz egy böngésző használatával történő bejelentkezés kérésével. A helyére írja be annak a *\<Tenant2 ID>* bérlőnek a bérlői azonosítóját, amelyhez a képtárat meg szeretné osztani. A helyére írja be a létrehozott alkalmazás- *\<Application (client) ID>* regisztráció alkalmazás-azonosítóját. Ha végzett a kihelyezéssel, illessze be az URL-címet egy böngészőben, és kövesse a bejelentkezési utasításokat a 2. Bérlőbe való bejelentkezéshez.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

A [Azure Portal](https://portal.azure.com) jelentkezzen be bérlői 2-ként, és adja meg az alkalmazás regisztrációs hozzáférését ahhoz az erőforráscsoporthoz, amelyben létre szeretné hozni a virtuális gépet.

1. Válassza ki az erőforráscsoportot, majd válassza a **hozzáférés-vezérlés (iam)** lehetőséget. A **szerepkör-hozzárendelés hozzáadása** területen válassza a **Hozzáadás** lehetőséget. 
1. A **szerepkör** alatt írja be a **közreműködőt**.
1. A **hozzáférés társítása a** következőhöz területen hagyja ezt az **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév** beállításnál.
1. A **Select** Type *MyGalleryApp* (típus kiválasztása) területen válassza ki azt, amikor megjelenik a listában. Amikor elkészült, válassza a **Mentés** gombot.

> [!NOTE]
> Meg kell várnia, amíg a rendszerkép verziója teljesen elkészült és replikálva lett ahhoz, hogy ugyanazt a felügyelt képet használhassa egy másik rendszerkép-verzió létrehozásához.


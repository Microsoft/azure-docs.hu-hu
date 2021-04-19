---
title: tároló-fájlok-létrehozás-tároló-fiók-portál
description: Tárfiók létrehozása az Azure Files számára.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33159ab6dd014a153c8fd317fd291aeca033d6e9
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717834"
---
A tárfiókok az Azure-fájlmegosztások vagy más tárolási erőforrások, például blobok vagy üzenetsorok üzembe helyezéséhez használható tárolók közös készletei. A tárfiókok korlátlan számú megosztást tartalmazhatnak, a megosztásokban pedig tetszőleges számú fájlt tárolhat, egészen a tárfiókja kapacitásának korlátjáig.

Tárfiók létrehozása:

1. A bal oldali menüben válassza **+** az erőforrás létrehozásához lehetőséget.
1. Tárfiók **létrehozásához** válassza a Tárfiók lehetőséget.

    :::image type="content" source="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png" alt-text="Képernyőkép a tárfiók lehetőségről az Erőforrás létrehozása panelen." lightbox="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png":::

1. A **Név** mezőbe írja be a *mystorageacct* kifejezést, majd írjon utána néhány véletlenszerű számot, amíg meg nem jelenik a név egyediségét jelző zöld pipa. A tárfiók nevének globálisan egyedinek kell lennie, és csak kisbetűkből állhat. Jegyezze fel a tárfiók nevét. Erre később még szüksége lesz. 
1. A **Teljesítmény beállításban** tartsa meg a Standard alapértelmezett **értékét.**
1. A **Replikáció** mezőben válassza a **Helyileg redundáns tárolás (LRS)** lehetőséget.
1. Az **Előfizetés** mezőben válassza ki azt az előfizetést, amelyben létrehozza a tárfiókot. Ha csak egy előfizetéssel rendelkezik, akkor az az alapértelmezett.
1. Az **Erőforráscsoport** területen válassza az **Új létrehozása** lehetőséget. Névként adja meg a *myResourceGroup* elnevezést.
1. A **Hely** mezőben válassza az **USA keleti régiója** lehetőséget.
1. Ha végzett, válassza a **Létrehozás** gombot az üzembe helyezés indításához.
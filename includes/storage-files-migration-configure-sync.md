---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 7aa3867fdc5de320c47a15737b655b8032f402a6
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075580"
---
Ez a lépés összekapcsolja a Windows Server-példányon beállított összes erőforrást és mappát az előző lépések során.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg a Storage Sync szolgáltatás erőforrását.
1. Hozzon létre egy új *szinkronizálási csoportot* a Storage Sync Service-erőforráson belül minden egyes Azure-fájlmegosztás esetében. Azure File Sync terminológiában az Azure-fájlmegosztás egy szinkronizálási csoport létrehozásával megjelenő szinkronizálási topológia *Felhőbeli végpontja* lesz. A szinkronizálási csoport létrehozásakor adjon neki egy ismerős nevet, hogy felismerje, hogy mely fájlok vannak szinkronizálva. Győződjön meg arról, hogy az Azure-fájlmegosztást egyező névvel hivatkozik.
1. A szinkronizálási csoport létrehozása után egy sor jelenik meg a szinkronizálási csoportok listájában. A szinkronizálási csoport tartalmának megjelenítéséhez válassza ki a nevet (egy hivatkozást). Az Azure-fájlmegosztás a **Felhőbeli végpontok** területen jelenik meg.
1. Keresse meg a **+ kiszolgáló-végpont hozzáadása** gombot. Az üzembe helyezett helyi kiszolgálón lévő mappa lesz a *kiszolgálói végpont* elérési útja.

---
title: Alkalmazás visszaállítása pillanatképből
description: Ismerje meg, hogyan állíthatja vissza az alkalmazást egy pillanatképből. A prémium szintű adatvesztés miatt nem várt adatvesztéssel állítható vissza az automatikus árnyékmásolatok.
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.topic: article
ms.date: 04/04/2018
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: f7edb632559dc8da2de32c58d994a7c51b1b09e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86169971"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Azure-beli alkalmazás visszaállítása pillanatképből
Ebből a cikkből megtudhatja, hogyan állíthat vissza egy alkalmazást [Azure app Service](../app-service/overview.md) egy pillanatképből. Az alkalmazás egy korábbi állapotba állítható vissza az alkalmazás pillanatképei alapján. Nem kell engedélyeznie a pillanatképek biztonsági mentését, a platform automatikusan elmenti az összes alkalmazás pillanatképét az adatok helyreállításához.

A pillanatképek növekményes árnyékmásolatok, és számos előnyt kínálnak a rendszeres [biztonsági mentésekhez](manage-backup.md):
- A fájlok zárolása miatt nem történt fájlmásolási hiba.
- Nincs tárolási méretre vonatkozó korlátozás.
- Nincs szükség konfigurációra.

A pillanatképek visszaállítása a **prémium** szintű vagy magasabb szintű alkalmazások számára érhető el. Az alkalmazás skálázásával kapcsolatos további információkért lásd: alkalmazás vertikális felskálázása [Az Azure-ban](manage-scale-up.md).

## <a name="limitations"></a>Korlátozások

- A szolgáltatás jelenleg előzetes verzióban érhető el.
- Csak ugyanarra az alkalmazásra vagy az alkalmazáshoz tartozó tárolóhelyre lehet visszaállítani.
- App Service leállítja a cél alkalmazást vagy a cél tárolóhelyet a visszaállítás során.
- A App Service a platform adathelyreállításának érdekében három hónapig is megőrzi a pillanatképeket.
- A pillanatképeket csak az elmúlt 30 napra állíthatja vissza.
- A App Service Environmenton futó App Services nem támogatja a pillanatképeket.
 

## <a name="restore-an-app-from-a-snapshot"></a>Alkalmazás visszaállítása pillanatképből

1. Az alkalmazás **Beállítások** lapján a [Azure Portalban](https://portal.azure.com)kattintson a **biztonsági** mentések elemre a **biztonsági mentések** lap megjelenítéséhez. Ezután kattintson a **visszaállítás** elemre a **Pillanatkép (előzetes verzió)** szakaszban.
   
    ![Képernyőkép, amely bemutatja, hogyan lehet visszaállítani egy alkalmazást egy pillanatkép biztonsági másolatából.](./media/app-service-web-restore-snapshots/1.png)

2. A **visszaállítás** lapon válassza ki a visszaállítani kívánt pillanatképet.
   
    ![Képernyőkép, amely bemutatja, hogyan választhatja ki a visszaállítani kívánt pillanatképet. ](./media/app-service-web-restore-snapshots/2.png)
   
3. Adja meg az alkalmazás visszaállításának célhelyét a **visszaállítási célhelyen**.
   
    ![A visszaállítás célhelyének megadását bemutató képernyőkép.](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Ha a **felülírás** lehetőséget választja, a rendszer az alkalmazás aktuális fájlrendszerében lévő összes meglévő adat törlődik és felülíródik. Mielőtt rákattintott az **OK** gombra, győződjön meg arról, hogy az, amit kíván tenni.
   > 
   > 
      
   > [!Note]
   > A jelenlegi technikai korlátozások miatt csak ugyanabban a skálázási egységben lévő alkalmazásokra lehet visszaállítani. Ez a korlátozás egy későbbi kiadásban lesz eltávolítva.
   > 
   > 
   
    A **meglévő alkalmazást** kiválaszthatja egy tárolóhelyre való visszaállításhoz. Ennek a lehetőségnek a használata előtt már létrehozott egy tárolóhelyet az alkalmazásban.

4. Dönthet úgy, hogy visszaállítja a hely konfigurációját.
   
    ![A hely konfigurációjának visszaállítását bemutató képernyőkép.](./media/app-service-web-restore-snapshots/4.png)

5. Kattintson az **OK** gombra.

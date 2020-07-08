---
title: Renderelési képességek használata
description: A Azure Batch renderelési képességeinek használata. Próbálja megismételni a Batch Explorer alkalmazást közvetlenül vagy egy ügyfélalkalmazás beépülő modulból meghívott alkalmazás használatával.
author: mscurrell
ms.author: markscu
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: 262431cfcc906b6df1054aaa7dc2b9573c3b8833
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85965246"
---
# <a name="using-azure-batch-rendering"></a>Azure Batch renderelés használata

A Azure Batch renderelés többféleképpen is használható:

* API-k:
  * Kód írása a Batch API-k bármelyikének használatával.  A fejlesztők a meglévő alkalmazásokhoz vagy munkafolyamatokhoz, akár a felhőben, akár a helyszínen is integrálhatja Azure Batch képességeit.
* Parancssori eszközök:
  * Az [Azure-parancssor](/cli/azure/) vagy a [PowerShell](/powershell/azure/overview) használható a Batch-használat parancsfájlhoz.
  * Különösen a [Batch CLI-sablon támogatása](./batch-cli-templates.md) sokkal egyszerűbbé teszi a készletek létrehozását és a feladatok elküldését.
* Batch Explorer felhasználói felület:
  * A [Batch Explorer](https://github.com/Azure/BatchLabs) egy platformfüggetlen ügyfél-eszköz, amely lehetővé teszi a Batch-fiókok felügyeletét és figyelését is.
  * Az egyes renderelési alkalmazások esetében számos készletet és feladattípust biztosítunk, amelyek segítségével egyszerűen hozhat létre készleteket, és elküldheti a feladatokat.  A sablonok egy halmaza szerepel az alkalmazás felhasználói felületén, és megtalálhatók a GitHubról elérhető sablonfájlok.
  * Az egyéni sablonok létrehozhatók a semmiből, vagy a GitHubról megadott sablonok másolhatók és módosíthatók.
* Ügyfélalkalmazások beépülő moduljai:
  * Olyan beépülő modulok érhetők el, amelyek lehetővé teszik, hogy a Batch rendering közvetlenül az ügyfél-tervezési és modellező alkalmazásokban legyen használatban.  A beépülő modulok elsősorban a Batch Explorer alkalmazást az aktuális 3D modell kontextusával hívja meg, és olyan funkciókat tartalmaznak, amelyek segítik az eszközök kezelését.

A legjobb módszer arra, hogy kipróbálja Azure Batch renderelést és legegyszerűbben a végfelhasználók számára, akik nem fejlesztők, és nem az Azure-szakértők, az Batch Explorer alkalmazást használják közvetlenül vagy egy ügyfélalkalmazás beépülő modullal.

## <a name="using-batch-explorer"></a>Batch Explorer használata

A következő témakörben talál részletes oktatóanyagot a renderelés végrehajtásához Batch Explorer használatával: [Blender oktatóanyag](./tutorial-rendering-batchexplorer-blender.md).

### <a name="download-and-install"></a>Letöltés és telepítés

Batch Explorer [letöltések elérhetők](https://azure.github.io/BatchExplorer/) a Windows, az OSX és a Linux rendszerekhez.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Sablonok használata készletek létrehozásához és feladatok futtatásához

A sablonok széles skáláját Batch Explorer használhatja, amely megkönnyíti a készletek létrehozását és feladatok elküldését a különböző renderelési alkalmazásokhoz anélkül, hogy meg kellene adnia a készletekhez, a feladatokhoz és a feladatokhoz közvetlenül a Batch használatával szükséges összes tulajdonságot.  A Batch Explorerban elérhető sablonokat a rendszer a GitHub- [tárházban](https://github.com/Azure/BatchExplorer-data/tree/master/ncj)tárolja és látja el.

![Batch Explorer gyűjtemény](./media/batch-rendering-using/batch-explorer-gallery.png)

A sablonok a piactéren elérhető virtuálisgép-lemezképeken található összes alkalmazásra vonatkoznak.  Minden egyes alkalmazáshoz több sablon létezik, beleértve a CPU-és GPU-készletek, a Windows-és Linux-készletek ellátásához szükséges készlet-sablonokat is. a feladatütemezés magában foglalja a teljes keret vagy a csempés turmixgép renderelését és a V-Ray elosztott renderelést. A megadott sablonok készlete az idő múlásával ki lesz bővítve az egyéb batch-képességekhez, például a készlet automatikus skálázásához.

Az egyéni sablonok előállítása is lehetséges, a semmiből vagy a megadott sablonok módosításával. Az egyéni sablonok a Batch Explorer gyűjtemény szakaszának "helyi sablonok" elemére kattintva használhatók.

### <a name="file-system-and-data-movement"></a>Fájlrendszer és adatáthelyezés

A Batch Explorer adatterülete lehetővé teszi a fájlok másolását a helyi fájlrendszer és az Azure Storage-fiókok között.

## <a name="client-application-plug-ins"></a>Ügyfélalkalmazások beépülő moduljai

Néhány ügyfélalkalmazás számára elérhetők a beépülő modulok.  A beépülő modulok lehetővé teszik, hogy a készletek és a feladatok közvetlenül az alkalmazásból jöjjenek létre, vagy meghívja a Batch Explorer.

* [Blender 2,79](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)
* [Blender 2.8 +](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender28)
* [Autodesk 3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
* [Autodesk Maya](https://github.com/Azure/azure-batch-maya)

## <a name="next-steps"></a>További lépések

A Batch rendering példái között próbálja ki a két oktatóanyagot:

* [Megjelenítés az Azure CLI használatával](./tutorial-rendering-cli.md)
* [Renderelés a Batch Explorer használatával](./tutorial-rendering-batchexplorer-blender.md)

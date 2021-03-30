---
title: Azure Storage Explorer kisegítő lehetőségek | Microsoft Docs
description: A Azure Storage Explorer elérhetőségének megismerése. Tekintse át, hogy milyen képernyőolvasók érhetők el, a nagyítási képesség, a kontrasztos megjelenítés és a billentyűparancsok.
services: storage
documentationcenter: na
author: MrayermannMSFT
manager: jinglouMSFT
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2018
ms.author: marayerm
ms.openlocfilehash: ca4a8d719277eaa1d853d53d282649f839256be9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88035485"
---
# <a name="storage-explorer-accessibility"></a>Storage Explorer kisegítő lehetőségek

## <a name="screen-readers"></a>Képernyőolvasók

Storage Explorer támogatja a képernyőolvasó használatát a Windows és Mac rendszereken. A következő képernyőolvasókat ajánlott minden platformhoz használni:

Platform | Képernyőolvasó
---------|--------------
Windows  | NVDA
Mac      | Hangalámondás
Linux    | (a képernyőolvasók Linux rendszeren nem támogatottak)

Ha Storage Explorer használatakor kisegítő problémákba kerül, [Nyisson meg egy problémát a githubon](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="zoom"></a>Zoom

A Storage Explorer nagyítással megteheti a szöveget. A nagyításhoz kattintson a Súgó menü **Nagyítás** elemére. A Súgó menüt is használhatja a nagyításhoz, és az alapértelmezett szintre állíthatja vissza a nagyítási szintet.

![Nagyítási beállítások a Súgó menüben][0]

A nagyítási beállítás növeli a legtöbb felhasználói felületi elem méretét. Azt javasoljuk, hogy az operációs rendszer nagyméretű szöveg-és nagyítási beállításait is engedélyezze, hogy az összes felhasználói felületi elem megfelelően legyen méretezhető.

## <a name="high-contrast-themes"></a>kontrasztos megjelenítés témák

Storage Explorer két kontrasztos témával rendelkezik, **Kontrasztos megjelenítés a világos** és a **sötét kontrasztos megjelenítés**. A témát a Súgó > témák menüjének bejelölésével módosíthatja.

![Témák almenü][1]

A téma beállítás megváltoztatja a legtöbb FELHASZNÁLÓIFELÜLET-elem színét. Javasoljuk, hogy az operációs rendszernek megfelelő kontrasztos témát is engedélyezze, hogy az összes felhasználói felületi elem megfelelően színes legyen.

## <a name="shortcut-keys"></a>Billentyűparancsok

### <a name="window-commands"></a>Ablak parancsai

Parancs       | Billentyűparancsok
--------------|--------------------
Új ablak    | **Vezérlő + Shift + N**
Szerkesztő lezárása  | **Vezérlő + F4**
Állj          | **Vezérlő + SHIFT + W**

### <a name="navigation-commands"></a>Navigációs parancsok

Parancs                | Billentyűparancsok
-----------------------|----------------------
Fókusz a következő panelen       | **F6**
Fókusz előző panelje   | **SHIFT + F6**
Explorer               | **Vezérlő + SHIFT + E**
Fiókkezelés     | **Vezérlő + SHIFT + A**
Oldalsó sáv váltógomb        | **Vezérlő + B**
Tevékenységnapló           | **Vezérlő + SHIFT + L**
Műveletek és tulajdonságok | **Vezérlő + SHIFT + P**
Aktuális szerkesztő         | **Vezérlő + Kezdőlap**
Következő szerkesztő            | **Vezérlő + oldal lefelé**
Előző szerkesztő        | **CTRL + PAGE UP**

### <a name="zoom-commands"></a>Nagyítási parancsok

Parancs  | Billentyűparancsok
---------|------------------
Nagyítás  | **Vezérlő + =**
Kicsinyítés | **Vezérlő +-**

### <a name="blob-and-file-share-editor-commands"></a>BLOB-és fájlmegosztási szerkesztői parancsok

Parancs | Billentyűparancsok
--------|--------------------
Vissza    | **ALT + balra nyíl**
hozás | **ALT + jobbra nyíl**
Fel      | **ALT + fel nyíl**

### <a name="editor-commands"></a>Szerkesztői parancsok

Parancs | Billentyűparancsok
--------|------------------
Másolás    | **Vezérlő + C**
Kivágás     | **Vezérlő + X**
Beillesztés   | **Vezérlő + V**
Frissítés  | **Vezérlő + R**

### <a name="other-commands"></a>Egyéb parancsok

Parancs                | Billentyűparancsok
-----------------------|------------------
Fejlesztői eszközök váltása | **F12**
Újratöltés                 | **ALT + CTRL + R**

[0]: ./media/vs-azure-tools-storage-explorer-accessibility/Zoom.png
[1]: ./media/vs-azure-tools-storage-explorer-accessibility/HighContrast.png

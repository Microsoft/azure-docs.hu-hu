---
title: Az Azure IoT Central felhasználói felületének testreszabása | Microsoft Docs
description: Az Azure IoT Central-alkalmazás témájának és súgójának testreszabása
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 660f6dfae23331455408ae7127274550a3a4cc3a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90019835"
---
# <a name="customize-the-azure-iot-central-ui"></a>Az Azure IoT Central felhasználói felületének testreszabása

Ez a cikk leírja, hogyan szabhatja testre az alkalmazás felhasználói felületét egyéni témák alkalmazásával, és módosíthatja a Súgó hivatkozásait úgy, hogy az egyéni súgó erőforrásaira mutasson. 

Az alábbi képernyőképen egy oldal látható a standard téma használatával:

![Standard IoT Central téma](./media/howto-customize-ui/standard-ui.png)

Az alábbi képernyőképen egy olyan oldal látható, amely egy egyéni képernyőképet használ a Kiemelt FELHASZNÁLÓIFELÜLET-elemek kiemelésével:

![Egyéni IoT Central téma](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Téma létrehozása

Egyéni téma létrehozásához navigáljon az **alkalmazás testreszabása** lapra az **Adminisztráció** szakaszban:

![IoT Central témák](./media/howto-customize-ui/themes.png)

Ezen az oldalon testreszabhatja az alkalmazás következő aspektusait:

### <a name="application-logo"></a>Alkalmazás emblémája

Egy 1 MB-nál nem nagyobb PNG-rendszerkép átlátszó háttérrel. Ez az embléma a IoT Central alkalmazás címsorának bal oldalán jelenik meg.

Ha az embléma képe tartalmazza az alkalmazás nevét, akkor elrejtheti az alkalmazásnév szövegét. További információ: [az alkalmazás kezelése](howto-administer.md#change-application-name-and-url).

### <a name="browser-icon-favicon"></a>Böngésző ikonja (favicon)

Egy PNG-rendszerkép, amely nem nagyobb, mint 32 x 32 képpont, átlátszó háttérrel. A webböngésző használhatja ezt a rendszerképet a címsor, előzmények, könyvjelzők és böngésző lapokon.

### <a name="browser-colors"></a>Böngésző színei

Megváltoztathatja az oldal fejlécének színét, valamint az ékezetes gombokhoz és egyéb csúcsfényekhez használt színeket. Használjon egy hat karakteres hexadecimális színértéket a formátumban `##ff6347` . További információ a **hexadecimális érték** színének jelöléséről: [HTML-színek](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> Az **alkalmazás testreszabása** oldalon bármikor visszatérhet az alapértelmezett beállításokra.

### <a name="changes-for-operators"></a>Operátorok módosításai

Ha egy rendszergazda egyéni témát hoz létre, akkor a kezelők és az alkalmazás más felhasználói már nem választhatnak témát a **beállításokban**.

## <a name="replace-help-links"></a>A Súgó hivatkozásainak cseréje

Ha egyéni súgót szeretne megadni a kezelők és más felhasználók számára, módosíthatja az alkalmazás **Súgó** menüjében található hivatkozásokat.

A Súgó hivatkozásainak módosításához navigáljon a **Súgó testreszabása** lapra az **Adminisztráció** szakaszban:

![IoT Central Súgó hivatkozásainak testreszabása](./media/howto-customize-ui/help-links.png)

Új bejegyzéseket is hozzáadhat a Súgó menühöz, és eltávolíthatja az alapértelmezett bejegyzéseket:

![Testreszabott IoT Central Súgó](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> A **Súgó testreszabása** lapon bármikor visszatérhet az alapértelmezett Súgó hivatkozásokra.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan szabhatja testre a felhasználói felületet a IoT Central alkalmazásban, néhány javasolt lépés:

- [Alkalmazás felügyelése](./howto-administer.md)
- [Csempék hozzáadása az irányítópulthoz](howto-add-tiles-to-your-dashboard.md)
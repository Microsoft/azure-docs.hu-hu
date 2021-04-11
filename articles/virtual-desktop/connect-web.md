---
title: Kapcsolódás a Windows rendszerű virtuális asztalhoz a webes ügyféllel – Azure
description: Kapcsolódás a Windows rendszerű virtuális asztalhoz a webes ügyfélprogram használatával.
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: b17ec374f2e8b6e268d993fc9a7b14ebeebd40df
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447895"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Kapcsolódás a Windows rendszerű virtuális asztalhoz a webes ügyféllel

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/connect-web-2019.md).

A webes ügyfél lehetővé teszi a Windows rendszerű virtuális asztali erőforrások elérését egy webböngészőből a hosszadalmas telepítési folyamat nélkül.

>[!NOTE]
>A webes ügyfél jelenleg nem rendelkezik a Mobile OS-támogatással.

## <a name="supported-operating-systems-and-browsers"></a>Támogatott operációs rendszerek és böngészők

Habár minden HTML5-kompatibilis böngészőnek működnie kell, a következő operációs rendszereket és böngészőket hivatalosan támogatjuk.

| Böngésző           | Támogatott operációs rendszer                     | Jegyzetek               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | 11-es vagy újabb verzió |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | 55-es vagy újabb verzió |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Távoli erőforrások hírcsatornájának elérése

Egy böngészőben nyissa meg a Windows rendszerű virtuális asztali webes ügyfélprogram Azure Resource Manager-integrált verzióját, <https://rdweb.wvd.microsoft.com/arm/webclient> és jelentkezzen be a felhasználói fiókjával.

>[!NOTE]
>Ha Azure Resource Manager integráció nélkül használja a Windows rendszerű virtuális asztalt (klasszikus), akkor ehelyett kapcsolódjon az erőforrásokhoz <https://rdweb.wvd.microsoft.com/webclient> .
>
> Ha a US Gov portált használja, használja a t <https://rdweb.wvd.azure.us/arm/webclient/index.html> .

>[!NOTE]
>Ha már bejelentkezett egy másik Azure Active Directory fiókkal, mint amelyet a Windowsos virtuális asztalhoz kíván használni, akkor ki kell jelentkeznie, vagy egy privát böngészőablakot kell használnia.

A bejelentkezést követően meg kell jelennie az erőforrások listájának. Az erőforrásokat úgy is elindíthatja, ha a **minden erőforrás** lapon a normál alkalmazást szeretné.

## <a name="using-an-input-method-editor"></a>A bemeneti módszer szerkesztőjének használata

A webes ügyfél a **1.0.21.16 vagy újabb** verzióban a távoli munkamenetben támogatja a bemeneti metódus-szerkesztő (IME) használatát. A távoli munkamenetben használni kívánt billentyűzet nyelvi csomagját telepíteni kell a gazdagép virtuális gépre. Ha többet szeretne megtudni a nyelvi csomagok távoli munkamenetben való beállításáról, tekintse meg a [nyelvi csomagok hozzáadása Windows 10-es többmunkamenetes rendszerképhez](language-packs.md)című részt.

IME-bemenet engedélyezése a webes ügyfél használatával:

1. A távoli munkamenethez való csatlakozás előtt lépjen a webes ügyfél **beállításai** panelre.

2. Állítsa **be az** **Input Method Editor engedélyezése** beállítást be értékre.

3. A legördülő menüben válassza ki a távoli munkamenetben használni kívánt billentyűzetet.

4. Kapcsolódjon a távoli munkamenethez.

A webes ügyfél letiltja a helyi ÍRÁSJEGYBEVIVŐ ablakát, amikor a távoli munkamenetre fókuszál. Ha már csatlakozott a távoli munkamenethez, az IME beállításainak módosítása nem lesz hatással.

>[!NOTE]
>Ha a nyelvi csomag nincs telepítve a gazdagép virtuális gépén, a távoli munkamenet alapértelmezett értéke az angol (Egyesült Államok) billentyűzet lesz.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a webes ügyfél használatáról, tekintse meg [a webes ügyfélprogram használatának első lépéseit](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).

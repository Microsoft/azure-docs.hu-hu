---
title: Windows rendszerű virtuális asztali (klasszikus) webes ügyfél összekötése – Azure
description: Kapcsolódás a Windows rendszerű virtuális asztalhoz (klasszikus) a webes ügyfélprogram használatával.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c8a55e35ba89caefc362024fc871ac5eb800447b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008423"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-web-client"></a>Kapcsolódás a Windows rendszerű virtuális asztalhoz (klasszikus) a webes ügyféllel

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat. Ha Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../connect-web.md).

A webes ügyfél lehetővé teszi a Windows rendszerű virtuális asztali erőforrások elérését egy webböngészőből a hosszadalmas telepítési folyamat nélkül.

>[!NOTE]
>A webes ügyfél jelenleg nem rendelkezik a Mobile OS-támogatással.

## <a name="supported-operating-systems-and-browsers"></a>Támogatott operációs rendszerek és böngészők

Habár minden HTML5-kompatibilis böngészőnek működnie kell, a következő operációs rendszereket és böngészőket hivatalosan támogatjuk.

| Böngésző           | Támogatott operációs rendszer                     | Jegyzetek               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | 55-es vagy újabb verzió |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Távoli erőforrások hírcsatornájának elérése

Egy böngészőben nyissa meg a Windows rendszerű virtuális asztali webes ügyfelet, <https://rdweb.wvd.microsoft.com/webclient> és jelentkezzen be a felhasználói fiókjával.

>[!NOTE]
>Ha Azure Resource Manager-integrációval rendelkező Windows rendszerű virtuális asztalt használ, ehelyett kapcsolódjon az erőforrásokhoz <https://rdweb.wvd.microsoft.com/arm/webclient> .

>[!NOTE]
>Ha már bejelentkezett egy másik Azure Active Directory fiókkal, mint amelyet a Windowsos virtuális asztalhoz kíván használni, akkor ki kell jelentkeznie, vagy egy privát böngészőablakot kell használnia.

A bejelentkezést követően meg kell jelennie az erőforrások listájának. Az erőforrásokat úgy is elindíthatja, ha a **minden erőforrás** lapon a normál alkalmazást szeretné.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a webes ügyfél használatáról, tekintse meg [a webes ügyfélprogram használatának első lépéseit](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).

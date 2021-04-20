---
title: Csatlakozás Windows Virtual Desktop webes ügyféllel – Azure
description: Csatlakozás a Windows Virtual Desktop a webes ügyféllel.
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: 80a090abee45f9cb3ec6ee5406aad6abf1d73a59
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725007"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Csatlakozás Windows Virtual Desktop a webes ügyféllel

>[!IMPORTANT]
>Ez a tartalom a Windows Virtual Desktop való Azure Resource Manager Windows Virtual Desktop vonatkozik. Ha a klasszikus Windows Virtual Desktop (klasszikus) Azure Resource Manager használ, tekintse meg [ezt a cikket.](./virtual-desktop-fall-2019/connect-web-2019.md)

A webes ügyféllel hosszadalmas telepítési folyamat Windows Virtual Desktop böngészőből férhet hozzá az erőforrásokhoz.

>[!NOTE]
>A webes ügyfél jelenleg nem támogatja a mobil operációs rendszereket.

## <a name="supported-operating-systems-and-browsers"></a>Támogatott operációs rendszerek és böngészők

Bár minden HTML5-kompatibilis böngésző működik, hivatalosan az alábbi operációs rendszereket és böngészőket támogatjuk.

| Böngésző           | Támogatott operációs rendszer                     | Jegyzetek               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | 11-es vagy újabb verzió |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | 55-ös vagy újabb verzió |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Távoli erőforrások elérése adatcsatorna

Egy böngészőben nyissa meg a Azure Resource Manager integrált verzióját a Windows Virtual Desktop a webhelyen, és jelentkezzen be <https://rdweb.wvd.microsoft.com/arm/webclient> a felhasználói fiókjával.

>[!NOTE]
>Ha integráció nélkül használ Windows Virtual Desktop (klasszikus) Azure Resource Manager, kapcsolódhat az <https://rdweb.wvd.microsoft.com/webclient> erőforrásokhoz a-hez a ehelyett.
>
> Ha a portált használja US Gov a <https://rdweb.wvd.azure.us/arm/webclient/index.html> következőt: .

>[!NOTE]
>Ha már bejelentkezett egy másik Azure Active Directory-fiókkal, mint a Windows Virtual Desktop-hoz használni kívánt fiók, jelentkezzen ki, vagy használjon privát böngészőablakot.

A bejelentkezés után megjelenik az erőforrások listája. Az erőforrásokat úgy indíthatja el, hogy kiválasztja őket, mint egy normál alkalmazást a **Minden erőforrás lapon.**

## <a name="next-steps"></a>Következő lépések

A webes ügyfél használatával kapcsolatos további információkért tekintse meg a Webes ügyfél [használatának első lépésekét.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)

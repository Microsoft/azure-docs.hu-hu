---
title: A Windows rendszerű virtuális asztali MSIX alkalmazás csatolásának áttekintése – Azure
description: Mit jelent az MSIX-alkalmazás csatolása? Ismerje meg ezt a cikket.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: cdbb7574f9da5ea6cd0695b74c269d8e1b88b7f4
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444071"
---
# <a name="what-is-msix-app-attach"></a>Mit jelent az MSIX-alkalmazás csatolása?

A MSIX egy új csomagolási formátum, amely számos funkciót kínál, amelyek célja az összes Windows-alkalmazás csomagolási élményének javítása. A MSIX kapcsolatos további tudnivalókért tekintse meg a [MSIX áttekintését](/windows/msix/overview).

A MSIX alkalmazás a MSIX-alkalmazások fizikai és virtuális gépekre történő továbbítására is lehetőséget biztosít. Azonban a MSIX-alkalmazás csatolása eltér a normál MSIX, mivel ez különösen a Windows rendszerű virtuális asztali környezethez készült. Ez a cikk leírja, hogy mi a MSIX-alkalmazás, és mit tehet az Ön számára.

## <a name="application-delivery-options-in-windows-virtual-desktop"></a>Alkalmazások kézbesítési lehetőségei a Windows rendszerű virtuális asztalon

Az alkalmazásokat a következő módszerek egyikével teheti elérhetővé a Windows rendszerű virtuális asztalon:

- Alkalmazások üzembe helyezése egy fő rendszerképben
- Eszközök, például a SCCM vagy az Intune használata a központi felügyelethez
- Dinamikus alkalmazások kiépítés (AppV, VMWare AppVolumes vagy Citrix AppLayering)
- Egyéni eszközök vagy parancsfájlok létrehozása a Microsoft és harmadik féltől származó eszköz használatával

## <a name="what-does-msix-app-attach-do"></a>Mit jelent a MSIX-alkalmazás csatolása?

A Windows rendszerű virtuális asztali környezetben a MSIX-alkalmazás a következőket teheti:

- Hozzon létre elkülönítést a felhasználói, az operációs rendszer és az alkalmazások között [MSIX-tárolók](/windows/msix/msix-container)használatával.
- Távolítsa el az újracsomagolás szükségességét az alkalmazások dinamikus kézbesítése során.
- Csökkentse a felhasználónak a bejelentkezéshez szükséges időt.
- Csökkentse az infrastruktúra követelményeit és költségeit.

A MSIX-alkalmazás csatolását VDI vagy SBC-n kívül kell megadni.

## <a name="traditional-app-layering-compared-to-msix-app-attach"></a>Hagyományos alkalmazás-réteg a MSIX-alkalmazáshoz képest

A következő táblázat összehasonlítja a MSIX-alkalmazások csatolásának és az alkalmazások rétegződésének főbb funkcióit.

| Szolgáltatás | Hagyományos alkalmazás-réteg  | MSIX-alkalmazás csatolása  |
|-----|-----------------------------|--------------------|
| Formátum               | Az alkalmazás-réteg különböző technológiáinak különböző tulajdonosi formátumokra van szükségük. | A natív MSIX-csomagolási formátummal működik.        |
| Terhelés újracsomagolása | A szabadalmaztatott formátumokhoz frissítés szükséges az előkészítéshez és az újracsomagoláshoz.         | A MSIX-ként közzétett alkalmazások nem igényelnek újracsomagolást. Ha azonban a MSIX csomag nem érhető el, az újracsomagolási terhelés továbbra is érvényes lesz. |
| Ökoszisztéma            | N/A (például a szállítók nem szállítanak App-V-t)  | A MSIX a Microsoft mainstream technológiája, amely a legfontosabb ISV-partnerek és a házon belüli alkalmazások (például az Office) bevezetése. A MSIX-t a virtuális asztalokon és a fizikai Windows rendszerű számítógépeken is használhatja. |
| Infrastruktúra       | További infrastruktúra szükséges (kiszolgálók, ügyfelek stb.) | Csak tárhely   |
| Felügyelet       | Karbantartást és frissítést igényel   | Egyszerűsíti az alkalmazások frissítéseit |
| Felhasználó felület      | A felhasználó bejelentkezési idejét befolyásolja. A határ az operációs rendszer állapota, az alkalmazás állapota és a felhasználói adat között van.  | A továbbított alkalmazások nem különböztethetők meg a helyileg telepített alkalmazásokból. |

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a MSIX-alkalmazás csatolásáról, tekintse meg a [szószedetet](app-attach-glossary.md) és a [gyakori kérdéseket](app-attach-faq.md). Ellenkező esetben Ismerkedjen [meg az alkalmazások csatlakoztatásának beállítása](app-attach.md)című lépéssel.

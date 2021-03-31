---
title: Microsoft Threat Modeling Tool kiadás 1/29/2019
titleSuffix: Azure
description: Olvassa el a 1/29/2019-on közzétett Microsoft Threat Modeling Tool kibocsátási megjegyzéseit. A megjegyzések közé tartoznak a funkciók változásai és az ismert problémák.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/25/2019
ms.openlocfilehash: 17147d412bd888cdd3cd270829ad6d6103867b34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87539050"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>A fenyegetésmodellezési eszköz frissített kiadása: 7.1.60126.1 – 1/29/2019

A Microsoft Threat Modeling Tool verziójának 7.1.60126.1 január 29 2019-én adták ki, és a következő módosításokat tartalmazza:

- A .NET-es verzió minimálisan szükséges verziója a [.net-4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262)lett megnövelve.
- A minimálisan szükséges Windows-verzió a .NET-függőség miatt megnőtt a [Windows 10 évfordulós frissítésére](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) .
- Egy modell-ellenőrzési váltógomb lett hozzáadva az eszköz beállítások menüjéhez.
- A fenyegetés tulajdonságai között több hivatkozás is frissült.
- Másodlagos UX-módosítások az eszköz kezdőképernyő képernyőjén.
- A Threat Modeling Tool mostantól örökli a gazda operációs rendszer TLS-beállításait, és olyan környezetekben támogatott, amelyeken TLS 1,2 vagy újabb rendszer szükséges.

## <a name="feature-changes"></a>Szolgáltatások módosításai

### <a name="model-validation-option"></a>Modell-ellenőrzési lehetőség

Az ügyfelek visszajelzései alapján a modell érvényesítésének engedélyezéséhez vagy letiltásához lehetőség lett hozzáadva az eszközhöz. Korábban, ha a sablon egyetlen egyirányú adatfolyamatot használt két objektum között, előfordulhat, hogy hibaüzenetet kapott az üzenetek keretében: a ObjectsName használatához legalább egy "any" érték szükséges. A modell érvényesítésének letiltása megakadályozza, hogy ezek a figyelmeztetések a nézetben legyenek megjelenítve.

A modell-ellenőrzés be-és kikapcsolásának lehetősége a file->Settings->Options menüben található. A beállítás alapértelmezett értéke le van tiltva.

![Modell-ellenőrzési lehetőség](./media/threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>System requirements (Rendszerkövetelmények)

- Támogatott operációs rendszerek
  - [Microsoft Windows 10 évfordulós frissítés](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) vagy újabb
- .NET-verzió szükséges
  - [.Net-4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) vagy újabb
- További követelmények
  - Az eszköz és a sablonok frissítéseinek fogadásához internetkapcsolat szükséges.

## <a name="known-issues"></a>Ismert problémák

### <a name="unsupported-systems"></a>Nem támogatott rendszerek

#### <a name="issue"></a>Probléma

A .NET 4.7.1 vagy újabb rendszerű Windows 10-es rendszerek felhasználói, például a Windows 10 Enterprise LTSB (1507-es verzió) nem tudják megnyitni az eszközt a frissítés után. A Windows régebbi verziói már nem támogatott platformok a Threat Modeling Tool számára, és nem kell telepíteni a legújabb frissítést.

#### <a name="workaround"></a>Áthidaló megoldás

A Windows 10 Enterprise LTSB (1507-es verzió) azon felhasználói, akik telepítették a legújabb frissítést, visszaállhatnak a Threat Modeling Tool korábbi verziójára az alkalmazások & funkcióinak eltávolítási párbeszédpanelén.

## <a name="documentation-and-feedback"></a>Dokumentáció és visszajelzés

- A Threat Modeling Tool dokumentációja a [docs.microsoft.com](threat-modeling-tool.md)található, és [az eszköz használatával](threat-modeling-tool-getting-started.md)kapcsolatos információkat tartalmaz.

## <a name="next-steps"></a>Következő lépések

Töltse le a [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)legújabb verzióját.

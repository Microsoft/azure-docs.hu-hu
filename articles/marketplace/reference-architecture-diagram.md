---
title: Hivatkozási architektúra diagramja | Azure piactér
description: A Microsoft kereskedelmi piactéren elérhető ajánlat hivatkozási architektúra diagramjának létrehozása.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: stmummer
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 02/18/2021
ms.openlocfilehash: 66e4d498ff7584188d680e35c89c6f10cc43c9cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604491"
---
# <a name="reference-architecture-diagram"></a>Hivatkozási architektúra diagramja

A hivatkozási architektúra diagram egy olyan modell, amely az Ön által kínált infrastruktúrát jelképezi. Az Azure IP-megoldások esetében a diagramnak azt is be kell mutatnia, hogy az ajánlat hogyan használja a Microsoft felhőalapú szolgáltatásait az IP-címek közös értékesítésének technikai követelményei alapján. Az architektúra minőségének felmérése nem ajánlott. Úgy tervezték, hogy a megoldás hogyan használja a Microsoft-szolgáltatásokat.

A hivatkozási architektúra diagramja több eszköz használatával is létrehozható. Javasoljuk, hogy a Microsoft Visio alkalmazáshoz több, az Azure Architecture-modelleket ábrázoló rajzsablon is tartozik.

A hivatkozási architektúra diagramjainak kiépítéséhez hasznos kiindulópont az Azure- [architektúra modelljeinek](/azure/architecture/browse/)kihasználása.

## <a name="typical-components-of-a-reference-architecture-diagram"></a>A hivatkozási architektúra diagramjának jellemző összetevői

- Felhőalapú szolgáltatások, amelyek az ajánlatát üzemeltetik és használják, beleértve az Azure-erőforrásokat is
- Az ajánlat által felhasznált adatkapcsolatok, adatrétegek és adatszolgáltatások
- A biztonság, a hitelesítés és az ajánlat felhasználóinak szabályozására használt felhőalapú szolgáltatások
- Felhasználói felületek és más olyan szolgáltatások, amelyek az ajánlatot teszik elérhetővé a felhasználóknak
- Hibrid vagy helyszíni kapcsolatok és integrációk, illetve a kettő kombinációja

Ez a képernyőkép a hivatkozási architektúra diagramjának példáját mutatja be.

[![Ez a kép egy példa közös értékesítésű architektúra-diagram.](./media/co-sell/co-sell-arch-diagram.png)](./media/co-sell/co-sell-arch-diagram.png#lightbox)

Ez a példa egy vertikális iparági Csevegőrobot, amely integrálható az intranetes helyekkel, így segítve az előrejelzési igényeket a gépi tanulási algoritmus segítségével. Ez a megoldás az ellátási láncot és a gyártási ütemterv adatait használja különböző ERP-rendszerekből. A robot úgy van kialakítva, hogy kérdéseket tegyen fel, amikor egy értékesítő véglegesítheti a megrendelés lehetséges kézbesítési dátumát.

## <a name="next-steps"></a>Következő lépések

- [Közös értékesítés konfigurálása kereskedelmi Piactéri ajánlathoz](commercial-marketplace-co-sell.md)

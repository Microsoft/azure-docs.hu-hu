---
title: Azure Functions-futtatókörnyezet áttekintése
description: A Azure Functions-futtatókörnyezet előzetes verziójának áttekintése
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 61e3b82e497afcdc8239a9f4fda3e4f739166a1f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92108439"
---
# <a name="azure-functions-runtime-overview-preview"></a>Azure Functions-futtatókörnyezet áttekintése (előzetes verzió)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

A Azure Functions-futtatókörnyezet (előzetes verzió) új módszert kínál a Azure Functions programozási modell egyszerűségének és rugalmasságának kihasználására a helyszínen. A Azure Functionsra épülő, ugyanazon a nyílt forráskódú gyökereken alapuló Azure Functions-futtatókörnyezet üzembe helyezése a helyszínen történik, hogy közel azonos fejlesztési élményt nyújtson a Cloud Service-nek.

![Azure Functions-futtatókörnyezet betekintő portál][1]

A Azure Functions-futtatókörnyezet a felhőbe való véglegesítés előtt lehetővé teszi a Azure Functions élményét. Így a létrehozott kód eszközei a felhőbe való áttelepítéskor is megadhatók.  A futtatókörnyezet emellett új lehetőségeket is nyit meg, például a helyszíni számítógépek tartalék számítási teljesítményének használatával, hogy egy egynapos kötegelt feldolgozást futtasson. A szervezeten belüli eszközöket is használhatja arra, hogy a helyszíni és a felhőben egyaránt más rendszerekre is küldje az adatküldés feltételeit.

A Azure Functions-futtatókörnyezet két darabból áll:

* Felügyeleti szerepkör Azure Functions-futtatókörnyezet
* Feldolgozói szerepkör Azure Functions-futtatókörnyezet

## <a name="azure-functions-management-role"></a>Felügyeleti szerepkör Azure Functions

A Azure Functions felügyeleti szerepkör egy gazdagépet biztosít a helyszíni függvények felügyeletéhez. Ez a szerepkör a következő feladatokat hajtja végre:

* A Azure Functions felügyeleti portál, amely ugyanaz, mint amit a [Azure Portal](https://portal.azure.com)láthat. A portál egységes felhasználói élményt nyújt, amellyel ugyanúgy fejlesztheti a függvényeket, mint a Azure Portal.
* A függvények elosztása több functions-feldolgozón keresztül.
* Közzétételi végpont biztosítása, hogy a közzétételi profil letöltésével és importálásával közvetlenül a Microsoft Visual studióból teheti közzé a funkcióit.

## <a name="azure-functions-worker-role"></a>Feldolgozói szerepkör Azure Functions

A Azure Functions feldolgozói szerepkörök Windows-tárolókban vannak telepítve, és a függvény kódját hajtja végre.  A szervezeten belül több feldolgozói szerepkört is üzembe helyezhet, és ez a lehetőség olyan kulcsfontosságú módszer, amelyben az ügyfelek igénybe vehetik a tartalék számítási teljesítményt.  Az egyik példa arra, hogy hol található a tartalék számítási szolgáltatás számos szervezeten belül, és a gépeket folyamatosan, de nagy ideig nem használják.

## <a name="minimum-requirements"></a>Minimális követelmények

A Azure Functions-futtatókörnyezet első lépéseihez rendelkeznie kell egy, a SQL Server-példányhoz hozzáféréssel rendelkező Windows Server 2016 vagy Windows 10 alkotói frissítéssel rendelkező géppel.

## <a name="next-steps"></a>Következő lépések

A [Azure functions-futtatókörnyezet előzetes](./functions-runtime-install.md) verziójának telepítése

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
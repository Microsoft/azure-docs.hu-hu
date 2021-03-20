---
title: A tanácsadási szolgáltatás díjszabásának és rendelkezésre állásának konfigurálása a Microsoft partner Centerben
description: Megtudhatja, hogyan konfigurálhatja a tanácsadási szolgáltatást a Microsoft kereskedelmi piactéren a partner Center használatával, az árak részleteit és a piacra való rendelkezésre állást.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: 6b386238bd759714bc0c8ad81d67c29aa1774aba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96780229"
---
# <a name="how-to-configure-your-consulting-service-pricing-and-availability"></a>A tanácsadási szolgáltatás díjszabásának és rendelkezésre állásának konfigurálása

Ez a cikk azt ismerteti, hogyan határozható meg a piac rendelkezésre állása és a tanácsadási szolgáltatás ajánlatának díjszabása a Microsoft kereskedelmi piactéren.

> [!NOTE]
> A tanácsadási szolgáltatás ajánlata csak listázást biztosít. Minden tranzakciót a kereskedelmi piactéren kívülről Önnek és ügyfeleinek kell kezelnie.

## <a name="markets"></a>Piacok

A **piacok** szakaszban válassza ki azokat az országokat vagy régiókat, ahol a tanácsadási szolgáltatás elérhető lesz.

1. A **piacok** területen válassza a **piacok szerkesztése** hivatkozást.
2. A megjelenő párbeszédpanelen válassza ki azokat a piacokat, amelyeken elérhetővé szeretné tenni az ajánlatát. Legalább egy 141-es piacot ki kell választania.
3. A párbeszédpanel bezárásához kattintson a **Mentés** gombra.

## <a name="preview-audience"></a>Előnézet célközönsége

Ha közzéteszi vagy frissíti a tanácsadási szolgáltatás ajánlatát, a partner Center létrehoz egy előzetes verziót a listáról. Ez az előzetes verzió csak azoknál a felhasználóknál látható, akik rendelkeznek **elrejtési kulccsal**.

A **kulcs elrejtése** mezőbe írjon be egy alfanumerikus karakterláncot, amelyet az ajánlat előzetes verziójának eléréséhez fog használni.

## <a name="pricing-informational-only"></a>Díjszabás (csak tájékoztató)

A **díjszabás** szakaszban adja meg, hogy ez egy ingyenes vagy fizetős ajánlat.

Fizetős ajánlatok esetén határozza meg, hogy az ár rögzített vagy becsült. Ha az árat becsültük, az ajánlat leírásában meg kell határozni, hogy milyen tényezők befolyásolják a díjszabást.

Ha a **piacok** szakaszban egyetlen országot vagy régiót választ, adja meg az árat a piac által támogatott pénznemben, majd válassza a **Piszkozat mentése** lehetőséget. A támogatott pénznemek listájáért tekintse meg [a kereskedelmi piactér földrajzi elérhetőségét és a pénznemek támogatását](./marketplace-geo-availability-currencies.md) ismertető témakört.

Ha több országot vagy régiót választ a **piacok** szakaszban, adja meg az árat Egyesült Államok dollárban (USD), majd válassza a **Piszkozat mentése** lehetőséget. A partner Center ezt az árat az összes kiválasztott piac helyi pénznemére alakítja át a Piszkozat mentésekor elérhető Exchange-díjszabás használatával.

Az átalakítás érvényesítéséhez, illetve az egyéni árak egyéni piacon történő beállításához exportálnia, módosítania és importálnia kell a díjszabási táblázatot:

1. A **díjszabás** területen válassza ki az **exportálási díjszabási** hivatkozást. Ezzel letölt egy fájlt az eszközére.
1. Nyissa meg a exportedPrice.xlsx fájlt a Microsoft Excelben.
1. A számolótáblában az egyes piacok árait és pénznemeit módosíthatja. A támogatott pénznemek listájáért tekintse meg [a kereskedelmi piactér földrajzi elérhetőségét és a pénznemek támogatását](./marketplace-geo-availability-currencies.md) ismertető témakört. Ha elkészült, mentse a fájlt.
1. A partner Center **díjszabás** területén válassza a **díjszabási adat importálása** hivatkozást. A fájl importálásakor a rendszer felülírja a korábbi díjszabási információkat.

> [!IMPORTANT]
> A partner Centerben definiált árak statikusak, és nem követik az Exchange-díjszabás variációit. Ha a közzététel után egy vagy több piacon szeretné módosítani az árat, frissítse és küldje el újra az ajánlatot a partner Centerben.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

* [Felülvizsgálat és közzététel](review-publish-offer.md)
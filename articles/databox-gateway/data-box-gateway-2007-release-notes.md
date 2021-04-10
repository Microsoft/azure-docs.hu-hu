---
title: Azure Stack Edge & Azure Data Box Gateway 2007 kibocsátási megjegyzések | Microsoft Docs
description: Az Azure Stack Edge és a Data Box Gateway 2007-es verziójának futtatásával kapcsolatos kritikus nyitott problémákat és megoldásokat ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 5dd835c99f5781b3734983ea64709535a75e1fa8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582196"
---
# <a name="azure-stack-edge-and-azure-data-box-gateway-2007-release-notes"></a>Azure Stack Edge és Azure Data Box Gateway 2007 kibocsátási megjegyzései

A következő kibocsátási megjegyzések azonosítják a kritikus nyitott problémákat, valamint az Azure Stack Edge és Data Box Gateway 2007-es kiadásával kapcsolatos megoldott problémákat.

A kibocsátási megjegyzések folyamatosan frissülnek, és olyan kritikus fontosságú problémák észlelhetők, amelyek megkerülő megoldást igényelnek. A Azure Stack Edge/Data Box Gateway üzembe helyezése előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

Ez a kiadás megfelel a szoftver verzióinak:

- **Azure stack Edge 2007 (1.6.1280.1667)** – kb 4566549
- **Data Box Gateway 2007 (1.6.1280.1667)** – kb 4566550

> [!NOTE]
> Az 2007-es frissítés csak az összes olyan eszközre alkalmazható, amely a szoftver általánosan elérhető (GA) verzióját futtatja.

## <a name="whats-new"></a>Újdonságok

Ez a kiadás a következő hibajavítást tartalmazza:

- **Probléma feltöltése** – ez a kiadás javítja a feltöltési problémát, ha a hibák miatti újraindítások lelassítják a feltöltés befejezési sebességét. Ez a probléma akkor fordulhat elő, ha olyan adatkészletet tölt fel, amely elsősorban a rendelkezésre álló sávszélességhez képest nagy méretű fájlokból áll, különösen, de nem kizárólagosan, amikor a sávszélesség-szabályozás aktív. Ez a változás biztosítja, hogy elegendő lehetőség legyen a feltöltés befejezésére az adott fájl feltöltésének újraindítása előtt.

Ez a kiadás a következő frissítéseket is tartalmazza:

- A Windows VHD alaprendszerképének frissítése megtörtént.
- A rendszer az összes kumulatív Windows-frissítést és a .NET-keretrendszer frissítését is tartalmazza, amelyek a május 2020-ig lettek közzétéve.
- Ez a kiadás támogatja IoT Edge 1.0.9.3 Azure Stack Edge-eszközökön.

## <a name="known-issues-in-this-release"></a>Az ebben a kiadásban ismert problémák

Ebben a kiadásban nem jelennek meg új problémák a kiadásban. Az összes megjelent kiadási probléma az előző kiadásokból lett végrehajtva. Ha meg szeretné tekinteni az ismert problémák listáját, lépjen [a ga kiadás ismert problémáira](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Következő lépések

- [Felkészülés az Azure Stack Edge üzembe helyezésére](../databox-online/azure-stack-edge-deploy-prep.md)
- [Az Azure Data Box Gateway üzembe helyezésének előkészítése](data-box-gateway-deploy-prep.md)

---
title: Azure Data Box Gateway 2101 kibocsátási megjegyzések | Microsoft Docs
description: Ismerteti a 2101-es kiadást futtató Azure Data Box Gateway kritikusan nyitott problémáit és megoldásait.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/29/2021
ms.author: alkohli
ms.openlocfilehash: 510f2677673363791ab5eb0f2c4dbcd25b697934
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99072639"
---
# <a name="azure-data-box-gateway-2101-release-notes"></a>Azure Data Box Gateway 2101 kibocsátási megjegyzései

A következő kibocsátási megjegyzések azonosítják a kritikus nyitott problémákat és a Azure Data Box Gateway 2101-es kiadásával kapcsolatos megoldott problémákat.

A kibocsátási megjegyzések folyamatosan frissülnek. A megkerülő megoldással kapcsolatos kritikus problémák észlelése esetén a rendszer hozzáadja azokat. A Azure Data Box Gateway üzembe helyezése előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.  

Ez a kiadás megfelel a szoftver verzióinak:

- **Data Box Gateway 2101 (1.6.1475.2528)** – kb 4603462

> [!NOTE]
> Az 2101-es frissítés csak az összes olyan eszközre alkalmazható, amely a szoftver általánosan elérhető (GA) verzióját futtatja.

## <a name="whats-new"></a>Újdonságok

Ez a kiadás a következő hibajavítást tartalmazza:

- **Probléma feltöltése** – ez a kiadás javít egy feltöltési problémát, ahol a feltöltések újraindulnak, mert a hibák miatt a feltöltési folyamat lassú lehet. Ez a probléma akkor fordulhat elő, ha olyan adatkészletet tölt fel, amely elsősorban a rendelkezésre álló sávszélességhez képest nagy méretű fájlokból áll, különösen, de nem kizárólagosan, amikor a sávszélesség-szabályozás aktív. Ez a módosítás elegendő lehetőséget biztosít a feltöltés befejezésére az adott fájl feltöltésének újraindítása előtt.

Ez a kiadás a következő frissítéseket is tartalmazza:

- Az összes kumulatív Windows-frissítés és a .NET-keretrendszer frissítései a 2020-es verzióban jelennek meg.
- A Azure Data Box Gateway statikus IP-címét a rendszer megőrzi a szoftverfrissítések között.

## <a name="known-issues-in-this-release"></a>Az ebben a kiadásban ismert problémák

Ebben a kiadásban nem jelennek meg új problémák a kiadásban. Az összes megjelent kiadási probléma az előző kiadásokból lett végrehajtva. Ha meg szeretné tekinteni az ismert problémák listáját, lépjen [a ga kiadás ismert problémáira](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Következő lépések

- [Az Azure Data Box Gateway üzembe helyezésének előkészítése](data-box-gateway-deploy-prep.md)

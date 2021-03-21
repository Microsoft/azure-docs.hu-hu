---
title: Azure Stack Edge Pro a FPGA 2101 kibocsátási megjegyzésekkel | Microsoft Docs
description: Az Azure Stack Edge 2101 kiadásának kritikusan nyitott problémáit és megoldásait ismerteti.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/01/2021
ms.author: alkohli
ms.openlocfilehash: 5c95da8d6ee88786ff983b4963c1cb96394886cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727549"
---
# <a name="azure-stack-edge-pro-with-fpga-2101-release-notes"></a>Azure Stack Edge Pro a FPGA 2101 kibocsátási megjegyzésekkel

A következő kibocsátási megjegyzések azonosítják a kritikus nyitott problémákat, valamint a Azure Stack Edge Pro 2101-es kiadásának feloldott hibáit egy beépített mezővel (FPGA).

A kibocsátási megjegyzések folyamatosan frissülnek. A megkerülő megoldással kapcsolatos kritikus problémák észlelése esetén a rendszer hozzáadja azokat. Az Azure Stack Edge-eszköz üzembe helyezése előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.  

Ez a kiadás a szoftver verziószámának felel meg:

- **Azure stack Edge 2101 (1.6.1475.2528)** – kb 4599267

> [!NOTE]
> Az 2101-es frissítés csak olyan eszközökre alkalmazható, amelyeken a szoftver általánosan elérhető (GA) verzióját futtatják vagy később.

## <a name="whats-new"></a>Újdonságok

Ez a kiadás a következő hibajavítást tartalmazza:

- **Probléma feltöltése** – ez a kiadás javít egy feltöltési problémát, ahol a sikertelen feltöltési újraindítások lassítják a feltöltés befejezési sebességét. Ez a probléma akkor fordulhat elő, ha olyan adatkészletet tölt fel, amely elsődlegesen a rendelkezésre álló sávszélességhez képest nagy mennyiségű fájlból áll, különösen a sávszélesség-szabályozás aktív időpontjában. Ez a módosítás elegendő lehetőséget biztosít a feltöltés befejezésére az adott fájl feltöltésének újraindítása előtt.

Ez a kiadás a következő frissítéseket is tartalmazza:

- Az összes kumulatív Windows-frissítés és a .NET-keretrendszer frissítései a 2020-es verzióban jelennek meg.
- A alaplapi felügyeleti vezérlő (BMC) belső vezérlőprogram-verziója a 3.32.32.32-ről a 3.36.36.36-re frissül a gyári telepítés során, hogy az újabb Dell tápegységekkel való inkompatibilitást foglalkozzon.
- Ez a kiadás támogatja IoT Edge 1.0.9.3 Azure Stack Edge-eszközökön.

## <a name="known-issues-in-this-release"></a>Az ebben a kiadásban ismert problémák

Ebben a kiadásban nem jelennek meg új problémák a kiadásban. Az összes megjelent kiadási probléma az előző kiadásokból lett végrehajtva. Ha meg szeretné tekinteni az ismert problémák listáját, lépjen [a ga kiadás ismert problémáira](../databox-gateway/data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Következő lépések

- [Felkészülés az Azure Stack Edge üzembe helyezésére](../databox-online/azure-stack-edge-deploy-prep.md)
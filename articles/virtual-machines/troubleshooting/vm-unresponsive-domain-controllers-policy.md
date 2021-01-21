---
title: A virtuális gép nem válaszol az alapértelmezett tartományvezérlő-szabályzat alkalmazása közben
titlesuffix: Azure Virtual Machines
description: Ez a cikk olyan problémák megoldását ismerteti, amelyekben az Alapértelmezett tartományvezérlői házirend megakadályozza az Azure-beli virtuális gépek indítását.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 34e6b765-3496-46a1-b7d7-6def00884394
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: v-miegge
ms.openlocfilehash: 6c139398182ca9d875de0d3b21c58afe503bd8a5
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632274"
---
# <a name="vm-is-unresponsive-while-applying-default-domain-controllers-policy"></a>A virtuális gép nem válaszol az alapértelmezett tartományvezérlő-szabályzat alkalmazása közben

Ez a cikk olyan problémák megoldását ismerteti, amelyekben az Alapértelmezett tartományvezérlői házirend megakadályozza egy Azure-beli virtuális gép (VM) indítását.

## <a name="symptom"></a>Hibajelenség

Ha [rendszerindítási diagnosztikát](./boot-diagnostics.md) használ a virtuális gép képernyőképének megtekintéséhez, látni fogja, hogy a képernyőképen az operációs rendszer nem válaszol, miközben az **Alapértelmezett tartományvezérlői házirendet** indítja.

  ![Az 1. ábra a "Default Domain Controllers Policy" üzenettel beragadt operációs rendszert jeleníti meg.](./media/vm-unresponsive-domain-controllers-policy/1-default-domain-controllers-policy.png)

## <a name="cause"></a>Ok

Ezt a problémát az Alapértelmezett tartományvezérlői házirendben történt legutóbbi módosítások okozhatja. Ellenkező esetben a rendszer elvégzi a memóriakép-fájlok elemzését a kiváltó ok okának megállapításához.

## <a name="solution"></a>Megoldás

> [!TIP]
> Ha a virtuális gép nemrég készült biztonsági másolattal rendelkezik, a rendszerindítási probléma megoldásához próbálja meg [visszaállítani a virtuális gépet a biztonsági mentésből](../../backup/backup-azure-arm-restore-vms.md) .

Ha nemrég módosította az Alapértelmezett tartományvezérlői házirendet, előfordulhat, hogy vissza kívánja vonni ezeket a módosításokat a probléma megoldásához. Ha nem biztos benne, hogy mi okozza a problémát, gyűjtsön egy memóriaképet, majd küldje el a támogatási jegyet.

### <a name="collect-the-memory-dump-file"></a>A memóriakép fájljának összegyűjtése

A probléma megoldásához először össze kell gyűjtenie az összeomláshoz tartozó memóriakép-fájlt, majd kapcsolatba kell lépnie a támogatási szolgálattal a memóriakép fájljával. A memóriakép-fájl összegyűjtéséhez kövesse az alábbi lépéseket:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Az operációsrendszer-lemez csatlakoztatása egy új javítási virtuális géphez

1. A virtuálisgép- [javítási parancsok](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) 1-3-es lépéseit használva készítse elő a javítási virtuális gépet.

1. A Távoli asztali kapcsolat kapcsolódása a javítási virtuális géphez.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>A memóriaképfájl megkeresése és támogatási jegy beküldése

1. A virtuális gép javítása területen lépjen a csatlakoztatott operációsrendszer-lemez Windows mappájába. Ha a csatolt operációsrendszer-lemez meghajtóbetűjele az `F`, akkor lépjen ide: `F:\Windows`.

1. Keresse meg a Memory. dmp fájlt, majd [küldjön el egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a memóriakép fájljával.

1. Ha nem sikerül megkeresni a Memory. dmp fájlt, előfordulhat, hogy [nem maszkolt megszakítási (NMI) hívásokat kíván használni a soros konzolon](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) . Az [összeomlási memóriakép fájljának NMI-hívásokkal történő létrehozásához](/windows/client-management/generate-kernel-or-complete-crash-dump)kövesse az útmutatót.
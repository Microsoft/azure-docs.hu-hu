---
title: Avere-vFXT támogatásának engedélyezése – Azure
description: Megtudhatja, hogyan engedélyezheti a fürtön a avere vFXT for Azure támogatási adatainak automatikus feltöltését az ügyfélszolgálat biztosításához.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: 93b99aa624a21d9312297e4279b1dcf053c79ae3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88272728"
---
# <a name="enable-support-uploads"></a>Támogatási feltöltések engedélyezése

Az Azure-hoz készült avere-vFXT automatikusan feltölthetik a fürtre vonatkozó támogatási információkat. Ezek a feltöltések lehetővé teszik a támogatási személyzet számára a lehető legjobb ügyfélszolgálatot.

## <a name="steps-to-enable-uploads"></a>A feltöltések engedélyezésének lépései

A támogatás aktiválásához kövesse az alábbi lépéseket a avere Vezérlőpultján. (Olvasási [hozzáférés a vFXT-fürthöz](avere-vfxt-cluster-gui.md) , hogy megtudja, hogyan nyithatja meg a Vezérlőpultot.)

1. Navigáljon a **Beállítások** lapfülre felül.
1. Kattintson a bal oldalon található **támogatás** hivatkozásra, és fogadja el az adatvédelmi szabályzatot.

   ![Képernyőfelvétel: a avere vezérlőpultja és az előugró ablak megerősítése gomb az adatvédelmi szabályzat elfogadásához](media/avere-vfxt-privacy-policy.png)

1. A támogatás konfigurálása lapon nyissa meg az **ügyfél adatai** szakaszt a bal oldali háromszögre kattintva.
1. Kattintson a **feltöltési adatok újraérvényesítése** gombra.
1. Adja meg a fürt támogatásának nevét az **egyedi fürt neve** alatt. Győződjön meg arról, hogy ez a név egyedileg azonosítja a fürtöt a munkatársak támogatásához.
1. A **statisztikák figyelésére**, az **általános adatok feltöltésére** és az **Összeomlási adatok feltöltésére** vonatkozó jelölőnégyzeteket itt találja.
1. Kattintson a **Submit (Küldés**) gombra.

   ![A támogatási beállítások oldalának Completed Customer info szakaszt tartalmazó képernyőképe](media/avere-vfxt-support-info.png)

1. Kattintson a **biztonságos proaktív támogatás (SPS)** bal oldalán található háromszögre a szakasz kibontásához.
1. Jelölje be az **SPS-hivatkozás engedélyezése** jelölőnégyzetet.
1. Kattintson a **Submit (Küldés**) gombra.

   ![A támogatási beállítások lapon található, biztonságos proaktív támogatásról szóló szakaszt tartalmazó képernyőkép](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Következő lépések

Ha helyszíni vagy meglévő felhőalapú tárolási rendszerre van szüksége a fürthöz, kövesse a [tárterület konfigurálása](avere-vfxt-add-storage.md)című témakör útmutatását.

Ha készen áll az ügyfelek csatlakoztatására a fürthöz, olvassa el [a avere vFXT-fürt csatlakoztatása](avere-vfxt-mount-clients.md)című lapot.

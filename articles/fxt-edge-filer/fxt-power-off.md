---
title: Microsoft Azure FXT Edge Filer-egység leállítása
description: Ismerje meg az Azure FXT Edge Filer-csomópont indítási és biztonságos leállítási eljárásait a fürt Vezérlőpult szoftverének használatával.
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: d597d5d1e86a2ea13363bc270fb55304d77dee5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88184873"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Az Azure FXT Edge Filer-Hardver biztonságos kikapcsolása

Bár a fizikai főkapcsoló gomb használatával egy adott csomópontra válthat, nem ajánlott az egységet normál körülmények között leállítani.

Miután egy Azure FXT Edge Filer-csomópont használatban van egy fürt részeként, a fürt Vezérlőpultjának szoftverét kell használnia a hardver leállításához. 

> [!NOTE] 
> A lehetséges adatvesztés és-sérülés elkerülése érdekében mindig a Vezérlőpult szoftver használatával állítsa le az Azure FXT Edge Filer-t. Ne használja a fizikai főkapcsoló gombot a leállításhoz, ha a Microsoft ügyfélszolgálata és támogatása nem utasítja erre a célra.
> 
> Elektromos vészhelyzet esetén válassza le a tápkábeleket, vagy használja az adatközpontjának villamosenergia-leválasztási mechanizmusát.

## <a name="shut-down-a-node-from-the-control-panel"></a>Csomópont leállítása a Vezérlőpultról

Kövesse ezeket az utasításokat az Azure FXT Edge Filer-csomópontjának biztonságos kikapcsolásához:

1. Jelentkezzen be a fürt Vezérlőpultján. (Útmutató a [Beállítások lapok megnyitásához](fxt-cluster-create.md#open-the-settings-pages))
1. Kattintson a **Beállítások** lapra, majd töltse be a **fürt**  >  **FXT-csomópontjai** lapot.
1. A fürtcsomópontok listájában keresse meg azt, amelyet le szeretne állítani. Kattintson a **kikapcsolás** gombra a **műveletek** oszlopban. 
1. Várjon néhány percet. A csomópont leáll, és ki is kapcsolhatja magát.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg az állapotjelző LED-eket és az [Azure FXT Edge Filer-hardver állapotának figyelésére](fxt-monitor.md)szolgáló egyéb indikátorokat.
* További információk az Azure FXT Edge Filer tápegységek [csatlakoztatásával](fxt-network-power.md#connect-power-cables)kapcsolatban.

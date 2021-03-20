---
title: Az alváz cseréje a StorSimple 8000 Series eszközön | Microsoft Docs
description: Leírja, hogyan távolíthatja el és cserélheti le az StorSimple elsődleges bekerítés vagy EBOD ház vázát.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: de0f6299f35f9d76fdade976bf70456426e5ec51
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85513428"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Az alváz cseréje a StorSimple-eszközön
## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja, hogyan távolíthat el és cserélhet le egy váz StorSimple 8000 sorozatú eszközön. A StorSimple 8100 modell egyetlen bekerítési eszköz (egy váz), míg a 8600 egy kettős bekerítésű eszköz (két ház). Egy 8600-es modell esetében előfordulhat, hogy az eszközön két alváz meghibásodik: az elsődleges ház vagy az EBOD ház alváza.

Mindkét esetben a Microsoft által szállított helyettesítő váz üres. A rendszer nem tartalmaz energiagazdálkodási és hűtési modulokat (PCMs), vezérlő modulokat, szilárdtest lemezmeghajtókat (SSD), merevlemez-meghajtókat (HDD-ket) vagy EBOD modulokat.

> [!IMPORTANT]
> Az alváz eltávolítása és cseréje előtt tekintse át a [StorSimple hardver-összetevő cseréje](storsimple-8000-hardware-component-replacement.md)biztonsági információit.


## <a name="remove-the-chassis"></a>Az alváz eltávolítása
A következő lépések végrehajtásával távolítsa el az alvázt a StorSimple-eszközön.

#### <a name="to-remove-a-chassis"></a>Váz eltávolítása
1. Győződjön meg arról, hogy a StorSimple-eszköz le van állítva, és le van választva az összes áramforrásról.
2. Távolítsa el az összes hálózatot és SAS-kábelt, ha van ilyen.
3. Távolítsa el az egységet a rackből.
4. Távolítsa el az egyes meghajtókat, és jegyezze fel azokat a tárolóhelyeket, amelyekről el vannak távolítva. További információ: [a lemezmeghajtó eltávolítása](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive).
5. A EBOD ház (ha ez a meghibásodott váz), távolítsa el a EBOD vezérlő modulokat. További információ: EBOD- [vezérlő eltávolítása](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller).
   
    Az elsődleges házban (ha ez az az alváz, amely nem sikerült), távolítsa el a vezérlőket, és jegyezze fel azokat a tárolóhelyeket, amelyekről el lettek távolítva. További információ: [vezérlő eltávolítása](storsimple-8000-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Az alváz telepítése
A következő lépésekkel telepítheti az alvázt a StorSimple-eszközön.

#### <a name="to-install-a-chassis"></a>Az alváz telepítése
1. Csatlakoztassa az alvázt a rackben. További információkért lásd: [a StorSimple 8100-eszköz csatlakoztatása](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) vagy [a StorSimple 8600-eszköz csatlakoztatása](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. Miután a váz csatlakoztatva van az állványhoz, telepítse a vezérlő modulokat ugyanabba a pozícióba, amelyben korábban telepítették őket.
3. Telepítse a meghajtókat ugyanazon pozíciókba és tárolóhelyekre, amelyekre korábban már telepítették őket.
   
   > [!NOTE]
   > Javasoljuk, hogy először telepítse az SSD-ket a bővítőhelyekre, majd telepítse a HDD-ket.
  
4. Az eszköz és a telepített összetevők csatlakoztatásával csatlakoztathatja az eszközt a megfelelő áramforrásokhoz, és bekapcsolhatja az eszközt. Részletekért lásd: [a StorSimple 8100-eszköz](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) vagy [a StorSimple 8600-eszköz](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device)csatlakoztatása.

## <a name="next-steps"></a>Következő lépések
További információ a [StorSimple hardveres összetevők cseréjéről](storsimple-8000-hardware-component-replacement.md).


---
title: StorSimple 8000 sorozat Update 5,1 kibocsátási megjegyzések
description: A StorSimple 8000 Series Update 5,1 új funkcióit, problémáit és megkerülő megoldásait ismerteti.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: cdb971851ba678ce18f5a1c7954e5620740f3a4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657569"
---
# <a name="storsimple-8000-series-update-51-release-notes"></a>StorSimple 8000 sorozat Update 5,1 kibocsátási megjegyzések

## <a name="overview"></a>Áttekintés

A következő kibocsátási megjegyzések ismertetik az új funkciókat, és azonosítják a StorSimple 8000 Series Update 5,1-es verziójának kritikus megnyitási problémáit. Emellett tartalmazzák az ebben a kiadásban található StorSimple szoftverfrissítések listáját is.

Az 5,1-es frissítés bármely, az 5. frissítést futtató StorSimple-eszközre alkalmazható. Ha 5-nél alacsonyabb verziót használ, először alkalmazza az 5. frissítést, majd alkalmazza a 5,1-et. Az 5,1-es frissítéshez társított 6.3.9600.17885.

Mielőtt telepítené a frissítést a StorSimple-megoldásban, tekintse át a kibocsátási megjegyzésekben található információkat.

> [!IMPORTANT]
>
> * Az 5,1-es frissítés kötelezően frissül, és azonnal telepítve kell lennie. További információ: a 5,1-es [frissítés alkalmazása](storsimple-8000-install-update-51.md).
> * Az 5,1-es frissítés csak biztonsági frissítésekkel rendelkezik. A frissítés telepítése körülbelül 30 percet vesz igénybe. Erősen ajánlott az 5,1-es frissítés alkalmazása az eszköz működésének biztosítására.
> * Az új kiadások esetében előfordulhat, hogy a frissítések nem jelennek meg azonnal, mert a frissítések fokozatos bevezetését hajtjuk végre. Várjon néhány napot, és ismételje meg a frissítések keresését, mivel ezek a frissítések hamarosan elérhetővé válnak.

## <a name="whats-new-in-update-51"></a>Az 5,1-es frissítés újdonságai

Az 5,1-es frissítés a következő kulcsfontosságú javításokat és hibajavításokat tartalmazza:

* **Tls 1,2** – ez a StorSimple-frissítés minden ügyfélen kikényszeríti a TLS 1,2-t. Ez egy kötelező frissítés a StorSimple 8000 sorozat valamennyi eszközén.

   Ha a következő figyelmeztetés jelenik meg, a továbblépés előtt frissítenie kell az eszközön lévő szoftvereket:

   Egy vagy több StorSimple-eszköz régebbi verziójú szoftvert futtat. A TLS 1,2 legújabb elérhető frissítése kötelező frissítés, amelyet azonnal telepíteni kell ezeken az eszközökön. A TLS 1,2 minden Azure Portal kommunikációhoz használatos, és a frissítés nélkül az eszköz nem fog tudni kommunikálni az StorSimple szolgáltatással.

## <a name="known-issues-in-update-51-from-previous-releases"></a>Az előző verziók 5,1-es frissítésének ismert problémái

Nincsenek új ismert problémák az 5,1-es frissítésben. A korábbi kiadások 5,1-es frissítésére vonatkozó problémák listáját a [3. frissítéssel kapcsolatos megjegyzések](storsimple-update3-release-notes.md#known-issues-in-update-3)című rész tartalmazza.

## <a name="storsimple-cloud-appliance-updates-in-update-51"></a>Frissítések StorSimple Cloud Appliance a 5,1-es frissítésben

Ez a frissítés nem alkalmazható a StorSimple Cloud Appliancera (más néven virtuális eszközre). Az új felhőalapú berendezéseket az 5,1-es frissítés használatával kell létrehozni. StorSimple Cloud Appliance létrehozásával kapcsolatos információkért lépjen a [StorSimple Cloud Appliance üzembe helyezése és kezelése](storsimple-8000-cloud-appliance-u2.md)című témakörben.

## <a name="next-step"></a>Következő lépés

Megtudhatja, hogyan [telepítheti a 5,1](storsimple-8000-install-update-51.md) -es frissítést a StorSimple-eszközön.

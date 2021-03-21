---
title: Helyszíni kódoló partner lesz
description: Ez a cikk azt ismerteti, hogyan ellenőrizheti a helyszíni élő adatfolyam-kódolókat.
services: media-services
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 08/31/2020
ms.topic: how-to
ms.service: media-services
ms.openlocfilehash: e07831ce4a016d49e0343da66efc0345effafd94
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954361"
---
# <a name="how-to-verify-your-on-premises-live-streaming-encoder"></a>Helyszíni élő adatfolyam-kódoló ellenőrzése

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services helyszíni kódoló partnerként a kódolót a nagyvállalati ügyfeleknek ajánljuk, Media Services népszerűsíti a terméket. Helyszíni kódoló partnernek való megfeleléshez ellenőriznie kell a helyszíni kódoló kompatibilitását Media Services használatával. Ehhez végezze el a következő ellenőrzéseket.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="pass-through-live-event-verification"></a>Áteresztő élő esemény ellenőrzése

1. A Media Services-fiókjában ellenőrizze, hogy fut-e a **folyamatos átviteli végpont** . 
2. Hozzon létre és indítsa el az **átmenő** élő eseményt. <br/> További információ: [élő események állapota és számlázása](live-event-states-billing.md).
3. Töltse le a betöltési URL-címeket, és konfigurálja a helyszíni kódolót úgy, hogy az URL-cím használatával egy többszörös sávszélességű élő streamet küldjön Media Services.
4. Szerezze be az előnézeti URL-címet, és annak ellenőrzéséhez, hogy a kódolóból érkező adatok fogadása ténylegesen megtörténik-e.
5. Hozzon létre **egy új objektum** objektumot.
6. Hozzon létre egy **élő kimenetet** , és használja a létrehozott eszköz nevét.
7. Hozzon létre egy **streaming-keresőt** a beépített **folyamatos átviteli házirend** -típusokkal.
8. A **streaming-lokátor** elérési útjának listázása a használni kívánt URL-címek visszaszerzéséhez.
9. Szerezze be annak a streaming- **végpontnak** az állomásnevét, amelyről a streamet továbbítani kívánja.
10. A teljes URL-cím lekéréséhez kombinálja a 8. lépésből álló URL-címet a 9. lépésben szereplő állomásnévvel.
11. Az élő kódolót körülbelül 10 percen belül futtathatja.
12. Állítsa le az élő eseményt. 
13. Az archivált eszközöket úgy is használhatja, mint a [Azure Media Player](https://aka.ms/azuremediaplayer) , így biztosítva, hogy a lejátszás nem tartalmaz minden minőségi szinten látható hibát. Vagy tekintse meg és ellenőrizze az előnézeti URL-cím használatával az élő munkamenet során.
14. Jegyezze fel az eszköz AZONOSÍTÓját, a közzétett streaming URL-címet az élő archívumhoz, valamint az élő kódoló beállításait és verzióját.
15. Az élő esemény állapotának alaphelyzetbe állítása az egyes minták létrehozása után.
16. Ismételje meg az 5 – 15. lépést a kódoló által támogatott összes konfiguráció esetében (az ad-jelzések, a feliratok vagy a különböző kódolási sebességek nélkül).

## <a name="live-encoding-live-event-verification"></a>Élő események élő kódolásának ellenőrzése

1. A Media Services-fiókjában ellenőrizze, hogy fut-e a **folyamatos átviteli végpont** . 
2. Hozza létre és indítsa el az élő **kódolás** élő eseményét. <br/> További információ: [élő események állapota és számlázása](live-event-states-billing.md).
3. Szerezze be a betöltési URL-címeket, és konfigurálja a kódolót úgy, hogy egyetlen sávszélességű élő streamet továbbítson Media Services.
4. Szerezze be az előnézeti URL-címet, és annak ellenőrzéséhez, hogy a kódolóból érkező adatok fogadása ténylegesen megtörténik-e.
5. Hozzon létre **egy új objektum** objektumot.
6. Hozzon létre egy **élő kimenetet** , és használja a létrehozott eszköz nevét.
7. Hozzon létre egy **streaming-keresőt** a beépített **folyamatos átviteli házirend** -típusokkal.
8. A **streaming-lokátor** elérési útjának listázása a használni kívánt URL-címek visszaszerzéséhez.
9. Szerezze be annak a streaming- **végpontnak** az állomásnevét, amelyről a streamet továbbítani kívánja.
10. A teljes URL-cím lekéréséhez kombinálja a 8. lépésből álló URL-címet a 9. lépésben szereplő állomásnévvel.
11. Az élő kódolót körülbelül 10 percen belül futtathatja.
12. Állítsa le az élő eseményt.
13. Az archivált eszközöket úgy is használhatja, mint a [Azure Media Player](https://aka.ms/azuremediaplayer) , így biztosítva, hogy a lejátszásban ne legyen látható hibák az összes minőségi szinten. Vagy tekintse meg és ellenőrizze az előnézeti URL-cím használatával az élő munkamenet során.
14. Jegyezze fel az eszköz AZONOSÍTÓját, a közzétett streaming URL-címet az élő archívumhoz, valamint az élő kódoló beállításait és verzióját.
15. Az élő esemény állapotának alaphelyzetbe állítása az egyes minták létrehozása után.
16. Ismételje meg az 5 – 15. lépést a kódoló által támogatott összes konfiguráció esetében (az ad-jelzések, a feliratok vagy a különböző kódolási sebességek nélkül).

## <a name="longevity-verification"></a>Élettartam ellenőrzése

Kövesse az [élő esemény ellenőrzésének](#pass-through-live-event-verification) megfelelő lépéseket, kivéve a 11. lépést. <br/>10 perc helyett futtassa az élő kódolót egy hétig vagy tovább. Az élő adatfolyamok időről időre (vagy archivált eszközre) való megtekintésével ellenőrizheti, hogy a lejátszás nem rendelkezik-e látható hibákkal. [Azure Media Player](https://aka.ms/azuremediaplayer)

## <a name="email-your-recorded-settings"></a>A rögzített beállítások elküldése e-mailben

Végezetül küldje el a rögzített beállításokat és az élő archiválási paramétereket, hogy a rendszer amshelp@microsoft.com értesítést küldjön az összes önellenőrzési ellenőrzés Azure Media Services. Adja meg a kapcsolattartási adatait is a követő feladatokhoz. A folyamattal kapcsolatos bármilyen kérdéssel kapcsolatba léphet a Azure Media Services csapatával.

## <a name="see-also"></a>Lásd még

[Tesztelt helyszíni kódolók](recommended-on-premises-live-encoders.md)

## <a name="next-steps"></a>Következő lépések

[Élő közvetítés a Media Services v3-val](live-streaming-overview.md)

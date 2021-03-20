---
title: Az Azure eszközök internetes hálózata (IoT) megoldás lehetőségei
description: Útmutató a platform-szolgáltatások vagy a felügyelt alkalmazások platformjának kiválasztásához egy IoT-megoldás létrehozásához. A platform szolgáltatás megközelítése olyan szolgáltatásokat használ, mint például a IoT Hub és a digitális Twins építőelemek. A felügyelt alkalmazás platformjának megközelítése IoT Central használatával gyorsan elsajátíthatja az első lépéseket.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 02/05/2020
ms.author: dobett
ms.openlocfilehash: 7ff8193e87d138878d95e783d144d445436e7a08
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96452998"
---
# <a name="what-is-the-right-iot-solution-for-your-business"></a>Mi a megfelelő IoT megoldás vállalata számára?

Ha IoT-megoldást szeretne létrehozni a vállalat számára, általában a *platform szolgáltatásai* vagy a *felügyelt alkalmazás platformjának* használatára van lehetősége.

A platform szolgáltatásai biztosítják a testreszabott és rugalmas IoT alkalmazások építőelemeit. Több lehetőség közül választhat és kódokat használhat az eszközök csatlakoztatásakor, valamint az adatgyűjtésben, tárolásban és elemzésben. Az Azure IoT platform szolgáltatásai közé tartoznak az Azure IoT Hub és az Azure Digital Twins termékei.

A felügyelt alkalmazások platformja lehetővé teszi az alkalmazások gyorsabb, mint a platform szolgáltatásainak megkezdését azáltal, hogy csökkenti az eredmények eléréséhez szükséges döntések számát. A felügyelt alkalmazás platformja gondoskodik a megoldás legtöbb eleméről, így az iparági ismeretek hozzáadására, valamint az eszközök méretezésére és csatlakoztatására koncentrálhat. Az Azure IoT Central felügyelt alkalmazás-platform.

A két megközelítés közötti választáshoz a következőket érdemes figyelembe venni:

- Hogyan szeretné kezelni a megoldást.
- A megoldáshoz használni kívánt testreszabási és szabályozási szintek.
- Milyen árképzési struktúrát szeretne használni.

## <a name="management"></a>Kezelés

Hová szeretné tölteni a rendszerkezelés idejét és erőforrásait? 

- Válassza ki a platform szolgáltatásainak megközelítését, hogy teljes körűen vezérelje a megoldás mögöttes szolgáltatásait. Például a következőket kívánja:

    - Az igények kielégítése érdekében kezelheti a skálázást és biztonságossá teheti a szolgáltatásokat.
    - A házon belüli vagy partneri szakértelem használata az eszközök bevezetéséhez és szolgáltatások kiépítéséhez.

- Válassza ki a felügyelt alkalmazás platformját, hogy kihasználhassa a IoT-alkalmazások és-eszközök méretezését, biztonságát és felügyeletét kezelő platformot.

## <a name="control"></a>Szabályozás

Milyen elemeket szeretne testreszabni a megoldásban?

- Válassza ki a platform szolgáltatásainak megközelítését a megoldási architektúra teljes testreszabásához és szabályozásához.

- Válassza a felügyelt alkalmazás platformját a branding, az irányítópultok, a felhasználói szerepkörök, az eszközök és a telemetria testreszabásához. Azonban nem kívánja kezelni az alapul szolgáló IoT-felügyeleti terhelést.

## <a name="pricing"></a>Díjszabás

Milyen díjszabási struktúra a legjobban megfelel az igényeinek?

- Válassza ki a platform szolgáltatásainak módszerét a szolgáltatások finomhangolásához és az általános költségek szabályozásához.

- Válassza ki a felügyelt alkalmazás platformjának megközelítését egy egyszerű, kiszámítható díjszabási struktúrához.

## <a name="summary"></a>Összefoglalás

A platform szolgáltatásainak megközelítése a felhőalapú megoldással és az eszköz szakértelmével kapcsolatos üzleti megoldások esetében megfelelő.

- A megoldás szolgáltatásainak finomhangolása.
- Jelentős mértékben szabályozhatja a megoldás szolgáltatásait.
- A megoldás teljes körű testreszabása.

A felügyelt alkalmazás platformjának megközelítése olyan vállalkozás számára megfelelő, amely:

- Nem szeretné kiterjedt erőforrásokkal kialakítani a rendszerek kialakítását, fejlesztését és felügyeletét.
- Kiszámítható árképzési struktúrát szeretne.
- Bizonyos testreszabási képességeket szeretne.

## <a name="next-steps"></a>Következő lépések

A különböző szolgáltatások és platformok részletes ismertetését, valamint a használatuk módját lásd: az [Azure IoT Services és Technologies](iot-services-and-technologies.md).

Ha többet szeretne megtudni a sikeres IoT-megoldások legfontosabb attribútumairól, tekintse meg a [sikeres IoT-megoldások 8 attribútumát](https://aka.ms/8attributes) ismertető tanulmányt.

Az IoT-architektúra részletes ismertetéséhez tekintse át a következő dokumentumot: [Microsoft Azure IoT Reference Architecture](/azure/architecture/reference-architectures/iot) (Microsoft Azure IoT-referenciaarchitektúra).
---
title: Hierarchikus névtér Azure Data Lake Storage Gen2
description: A Azure Data Lake Storage Gen2 hierarchikus névterének fogalmát ismerteti.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 26062d73ae4c61af77b15dd2cac0541f2a988d11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95912994"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Hierarchikus névtér Azure Data Lake Storage Gen2

Egy kulcsfontosságú mechanizmus, amely lehetővé teszi, hogy a Azure Data Lake Storage Gen2 a fájlrendszer teljesítményének biztosítása az objektum tárolási skálázása és az árak között egy **hierarchikus névtér** hozzáadásával. Ez lehetővé teszi, hogy a fiókban lévő objektumok/fájlok gyűjteményét a rendszer a címtárak és a beágyazott alkönyvtárak hierarchiába rendezi, ugyanúgy, ahogy a számítógép fájlrendszere. Ha engedélyezve van egy hierarchikus névtér, a Storage-fiók képes lesz biztosítani az objektumok tárterületének méretezhetőségét és költséghatékonyságát, valamint az elemzési motorok és keretrendszerek által ismert fájlrendszer-szemantikai funkciókkal.

## <a name="the-benefits-of-a-hierarchical-namespace"></a>A hierarchikus névtér előnyei

A következő előnyök olyan fájlrendszerekhez vannak társítva, amelyek a blob adatain alapuló hierarchikus névteret implementálnak:

- **Atomi címtár-manipuláció:** Az objektum megközelíti a címtár-hierarchiát úgy, hogy az elérési út szegmensének megjelenítéséhez egy beágyazási perjelet (/) tartalmazó egyezményt fogad el az objektum nevében. Habár ez az egyezmény az objektumok rendszerezésére is használható, az egyezmény nem nyújt segítséget olyan műveletekhez, mint például a könyvtárak áthelyezése, átnevezése vagy törlése. Valódi könyvtárak nélkül az alkalmazásoknak több millió egyedi blobot kell feldolgozniuk a címtár szintű feladatok eléréséhez. Ezzel szemben a hierarchikus névtér ezeket a feladatokat egy adott bejegyzés (a szülő könyvtár) frissítésével dolgozza fel.

    Ez a drámai optimalizálás különösen sok big data elemzési keretrendszer esetében jelentős jelentőséggel bír. Az olyan eszközök, mint például a kaptár, a Spark stb. gyakran küldenek kimenetet ideiglenes helyekre, majd átnevezik a helyet a feladatok befejezésekor. Hierarchikus névtér nélkül ez az Átnevezés gyakran hosszabb időt vehet igénybe, mint maga az elemzési folyamat. Az alacsonyabb feladatok késése az elemzési számítási feladatokhoz tartozó teljes tulajdonlási költséget (TCO) csökkenti.

- **Ismerős kezelőfelület stílusa:** A fájlrendszerek jól értik a fejlesztők és a felhasználók számára. A felhőre való áttéréskor nem kell új tárolási paradigmát megtanulnia, mivel a Data Lake Storage Gen2 által közzétett fájlrendszer-felület ugyanaz a paradigma, amely a számítógépek, a kis-és a nagyvállalatok által használt.

Az egyik oka, hogy az objektumok tárolói nem támogatják a hierarchikus névtereket, a hierarchikus névtér korlátozza a méretezést. A Data Lake Storage Gen2 hierarchikus névtér azonban lineárisan méretezhető, és nem csökkenti az adatkapacitást vagy a teljesítményt.

## <a name="deciding-whether-to-enable-a-hierarchical-namespace"></a>Annak eldöntése, hogy engedélyezi-e a hierarchikus névteret

Miután engedélyezte a hierarchikus névteret a fiókjában, nem visszaállíthatja vissza egy egyszerű névtérre. Ezért érdemes megfontolni, hogy érdemes-e egy hierarchikus névteret engedélyezni az objektum-tároló számítási feladatainak természetétől függően.

Előfordulhat, hogy egyes munkaterhelések nem kapnak előnyt a hierarchikus névtér engedélyezésével. Ilyenek például a biztonsági másolatok, a képtárolók és az egyéb alkalmazások, amelyekben az Object Organization külön tárolódik az objektumokból (például egy különálló adatbázisban). 

Emellett a blob Storage-funkciók és az Azure-szolgáltatások ökoszisztémájának támogatása továbbra is nő, még mindig vannak olyan funkciók és Azure-szolgáltatások, amelyek még nem támogatottak a hierarchikus névtérrel rendelkező fiókokban. Tekintse meg az [ismert problémákat](data-lake-storage-known-issues.md). 

Általánosságban elmondható, hogy a könyvtárakat kezelő fájlrendszerekhez készült hierarchikus névteret be kell kapcsolni a tárolási munkaterhelésekhez. Ebbe beletartozik az összes olyan számítási feladat, amely elsősorban az elemzések feldolgozására szolgál. A nagyfokú szervezetet igénylő adatkészletek a hierarchikus névtér engedélyezésével is hasznosak lesznek.

A hierarchikus névtér engedélyezésének okait a TCO-elemzés határozza meg. Általánosságban elmondható, hogy a tárolási gyorsítás miatti munkaterhelések késésének fejlesztése a számítási erőforrásokat kevesebb ideig igényli. Számos számítási feladat késése a hierarchikus névtér által engedélyezett atomi címtár-manipuláció miatt javítható. Számos munkaterhelésben a számítási erőforrás a teljes költség > 85%-át, így a munkaterhelés késésének szerény csökkenése is jelentős mennyiségű TCO-megtakarítást jelent. Még azokban az esetekben is, amikor a hierarchikus névtér engedélyezése növeli a tárolási költségeket, a TCO-t továbbra is csökkentik a számítási költségek miatt.

Az adattárolási árak, a tranzakciós árak és a tárolási kapacitás foglalási díjszabása közötti különbségeket elemezheti az olyan fiókok között, amelyek hierarchikus névtérrel rendelkeznek, és hierarchikus névtereket is tartalmaz: [Azure Data Lake Storage Gen2 díjszabása](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="next-steps"></a>Következő lépések

- [Storage-fiók létrehozása](../common/storage-account-create.md)
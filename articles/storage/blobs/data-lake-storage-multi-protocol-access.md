---
title: Több protokollos hozzáférés a Azure Data Lake Storageon | Microsoft Docs
description: Használjon olyan blob API-kat és alkalmazásokat, amelyek blob API-kat használnak Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "77914858"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Több protokollos hozzáférés Azure Data Lake Storage

A blob API-k mostantól hierarchikus névtérrel rendelkező fiókokkal működnek. Ezzel feloldja az eszközök, alkalmazások és szolgáltatások ökoszisztémáját, valamint számos blob Storage-szolgáltatást a hierarchikus névtérrel rendelkező fiókokhoz.

A legutóbbi időpontig előfordulhat, hogy külön tárolási megoldásokat kellett volna fenntartania az objektumok tárolásához és az elemzések tárolásához. Ennek az az oka, hogy Azure Data Lake Storage Gen2 korlátozott az ökoszisztéma-támogatással. Emellett korlátozott hozzáféréssel rendelkezik Blob service-funkciókhoz, például a diagnosztikai naplózáshoz. A töredezett tárolási megoldás nehezen kezelhető, mert a különböző forgatókönyvek elvégzése érdekében át kell helyeznie az adatátvitelt a fiókok között. Már nem kell ezt megtennie.

A Data Lake Storage többprotokollos hozzáférése révén az adataival az eszközök, alkalmazások és szolgáltatások ökoszisztémájának használatával dolgozhat. Ez magában foglalja a harmadik féltől származó eszközöket és alkalmazásokat is. Azokat olyan fiókokra irányíthatja, amelyek hierarchikus névtérrel rendelkeznek anélkül, hogy módosítani kellene őket. Ezek az alkalmazások *ugyanúgy működnek, mint a* blob API-k meghívásával, mivel a blob API-k mostantól hierarchikus névtérrel rendelkező fiókokban is működhetnek.

A blob Storage olyan funkciói, mint a [diagnosztikai naplózás](../common/storage-analytics-logging.md), a [hozzáférési szintek](storage-blob-storage-tiers.md)és a [blob Storage életciklus-kezelési szabályzatai](storage-lifecycle-management-concepts.md) mostantól hierarchikus névtérrel rendelkező fiókokkal működnek. Ezért a blob Storage-fiókokban is engedélyezheti a hierarchikus névtereket anélkül, hogy ezekhez a fontos funkciókhoz kellene hozzáférni. 

> [!NOTE]
> A több protokollon keresztüli hozzáférés Data Lake Storage általánosan elérhető, és minden régióban elérhető. A több protokollos hozzáférés által engedélyezett Azure-szolgáltatások vagy blob Storage-funkciók előzetes verzióban maradnak.  Ezek a cikkek összefoglalják a blob Storage-funkciók és az Azure-szolgáltatások integrációjának jelenlegi támogatását. 
>
> [Blob Storage elérhető szolgáltatások a Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
>
>[Az Azure Data Lake Storage Gen2t támogató Azure-szolgáltatások](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>A multi-Protocol-hozzáférés a (z) adatközpont-tárolóban

A blob API-k és a Data Lake Storage Gen2 API-k ugyanazon az adattárban működhetnek, mint a hierarchikus névtérrel rendelkező Storage-fiókok. Data Lake Storage Gen2 a blob API-kat a hierarchikus névtéren keresztül irányítja, így az első osztályú címtár-műveletek és a POSIX-kompatibilis hozzáférés-vezérlési listák (ACL-ek) előnyeit érheti el. 

![Több protokollos hozzáférés Data Lake Storage fogalmi](./media/data-lake-storage-interop/interop-concept.png) 

A blob API-t használó meglévő eszközök és alkalmazások automatikusan hozzáférhetnek ezekhez az előnyökhöz. A fejlesztőknek nem kell módosítaniuk azokat. Data Lake Storage Gen2 konzisztensen alkalmazza a címtár-és a fájl szintű ACL-eket, függetlenül attól, hogy az eszközök és az alkalmazások milyen protokollt használnak az adateléréshez. 

## <a name="see-also"></a>Lásd még

- [Blob Storage elérhető szolgáltatások a Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Az Azure Data Lake Storage Gen2t támogató Azure-szolgáltatások](data-lake-storage-supported-azure-services.md)
- [A Azure Data Lake Storage Gen2t támogató nyílt forráskódú platformok](data-lake-storage-supported-open-source-platforms.md)
- [Ismert problémák a Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)





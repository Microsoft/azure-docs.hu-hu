---
title: Mi a dokumentum fordítása?
description: A felhőalapú batch-dokumentum fordítási szolgáltatásának és folyamatának áttekintése.
ms.topic: overview
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: 255de7d6739691c479e1ae98e37bc6d3e01a6710
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100643631"
---
# <a name="what-is-document-translation-preview"></a>Mi a dokumentum fordítása (előzetes verzió)?

A dokumentum fordítása az [Azure Translator](../translator-info-overview.md) Service felhőalapú funkciója, amely a REST API-k Azure kognitív szolgáltatás családjának részét képezi. A dokumentumok fordítási API-ját több mint 70 nyelvre fordítja le, miközben a dokumentum szerkezete és adatformátumának megőrzése mellett.

## <a name="document-translation-key-features"></a>Dokumentumok fordítási kulcsának funkciói

| Szolgáltatás | Leírás |
| ---------| -------------|
| **Nagyméretű fájlok fordítása**| A teljes dokumentumok aszinkron módon fordíthatók le.|
|**Számos fájl fordítása**|Több, mint 70 nyelvről származó fájl fordítása.|
|**Forrásfájlok megjelenítésének megőrzése**| Fájlok fordítása az eredeti elrendezés és formátum megőrzése mellett.|
|**Egyéni fordítás alkalmazása**| A dokumentumok általános és [Egyéni fordítási](../customization.md#custom-translator) modellek használatával fordíthatók le.|
|**Egyéni szószedetek alkalmazása**|Dokumentumok fordítása egyéni szószedetek használatával.|

## <a name="how-to-get-started"></a>Első lépések

A útmutatóban megtudhatja, hogyan kezdheti el gyorsan a dokumentum-fordító használatát. Első lépésként aktív [Azure-fiókra](https://azure.microsoft.com/free/cognitive-services/)lesz szüksége.  Ha még nem rendelkezik ilyennel, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free).

> [!div class="nextstepaction"]
> [Első lépések](get-started-with-document-translation.md)

## <a name="supported-document-formats"></a>Támogatott dokumentumformátumok

A dokumentumok fordítása a következő dokumentum-fájltípusokat támogatja:

| Fájl típusa| Fájlkiterjesztés|Description|
|---|---|--|
|Adobe PDF|.pdf|Adobe Acrobat – hordozható dokumentum formátuma|
|HTML|.html|Hyper-szöveg korrektúrájának nyelve|
|Honosítási adatcsere fájlformátuma|XLF. , xliff| Párhuzamos dokumentum formátuma, fordítási memória-rendszerek exportálása. A használt nyelvek definiálva vannak a fájlon belül.|
|Microsoft Excel|.xlsx|Egy táblázatos fájl az adatelemzéshez és a dokumentációhoz.|
|Microsoft Outlook|. msg|A Microsoft Outlookban létrehozott vagy mentett e-mail-üzenet.|
|Microsoft PowerPoint|.pptx| Egy bemutató fájl, amely a tartalmak diavetítéses formátumban való megjelenítésére szolgál.|
|Microsoft Word|.docx| Egy szöveges dokumentum fájlja.|
|Tabulátorral tagolt értékek/TAB|. TSV/. TAB| tabulátorral tagolt nyers adatfájl, amelyet táblázatkezelő programok használnak.|
|Szöveg|.txt| Formázatlan szöveges dokumentum.|
|Fordítási memória cseréje|. TMX|Nyílt XML-szabvány, amely a számítógéppel segített fordítás (CAT) és a honosítási alkalmazások által létrehozott fordítási memória (TM)-adatcseréhez használatos.|

## <a name="supported-glossary-formats"></a>A Szószedet támogatott formátuma

A dokumentumok fordítása a következő Szószedet-fájltípusokat támogatja:

| Fájl típusa| Fájlkiterjesztés|Description|
|---|---|--|
|Honosítási adatcsere fájlformátuma|XLF. , xliff| Párhuzamos dokumentum formátuma, fordítási memória-rendszerek exportálása. A használt nyelvek definiálva vannak a fájlon belül.|
|Tabulátorral tagolt értékek/TAB|. TSV/. TAB| tabulátorral tagolt nyers adatfájl, amelyet táblázatkezelő programok használnak.|

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés a dokumentumok fordításával](get-started-with-document-translation.md)
>
>
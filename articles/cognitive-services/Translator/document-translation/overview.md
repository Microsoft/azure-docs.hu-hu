---
title: Mi a dokumentum fordítása?
description: A felhőalapú batch-dokumentum fordítási szolgáltatásának és folyamatának áttekintése.
ms.topic: overview
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: f84dc66828f2ebf13316b3348fd93933a2223b5a
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612279"
---
# <a name="what-is-document-translation-preview"></a>Mi a dokumentum fordítása (előzetes verzió)?

A dokumentum fordítása az [Azure Translator](../translator-info-overview.md) Service felhőalapú funkciója, amely a REST API-k Azure kognitív szolgáltatás családjának részét képezi. A dokumentum-fordítási API a dokumentumokat 90 nyelvekre és dialektusokra fordítja le, miközben megőrzi a dokumentumok szerkezetét és az adatformátumot.

Ez a dokumentáció a következő cikk-típusokat tartalmazza:  

* [**A gyors**](get-started-with-document-translation.md) üzembe helyezési útmutató végigvezeti Önt a szolgáltatásra irányuló kérések lépésein.
* A [**útmutatók**](create-sas-tokens.md) útmutatásai a szolgáltatás részletesebb vagy testreszabott módokon történő használatára vonatkozó utasításokat tartalmaznak.  

## <a name="document-translation-key-features"></a>Dokumentumok fordítási kulcsának funkciói

| Szolgáltatás | Leírás |
| ---------| -------------|
| **Nagyméretű fájlok fordítása**| A teljes dokumentumok aszinkron módon fordíthatók le.|
|**Számos fájl fordítása**|Több fájl fordítása 90 nyelvekre és dialektusokra.|
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
|Tabulátorral tagolt értékek/TAB|. TSV/. TAB| Tabulátorral tagolt nyers adatfájl, amelyet táblázatkezelő programok használnak.|
|Szöveg|.txt| Formázatlan szöveges dokumentum.|
|Fordítási memória cseréje|. TMX|Nyílt XML-szabvány, amely a számítógéppel segített fordítás (CAT) és a honosítási alkalmazások által létrehozott fordítási memória (TM)-adatcseréhez használatos.|

## <a name="supported-glossary-formats"></a>A Szószedet támogatott formátuma

A dokumentumok fordítása a következő Szószedet-fájltípusokat támogatja:

| Fájl típusa| Fájlkiterjesztés|Description|
|---|---|--|
|Honosítási adatcsere fájlformátuma|XLF. , xliff| Párhuzamos dokumentum formátuma, fordítási memória-rendszerek exportálása. A használt nyelvek definiálva vannak a fájlon belül.|
|Tabulátorral tagolt értékek/TAB|. TSV/. TAB| Tabulátorral tagolt nyers adatfájl, amelyet táblázatkezelő programok használnak.|

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés a dokumentumok fordításával](get-started-with-document-translation.md)

---
title: Tartalom metaadatainak tulajdonságai
titleSuffix: Azure Cognitive Search
description: A dokumentumok metaadat-tulajdonságai megadhatnak tartalmat a keresési index mezőihez, illetve olyan információkat, amelyek a futási időben tájékoztatják az indexelési viselkedést. Ez a cikk az Azure Cognitive Search által támogatott metaadat-tulajdonságokat sorolja fel.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/22/2021
ms.openlocfilehash: cbb35f596a1d32816d1a73b462bf590d9dde0d52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101668418"
---
# <a name="content-metadata-properties-used-in-azure-cognitive-search"></a>Az Azure Cognitive Searchban használt tartalmi metaadatok tulajdonságai

A SharePoint Online és az Azure Blob Storage különböző tartalmakat tartalmazhat, és ezeknek a tartalomtípusoknak sok típusa olyan metaadatokat tartalmaz, amelyek hasznosak lehetnek az indexeléshez. Ugyanúgy, mint a szabványos blob-tulajdonságok keresési mezőinek létrehozásához **`metadata_storage_name`** , például létrehozhat mezőket a dokumentum formátumára jellemző metaadatok tulajdonságaihoz.

## <a name="supported-document-formats"></a>Támogatott dokumentumformátumok

Cognitive Search támogatja a blob-indexelést és a SharePoint Online-dokumentumok indexelését a következő dokumentum-formátumokhoz:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="properties-by-document-format"></a>Tulajdonságok dokumentum formátuma szerint

A következő táblázat összefoglalja az egyes dokumentumok formátumának feldolgozását, valamint ismerteti a blob-indexelő és a SharePoint Online indexelő által kinyert metaadatok tulajdonságait.

| Dokumentum formátuma/tartalmának típusa | Kinyert metaadatok | Feldolgozás részletei |
| --- | --- | --- |
| HTML (text/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |HTML-jelölés és szöveg kinyerése |
| PDF (alkalmazás/PDF) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is (képek nélkül) |
| DOCX (Application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| DOC (alkalmazás/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| DOCM (Application/vnd.ms-word.document. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| WORD XML (Application/vnd. MS-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Szalag XML-kódjának és kinyerésének szövege |
| WORD 2003 XML (Application/vnd. MS-WordML) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Szalag XML-kódjának és kinyerésének szövege |
| XLSX (Application/vnd. openxmlformats-officedocument. SpreadsheetXML. Sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| XLS (Application/vnd. MS-Excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| XLSM (Application/vnd. MS-Excel. Sheet. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| PPTX (Application/vnd. openxmlformats-officedocument. presentationml. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| PPT (Application/vnd. MS-PowerPoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| PPTM (Application/vnd. MS-PowerPoint. Presentation. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| MSG (Application/vnd. MS-Outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Szöveg kinyerése, beleértve a mellékletekből kinyert szöveget is. `metadata_message_to_email``metadata_message_cc_email`és `metadata_message_bcc_email` karakterlánc-gyűjtemények, a többi mező sztring.|
| ODT (Application/vnd. Oasis. OpenDocument. Text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| ODS (Application/vnd. Oasis. OpenDocument. számolótábla) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| ODP (Application/vnd. Oasis. OpenDocument. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| ZIP (alkalmazás/zip) |`metadata_content_type` |Szöveg kinyerése az archívumban található összes dokumentumból |
| GZ (alkalmazás/gzip) |`metadata_content_type` |Szöveg kinyerése az archívumban található összes dokumentumból |
| EPUB (alkalmazás/EPUB + zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Szöveg kinyerése az archívumban található összes dokumentumból |
| XML (alkalmazás/XML) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Szalag XML-kódjának és kinyerésének szövege |
| JSON (alkalmazás/JSON) |`metadata_content_type`<br/>`metadata_content_encoding` |Szöveg kinyerése<br/>Megjegyzés: Ha több dokumentumot szeretne kinyerni egy JSON-blobból, tekintse meg a részletek a JSON-Blobok [indexelésével](search-howto-index-json-blobs.md) foglalkozó témakört. |
| EML (üzenet/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Szöveg kinyerése, beleértve a mellékleteket |
| RTF (alkalmazás/RTF) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Szöveg kinyerése|
| Egyszerű szöveg (szöveg/egyszerű) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Szöveg kinyerése|

## <a name="see-also"></a>Lásd még

* [Indexelők az Azure Cognitive Searchben](search-indexer-overview.md)
* [Blobok ismertetése a mesterséges intelligenciával](search-blob-ai-integration.md)
* [A blob indexelésének áttekintése](search-blob-storage-integration.md)
* [SharePoint Online indexelés](search-howto-index-sharepoint-online.md)
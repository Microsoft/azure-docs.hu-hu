---
title: azcopy load | Microsoft Docs
titleSuffix: Azure Storage
description: Ez a cikk az azcopy load parancs referenciainformációit biztosítja.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4c2187f52503159c8efc199cb7d09a147c2c1455
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503252"
---
# <a name="azcopy-load"></a>azcopy load

Az adatok adott formátumban történő átviteléhez kapcsolódó alparancsok

## <a name="synopsis"></a>Áttekintés

Az adatok adott formátumban, például a Microsoft Avere Cloud FileSystem (CLFS) formátumban történő átviteléhez kapcsolódó alparancsok.

## <a name="related-conceptual-articles"></a>Kapcsolódó elméleti cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele az AzCopy és a Blob Storage használatával](./storage-use-azcopy-v10.md#transfer-data)
- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)

## <a name="examples"></a>Példák

Töltsön be egy teljes könyvtárat egy TÁROLÓba CLFS formátumú SAS-sel:

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|-h, --help|Megjeleníti a betöltés parancs súgótartalmat.|

## <a name="options-inherited-from-parent-commands"></a>A szülőparancsok által örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--cap-mbps lebegőpontos érték|Megabit/másodpercben megszabja az átviteli sebességet. A pillanatnyi átviteli sebesség kis mértékben eltérhet a felső teljesítménytől. Ha ez a beállítás nulla vagy nincs megadva, az átviteli sebesség nincs korlátozva.|
|--output-type string|A parancs kimenetének formátuma. A lehetőségek a következők: text, json. Az alapértelmezett érték a "text".|
|--trusted-microsoft-suffixes sztring   | További tartomány-utótagokat ad meg, Azure Active Directory bejelentkezési jogkivonatokat lehet küldeni.  Az alapértelmezett érték a '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Az itt felsoroltak az alapértelmezett értékhez kerülnek. A biztonság érdekében itt csak a Microsoft Azure helyezzen el. Több bejegyzést pontosvesszővel válassza el egymástól.|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)

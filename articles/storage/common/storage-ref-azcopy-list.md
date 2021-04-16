---
title: azcopy list | Microsoft Docs
description: Ez a cikk az azcopy list parancs referenciainformációit tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ca436ee440ad7c1dd2caee3a65790f8b630fd949
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503286"
---
# <a name="azcopy-list"></a>azcopy list

Felsorolja egy adott erőforrás entitását.

## <a name="synopsis"></a>Áttekintés

Az aktuális kiadásban csak a blobtárolók támogatottak.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó elméleti cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele az AzCopy és a Blob Storage használatával](./storage-use-azcopy-v10.md#transfer-data)
- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)

## <a name="examples"></a>Példák

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|-h, --help|Súgótartalom megjelenítése a listaparancshoz.|
|--machine-readable|A fájlméreteket listázza bájtban.|
|--mega-units|Az egységeket 1000-es megrendelésekben jeleníti meg, nem 1024-ben.|
|--running-tally|Megszámol minden fájlt és azok méretét.|

## <a name="options-inherited-from-parent-commands"></a>A szülőparancsok által örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--cap-mbps lebegőpontos érték|Megabit/másodpercben megszabja az átviteli sebességet. A pillanatnyi átviteli sebesség kis mértékben eltérhet a felső felsőértéktől. Ha ez a beállítás nulla vagy nincs megadva, az átviteli sebesség nincs korlátozva.|
|--output-type string|A parancs kimenetének formátuma. A lehetőségek a következők: szöveg, json. Az alapértelmezett érték a "text".|
|--trusted-microsoft-suffixes sztring   |További tartomány-utótagokat ad meg, Azure Active Directory bejelentkezési jogkivonatokat lehet küldeni.  Az alapértelmezett érték a '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Az itt felsoroltak az alapértelmezett értékhez kerülnek. A biztonság érdekében itt csak a Microsoft Azure helyezzen el. Több bejegyzést pontosvesszővel válassza el egymástól.|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)

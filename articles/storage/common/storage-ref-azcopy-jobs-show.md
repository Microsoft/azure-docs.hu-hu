---
title: azcopy jobs show | Microsoft Docs
description: Ez a cikk az azcopy jobs show parancs referenciainformációit mutatja be.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: a14546d8bfc50531902b5150d38ee5ce8b8b5769
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503354"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Az adott feladatazonosító részletes információit jeleníti meg.

## <a name="synopsis"></a>Áttekintés

Ha csak a feladatazonosító van megadva jelző nélkül, akkor a feladat folyamatösszegzése lesz visszaadva.

A parancs futtatásakor megjelenő bájtok száma és százalékos aránya csak a feladatban befejezett fájlokat tükrözi. Nem tükrözik a részben befejezett fájlokat.

Ha a jelző be van állítva, megjelenik a feladatban a megadott értékkel történő `with-status` átvitelek listája.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó elméleti cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele az AzCopy és a Blob Storage használatával](./storage-use-azcopy-v10.md#transfer-data)
- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|-h, --help|Megjeleníti a show parancs súgótartalmat.|
|--with-status sztring|Csak az alábbi állapottal listába sorolja fel a feladatátviteleket, az elérhető értékeket: Elindítva, Sikeres, Sikertelen|

## <a name="options-inherited-from-parent-commands"></a>A szülőparancsok által örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--cap-mbps lebegőpontos érték|Megabit/másodpercben megszabja az átviteli sebességet. A pillanatnyi átviteli sebesség kis mértékben eltérhet a felső felsőértéktől. Ha ez a beállítás nulla vagy nincs megadva, az átviteli sebesség nincs korlátozva.|
|--output-type string|A parancs kimenetének formátuma. A lehetőségek a következők: text, json. Az alapértelmezett érték a "text".|
|--trusted-microsoft-suffixes sztring   |További tartomány-utótagokat ad meg, Azure Active Directory bejelentkezési jogkivonatokat lehet küldeni.  Az alapértelmezett érték a '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Az itt felsoroltak hozzáadva az alapértelmezetthez. A biztonság érdekében itt csak a Microsoft Azure helyezzen el. Több bejegyzést pontosvesszővel válassza el egymástól.|

## <a name="see-also"></a>Lásd még

- [azcopy jobs](storage-ref-azcopy-jobs.md)

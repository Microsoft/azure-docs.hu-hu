---
title: azcopy kijelentkezése | Microsoft Docs
description: Ez a cikk a azcopy kijelentkezési parancsával kapcsolatos információkat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 987f5de78edc4c0fe2814259a1461e58018ec270
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879020"
---
# <a name="azcopy-logout"></a>azcopy logout

Naplózza a felhasználót, és leállítja az Azure Storage-erőforrásokhoz való hozzáférést.

## <a name="synopsis"></a>Áttekintés

Ezzel a paranccsal az aktuális felhasználó összes gyorsítótárazott bejelentkezési adata törlődik.

```azcopy
azcopy logout [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó fogalmi cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](./storage-use-azcopy-v10.md#transfer-data)
- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)
- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|-h,-– Súgó|A kijelentkezési parancshoz tartozó súgótartalom megjelenítése.|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--Cap-Mbps lebegőpontos|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|
|--megbízható-Microsoft-utótagok karakterlánca   |További tartomány-utótagokat határoz meg, amelyekben Azure Active Directory bejelentkezési tokenek küldhetők.  Az alapértelmezett érték: "*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Az itt felsorolt beállítások az alapértelmezett értékre kerülnek. A biztonság érdekében itt csak Microsoft Azure-tartományokat helyezhet el. Több bejegyzést pontosvesszővel kell elválasztani.|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)

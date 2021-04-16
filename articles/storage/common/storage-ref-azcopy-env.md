---
title: azcopy env | Microsoft Docs
description: Ez a cikk az azcopy env parancs referenciainformációit biztosítja.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 71a4c27b84a16a4acb37c196ccd8ee571c2b2468
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503490"
---
# <a name="azcopy-env"></a>azcopy env

Megjeleníti az AzCopy viselkedését konfiguráló környezeti változókat. A környezeti változók teljes listájáért lásd: [AzCopy 10-es](storage-ref-azcopy-configuration-settings.md)vagy esés konfigurációs beállításai (Azure Storage) .

## <a name="synopsis"></a>Áttekintés

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> Ha a parancssor használatával környezeti változót ad meg, az olvasható lesz a parancssori előzményekben. Érdemes lehet törölni a hitelesítő adatokat tartalmazó változókat a parancssori előzményekből. Ha meg kell tartania, hogy a változók ne jelenjenek meg az előzményekben, egy szkript használatával meg kell adnia a felhasználónak a hitelesítő adatait, és be kell állítania a környezeti változót.

## <a name="related-conceptual-articles"></a>Kapcsolódó elméleti cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele az AzCopy és a Blob Storage használatával](./storage-use-azcopy-v10.md#transfer-data)
- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|-h, --help|Megjeleníti az env parancs súgótartalmat. |
|--show-sensitive|Bizalmas/titkos környezeti változókat mutat.|

## <a name="options-inherited-from-parent-commands"></a>A szülőparancsok által örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--cap-mbps lebegőpontos érték|Megabit/másodpercben megszabja az átviteli sebességet. A pillanatnyi átviteli sebesség kis mértékben eltérhet a felső felsőértéktől. Ha ez a beállítás nulla vagy nincs megadva, az átviteli sebesség nincs korlátozva.|
|--output-type string|A parancs kimenetének formátuma. A lehetőségek a következők: szöveg, json. Az alapértelmezett érték a "text".|
|--trusted-microsoft-suffixes sztring  | További tartomány-utótagokat ad meg, Azure Active Directory bejelentkezési jogkivonatokat lehet küldeni.  Az alapértelmezett érték a '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Az itt felsoroltak az alapértelmezett értékhez kerülnek. A biztonság érdekében itt csak a Microsoft Azure helyezzen el. Több bejegyzést pontosvesszővel válassza el egymástól.|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)

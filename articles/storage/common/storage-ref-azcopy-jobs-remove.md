---
title: azcopy jobs remove | Microsoft Docs
description: Ez a cikk az azcopy jobs remove parancs referenciainformációit biztosítja.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c6a4745c4059c81384448deba37495030c4bf3a3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503371"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Távolítsa el az adott feladatazonosítóhoz társított összes fájlt.

> [!NOTE] 
> Testreszabhatja a napló- és tervfájlok mentésének helyét. További információért tekintse meg [az azcopy env](storage-ref-azcopy-env.md) parancsot.

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó elméleti cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele az AzCopy és a Blob Storage használatával](./storage-use-azcopy-v10.md#transfer-data)
- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)

## <a name="examples"></a>Példák

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Beállítások

**--help**                Segítség az eltávolításhoz.

## <a name="options-inherited-from-parent-commands"></a>A szülőparancsok által örökölt beállítások

**--cap-mbps lebegőpontos érték**      Megabit/másodpercben megszabja az átviteli sebességet. A pillanatnyi átviteli sebesség kis mértékben eltérhet a felső felsőértéktől. Ha ez a beállítás nulla vagy nincs megadva, az átviteli sebesség nincs korlátozva.

**--output-type** string A parancs kimenetének formátuma. A lehetőségek a következők: szöveg, json. Az alapértelmezett érték `text`. (alapértelmezett `text` )

**--trusted-microsoft-suffixes** sztring További tartomány-utótagokat ad meg, Azure Active Directory bejelentkezési jogkivonatokat lehet küldeni.  Az alapértelmezett érték a '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Az itt felsoroltak az alapértelmezett értékhez kerülnek. A biztonság érdekében itt csak a Microsoft Azure helyezzen el. Több bejegyzést pontosvesszővel válassza el egymástól.

## <a name="see-also"></a>Lásd még

- [azcopy jobs](storage-ref-azcopy-jobs.md)

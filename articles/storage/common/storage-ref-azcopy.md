---
title: azcopy | Microsoft Docs
description: Ez a cikk az azcopy parancsra vonatkozó referenciainformációt tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b3b4f7737320cc0359192f947271a0f4beb3c478
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502997"
---
# <a name="azcopy"></a>azcopy

Az AzCopy egy parancssori eszköz, amely adatokat mozgat az Azure Storage-ba és onnan másba. Az [AzCopy letöltését](storage-use-azcopy-v10.md) az Első lépések az AzCopyval cikkben olvashatja el, és megtudhatja, hogyan biztosítanak hitelesítési hitelesítő adatokat a tárolási szolgáltatásnak.

## <a name="synopsis"></a>Áttekintés

A parancsok általános formátuma a következő: `azcopy [command] [arguments] --[flag-name]=[flag-value]` .

A problémák jelentéséhez vagy az eszközzel kapcsolatos további információkért lásd: [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy) .

## <a name="related-conceptual-articles"></a>Kapcsolódó elméleti cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Oktatóanyag: Helyszíni adatok áttelepítése felhőalapú tárolóba az AzCopy használatával](storage-use-azcopy-migrate-on-premises-data.md)
- [Adatok átvitele az AzCopy és a Blob Storage használatával](./storage-use-azcopy-v10.md#transfer-data)
- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)

## <a name="options"></a>Beállítások

**--cap-mbps** (lebegőpontos) Megabit/másodpercben megszabja az átviteli sebességet. A pillanatnyi átviteli sebesség kis mértékben eltérhet a felső felsőértéktől. Ha ez a beállítás nulla vagy nincs megadva, az átviteli sebesség nincs korlátozva.

**--help** Az azcopy súgója
      
**--output-type**  (sztring) A parancs kimenetének formátuma. A lehetőségek a következők: szöveg, json. Az alapértelmezett érték `text`. (alapértelmezett `text` )

**--trusted-microsoft-suffixes** (sztring) További tartomány-utótagokat ad meg, Azure Active Directory bejelentkezési jogkivonatokat lehet küldeni.  Az alapértelmezett érték a '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Az itt felsoroltak az alapértelmezett értékhez kerülnek. A biztonság érdekében itt csak a Microsoft Azure helyezzen el. Több bejegyzést pontosvesszővel válassza el egymástól.

## <a name="see-also"></a>Lásd még

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [azcopy bench](storage-ref-azcopy-bench.md)
- [azcopy copy](storage-ref-azcopy-copy.md)
- [azcopy doc](storage-ref-azcopy-doc.md)
- [azcopy env](storage-ref-azcopy-env.md)
- [azcopy jobs](storage-ref-azcopy-jobs.md)
- [azcopy jobs clean](storage-ref-azcopy-jobs-clean.md)
- [azcopy jobs list](storage-ref-azcopy-jobs-list.md)
- [azcopy jobs remove](storage-ref-azcopy-jobs-remove.md)
- [azcopy jobs resume](storage-ref-azcopy-jobs-resume.md)
- [azcopy jobs show](storage-ref-azcopy-jobs-show.md)
- [azcopy list](storage-ref-azcopy-list.md)
- [azcopy login](storage-ref-azcopy-login.md)
- [azcopy logout](storage-ref-azcopy-logout.md)
- [azcopy make](storage-ref-azcopy-make.md)
- [azcopy remove](storage-ref-azcopy-remove.md)
- [azcopy sync](storage-ref-azcopy-sync.md)

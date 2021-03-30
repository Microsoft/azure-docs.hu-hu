---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67179750"
---
A küldő házirend-keretrendszer (SPF) rekordjai megadják, hogy mely e-mail kiszolgálók küldhetnek e-mailt egy tartománynév nevében. Az SPF-rekordok helyes konfigurálása fontos, hogy megakadályozza, hogy a címzettek levélszemétként megjelölik az e-maileket.

A DNS RFC-k eredetileg egy új SPF-bejegyzéstípust vezetett be a forgatókönyv támogatásához. A régebbi névkiszolgálók támogatásához a TXT bejegyzéstípus használatát is engedélyezte az SPF rekordok megadásához. Ez a kétértelműség zavart okozott, amelyet a [7208-es RFC-dokumentum](http://tools.ietf.org/html/rfc7208#section-3.1)feloldott. Megállapítja, hogy az SPF-rekordokat a TXT-bejegyzéstípus használatával kell létrehozni. Azt is jelzi, hogy az SPF-rekord típusa elavult.

**Az SPF-rekordokat a Azure DNS támogatja, és a TXT bejegyzéstípus használatával kell létrehozni.** Az elavult SPF-rekord típusa nem támogatott. A [DNS-zónafájl importálásakor](../articles/dns/dns-import-export.md)a rendszer az SPF-bejegyzéstípust használó SPF-rekordokat a txt-bejegyzéstípusra konvertálja.

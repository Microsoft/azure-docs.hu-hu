---
title: Szolgáltatási korlátozások
titleSuffix: Azure Digital Twins
description: A szolgáltatás korlátait Azure Digital Twins diagram.
author: baanders
ms.author: baanders
ms.date: 04/08/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 651922837b2193f7a8387c4dec6a1e20b84a41a5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728021"
---
# <a name="azure-digital-twins-service-limits"></a>Azure Digital Twins szolgáltatási korlátok

Ezek a szolgáltatásra vonatkozó Azure Digital Twins.

> [!NOTE]
> A szolgáltatás egyes területeinek korlátai módosíthatók. Ezt az alábbi táblázatokban a *Adjustable?* oszlop mutatja. Ha a korlát módosítható, a *Adjustable? (Módosítható?* ) értéke *Yes (Igen).*
>
> Ha vállalata az alapértelmezett korlát fölé kell állítania egy módosítható korlátot vagy kvótát, további erőforrásokat kérhet egy [támogatási jegy megnyitásával.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="limits-by-type"></a>Korlátok típus szerint

[!INCLUDE [Azure Digital Twins limits](../../includes/digital-twins-limits.md)]

## <a name="working-with-limits"></a>A korlátokkal való munka

A korlát elérésekor a szolgáltatás további kéréseket korlátoz. Ez a kérések 429-es hibaválaszát eredményezi.

Ennek kezeléséhez íme néhány javaslat a korlátok kezeléséhez.
* **Használjon újrapróbálkozási logikát.** A [Azure Digital Twins SDK-k](how-to-use-apis-sdks.md) implementálja a sikertelen kérelmek újrapróbálkozási logikáját, így ha egy megadott SDK-val dolgozik, ez már beépített. Ellenkező esetben fontolja meg az újrapróbálkozási logika alkalmazását a saját alkalmazásában. A szolgáltatás visszaküld egy fejlécet a hibaválaszban, amellyel meghatározhatja, hogy mennyi ideig kell várnia `Retry-After` az újrapróbálkozás előtt.
* **Használjon küszöbértékeket és értesítéseket, hogy figyelmeztetést ad a megközelítő korlátokról.** A szolgáltatási korlátok egy része Azure Digital Twins [](troubleshoot-metrics.md) vonatkozó metrikával, amelyek a használat nyomon követésére használhatók ezeken a területeken. A küszöbértékek konfigurálásáról és a küszöbértékek megközelítése esetén bármilyen metrikáról riasztást állíthat be, tekintse meg a Hibaelhárítás: Riasztások [*beállítása témakör utasításait.*](troubleshoot-alerts.md) Ha olyan egyéb korlátokhoz is beállít értesítéseket, amelyek nem biztosítanak metrikákat, fontolja meg a logika alkalmazását a saját alkalmazáskódjában.

## <a name="next-steps"></a>Következő lépések

A szolgáltatás aktuális kiadásának Azure Digital Twins a szolgáltatás áttekintésében talál további információt:
* [*Áttekintés: Mi a Azure Digital Twins?*](overview.md)

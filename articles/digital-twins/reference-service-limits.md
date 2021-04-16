---
title: Szolgáltatási korlátozások
titleSuffix: Azure Digital Twins
description: A szolgáltatás korlátait Azure Digital Twins diagram.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 15c76bc042cb66dafbdeebac2951f5cb68310aa4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482791"
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
* **Használjon küszöbértékeket és értesítéseket, hogy figyelmeztetést ad a megközelítő korlátokról.** A szolgáltatási korlátok némelyike Azure Digital Twins megfelelő [](troubleshoot-metrics.md) metrikákkal, amelyek a használat nyomon követésére használhatók ezeken a területeken. A küszöbértékek konfigurálásáról és a küszöbértékeket megközelítő metrikákra vonatkozó riasztások beállításáról a [*Hibaelhárítás: Riasztások beállításacímű témakörben található utasításokban található.*](troubleshoot-alerts.md) Ha olyan egyéb korlátokhoz is be kell állítania értesítéseket, amelyek nem biztosítanak metrikákat, fontolja meg a logika alkalmazását a saját alkalmazáskódjában.

## <a name="next-steps"></a>Következő lépések

A szolgáltatás aktuális kiadásának Azure Digital Twins a szolgáltatás áttekintésében talál további információt:
* [*Áttekintés: Mi a Azure Digital Twins?*](overview.md)

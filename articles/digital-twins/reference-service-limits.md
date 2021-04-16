---
title: Szolgáltatási korlátozások
titleSuffix: Azure Digital Twins
description: A szolgáltatás korlátait Azure Digital Twins diagram.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 46f378baad51e959f8b3c074cc24e5bbdfdd95d4
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389604"
---
# <a name="azure-digital-twins-service-limits"></a>Azure Digital Twins szolgáltatási korlátok

Ezek a szolgáltatásokra vonatkozó Azure Digital Twins.

> [!NOTE]
> A szolgáltatás egyes területeinek korlátai módosíthatók. Ezt az alábbi táblázatokban a *Adjustable?* oszlop mutatja. Ha a korlát módosítható, a *Adjustable? (Módosítható?* ) értéke *Yes (Igen)* lesz.
>
> Ha a vállalatnak az alapértelmezett korlát fölé kell állítania egy módosítható korlátot vagy kvótát, egy támogatási jegy megnyitásával kérhet további [erőforrásokat.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="limits-by-type"></a>Korlátok típus szerint

[!INCLUDE [Azure Digital Twins limits](../../includes/digital-twins-limits.md)]

## <a name="working-with-limits"></a>Korlátokkal való munka

A korlát elérésekor a szolgáltatás további kéréseket is korlátoz. Ez a kérések 404-es hibaválaszát eredményezi.

Ennek kezeléséhez íme néhány javaslat a korlátok kezeléséhez.
* **Használjon újrapróbálkozási logikát.** A [Azure Digital Twins SDK-k](how-to-use-apis-sdks.md) implementálja a sikertelen kérelmek újrapróbálkozási logikáját, így ha egy megadott SDK-val dolgozik, ez már beépített. Ellenkező esetben fontolja meg az újrapróbálkozási logika alkalmazását a saját alkalmazásában. A szolgáltatás visszaküld egy fejlécet a hibaválaszban, amellyel meghatározhatja, hogy mennyi ideig várjon az `Retry-After` újrapróbálkozás előtt.
* **Használjon küszöbértékeket és értesítéseket, hogy figyelmeztetést ad a megközelítő korlátokról.** A szolgáltatásokra vonatkozó Azure Digital Twins vonatkozó metrikák a használat nyomon követésére használhatók ezeken a területeken. [](troubleshoot-metrics.md) A küszöbértékek konfigurálásáról és a metrikákra vonatkozó riasztások beállításáról a küszöbértékek megközelítése esetén tekintse meg a Hibaelhárítás: Riasztások [*beállításacímű témakört.*](troubleshoot-alerts.md) Ha más olyan korlátokhoz is be kell állítania értesítéseket, amelyekben a metrikák nincsenek megszabadva, érdemes lehet ezt a logikát a saját alkalmazáskódjában is bevetni.

## <a name="next-steps"></a>Következő lépések

A szolgáltatás aktuális kiadásának Azure Digital Twins a szolgáltatás áttekintésében talál:
* [*Áttekintés: Mi a Azure Digital Twins?*](overview.md)

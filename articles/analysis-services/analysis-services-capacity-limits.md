---
title: Erőforrás-és objektum-korlátok Azure Analysis Servicesa | Microsoft Docs
description: Ez a cikk egy Azure Analysis Services-kiszolgáló erőforrás-és objektumokra vonatkozó korlátozásait ismerteti.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c2caab4e449f4299d00fff14b697887ec00f35e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "83697490"
---
# <a name="analysis-services-resource-and-object-limits"></a>Erőforrás-és objektum-korlátok Analysis Services

Ez a cikk az erőforrás-és modell-objektumok korlátait ismerteti.

## <a name="tier-limits"></a>Rétegek korlátai

A fejlesztői, alapszintű és standard szintű QPU és a memória korlátozásait a [Azure Analysis Services díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/analysis-services/)tekintheti meg.

## <a name="object-limits"></a>Objektum korlátai

Ezek a korlátok elméletiak. A teljesítmény csökkenése alacsonyabb számokkal történik.

|Objektum|Maximális méretek/számok|  
|------------|----------------------------|  
|Példányban lévő adatbázisok|16000|  
|Táblák és oszlopok együttes száma egy adatbázisban|16000|  
|Táblázat sorai|Korlátlan<br /><br /> **Figyelmeztetés:** Azzal a korlátozással, hogy a táblában egyetlen oszlop sem rendelkezhet több mint 1 999 999 997 különböző értékkel.|  
|Hierarchiák egy táblában|15 999|  
|Hierarchia szintjei|15 999|  
|Kapcsolatok|8,000|  
|A kulcsok oszlopai az összes táblában|15 999|  
|Mértékek a táblákban|2 ^ 31-1 = 2 147 483 647|  
|Lekérdezés által visszaadott cellák|2 ^ 31-1 = 2 147 483 647|  
|A forrás-lekérdezés rekordjának mérete|64 K|  
|Az objektumok neveinek hossza|512 karakter|  



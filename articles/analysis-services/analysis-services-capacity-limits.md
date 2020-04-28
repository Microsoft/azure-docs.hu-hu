---
title: Erőforrás-és objektum-korlátok Azure Analysis Servicesa | Microsoft Docs
description: Ez a cikk egy Azure Analysis Services-kiszolgáló erőforrás-és objektumokra vonatkozó korlátozásait ismerteti.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f309c9863eb2f3065251537380a2977839f990d8
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "73573203"
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



---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "80272718"
---
| Adattípus | Lambda kifejezésekben engedélyezett funkciók `any` | Lambda kifejezésekben engedélyezett funkciók `all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Minden `search.ismatch` , kivéve és `search.ismatchscoring` | Ugyanaz |
| `Collection(Edm.String)` | Összehasonlítás a `eq` vagy a `search.in` <br/><br/> Alkifejezések kombinálása a `or` | Összehasonlítás a `ne` vagy a `not search.in()` <br/><br/> Alkifejezések kombinálása a `and` |
| `Collection(Edm.Boolean)` | Összehasonlítás a `eq` vagy a `ne` | Ugyanaz |
| `Collection(Edm.GeographyPoint)` | `geo.distance`A vagy a használatával `lt``le` <br/><br/> `geo.intersects` <br/><br/> Alkifejezések kombinálása a `or` | `geo.distance`A vagy a használatával `gt``ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Alkifejezések kombinálása a `and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Összehasonlítások a `eq` ,,,,, `ne` `lt` `gt` `le` vagy `ge` <br/><br/> Összehasonlítások kombinálása más alkifejezésekkel a használatával `or` <br/><br/> Összehasonlítások kombinálása `ne` , kivéve más alkifejezésekkel `and` <br/><br/> A `and` és `or` a [diszjunkt kötelezőségi normál formában (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) használt kifejezések | Összehasonlítások a `eq` ,,,,, `ne` `lt` `gt` `le` vagy `ge` <br/><br/> Összehasonlítások kombinálása más alkifejezésekkel a használatával `and` <br/><br/> Összehasonlítások kombinálása `eq` , kivéve más alkifejezésekkel `or` <br/><br/> A `and` és `or` a [Conjunctive normál formában (cnf)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) használt kifejezések |

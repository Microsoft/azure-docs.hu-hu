---
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/05/2020
ms.author: rohink
ms.openlocfilehash: 7011b92485c56187021c9043ba84bc85e448a98f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94329517"
---
**Nyilvános DNS-zónák**

| Erőforrás | Korlát |
| --- | --- |
| Nyilvános DNS-zónák/előfizetés |250 <sup>1</sup> |
| Rekordhalmazok nyilvános DNS-zónában |10 000 <sup>1</sup> |
| Rekord/rekordhalmaz a nyilvános DNS-zónában |20 |
| Egyetlen Azure-erőforráshoz tartozó alias-rekordok száma |20|

<sup>1</sup> Ha meg kell emelnie ezeket a korlátokat, forduljon az Azure ügyfélszolgálatához.

**saját DNS zónák**

| Erőforrás | Korlát |
| --- | --- |
| saját DNS zónák/előfizetés |1000|
| Rekordhalmazok egy saját DNS-zónában |25000|
| Rekord/rekordhalmaz a magánhálózati DNS-zónákhoz |20|
| Privát DNS-zóna Virtual Network hivatkozásai |1000|
| A virtuális hálózatok privát DNS-zónákon keresztül, automatikus regisztráció engedélyezve |100|
| Azon privát DNS-zónák száma, amelyekhez a virtuális hálózat elérhetővé válik, és engedélyezve van az automatikus regisztráció |1|
| Saját DNS-zónák száma, amelyekhez a virtuális hálózat kapcsolódhat |1000|
| A virtuális gép által elküldhető DNS-lekérdezések száma másodpercenként Azure DNS feloldó számára |1000 <sup>1</sup> |
| A várólistára helyezett DNS-lekérdezések maximális száma (Válasz függőben) virtuális gépenként |200 <sup>1</sup> |

<sup>1</sup> Ezeket a korlátokat minden egyes virtuális gépre alkalmazza a rendszer, nem pedig a virtuális hálózat szintjén. A határértékeket meghaladó DNS-lekérdezések el lettek dobva.

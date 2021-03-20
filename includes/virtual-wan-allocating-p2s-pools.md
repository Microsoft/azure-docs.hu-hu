---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: include
ms.date: 01/12/2021
ms.author: wellee
ms.custom: include file
ms.openlocfilehash: 67a996dbe1eb7090ea5f9ee9f0880698f4ba74a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919067"
---
Ez a szakasz azokat az irányelveket és követelményeket ismerteti, amelyek az ügyfél-címtartomány kiosztásához szükségesek, ahol a kiválasztott virtuális WAN hub pont – hely VPN Gateway skálázási egysége nagyobb vagy egyenlő, mint 40.

### <a name="background"></a>Háttér

A virtuális WAN-központban a pont – hely VPN-átjárók több példányban is üzembe helyezhetők. Egy pont – hely típusú VPN-átjáró minden példánya legfeljebb 10 000 egyidejű, pont – hely típusú felhasználói kapcsolatot tud támogatni. Ennek eredményeképpen a 40-nál nagyobb skálázási egységek esetében a virtuális WAN-nak további kapacitást kell üzembe helyeznie, amelyhez a különböző méretezési egységekhez lefoglalt címkészlet minimális száma szükséges.

Ha például a 100-es méretezési egység van kiválasztva, 5 példány van telepítve a pont – hely VPN Gateway számára a virtuális központban. Ez a központi telepítés 50 000 egyidejű kapcsolatot és **legalább** 5 különböző címkészletet is támogat.

**Rendelkezésre álló skálázási egységek**

| Méretezési egység | Támogatott ügyfelek maximális száma | Címkészlet minimális száma |
|--- |--- |--- |
| 40 | 20000 | 2 |
| 60 | 30000 | 3 |
| 80 | 40000 | 4 |
| 100 | 50000 | 5 |
| 120 | 60000 | 6 |
| 140 | 70000 | 7 |
| 160 | 80000 | 8 |
| 180 | 90000 | 9 |
| 200 | 100000 | 10 |

### <a name="specifying-address-pools"></a>Címkészlet meghatározása

Az alábbiakban néhány útmutatást talál a címkészlet kiválasztásához. Vegye figyelembe, hogy a pont – hely VPN-címkészlet hozzárendeléseit a virtuális WAN automatikusan elvégzi.

1. Egy átjáró-példány legfeljebb 10 000 egyidejű kapcsolatot tesz lehetővé. Ennek megfelelően minden címkészlet legalább 10 000 egyedi RFC1918 IP-címet tartalmazhat.
1. Több címkészlet-tartomány automatikusan össze van osztva és hozzá van rendelve **egyetlen** átjáró-példányhoz. Ez a folyamat a 10 000-nél kevesebb IP-címmel rendelkező átjáró-példányok esetében ciklikusan megjelenő módon történik. Egy 5 000-es címmel rendelkező készlet például automatikusan egyesíthető a Virtual WAN és egy másik, 8 000-címmel rendelkező készlettel, és egyetlen átjáró-példányhoz van rendelve.
1. Egy címkészlet csak egyetlen átjáró-példányhoz van hozzárendelve virtuális WAN-kapcsolaton keresztül.
1. A címkészlet eltérőnek kell lennie. A címkészlet nem lehet átfedésben.

> [!NOTE] 
> Ha egy címkészlet a karbantartás alatt álló átjáró-példányhoz van társítva, a címkészlet nem rendelhető hozzá újra másik példányhoz.

### <a name="example"></a>Példa 

Az alábbi példa azt a helyzetet ismerteti, amelyben az 60-es skálázási egységek legfeljebb 30 000 kapcsolatot támogatnak, de a lefoglalt címkészlet kevesebb, mint 30 000 párhuzamos kapcsolatot eredményez.

A telepítő által támogatott egyidejű kapcsolatok teljes száma 28 192. Az első átjáró-példány a 10 000-es címeket, a második példány 8 192-kapcsolatokat, a harmadik példány pedig az 10 000-címeket is támogatja.

| Címkészlet száma | Címkészlet | Támogatott kapcsolatok |
|--- |--- |--- |
| 1 | 10.12.0.0/15 | 10000 |
| 2 | 10.13.0.0/19 | 8192 |
| 3 | 10.14.0.0/15 | 10000|

**Javaslat #1: gondoskodjon arról, hogy a címkészlet #2 legalább 10 000 különböző IP-címmel rendelkezik. (példa: 10.13.0.0/15)**

**Javaslat #2: adjon hozzá még egy címkészletet. (példa: címkészlet #4 10.15.0.0/21 2048-címmel). A 2. és a 4. címkészlet automatikusan össze lesz egyesítve, és lehetővé teszi, hogy az átjáró-példány támogassa a 10 000 egyidejű kapcsolatokat.**

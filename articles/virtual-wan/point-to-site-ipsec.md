---
title: Virtuális WAN pont – hely IPsec-házirendek
titleSuffix: Azure Virtual WAN
description: Ismerje meg az Azure virtuális WAN pont – hely IPsec-kapcsolati házirendjeit.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/24/2021
ms.author: wellee
ms.openlocfilehash: d64748bf7719af52b76c5e4141bfbbabe80bbae2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745912"
---
# <a name="point-to-site-ipsec-policies"></a>Pont – hely típusú IPsec-házirendek

Ez a cikk a pont – hely VPN-kapcsolat támogatott IPsec-házirendjének kombinációit mutatja be az Azure Virtual WAN-ban.

## <a name="default-ipsec-policies"></a>Alapértelmezett IPsec-házirendek

Az alábbi táblázat a pont – hely típusú VPN-kapcsolatokhoz tartozó alapértelmezett IPsec-paramétereket mutatja be. Ezeket a paramétereket a virtuális WAN pont – hely VPN-átjáró automatikusan kiválasztja a pont – hely profil létrehozásakor.

| Beállítás | Paraméterek |
|--- |--- |
| 1. fázis – IKE-titkosítás | AES256 |
| 1. fázis – IKE-integritás |  SHA256 |
| DH-csoport | DHGroup24 |
| 2. fázis IPsec-titkosítás | GCMAES256|
| 2. fázis IPsec-integritás | GCMAES25 |
| PFS-csoport |PFS24|

## <a name="custom-ipsec-policies"></a>Egyéni IPsec-házirendek

Ha egyéni IPsec-házirendekkel dolgozik, vegye figyelembe a következő követelményeket:

* **IKE** – az 1. fázisú IKE esetében bármelyik paramétert kiválaszthatja az IKE-titkosításból, valamint bármely paramétert az IKE-integritásból, valamint a DH csoport bármely paraméterét.
* **IPSec** – a 2. fázis IPSec esetében bármelyik paramétert kiválaszthatja az IPSec-titkosításból, valamint az IPSec integritásának paramétereit, valamint a PFS-t is. Ha az IPsec-titkosításhoz vagy az IPsec-integritáshoz tartozó paraméterek bármelyike GCM, akkor az IPsec-titkosításnak és az integritásnak is ugyanazt az algoritmust kell használnia. Ha például az GCMAES128 IPsec-titkosításra van kiválasztva, akkor az IPsec-integritás GCMAES128 is meg kell adni.  

Az alábbi táblázat a pont – hely típusú VPN-kapcsolatokhoz elérhető IPsec-paramétereket mutatja be.

| Beállítás | Paraméterek |
|--- |--- |
| 1. fázis – IKE-titkosítás | AES128, AES256, GCMAES128, GCMAES256 |
| 1. fázis – IKE-integritás |  SHA256, SHA384 |
| DH-csoport | DHGroup14, DHGroup24, ECP256, ECP384 |
| 2. fázis IPsec-titkosítás | GCMAES128, GCMAES256, SHA256|
| 2. fázis IPsec-integritás | GCMAES128, GCMAES256 |
| PFS-csoport |PFS14, PFS24, ECP256, ECP384|

## <a name="next-steps"></a>Következő lépések

Lásd: [pont – hely kapcsolat létrehozása](virtual-wan-point-to-site-portal.md)

A virtuális WAN-ról további információt a [virtuális WAN gyakori kérdések](virtual-wan-faq.md)című témakörben talál.

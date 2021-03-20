---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 83f0ce27172879a37de9488499e46de30b8e112c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98147466"
---
Ha egyéni IPsec-házirendekkel dolgozik, vegye figyelembe a következő követelményeket:

* **IKE** – az IKE esetében bármelyik paramétert kiválaszthatja az IKE-titkosításból, továbbá bármely paramétert az IKE-integritásból, valamint a DH csoport bármely paraméterét.
* **IPSec** – az IPSec esetében bármelyik paramétert kiválaszthatja az IPSec-titkosításból, valamint az IPSec integritásának paramétereit, valamint a PFS-t is. Ha az IPsec-titkosításhoz vagy az IPsec-integritáshoz tartozó paraméterek bármelyike GCM, akkor mindkét beállítás paramétereinek GCM kell lennie.

>[!NOTE]
> Az Egyéni IPsec-házirendekkel nincs a válaszadó és a kezdeményező fogalma (az alapértelmezett IPsec-házirendektől eltérően). Mindkét oldal (helyszíni és Azure VPN Gateway) ugyanazokat a beállításokat fogja használni az IKE 1. és 2. fázisában. A IKEv1 és a IKEv2 protokollok egyaránt támogatottak. Az Azure-t csak válaszadóként lehet támogatni.
>

**Elérhető beállítások és paraméterek**

| Beállítás | Paraméterek |
|--- |--- |
| IKE-titkosítás | GCMAES256, GCMAES128, AES256, AES128 |
| IKE-integritás | SHA384, SHA256 |
| DH-csoport | ECP384, ECP256, DHGroup24, DHGroup14 |
| IPsec-titkosítás | GCMAES256, GCMAES128, AES256, AES128, none |
| IPsec-integritás | GCMAES256, GCMAES128, SHA256 |
| PFS-csoport | ECP384, ECP256, PFS24, PFS14, none |
| SA élettartama |egész min. 300/alapértelmezett 27000 másodperc |

---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eea8cb61ce98b2394abff6995e5cc89f00a7cf46
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628923"
---
1. A virtuális WAN lapján válassza a **felhasználói VPN-konfigurációk** lehetőséget.
1. A **felhasználói VPN-konfigurációk** lapon válasszon ki egy konfigurációt, majd válassza a **virtuális WAN-felhasználói VPN-profil letöltése** lehetőséget. A WAN szintű konfiguráció letöltésekor egy beépített Traffic Manager-alapú felhasználói VPN-profilt kap. A globális profilokkal vagy a hub-alapú profillal kapcsolatos további információkért lásd: [hub-profilok](../articles/virtual-wan/global-hub-profile.md). A feladatátvételi forgatókönyvek egyszerűsített globális profillal.

   
   Ha valamilyen okból kifolyólag egy központ nem érhető el, a szolgáltatás által biztosított beépített forgalom biztosítja a kapcsolódást (egy másik központon keresztül) az Azure-erőforrások felé a pont – hely felhasználók számára. A hub-specifikus VPN-konfigurációt mindig letöltheti a központba. A **felhasználói VPN (pont – hely)** területen töltse le a virtuális központ **felhasználói VPN-** profilját.
1. A **virtuális WAN-felhasználói VPN-profil letöltése** lapon válassza ki a **hitelesítés típusát**, majd válassza a **profil előállítása és letöltése** lehetőséget. A rendszer létrehozza a profil-csomagot, és a konfigurációs beállításokat tartalmazó zip-fájlt fogja letölteni.

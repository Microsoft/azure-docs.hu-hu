---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: cf9d4c3fd96df83361e7d9aa89ba702d37265ec6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95562066"
---
A GatewaySubnet 0.0.0.0/0 célhelykel és NSG rendelkező felhasználó által megadott útvonalak **nem támogatottak**. Az ezzel a konfigurációval létrehozott átjárókat a rendszer letiltja a létrehozásból. Az átjárók a megfelelő működés érdekében a felügyeleti vezérlőkhöz való hozzáférést igényelnek. A [BGP-útvonal propagálását](../articles/virtual-network/virtual-networks-udr-overview.md#border-gateway-protocol) "enabled" értékre kell állítani a GatewaySubnet az átjáró elérhetőségének biztosítása érdekében. Ha letiltott értékre van állítva, az átjáró nem fog működni.
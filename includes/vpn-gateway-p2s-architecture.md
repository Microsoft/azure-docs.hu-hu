---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/30/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e3ad8fcb6cd5cf68b02ac522d0e5ef5a18ba8a3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93144995"
---
A pont – hely natív Azure-tanúsítvány hitelesítési kapcsolatai a következő elemeket használják, amelyeket ebben a gyakorlatban konfigurálhat:

* Útvonalalapú VPN-átjáró.
* A nyilvános kulcs (.cer fájl) egy főtanúsítványhoz, amely az Azure-ba van feltöltve. A tanúsítványt a feltöltését követően megbízható tanúsítványnak tekinti a rendszer, és ezt használja hitelesítéshez.
* A főtanúsítványból létrejött ügyféltanúsítvány, amely a virtuális hálózathoz csatlakozó egyes ügyfélszámítógépekre telepített ügyféltanúsítvány. A rendszer ezt a tanúsítványt használja ügyfélhitelesítéshez.
* VPN-ügyfél konfigurációja. A VPN-ügyfél VPN-ügyfél konfigurációs fájljai használatával van konfigurálva. Ezek a fájlok tartalmazzák a szükséges információkat ahhoz, hogy az ügyfél csatlakozhasson a VNet. A csomag konfigurálja az operációs rendszer meglévő, natív VPN-ügyfelét. Minden csatlakozó ügyfelet a konfigurációs fájlokban szereplő beállításokkal kell konfigurálni.

---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 59d18c89a8f66f781f01d1dace9d8968df17a626
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041527"
---
Szerezze be a. cer fájlt a főtanúsítványhoz. Használhat egy nagyvállalati megoldással (ajánlott) létrehozott főtanúsítványt, vagy létrehozhat egy önaláírt tanúsítványt. A főtanúsítvány létrehozása után exportálja a nyilvános tanúsítvány (nem a titkos kulcs) adatkészletét Base64 kódolású X. 509. cer fájlként. Ezt a fájlt később fel kell tölteni az Azure-ba.

* **Vállalati tanúsítvány:** Ha vállalati megoldást használ, használhatja a meglévő tanúsítványláncot. Szerezze be a használni kívánt főtanúsítványhoz tartozó. cer fájlt.
* **Önaláírt főtanúsítvány:** Ha nem vállalati Tanúsítványos megoldást használ, hozzon létre egy önaláírt főtanúsítványt. Ellenkező esetben a létrehozott tanúsítványok nem lesznek kompatibilisek a P2S-kapcsolatokkal, és az ügyfelek csatlakozási hibaüzenetet kapnak, amikor megpróbálnak csatlakozni. Használhatja az Azure PowerShellt, a MakeCertet vagy az OpenSSL-t. Az alábbi cikkekben ismertetett lépések bemutatják, hogyan hozhatja ki a kompatibilis önaláírt főtanúsítványokat:

  * [A Windows 10 PowerShellre vonatkozó utasítások](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): Ezekhez az utasításokhoz Windows 10 és PowerShell szükséges a tanúsítványok létrehozásához. A főtanúsítványból létrehozott ügyféltanúsítványok bármely támogatott P2S-ügyfélen telepíthetőek.
  * [A MakeCertre vonatkozó utasítások](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Ha nem rendelkezik hozzáféréssel Windows 10 rendszerű számítógéphez, a Makecert használatával is létrehozhat tanúsítványokat. Bár a MakeCert elavult, továbbra is használhatja tanúsítvány létrehozásához. A főtanúsítványból létrehozott Ügyféltanúsítványok bármelyik támogatott P2S-ügyfélen telepíthetők.
  * [Linux-utasítások](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md).
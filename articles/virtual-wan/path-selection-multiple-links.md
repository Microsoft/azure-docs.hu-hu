---
title: Az Azure Path kiválasztása több ISP-kapcsolaton keresztül
titleSuffix: Azure Virtual WAN
description: Az Azure Path Selection és a Virtual WAN megismerése
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: f24696c0db3155a59106e1361b01454b9ac16a20
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91267754"
---
# <a name="azure-path-selection-across-multiple-isp-links"></a>Az Azure Path kiválasztása több ISP-kapcsolaton keresztül

Az Azure Virtual WAN lehetővé teszi a felhasználó számára, hogy hivatkozásokkal kapcsolatos információkat tartalmazzon egy VPN-helyen, amely lehetővé teszi, hogy a VPN/SD-WAN eszköz megoldása olyan ág-specifikus házirendeket adjon meg, amelyek az Azure-ba irányuló adatforgalom irányítására szolgálnak. Ezt nevezzük az **Azure Path kiválasztásának**.

## <a name="architecture"></a>Architektúra

Az Azure Path Selection működésének megismeréséhez használja a virtuális WAN VPN-hely és a helyek közötti kapcsolat példáját.

A VPN-helyek a helyszíni SD-WAN/VPN-eszközt jelölik, például a nyilvános IP-címet, az eszköz modelljét és a nevet stb. Előfordulhat, hogy a tényleges helyszíni VPN-hely több ISP-hivatkozással is rendelkezhet, amelyek a virtuális WAN VPN-hely információi között is szerepelhetnek. Ez lehetővé teszi, hogy megtekintse a hivatkozás adatait az Azure-ban.

Egy virtuális WAN VPN-kapcsolata a virtuális központon belüli VPN Gateway-példányokon leáll. A helyek közötti kapcsolat a VPN-hely és az Azure VPN-átjáró közötti kapcsolatot jelenti. Egy vagy több kapcsolati kapcsolatból áll. Minden kapcsolati kapcsolat két alagútból áll, az egyes alagutak pedig az Azure Virtual WAN VPN Gateway egyedi példányán leállnak. Akár négy kapcsolati kapcsolat is beállítható a helyek közötti kapcsolatban, ami lehetővé teszi, hogy a helyek közötti kapcsolaton belül legfeljebb nyolc alagút legyen elérhető. Az Azure akár 2000 bújtatást is támogat egyetlen virtuális WAN VPN-átjárón belül.

:::image type="content" source="./media/path-selection-multiple-links/multi-link-site.png" alt-text="Többszörös hivatkozás diagramja":::

Ez az ábra több hivatkozást mutat be egy, az Azure Virtual WAN-hoz csatlakozó helyen. Ebben a diagramban:

* A helyszíni fiókirodában két ISP-kapcsolat található (VPN/SD-WAN-eszköz). Minden ISP-hivatkozás egy kapcsolati kapcsolatnak felel meg.

* Feltételezzük, hogy a helyszíni ügyfél-kezelő VPN/SD-WAN eszköz támogatja a IKEv1 vagy a IKEv2 IPsec protokollt.

* Minden egyes Azure-helyek közötti virtuális WAN-kapcsolat a kapcsolati kapcsolaton belül áll. A kapcsolat legfeljebb négy kapcsolati kapcsolatot támogat. Az Azure a virtuális WAN-kapcsolathoz tartozó kapcsolati egység díját számítja fel. A kapcsolati kapcsolatokért nem számítunk fel díjat.

* A kapcsolási kapcsolatok két olyan IPsec-alagutat foglalnak magukban, amelyek a virtuális WAN VPN-átjáró két különböző példányán állhatnak le. Az átjárók aktív-aktív átjáróként vannak beállítva a rugalmasság érdekében. Minden kapcsolati kapcsolathoz egyedi IP-cím és BGP-társ IP-cím szükséges. Az ábrán a 0. alagút a 0. példányon leáll, az 1-es alagút pedig leállhat az 1. példányon.

* Az útvonalak kijelölését biztosító ág-eszközök lehetővé teszik a megfelelő szabályzat megadását a fiókirodai megoldásban az Azure-ba irányuló több hivatkozás közötti forgalom irányításához. Például az 1. ISP-hivatkozás magasabb prioritású forgalomhoz használható, és az ISP 2 hivatkozás biztonsági mentésként is használható.

* Fontos megjegyezni, hogy a Virtual HUB VPN a ECMP-t használja (az egyenlő árú, több útvonalú útválasztást) az összes megszakítási alagútban.

## <a name="next-steps"></a>Következő lépések

Tekintse meg az [Azure gyakori kérdések](virtual-wan-faq.md)című témakört.
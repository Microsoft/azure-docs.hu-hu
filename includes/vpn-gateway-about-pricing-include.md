---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/08/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8f8d366961049deb3eda193718ccb553aac930e3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "67666376"
---
Két dologért fizet: a virtuális hálózati átjáró óránkénti számítási költségeiért és a virtuális hálózati átjáró kimenő adatátviteléért. A díjakról a [Díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway) oldalon tájékozódhat. Az örökölt átjáró SKU díjszabásához tekintse meg a [ExpressRoute díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/expressroute) , és görgessen a **Virtual Network Gateways (átjárók** ) szakaszhoz.

**A virtuális hálózati átjáró számítási költségei**<br>Minden virtuális hálózati átjáró óránkénti számítási költségekkel rendelkezik. Az ár a virtuális hálózati átjáró létrehozáskor megadott termékváltozatán alapul. A költség magára az átjáróra vonatkozik, és az átjárón áthaladó adatátvitelen felül értendő. Az aktív-aktív telepítés díja megegyezik az aktív-passzív beállítással.

**Adatátviteli költségek**<br>Az adatátviteli költségek kiszámítása a forrás virtuális hálózati átjáróról származó kimenő forgalmon alapul.

* Ha forgalmat küld helyszíni VPN-eszközére, a költségek az internetes kimenő adatátviteli tarifa alapján lesznek megállapítva.
* Ha különböző régiókban található virtuális hálózatok között küld forgalmat, a díjszabás a régióhoz igazodik.
* Ha csak azonos régióban található virtuális hálózatok között küld forgalmat, nincsenek adatátviteli költségek. Az azonos régióban található virtuális hálózatok közötti forgalom ingyenes.

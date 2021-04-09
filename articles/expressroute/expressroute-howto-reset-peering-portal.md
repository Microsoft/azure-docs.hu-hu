---
title: 'Azure ExpressRoute: áramköri társítások alaphelyzetbe állítása a Azure Portal használatával'
description: Megtudhatja, hogyan tilthatja le és engedélyezheti az Azure ExpressRoute-áramkörek összevonását a Azure Portal használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: 432ecedbbb8965926499380eb1165fdf43018426
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680276"
---
# <a name="reset-expressroute-circuit-peerings-by-using-the-azure-portal"></a>ExpressRoute-áramköri társítások alaphelyzetbe állítása a Azure Portal használatával

Ez a cikk bemutatja, hogyan tilthatja le és engedélyezheti egy Azure ExpressRoute-áramkör összevonását a Azure Portal használatával. Ha letilt egy társítást, akkor a ExpressRoute áramkör elsődleges és másodlagos kapcsolatához tartozó Border Gateway Protocol (BGP) munkamenet le van állítva. Ha engedélyezi a társítást, a rendszer visszaállítja a BGP-munkamenetet a ExpressRoute-áramkör elsődleges és másodlagos kapcsolatán.

> [!Note]
> Amikor először konfigurálja a ExpressRoute áramkörön lévő társításokat, a rendszer alapértelmezés szerint engedélyezi a társításokat.

A ExpressRoute-társítások alaphelyzetbe állítása a következő esetekben lehet hasznos:

* Teszteli a vész-helyreállítási tervét és megvalósítását. Tegyük fel például, hogy két ExpressRoute-áramkörrel rendelkezik. Letilthatja egy áramkör társítását, és kényszerítheti a hálózati forgalmat a másik áramkör használatára.

* Engedélyezni szeretné a kétirányú továbbítási észlelést (BFD) az Azure-beli privát és a Microsoft-partnerek számára. Ha a ExpressRoute-áramkört 2018-as augusztus 1. előtt hozták létre az Azure Private-vagy 2020-es január 10. előtt, a BFD alapértelmezés szerint nem volt engedélyezve. Állítsa vissza a társítást a BFD engedélyezéséhez.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Egy böngészőben nyissa meg a [Azure Portal](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

## <a name="reset-a-peering"></a>Társítás alaphelyzetbe állítása

Alaphelyzetbe állíthatja a Microsoft-és az Azure-beli privát társ-összevonást egy ExpressRoute-áramkörön egymástól függetlenül.

1. Válassza ki a módosítani kívánt áramkört.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="Képernyőkép, amely egy áramkör kiválasztását mutatja az ExpressRoute áramköri listán.":::

1. Válassza ki az alaphelyzetbe állítani kívánt társítási konfigurációt.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="A ExpressRoute áramkör áttekintésében a társítás kiválasztását bemutató képernyőkép.":::

1. Törölje a társítás **engedélyezése** jelölőnégyzet jelölését, majd a társítási konfiguráció letiltásához kattintson a **Mentés** gombra.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Képernyőfelvétel: a társítás engedélyezése jelölőnégyzet bejelölésének törlése.":::

1. Jelölje be a társítás **engedélyezése** jelölőnégyzetet, majd válassza a **Mentés** lehetőséget a társítási konfiguráció újbóli engedélyezéséhez.

## <a name="next-steps"></a>Következő lépések

A ExpressRoute kapcsolatos problémák elhárításához tekintse meg a következő cikkeket:

* [Az ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
* [A hálózati teljesítmény hibaelhárítása](expressroute-troubleshooting-network-performance.md)

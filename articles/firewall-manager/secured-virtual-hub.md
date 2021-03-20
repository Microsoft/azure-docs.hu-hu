---
title: Mi az a biztonságos virtuális központ?
description: A biztonságos virtuális központok ismertetése
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: victorh
ms.openlocfilehash: c840bf9e82b8dcdb1fbf9b380ea847b3d1b08dd9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91948068"
---
# <a name="what-is-a-secured-virtual-hub"></a>Mi az a biztonságos virtuális központ?

A virtuális központ egy Microsoft által felügyelt virtuális hálózat, amely lehetővé teszi a más erőforrásokból való kapcsolódást. Ha egy virtuális hubot a Azure Portal virtuális WAN-ból hoz létre, a rendszer a virtuális központ VNet és átjáróit (opcionális) hozza létre összetevőkként.

A *biztonságos* virtuális központ egy Azure-beli [virtuális WAN-központ](../virtual-wan/virtual-wan-about.md#resources) , amely a Azure Firewall Manager által konfigurált biztonsági és útválasztási házirendekkel rendelkezik. A biztonságos virtuális hubok használatával könnyedén hozhat létre olyan sugaras és tranzitív architektúrákat, amelyek natív biztonsági szolgáltatásokat biztosítanak a forgalom szabályozásához és védelméhez. 

Biztonságos virtuális központ használatával szűrheti a virtuális hálózatok (V2V), a virtuális hálózatok és a fiókirodák (B2V) és az internet felé irányuló forgalom közötti forgalmat (B2I/V2I). A biztonságos virtuális központ automatizált útválasztást biztosít. Nincs szükség a saját UDR (felhasználó által megadott útvonalak) konfigurálására a tűzfalon keresztüli forgalom irányításához.

Kiválaszthatja a szükséges biztonsági szolgáltatókat a hálózati forgalom védelméhez és szabályozásához, beleértve a Azure Firewall, a harmadik féltől származó biztonsági szolgáltatásokat (SECaaS) szolgáltatókat vagy mindkettőt. A biztonságos központ jelenleg nem támogatja az ágak közötti (B2B) szűrést és szűrést több hubokon. További információ: [What is Azure Firewall Manager?](overview.md#known-issues). 

## <a name="create-a-secured-virtual-hub"></a>Biztonságos virtuális központ létrehozása

A Azure Portal a Firewall Manager használatával létrehozhat egy új biztonságos virtuális központot, vagy átalakíthat egy meglévő virtuális hubot, amelyet korábban az Azure Virtual WAN használatával hozott létre.

## <a name="next-steps"></a>Következő lépések

A biztonságos virtuális központ létrehozásához és a központi és küllős hálózat biztonságossá tételéhez és használatához tekintse meg a következőt [: oktatóanyag: a felhőalapú hálózat védelme a Azure Firewall Managerrel a Azure Portal használatával](secure-cloud-network.md).
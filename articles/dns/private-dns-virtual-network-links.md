---
title: Mi a virtuális hálózati kapcsolat alerőforrása Azure DNS privát zónákhoz
description: A virtuális hálózati kapcsolat alerőforrások Azure DNS privát zóna áttekintése
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 5e43ad35cdaad89d5b9f43007bad8782f358c1a6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954341"
---
# <a name="what-is-a-virtual-network-link"></a>Mi az a virtuális hálózati kapcsolat?

Miután létrehozta a saját DNS-zónát az Azure-ban, az nem érhető el azonnal bármely virtuális hálózatról. Ahhoz, hogy egy adott hálózatban üzemeltetett virtuális gép hozzáférhessen a magánhálózati DNS-zónához, össze kell kapcsolni azt egy virtuális hálózattal.
Ha a magánhálózati DNS-zónát virtuális hálózattal szeretné összekapcsolni, létre kell hoznia egy virtuális hálózati kapcsolatot a magánhálózati DNS-zónában. Minden magán DNS-zónához tartozik egy virtuális hálózati kapcsolati alárendelt erőforrás gyűjteménye. Ezen erőforrások mindegyike egy virtuális hálózathoz való kapcsolódást jelent.

Egy virtuális hálózatot egy magánhálózati DNS-zónához is csatolhat regisztrációs virtuális hálózatként vagy megoldásként szolgáló virtuális hálózatként.

## <a name="registration-virtual-network"></a>Regisztrációs virtuális hálózat

Amikor [kapcsolatot hoz létre](./private-dns-getstarted-portal.md#link-the-virtual-network) egy privát DNS-zóna és egy virtuális hálózat között, lehetősége van a DNS-rekordok automatikus [regisztrációjának](./private-dns-autoregistration.md) bekapcsolására a virtuális gépeken. Ha ezt a lehetőséget választja, a virtuális hálózat a magánhálózati DNS-zóna regisztrációs virtuális hálózata lesz. A rendszer automatikusan létrehoz egy DNS-rekordot a hálózaton üzembe helyezett virtuális gépekhez. A rendszer DNS-rekordokat hoz létre a virtuális hálózatban már üzembe helyezett virtuális gépekhez. A virtuális hálózat szemszögéből a saját DNS-zóna lesz az adott virtuális hálózat regisztrációs zónája.
Egy privát DNS-zónának több regisztrációs virtuális hálózata is lehet, azonban minden virtuális hálózatnak pontosan egy regisztrációs zónája van társítva.

## <a name="resolution-virtual-network"></a>Megoldás virtuális hálózata

Ha egy magánhálózati DNS-zónában hoz létre virtuális hálózati kapcsolatot, és nem engedélyezi a DNS-rekordok automatikus regisztrálását, a virtuális hálózatot csak feloldási virtuális hálózatként kezeli a rendszer. Az ilyen hálózatokban üzembe helyezett virtuális gépek DNS-rekordjait a rendszer nem hozza létre automatikusan a csatolt privát DNS-zónában. Az ilyen hálózatban üzembe helyezett virtuális gépek azonban sikeresen le tudják kérdezni a DNS-rekordokat a magánhálózati DNS-zónából. Ezek a rekordok manuálisan hozhatók létre, vagy más virtuális hálózatokból is feltölthetők, amelyek regisztrációs hálózatként vannak csatolva a magánhálózati DNS-zónával.
Egy privát DNS-zónának több feloldási virtuális hálózata lehet, és a virtuális hálózat több feloldási zónával is rendelkezhet.

## <a name="limits"></a>Korlátok

Ha meg szeretné tudni, hogy hány regisztrációs és feloldási hálózattal rendelkezik, a magánhálózati DNS-zónákra mutató hivatkozásokat lásd: [Azure DNS korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits)

## <a name="other-considerations"></a>További szempontok

* A klasszikus üzemi modell használatával üzembe helyezett virtuális hálózatok nem támogatottak.

* Csak egy kapcsolatot hozhat létre egy privát DNS-zóna és egy virtuális hálózat között.

* A magánhálózati DNS-zónában lévő minden egyes virtuális hálózati kapcsolatnak egyedi névvel kell rendelkeznie a magánhálózati DNS-zóna környezetében. A különböző magánhálózati DNS-zónákban azonos nevű hivatkozásokat használhat.

* A virtuális hálózati kapcsolat létrehozása után a virtuális hálózati kapcsolat erőforrás "kapcsolat állapota" mezőjében keresse meg a következőt:. A virtuális hálózat méretétől függően néhány percig is eltarthat, amíg a hivatkozás működik, és a hivatkozás állapota *Befejezettre* változik.

* Ha töröl egy virtuális hálózatot, a rendszer a különböző privát DNS-zónákban lévő összes virtuális hálózati kapcsolatot és automatikusan regisztrált DNS-rekordot automatikusan törli.

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan kapcsolhat egy virtuális hálózatot egy magánhálózati DNS-zónához [Azure Portal](./private-dns-getstarted-portal.md#link-the-virtual-network) használatával

* Megtudhatja, hogyan hozhat létre saját zónát Azure DNSban [Azure PowerShell](./private-dns-getstarted-powershell.md) vagy az [Azure CLI](./private-dns-getstarted-cli.md)használatával.

* További információ [a privát zónák](./private-dns-scenarios.md) Azure DNS-beli privát zónákkal való használatával kapcsolatban.

* A Azure DNS privát zónákkal kapcsolatos gyakori kérdések és válaszok, beleértve a bizonyos típusú műveletek várható viselkedését, [saját DNS a gyakori](./dns-faq-private.md)kérdések című témakört.
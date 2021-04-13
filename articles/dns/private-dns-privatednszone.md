---
title: Mi az a Azure DNS privát zóna?
description: Privát DNS-zóna áttekintése
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/09/2021
ms.author: rohink
ms.openlocfilehash: 0e04d7525cbd0c707ba0050f31414c2472602d1b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311413"
---
# <a name="what-is-a-private-azure-dns-zone"></a>Mi az a privát Azure DNS zóna?

Az Azure saját DNS megbízható, biztonságos DNS-szolgáltatást biztosít a tartománynevek kezeléséhez és megoldásához egy virtuális hálózaton anélkül, hogy egyéni DNS-megoldást kellene hozzáadnia. Privát DNS-zónák használatával a jelenleg elérhető Azure-nevek helyett saját egyéni tartományneveket is használhat. 

A magánhálózati DNS-zónában található rekordok nem oldhatók fel az internetről. A magánhálózati DNS-zóna DNS-feloldása csak a hozzá társított virtuális hálózatokból működik.

A [virtuális hálózati kapcsolatok](./private-dns-virtual-network-links.md)létrehozásával összekapcsolhatja a magánhálózati DNS-zónákat egy vagy több virtuális hálózattal.
Engedélyezheti az automatikus [regisztrálás](./private-dns-autoregistration.md) funkciót is, amellyel automatikusan kezelheti a virtuális hálózatban üzembe helyezett virtuális gépek DNS-rekordjainak életciklusát.

## <a name="limits"></a>Korlátok

Ha szeretné megtudni, hogy hány privát DNS-zóna hozható létre egy előfizetésben, és hány rekordhalmaz támogatott egy privát DNS-zónában, tekintse meg a következőt: [Azure DNS korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits)

## <a name="restrictions"></a>Korlátozások

* Az egycímkével ellátott privát DNS-zónák nem támogatottak. A privát DNS-zónának két vagy több címkével kell rendelkeznie. Például a contoso.com két címkéje van, és egy pont választja el egymástól. A privát DNS-zónák legfeljebb 34 címkével rendelkezhetnek.
* A zóna-delegálások (NS-rekordok) nem hozhatók létre egy privát DNS-zónában. Ha gyermektartomány használatát kívánja használni, a tartományt közvetlenül saját DNS-zónáként is létrehozhatja. Ezt követően összekapcsolhatja azt a virtuális hálózattal anélkül, hogy egy névkiszolgáló-delegálást kellene beállítania a szülő zónából.

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan hozhat létre saját zónát Azure DNSban [Azure PowerShell](./private-dns-getstarted-powershell.md) vagy az [Azure CLI](./private-dns-getstarted-cli.md)használatával.

* További információ [a privát zónák](./private-dns-scenarios.md) Azure DNS-beli privát zónákkal való használatával kapcsolatban.

* A Azure DNS privát zónákkal kapcsolatos gyakori kérdések és válaszok: [saját DNS GYIK](./dns-faq-private.md).
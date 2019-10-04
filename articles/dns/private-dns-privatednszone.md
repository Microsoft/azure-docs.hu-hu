---
title: Mi az a Azure DNS privát zóna?
description: Privát DNS-zóna áttekintése
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 462c873221a4bdc622a9b118b6699a9719a5f88d
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961184"
---
# <a name="what-is-a-private-azure-dns-zone"></a>Mi az a privát Azure DNS zóna?

Az Azure saját DNS megbízható, biztonságos DNS-szolgáltatást biztosít a tartománynevek kezeléséhez és megoldásához egy virtuális hálózaton anélkül, hogy egyéni DNS-megoldást kellene hozzáadnia. Privát DNS-zónák használatával a jelenleg elérhető Azure-nevek helyett saját egyéni tartományneveket is használhat. 

A magánhálózati DNS-zónában található rekordok nem oldhatók fel az internetről. A magánhálózati DNS-zóna DNS-feloldása csak a hozzá társított virtuális hálózatokból működik.

A [virtuális hálózati kapcsolatok](./private-dns-virtual-network-links.md)létrehozásával összekapcsolhatja a magánhálózati DNS-zónákat egy vagy több virtuális hálózattal.
Engedélyezheti az [automatikus regisztrálás](./private-dns-autoregistration.md) funkciót is, amellyel automatikusan kezelheti a virtuális hálózatban üzembe helyezett virtuális gépek DNS-rekordjainak életciklusát.

## <a name="limits"></a>Korlátok

Ha szeretné megismerni, hogy hány privát DNS-zóna hozható létre egy előfizetésben, és hány rekordhalmaz támogatott a privát DNS-zónában, tekintse meg a [Azure DNS határértékeket](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits)

## <a name="restrictions"></a>Korlátozások

* Az egycímkével ellátott privát DNS-zónák nem támogatottak. A privát DNS-zónának két vagy több címkével kell rendelkeznie. Például a contoso.com két címkéje van, és egy pont választja el egymástól. A privát DNS-zónák legfeljebb 34 címkével rendelkezhetnek.
* A zóna-delegálások (NS-rekordok) nem hozhatók létre egy privát DNS-zónában. Ha gyermektartomány használatát kívánja használni, közvetlenül is létrehozhatja a tartományt magánhálózati DNS-zónáként, és összekapcsolhatja azt a virtuális hálózattal anélkül, hogy a szülő zónából névszerver-delegálást kellene beállítania.

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan hozhat létre saját zónát Azure DNSban [Azure PowerShell](./private-dns-getstarted-powershell.md) vagy az [Azure CLI](./private-dns-getstarted-cli.md)használatával.

* További információ [a privát zónák](./private-dns-scenarios.md) Azure DNS-beli privát zónákkal való használatával kapcsolatban.

* A Azure DNS privát zónákkal kapcsolatos gyakori kérdések és válaszok, beleértve a bizonyos típusú műveletek várható viselkedését, [saját DNS a gyakori](./dns-faq-private.md)kérdések című témakört.

---
title: Adattárolási hely
description: Adatok tartózkodási helye és információk az Azure arc használatára képes kiszolgálókról.
ms.topic: reference
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: d4764772473bbf2e5aafe2607a9462c9a6a15203
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100559513"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Azure arc-kompatibilis kiszolgálók: az adatrezidens

Ez a cikk az adattárolási koncepciót ismerteti, valamint azt, hogy hogyan vonatkozik az Azure arc-kompatibilis kiszolgálókra.

Az Azure arc-kompatibilis kiszolgálók a **Egyesült Államokban, Európában, az Egyesült Királyságban, Ausztráliában és Ázsia és a csendes-óceáni térség** **[érhetők el](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** .

## <a name="data-residency"></a>Adattárolási hely

Az Azure arc használatára képes kiszolgálók tárolják az Azure virtuálisgép- [bővítmény](manage-vm-extensions.md) konfigurációs beállításait (azaz a tulajdonság értékeit) a bővítménynek meg kell határoznia a csatlakozást, mielőtt engedélyezné a csatlakoztatott számítógépen. Ha például engedélyezi a Log Analytics virtuálisgép-bővítményt, a Log Analytics **munkaterület-azonosítót** és az **elsődleges kulcsot** kéri.

A rendszer a csatlakoztatott géppel kapcsolatos metaadatokat is gyűjti. Ezek konkrétan a következők:

* Operációs rendszer neve, típusa és verziója
* Számítógép neve
* Számítógép teljesen minősített tartományneve (FQDN)
* Csatlakoztatott gépi ügynök verziója
* Active Directory és DNS teljes tartományneve (FQDN)
* UUID (BIOS-AZONOSÍTÓ)
* Csatlakoztatott számítógép-ügynök szívverése
* Csatlakoztatott gépi ügynök verziója
* Felügyelt identitás nyilvános kulcsa
* A szabályzat megfelelőségi állapota és részletei (ha Azure Policy vendég konfigurációs házirendeket használ)

Az ív használatára képes kiszolgálók lehetővé teszik, hogy megadja a régiót, ahol az adatait tárolni fogja. A Microsoft más régiókban is replikálhat adatrugalmasságot, de a Microsoft nem replikálja és nem helyezi át az adatátvitelt a földrajzon kívül. Ezeket az adatforrásokat abban a régióban tárolja a rendszer, ahol az Azure arc machine-erőforrás konfigurálva van. Ha például a gép az USA keleti régiójában az ív értékkel van regisztrálva, akkor ezeket az adatterületet az USA régióban tárolja.

További információ a regionális rugalmasságról és megfelelőségi támogatásról: az [Azure földrajza](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Következő lépések

További információ az [Azure rugalmasságának](/azure/architecture/reliability/architect)tervezéséről.

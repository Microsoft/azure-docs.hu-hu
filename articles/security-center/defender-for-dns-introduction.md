---
title: A DNS-hez készült Azure Defender – az előnyök és funkciók
description: Az Azure Defender for DNS előnyeinek és funkcióinak megismerése
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: dffb505719e6778adfdd8e99f62790df9ebd615a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100695"
---
# <a name="introduction-to-azure-defender-for-dns"></a>A DNS-hez készült Azure Defender bemutatása

A [Azure DNS](../dns/dns-overview.md) olyan DNS-tartományok üzemeltetési szolgáltatása, amelyek névfeloldást biztosítanak Microsoft Azure-infrastruktúra használatával. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti.

A DNS-hez készült Azure Defender további védelmi réteget biztosít a felhőalapú erőforrások számára:

- Az Azure-erőforrások összes DNS-lekérdezésének folyamatos figyelése
- speciális biztonsági Analitika futtatása a gyanús tevékenységekkel kapcsolatos riasztáshoz

## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Előnézet<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Árképzési|A **DNS-hez készült Azure Defender** számlázása [Security Center díjszabásban](https://azure.microsoft.com/pricing/details/security-center/) látható|
|Felhők|![Igen](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Nem](./media/icons/no-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>Milyen előnyökkel jár az Azure Defender for DNS?

A DNS-hez készült Azure Defender a következő problémákkal véd:

- Adatok kiszűrése az Azure-erőforrásoktól a DNS-bújtatás használatával
- A C&C kiszolgálóval kommunikáló kártevő szoftverek
- Kommunikáció rosszindulatú tartományokkal adathalászattal és kriptográfiai adatbányászatgal
- DNS-támadások – kommunikáció rosszindulatú DNS-feloldókkal 

Az Azure Defender által a DNS-hez biztosított riasztások teljes listája a [riasztások hivatkozási oldalán](alerts-reference.md#alerts-dns)található.

## <a name="dependencies"></a>Függőségek

Az Azure Defender for DNS nem használ ügynököket. 

A DNS-réteg védelemmel való ellátásához engedélyezze az Azure Defender számára a DNS-t minden előfizetéshez az [Azure Defender engedélyezése](enable-azure-defender.md)című témakörben leírtak szerint.


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta a DNS-hez készült Azure Defender szolgáltatást. Kapcsolódó anyagok esetében tekintse meg a következő cikket: 

- Előfordulhat, hogy a biztonsági riasztásokat a Security Center vagy a különböző biztonsági termékekből Security Center fogadja. Ha az összes riasztást az Azure Sentinelbe, harmadik féltől származó SIEM-re vagy más külső eszközre kívánja exportálni, kövesse a [riasztások a Siem](continuous-export.md)-ben való exportálásának utasításait.

- > [!div class="nextstepaction"]
    > [Az Azure Defender engedélyezése](enable-azure-defender.md)
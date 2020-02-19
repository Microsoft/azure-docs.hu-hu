---
title: Azure Firewall központi felügyelet
description: Tudnivalók a Azure Firewall Manager központi felügyeletéről
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 58f670f3f55a63f0c1823adc13c98f5863d4d650
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444549"
---
# <a name="azure-firewall-central-management"></a>Azure Firewall központi felügyelet

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Ha több tűzfalat is kezel, akkor tudja, hogy a tűzfalszabályok folyamatosan változnak, hogy megnehezítik a szinkronizálást. A központi informatikai csapatoknak meg kell határozniuk az alapszintű tűzfal házirendjeit, és ki kell kényszeríteni őket több üzleti egység között. Ugyanakkor a DevOps-csapatok saját helyi származtatott tűzfalszabályok létrehozására is szükségük van a jobb mozgékonyság érdekében.

A Azure Firewall Manager előzetes verziója segíthet megoldani ezeket a problémákat.


## <a name="azure-firewall-manager-preview"></a>Azure Firewall Manager – előzetes verzió

A Azure Firewall Manager előzetes verziója egy hálózati biztonsági felügyeleti szolgáltatás, amely központi biztonsági házirendet és útválasztási felügyeletet biztosít a felhőalapú biztonsági körzetek számára. Megkönnyíti a vállalati informatikai csapatok számára, hogy központilag határozzák meg a hálózati és az alkalmazási szintű szabályokat a több Azure Firewall példány közötti forgalom szűréséhez. Különböző Azure-régiókat és-előfizetéseket is kioszthat a középpontban és küllős architektúrákban a forgalmi irányítás és a védelem terén. Emellett jobb rugalmasságot biztosít a különböző szervezetek által megvalósított helyi tűzfal-biztonsági házirendek DevOps.

### <a name="firewall-policy"></a>Tűzfal házirendje

A tűzfalszabályok olyan Azure-erőforrások, amelyek NAT-, hálózat-és alkalmazás-szabály-gyűjteményeket, valamint veszélyforrások felderítési beállításait tartalmazzák. Ez egy globális erőforrás, amely a *biztonságos virtuális hubok* és a *hub virtuális hálózatok*több Azure Firewall példányára is használható. Új szabályzatok hozhatók létre a semmiből, vagy a meglévő szabályzatokból örökölhető. Az öröklés lehetővé teszi, hogy a DevOps helyi tűzfal-házirendeket hozzon létre a szervezethez kötött alapházirend alapján. A szabályzatok a régiók és az előfizetések között működnek.
 
A Azure Firewall Managerrel hozhat létre tűzfal-házirendet és társításokat. A szabályzatokat azonban REST API, sablonok, Azure PowerShell és parancssori felület használatával is létrehozhatja és kezelheti. Miután létrehozta a szabályzatot, társíthatja azt egy virtuális WAN-központban található tűzfallal, így *biztonságos virtuális hubot* és/vagy tűzfalat hozhat létre egy virtuális hálózatban, amely az informatikai *központ Virtual Network*.

### <a name="pricing"></a>Díjszabás

A szabályzatok a tűzfal-társítások alapján lesznek kiszámlázva. Egy nulla vagy egy tűzfal társítással rendelkező szabályzat díjmentes. A több tűzfallal foglalkozó szabályzatot rögzített díjszabás szerint számítjuk fel. További információ: [Azure Firewall Manager díjszabása](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="azure-firewall-management-partners"></a>Azure Firewall felügyeleti partnerek

A következő vezető harmadik féltől származó megoldások támogatják a standard Azure REST API-k használatával Azure Firewall központi felügyeletet. Mindegyik megoldás saját egyedi tulajdonságokkal és szolgáltatásokkal rendelkezik:

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Barracuda Cloud Security Guardian](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Tufin Orka](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>Következő lépések

További információ a Azure Firewall Manager előzetes verziójáról: [Mi az a Azure Firewall Manager Preview?](../firewall-manager/overview.md)
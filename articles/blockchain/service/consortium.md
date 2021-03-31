---
title: Azure Blockchain Service Consortium
description: Áttekintés arról, hogy az Azure Blockchain szolgáltatás hogyan valósítja meg a konzorcium Blockchain hálózatait.
ms.date: 11/21/2019
ms.topic: conceptual
ms.reviewer: zeyadr
ms.openlocfilehash: 7b8885ba08d35db20d1eb7e75141cb173913b386
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84712530"
---
# <a name="azure-blockchain-service-consortium"></a>Azure Blockchain Service Consortium

Az Azure Blockchain szolgáltatással olyan privát konzorciumi Blockchain-hálózatokat hozhat létre, amelyekben az egyes Blockchain-hálózatok a hálózat meghatározott résztvevőinek korlátozhatók. Csak a privát konzorcium blockchain-hálózatának résztvevői tekinthetik meg és kezelhetik a blockchain. Az Azure Blockchain szolgáltatásban található konzorciumi hálózatok két típusú tag-résztvevő szerepkört tartalmazhatnak:

* **Rendszergazdai** jogosultsággal rendelkező résztvevők, akik konzorcium-felügyeleti műveleteket végezhetnek, és részt vehetnek a blockchain-tranzakciókban.

* **Felhasználó** – azok a résztvevők, akik nem végezhetnek konzorcium-felügyeleti műveleteket, de részt vehetnek a blockchain-tranzakciókban.

A konzorciumi hálózatok a résztvevői szerepkörök vegyesen használhatók, és tetszőleges számú szerepkörrel rendelkezhetnek. Legalább egy rendszergazdának kell lennie.

A következő ábra egy több résztvevős konzorciumi hálózatot mutat be:

![Privát konzorcium hálózati diagramja](./media/consortium/network-diagram.png)

Az Azure Blockchain szolgáltatásban a konzorciumok felügyeletével felügyelheti a résztvevőket a konzorcium hálózatában. A konzorcium felügyelete a hálózat konszenzusi modelljén alapul. Az aktuális előzetes kiadásban az Azure Blockchain szolgáltatás központosított konszenzusos modellt biztosít a konzorciumok felügyeletéhez. A felügyeleti szerepkörrel rendelkező Kiemelt résztvevők olyan konzorcium-felügyeleti műveleteket végezhetnek, mint például a résztvevők hozzáadása vagy eltávolítása a hálózatból.

## <a name="roles"></a>Szerepkörök

A konzorcium résztvevői lehetnek magánszemélyek vagy szervezetek, és felhasználói szerepkört vagy rendszergazdai szerepkört rendelhetnek hozzá. A következő táblázat a két szerepkör közötti magas szintű különbségeket sorolja fel:

| Művelet | Felhasználói szerepkör | Rendszergazdai szerepkör
|--------|:----:|:------------:|
| Új tag létrehozása | Igen | Yes |
| Új tagok meghívása | Nem | Igen |
| Tag résztvevői szerepkörének beállítása vagy módosítása | Nem | Igen |
| Tag megjelenítendő nevének módosítása | Csak a saját tag számára | Csak a saját tag számára |
| Tagok eltávolítása | Csak a saját tag számára | Yes |
| Részvétel a blockchain-tranzakciókban | Igen | Yes |

### <a name="user-role"></a>Felhasználói szerepkör

A felhasználók nem rendszergazdai képességekkel rendelkező konzorciumi résztvevők. Nem vehetnek részt a konzorciumhoz kapcsolódó tagok felügyeletében. A felhasználók megváltoztathatják a tag megjelenítendő nevét, és maguk is eltávolíthatják a konzorciumot.

### <a name="administrator"></a>Rendszergazda

A rendszergazdák a konzorcium tagjait is kezelhetik. A rendszergazdák meghívhatják a tagokat, eltávolíthatnak tagokat vagy frissíthetik a tagok szerepkörét a konzorciumon belül.
A konzorciumon belül mindig legalább egy rendszergazdának kell lennie. Az utolsó rendszergazdának egy másik résztvevőt kell megadnia rendszergazdai szerepkörként a konzorcium elhagyása előtt.

## <a name="managing-members"></a>Tagok kezelése

Csak rendszergazdák hívhatnak meg más résztvevőket a konzorciumba. A rendszergazdák az Azure-előfizetésük azonosítójával meghívhatják a résztvevőket.

A meghívást követően a résztvevők egy új tag üzembe helyezésével csatlakozhatnak az blockchain-konzorciumhoz az Azure Blockchain szolgáltatásban. A meghívott konzorcium megtekintéséhez és csatlakoztatásához ugyanazt az Azure-előfizetési azonosítót kell megadnia, amelyet a hálózati rendszergazda meghívóban használ.

A rendszergazdák eltávolíthatják a konzorcium bármely résztvevőjét, beleértve a többi rendszergazdát is. A tagok csak a konzorciumból tudják eltávolítani magukat.

## <a name="consortium-management-smart-contract"></a>A Consortium Management intelligens szerződése

Az Azure Blockchain szolgáltatásban a konzorciumok kezelése intelligens szerződések használatával történik. Az intelligens szerződéseket a rendszer automatikusan telepíti a csomópontokra új blockchain-tag telepítésekor.

A gyökérszintű konzorcium felügyeleti intelligens szerződésének címe a Azure Portal tekinthető meg. A **RootContract címe** a blockchain tag Áttekintés szakaszában található.

![RootContract címe](./media/consortium/rootcontract-address.png)

Az Azure Blockchain szolgáltatás által generált Ethereum-fiók használatával a Consortium Management [PowerShell-modullal](manage-consortium-powershell.md), Azure Portal vagy közvetlenül az intelligens szerződés használatával dolgozhat a konzorcium-felügyeleti intelligens szerződéssel.

## <a name="ethereum-account"></a>Ethereum-fiók

Egy tag létrehozásakor létrejön egy Ethereum-fiók kulcsa. Az Azure Blockchain szolgáltatás a kulcsot használja a konzorcium-felügyelettel kapcsolatos tranzakciók létrehozásához. A Ethereum fiók kulcsát az Azure Blockchain szolgáltatás automatikusan kezeli.

A tag fiók megtekinthető a Azure Portalban. A tag fiók a blockchain tag Áttekintés szakaszában található.

![Tag fiók](./media/consortium/member-account.png)

A Ethereum-fiók alaphelyzetbe állításához kattintson a tag fiókjára, és adjon meg egy új jelszót. A Ethereum-fiók címe és a jelszó is vissza lesz állítva.  

## <a name="next-steps"></a>Következő lépések

A konzorcium-felügyeleti műveletek a PowerShellen keresztül érhetők el. További információ: a [konzorcium tagjainak kezelése az Azure Blockchain szolgáltatásban a PowerShell használatával](manage-consortium-powershell.md).

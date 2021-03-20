---
title: Mi az Azure Blockchain Service-hez készült Blockchain Data Manager
description: A Blockchain Data Manager rögzíti, átalakítja és kézbesíti a Blockchain-információt Event Grid témakörökbe.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: chroyal
ms.openlocfilehash: 78c93880007259267b26cf53e93de722be1c7323
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "77209443"
---
# <a name="what-is-blockchain-data-manager-for-azure-blockchain-service"></a>Mi az Azure Blockchain Service-hez készült Blockchain Data Manager?

A Blockchain Data Manager az Azure Blockchain szolgáltatás tranzakciós információit rögzíti, átalakítja és biztosítja az Azure-szolgáltatásokkal való megbízható és skálázható Blockchain-integrációt biztosító témakörök Azure Event Grid.

A legtöbb vállalati blockchain forgatókönyvben a blockchain Ledger egy megoldás egyik része. Például egy eszköz egyik entitásból egy másikba való átadásához szüksége van egy mechanizmusra a tranzakció elküldéséhez. Ezután szükség van egy olyan mechanizmusra, amely lehetővé teszi a főkönyvi információk olvasását a tranzakció bevezetésének biztosításához, és az eredményül kapott állapot módosításait a rendszer integrálja a teljes körű megoldással. Ebben a példában, ha intelligens szerződést ír az eszközök átadásához, a Blockchain Data Manager használatával integrálhatja az alkalmazásokat és az adattárakat. Ha például egy adategységet a blockchain továbbít, az események és a tulajdonságok állapotának módosításait a Blockchain Data Manager a Event Gridon keresztül továbbítja. Ezt követően több lehetséges eseménykezelőt is használhat a Event Grid számára a blockchain-adatkapcsolat nélküli tároláshoz, vagy valós időben reagálhat az állapot változásaira.

A Blockchain Data Manager három fő funkciót hajt végre: a rögzítést, átalakítást és továbbítást.

![Blockchain Data Manager függvények](./media/data-manager/functions.png)

## <a name="capture"></a>Rögzítés

Minden Blockchain-Data Manager példány egy Azure Blockchain-szolgáltatási tag tranzakciós csomópontjára csatlakozik. Csak a tranzakciós csomóponthoz hozzáféréssel rendelkező felhasználók hozhatnak létre olyan kapcsolatot, amely biztosítja a megfelelő hozzáférés-vezérlést az ügyféladatok számára. A Blockchain Data Manager példányok megbízhatóan rögzítik az összes nyers blokkot és nyers tranzakciós adatmennyiséget a tranzakciós csomópontból, és méretezhetők a vállalati munkaterhelések támogatásához.

## <a name="transform"></a>Átalakítás

Az esemény-és Blockchain Data Manager az intelligens szerződési alkalmazások Blockchain-Data Manager belüli konfigurálásával is dekódolhatja. Intelligens szerződés hozzáadásához adja meg az ABI és a bytecode szerződést. A Blockchain Data Manager az intelligens szerződési összetevők használatával dekódolja és felderíti a szerződések címeit. Miután hozzáadta a blockchain alkalmazást a példányhoz, a Blockchain Data Manager dinamikusan felfedi az intelligens szerződési címeket, amikor az intelligens szerződést üzembe helyezi a konzorciumban, és a dekódolású eseményt és tulajdonságot a konfigurált célhelyekre küldi.

## <a name="deliver"></a>Kézbesítés

A Blockchain Data Manager több Event Grid témakör kimenő kapcsolatait is támogatja bármely adott Blockchain Data Manager-példány esetében. Blockchain-adatküldést egyetlen célhelyre is küldhet, vagy több célhelyre is küldhet blockchain-fájlokat. A Blockchain Data Manager használatával méretezhető eseményvezérelt adatközzétételi megoldást hozhat létre bármely Blockchain-telepítéshez.

## <a name="configuration-options"></a>Beállítási lehetőségek

A Blockchain Data Manager konfigurálhatja a megoldás igényeinek kielégítéséhez. Például a következőket teheti:

* Egyetlen Blockchain Data Manager példány egy Azure Blockchain-szolgáltatás tagja számára.
* Egy Blockchain Data Manager példány Azure Blockchain szolgáltatás-tranzakciós csomóponton. Például a privát tranzakciós csomópontok rendelkezhetnek saját Blockchain Data Manager-példánnyal a titkosság fenntartása érdekében.
* Egy Blockchain Data Manager-példány több kimeneti kapcsolatot is támogat. Egy Blockchain Data Manager-példány használatával felügyelhető az Azure Blockchain-szolgáltatás összes adatközzétételi integrációs pontja.

## <a name="next-steps"></a>Következő lépések

Próbáljon meg [Blockchain Data Manager példányt létrehozni](data-manager-portal.md) egy Azure Blockchain-szolgáltatás tagjához.

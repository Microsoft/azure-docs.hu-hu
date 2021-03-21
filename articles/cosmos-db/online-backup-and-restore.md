---
title: Online biztonsági mentési és igény szerinti adatvisszaállítás Azure Cosmos DBban.
description: Ez a cikk azt ismerteti, hogyan működik az automatikus biztonsági mentés az igény szerinti adatvisszaállítással. Emellett a folyamatos és rendszeres biztonsági mentési módok közötti különbséget is ismerteti.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 2629e9c6e048620d9490a1e091a16c138fd1e615
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99525432"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Online biztonsági mentés és igény szerinti adat-visszaállítás az Azure Cosmos DB-ben
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Az Azure Cosmos DB rendszeres időközönként automatikusan biztonsági másolatot készít az adatokról. Az automatikus biztonsági mentések az adatbázis-műveletek teljesítményét vagy rendelkezésre állását nem befolyásolják. Az összes biztonsági mentés tárolása külön történik egy tárolószolgáltatásban. Az automatikus biztonsági mentések hasznosak lehetnek az olyan helyzetekben, amikor véletlenül törli vagy frissíti az Azure Cosmos-fiókot, -adatbázist vagy -tárolót, és később szükség van az adatok helyreállítására. Két biztonsági mentési mód létezik:

* **Rendszeres biztonsági mentési mód** – ez a mód az alapértelmezett biztonsági mentési mód az összes meglévő fiókhoz. Ebben a módban a biztonsági mentés rendszeres időközönként történik, és a rendszer visszaállítja az adatgyűjtést a támogatási csapattal való kérelem létrehozásával. Ebben a módban a biztonsági mentési időközt és a fiók megőrzését konfigurálja. A maximális megőrzési idő egy hónapra terjed ki. A minimális biztonsági mentési időköz egy óra lehet.  További tudnivalókat az [Időszakos biztonsági mentési mód](configure-periodic-backup-restore.md) című cikkben talál.

* **Folyamatos biztonsági mentési mód** (jelenleg nyilvános előzetes verzióban) – ezt a módot választja a Azure Cosmos db fiók létrehozásakor. Ez a mód lehetővé teszi, hogy az elmúlt 30 napban bármikor visszaállítsa az adott időpontra. További információt a [folyamatos biztonsági mentési mód bemutatása](continuous-backup-restore-introduction.md), a folyamatos biztonsági mentés konfigurálása [Azure Portal](continuous-backup-restore-portal.md), a [PowerShell](continuous-backup-restore-powershell.md), a [parancssori](continuous-backup-restore-command-line.md)felület és a [Resource Manager](continuous-backup-restore-template.md) -cikkek című cikkben talál.

  > [!NOTE]
  > Ha egy új fiókot folyamatos biztonsági mentéssel konfigurál, az önkiszolgáló visszaállítást Azure Portal, a PowerShell vagy a parancssori felület használatával végezheti el. Ha a fiókja folyamatos módban van konfigurálva, nem válthat vissza rendszeres módba. Jelenleg az időszakos biztonsági mentési móddal rendelkező meglévő fiókok nem módosíthatók folytonos módba.  

## <a name="next-steps"></a>Következő lépések

A következő témakörből megtudhatja, hogyan konfigurálhatja és kezelheti a fiókja rendszeres és folyamatos biztonsági mentési módjait:

* [Az időszakos biztonsági mentési szabályzat konfigurálása és kezelése](configure-periodic-backup-restore.md) .
* Mi a [folyamatos biztonsági mentési](continuous-backup-restore-introduction.md) mód?
* A folyamatos biztonsági mentés konfigurálása és kezelése [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [parancssori](continuous-backup-restore-command-line.md)felület vagy [Azure Resource Manager](continuous-backup-restore-template.md)használatával.
* [Kezelheti](continuous-backup-restore-permissions.md) az adathelyreállításhoz szükséges engedélyeket a folyamatos biztonsági mentési módban.

---
title: Riasztások figyelése és jelentések – gyakori kérdések
description: Ebben a cikkben a Azure Backup figyelési riasztással és Azure Backup jelentésekkel kapcsolatos gyakori kérdésekre adott válaszokat talál.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 4e2585009b531512e54ffa889a05ec50038dd619
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98805866"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Azure Backup figyelési riasztás – gyakori kérdések

Ez a cikk Azure Backup figyeléssel és jelentéskészítéssel kapcsolatos gyakori kérdésekre ad választ.

## <a name="configure-azure-backup-reports"></a>Azure Backup-jelentések konfigurálása

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>Hogyan ellenőrizze, hogy megkezdődött-e a jelentéskészítési adatforgalom egy Log Analytics (LA) munkaterületen?

Navigáljon a konfigurált LA munkaterületre. Navigáljon a **naplók** menüponthoz, és futtassa a lekérdezést `CoreAzureBackup | take 1` . Ha egy visszaadott rekord jelenik meg, az azt jelenti, hogy az adatok a munkaterületre áramlanak. A kezdeti adatküldés akár 24 óráig is eltarthat.

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>Milyen gyakorisággal történik az adatküldés egy LA munkaterületre?

A tárolóból származó diagnosztikai adatok bekerülnek a Log Analytics munkaterületre, és némi késéssel. Minden esemény 20 – 30 perccel a Log Analytics munkaterületen érkezik, miután leküldte a Recovery Services-tárolóból. További részletek a lag-ról:

* Az összes megoldás esetében a biztonsági mentési szolgáltatás beépített riasztásait azonnal leküldi a rendszer a létrehozásuk után. Így általában 20 – 30 perc múlva jelennek meg az Log Analytics munkaterületen.
* Minden megoldásban az igény szerinti biztonsági mentési feladatok és a visszaállítási feladatok a befejezésük után azonnal leküldve lesznek.
* Az SQL Backup kivételével az összes megoldás esetében az ütemezett biztonsági mentési feladatok a befejezésük után azonnal leküldve lesznek.
* Az SQL Backup szolgáltatásban, mivel a naplók biztonsági mentései 15 percenként fordulnak elő, az összes befejezett ütemezett biztonsági mentési feladatra vonatkozó információ, beleértve a naplókat, a kötegbe kerül, és 6 óránként küldi el azokat.
* Minden megoldásban, például a biztonsági másolati elem, a házirend, a helyreállítási pontok, a tárterület és így tovább, naponta legalább egyszer leküldjük.
* A biztonsági mentési konfiguráció (például a házirend módosítása vagy a szerkesztési szabályzat) változása elindítja az összes kapcsolódó biztonsági mentési információt.

### <a name="how-long-can-i-retain-reporting-data"></a>Mennyi ideig tarthatom a jelentési adat?

Egy LA munkaterület létrehozása után megadhatja, hogy legfeljebb 2 évig őrizze meg az adatmennyiséget. Alapértelmezés szerint a LA munkaterület 31 napig őrzi meg az adatmegőrzési időtartamot.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>Megjelenik az összes adataim a jelentésekben az LA munkaterület konfigurálása után?

 A rendszer a diagnosztikai beállítások konfigurálása után létrehozott összes adathalmazt a LA munkaterületre küldi, és elérhetővé válik a jelentésekben. A folyamatban lévő feladatok nem továbbítódnak a jelentéskészítéshez. Miután a feladatok befejeződik vagy sikertelenek lesznek, a rendszer elküldi a jelentéseknek.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Megtekinthetem a jelentéseket a tárolók és az előfizetések között?

Igen, megtekintheti a jelentéseket a tárakban és az előfizetésekben, valamint a régiókban is. Előfordulhat, hogy az adatai egyetlen LA munkaterületen vagy egy LA munkaterületen belül találhatók.

### <a name="can-i-view-reports-across-tenants"></a>Megtekinthetem a jelentéseket a bérlők között?

Ha Ön [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) -felhasználó, aki delegált hozzáféréssel rendelkezik ügyfelei előfizetéséhez vagy a La munkaterületekhez, a biztonsági mentési jelentések segítségével megtekintheti az összes bérlő adatait.

## <a name="recovery-services-vault"></a>Recovery Services-tároló

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Mennyi ideig tart a Azure Backup ügynök feladatainak állapota, hogy tükrözze a portálon?

A Azure Portal akár 15 percet is igénybe vehet, hogy tükrözze a Azure Backup Agent-feladatok állapotát.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>A biztonsági mentési feladat sikertelensége után mennyi időt vesz igénybe egy riasztás küldése?

A riasztást a Azure Backup meghibásodása után 20 percen belül kiemeljük.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Van olyan eset, amikor a rendszer nem küld e-mailt, ha az értesítések konfigurálva vannak?

Igen. A következő helyzetekben az értesítések nem lesznek elküldve:

* Ha a rendszer óránként konfigurálja az értesítéseket, és az órán belül riasztást vált ki és oldott fel
* Feladat megszakításakor
* Ha egy második biztonsági mentési művelet meghiúsul, mert az eredeti biztonsági mentési feladata folyamatban van

## <a name="next-steps"></a>Következő lépések

További gyakori kérdéseket is áttekinthet:

* [Gyakori kérdések](backup-azure-vm-backup-faq.yml) az Azure-beli virtuális gépek biztonsági mentéséről.
* [Gyakori kérdések](backup-azure-file-folder-backup-faq.md) az Azure Backup-ügynökről

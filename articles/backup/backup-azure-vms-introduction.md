---
title: Azure-beli virtuális gépek biztonsági mentése
description: Ebből a cikkből megtudhatja, hogy az Azure Backup szolgáltatás hogyan biztonságimenti az Azure-beli virtuális gépeket, és hogyan kövesse az ajánlott eljárásokat.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 5ce76f64093bab362d62afcc3f94d07f7ee7883d
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718448"
---
# <a name="an-overview-of-azure-vm-backup"></a>Az Azure-beli virtuális gépek biztonsági mentésének áttekintése

Ez a cikk azt ismerteti, [hogy Azure Backup szolgáltatás](./backup-overview.md) hogyan biztonságimenti az Azure-beli virtuális gépeket.

Az Azure Backup független és elkülönített biztonsági másolatokat biztosít, ezzel védelmet nyújtva a virtuális gépeken lévő adatok nem szándékos megsemmisítésével szemben. A biztonsági másolatok egy helyreállítási tárban vannak tárolva, a helyreállítási pontok beépített kezelésével. A konfigurálás és a skálázás egyszerű, a biztonsági másolatok optimalizálva vannak, és szükség esetén egyszerűen helyreállíthatók.

A biztonsági mentési folyamat részeként [a](#snapshot-creation)rendszer pillanatképet készít, és az adatokat az éles számítási feladatok befolyásolása nélkül továbbítja a helyreállítási tárba. A pillanatkép különböző konzisztenciaszinteket biztosít az itt [leírt módon.](#snapshot-consistency)

Azure Backup adatbázis-számítási feladatokhoz (például [SQL Server](backup-azure-sql-database.md) és [SAP HANA)](sap-hana-db-about.md) speciális ajánlatokkal is rendelkezik, amelyek munkaterhelés-támpontokat támpontosak, 15 perces RPO-t (helyreállítási időpont-célkitűzést) kínálnak, és lehetővé teszik az egyes adatbázisok biztonsági mentését és visszaállítását.

## <a name="backup-process"></a>A biztonsági mentés folyamata

Az Azure Backup a következő módon készíti el az Azure-beli virtuális gépek biztonsági másolatát:

[!INCLUDE [azure-vm-backup-process.md](../../includes/azure-vm-backup-process.md)]

## <a name="encryption-of-azure-vm-backups"></a>Azure-beli virtuális gépek biztonsági mentésének titkosítása

Amikor azure-beli virtuális gépekről biztonsági Azure Backup, a rendszer titkosítja az Storage Service Encryption (SSE) használatával. Azure Backup segítségével titkosított Azure-beli virtuális gépekről is Azure Disk Encryption.

**Titkosítás** | **Részletek** | **Támogatás**
--- | --- | ---
**Sse** | Az SSE segítségével az Azure Storage az adatok automatikus titkosításával biztosítja az adatok titkosítását a tárolás előtt. Az Azure Storage az adatok leolvasása előtt is visszafejti az adatokat. Azure Backup két típusú virtuális gép biztonsági mentését Storage Service Encryption:<li> **SSE platform által felügyelt kulcsokkal:** Ez a titkosítás alapértelmezés szerint a virtuális gépek összes lemezén be van állítva. További információt [itt láthat.](../virtual-machines/disk-encryption.md#platform-managed-keys)<li> **SSE ügyfél által felügyelt kulcsokkal.** A CMK-val kezelheti a lemezek titkosításához használt kulcsokat. További információt [itt láthat.](../virtual-machines/disk-encryption.md#customer-managed-keys) | Azure Backup SSE használatával titkosítja az Azure-beli virtuális gépeket.
**Azure Disk Encryption** | Azure Disk Encryption azure-beli virtuális gépek operációsrendszer-lemezét és adatlemezét is titkosítja.<br/><br/> Azure Disk Encryption integrálható a BitLocker titkosítási kulcsokkal (BEK-ekkel), amelyek titkos kulcsként vannak védve a kulcstartóban. Azure Disk Encryption kulcstitkosítási kulcsokkal (KEK Azure Key Vault is integrálható. | Azure Backup csak BEK-ekkel titkosított felügyelt és nem felügyelt Azure-beli virtuális gépek biztonsági mentését támogatja, illetve a KEK-ekkel együtt.<br/><br/> A BEK-k és a KEK-k is biztonsági adatokat és titkosítva vannak.<br/><br/> Mivel a KEK-ről és a BEK-ről biztonsági másolat van, a szükséges engedélyekkel rendelkező felhasználók szükség esetén visszaállítják a kulcsokat és titkos kulcsokat a kulcstartóba. Ezek a felhasználók a titkosított virtuális gépet is helyreállíthatja.<br/><br/> A titkosított kulcsokat és titkos kulcsokat nem olvashatják illetéktelen felhasználók vagy az Azure.

Felügyelt és nem felügyelt Azure-beli virtuális gépek esetén a Backup csak BEK-ekkel titkosított virtuális gépeket, illetve a KEK-ekkel együtt BEK-ekkel titkosított virtuális gépeket is támogat.

A biztonságimentett BEK-k (titkos kulcsok) és a KEK-k (kulcsok) titkosítva vannak. Csak akkor olvashatók és használhatók, ha a jogosult felhasználók visszaállították őket a kulcstartóba. Sem a jogosulatlan felhasználók, sem az Azure nem olvashatják és nem használhatnak biztonsági mentésre használt kulcsokat vagy titkos kulcsokat.

A beK-kről is biztonsági ás biztonsági okat is. Így ha a BEK-ek elvesznek, az arra jogosult felhasználók visszaállítják a BEK-eket a kulcstartóba, és helyreállítják a titkosított virtuális gépeket. A titkosított virtuális gépekről, kulcsokról és titkos kulcsokról csak a szükséges szintű jogosultságokkal rendelkező felhasználók tudnak biztonsági mentést és visszaállítást.

## <a name="snapshot-creation"></a>Pillanatkép létrehozása

Azure Backup pillanatképeket készít a biztonsági mentés ütemezése alapján.

- **Windows rendszerű virtuális gépek:** Windows rendszerű virtuális gépek esetén a Backup szolgáltatás koordinálja a VSS-t, hogy alkalmazás-konzisztens pillanatképet készítsen a virtuálisgép-lemezekről.  Alapértelmezés szerint a Azure Backup teljes VSS biztonsági mentést készít (az alkalmazásnaplókat , például a SQL Server-t csonkol a biztonsági mentéskor, hogy alkalmazásszintű konzisztens biztonsági mentést kap).  Ha azure-beli virtuális SQL Server biztonsági mentést használó adatbázist használ, a beállítást módosíthatja úgy, hogy a VSS másolási biztonsági mentést készítsen (a naplók megőrzése érdekében). További információkért tekintse meg [ezt a cikket](./backup-azure-vms-troubleshoot.md#troubleshoot-vm-snapshot-issues).

- **Linux rendszerű virtuális gépek:** A Linux rendszerű virtuális gépek alkalmazáskonzisztens pillanatképének létrehozásához használja a Linux szkript előtti és utáni keretrendszerét a konzisztencia biztosítása érdekében saját egyéni szkriptek írása érdekében.

  - Azure Backup csak az Ön által írt előkészítő/utólagos szkripteket hívja meg.
  - Ha az előkészítő és utólagos szkriptek végrehajtása sikeresen megtörtént, a Azure Backup alkalmazás-konzisztensként jelöli meg a helyreállítási pontot. Egyéni szkriptek használata esetén azonban végső soron Az alkalmazás konzisztenciája az Ön feladata.
  - [További információ](backup-azure-linux-app-consistent.md) a szkriptek konfigurálásról.

## <a name="snapshot-consistency"></a>Pillanatkép-konzisztencia

Az alábbi táblázat ismerteti a pillanatkép-konzisztencia különböző típusait:

**Pillanatkép** | **Részletek** | **Helyreállítási** | **Megfontolandó**
--- | --- | --- | ---
**Alkalmazás-konzisztens** | Az alkalmazás-konzisztens biztonsági mentések rögzítik a memóriatartalmakat és a függőben lévő I/O-műveleteket. Az alkalmazáskonzisztens pillanatképek VSS-írót (vagy linuxos előkészítő/utólagos szkripteket) használnak az alkalmazásadatok konzisztenciájának biztosításához a biztonsági mentés előtt. | Ha alkalmazás-konzisztens pillanatképet készít egy virtuális gép helyreállításakor, a virtuális gép elindul. Nincs adatsérülés vagy adatvesztés. Az alkalmazások konzisztens állapotban indulnak. | Windows: Minden VSS-író sikeres volt<br/><br/> Linux: Az előkészítő/utólagos szkriptek konfigurálva vannak és sikeresek
**Fájlrendszer-konzisztens** | A fájlrendszer-konzisztens biztonsági mentések konzisztenciát biztosítanak azáltal, hogy az összes fájlról egyszerre készít pillanatképet.<br/><br/> | Amikor fájlrendszer-konzisztens pillanatképet készít egy virtuális gép helyreállításakor, a virtuális gép elindul. Nincs adatsérülés vagy adatvesztés. Az alkalmazásoknak saját "hibajavítási" mechanizmust kell megvalósítaniuk, hogy a visszaállított adatok konzisztensek legyenek. | Windows: Egyes VSS-írók sikertelenek <br/><br/> Linux: Alapértelmezett (ha az előkészítő/utólagos szkriptek nincsenek konfigurálva vagy sikertelenek)
**Összeomlás-konzisztens** | Összeomlás-konzisztens pillanatképek általában akkor fordulnak elő, ha egy Azure-beli virtuális gép a biztonsági mentéskor leáll. A rendszer csak a lemezen a biztonsági mentéskor már létező adatokat rögzíti, és készít biztonsági mentést. | A virtuális gép rendszerindítási folyamatával, majd a sérülési hibák kijavítása érdekében egy lemezellenőrzéssel kezdődik. Minden memória-adat vagy írási művelet, amely nem került át a lemezre az összeomlás előtt. Az alkalmazások saját adatellenőrzést valósítanak meg. Egy adatbázis-alkalmazás például használhatja a tranzakciónaplóját az ellenőrzéshez. Ha a tranzakciónapló olyan bejegyzéseket tartalmaz, amelyek nincsenek az adatbázisban, az adatbázisszoftver visszaállítja a tranzakciókat, amíg az adatok konzisztensek nem maradnak. | A virtuális gép le van állítva (leállítva/felszabadítva) állapotban van.

>[!NOTE]
> Ha a kiépítési állapot **sikeres,** a Azure Backup fájlrendszer-konzisztens biztonsági mentéseket készít. Ha a kiépítési állapot **nem érhető** el vagy **meghiúsult,** a rendszer összeomlás-konzisztens biztonsági mentéseket készít. Ha a kiépítési állapot létrehozza vagy törli a et, az azt Azure Backup újrapróbálja a műveleteket.  

## <a name="backup-and-restore-considerations"></a>Biztonsági mentéssel és visszaállítással kapcsolatos megfontolandó szempontok

**Megfontolandó** | **Részletek**
--- | ---
**Lemez** | A virtuálisgép-lemezek biztonsági mentése párhuzamos. Ha például egy virtuális gép négy lemezzel rendelkezik, a Backup szolgáltatás mind a négy lemez biztonsági mentését kísérli meg párhuzamosan. A biztonsági mentés növekményes (csak módosított adatok).
**Ütemezés** |  A biztonsági másolatok forgalmának csökkentése érdekében a nap különböző időpontokban készítse el a különböző virtuális gépek biztonsági másolatát, és győződjön meg arról, hogy az időpontok nem fedik át egymást. A virtuális gépek egyidejű biztonsági mentése az adatformalom elakadását okozza.
**Biztonsági másolatok előkészítése** | Tartsa szem előtt a biztonsági mentés előkészítéséhez szükséges időt. Az előkészítési idő magában foglalja a biztonsági mentési bővítmény telepítését vagy frissítését, valamint a pillanatkép biztonsági mentési ütemezésnek megfelelő aktiválását.
**Adatátvitel** | Gondolja át, hogy Azure Backup az előző biztonsági mentés növekményes változásainak azonosításához szükséges idő.<br/><br/> A növekményes biztonsági mentések során az Azure Backup a blokk ellenőrzőösszegének kiszámításával határozza meg a módosításokat. Ha egy blokk módosul, a rendszer megjelöli a tárolóba való átvitelhez. A szolgáltatás elemzi az azonosított blokkokat, hogy tovább csökkenthesse az átvitelre váró adatok mennyiségét. Az összes módosított blokk kiértékelése után az Azure Backup átviszi a módosításokat a tárolóba.<br/><br/> A pillanatkép elkészítése és tárolóba való másolása között késés lehet. Csúcsidőben akár nyolc órát is igénybe vehet, hogy a pillanatképek átkerülnek a tárolóba. A virtuális gépek biztonsági mentésének időtartama a napi biztonsági mentés esetén 24 óránál kevesebb lesz.
**Kezdeti biztonsági mentés** | Bár a növekményes biztonsági mentések teljes időtartama kevesebb mint 24 óra, az első biztonsági mentésre ez nem feltétlenül igaz. A kezdeti biztonsági mentéshez szükséges idő az adatok méretétől és a biztonsági mentés feldolgozásának időpontjától függ.
**Üzenetsor visszaállítása** | Azure Backup egyszerre több tárfiókból is feldolgozza a visszaállítási feladatokat, és a visszaállítási kérelmeket egy üzenetsorba helyezi.
**Másolat visszaállítása** | A visszaállítási folyamat során a rendszer átmásolja az adatokat a tárolóból a tárfiókba.<br/><br/> A visszaállítás teljes időtartama a másodpercenkénti I/O-műveletektől (IOPS) és a tárfiók átviteli sebességétől függ.<br/><br/> A másolási idő csökkentése érdekében válasszon ki egy tárfiókot, amely nincs más alkalmazásírásokkal és -olvasásokkal együtt betöltve.

### <a name="backup-performance"></a>A biztonsági mentés teljesítménye

Ezek a gyakori forgatókönyvek befolyásolhatják a biztonsági mentés teljes idejét:

- **Új lemez hozzáadása egy védett Azure-beli virtuális géphez:** Ha egy virtuális gép növekményes biztonsági mentésen megy keresztül, és új lemezt ad hozzá, a biztonsági mentés ideje megnő. A biztonsági mentés teljes ideje az új lemez kezdeti replikációja, valamint a meglévő lemezek változásreplikációja miatt több mint 24 órát is igénybehet.
- **Töredezett lemezek:** A biztonsági mentési műveletek gyorsabbak, ha a lemezváltozások összefüggőek. Ha a módosítások szét vannak terülve és töredezettek egy lemezen, a biztonsági mentés lassabb lesz.
- **Lemezváltozás:** Ha a növekményes biztonsági mentésen áteső védett lemezek napi adatváltozása több mint 200 GB, a biztonsági mentés hosszú időt (több mint nyolc órát) vehet igénybe.
- **Biztonsági mentési verziók:** A Biztonsági mentés (más néven azonnali visszaállítás) legújabb verziója az ellenőrzőszámok összehasonlításához optimalizáltabb folyamatot használ a változások azonosításához. Ha azonban azonnali visszaállítást használ, és törölt egy biztonsági mentési pillanatképet, a biztonsági mentés az ellenőrző adatok összehasonlítására vált. Ebben az esetben a biztonsági mentési művelet túllépi a 24 órát (vagy sikertelen lesz).

### <a name="restore-performance"></a>Visszaállítási teljesítmény

Ezek a gyakori forgatókönyvek befolyásolhatják a visszaállítás teljes idejét:

- A visszaállítás teljes időtartama a másodpercenkénti bemeneti/kimeneti műveletektől (IOPS) és a tárfiók átviteli sebességétől függ.
- A visszaállítás teljes idejét befolyásolhatja, ha a cél tárfiók más alkalmazásírási és -olvasási műveletekkel van betöltve. A visszaállítási művelet javításához válasszon ki egy olyan tárfiókot, amely nincs más alkalmazásadatokkal feltöltve.

## <a name="best-practices"></a>Ajánlott eljárások

Ha virtuális gépek biztonsági mentését konfigurálja, javasoljuk, hogy kövesse a következő eljárásokat:

- Módosítsa a szabályzatban beállított alapértelmezett ütemezési időpontokat. Ha például a szabályzatban az alapértelmezett időpont 00:00, növelje az időzítést néhány perccel, hogy az erőforrás-használat optimális legyen.
- Ha egyetlen tárolóból állítja vissza a virtuális gépeket, javasoljuk, hogy használjon különböző általános célú [v2 tárfiókokat](../storage/common/storage-account-upgrade.md) annak érdekében, hogy a cél tárfiók ne legyen szabályozás alatt. Például minden virtuális gépnek külön tárfiókkal kell lennie. Ha például 10 virtuális gép van visszaállítva, használjon 10 különböző tárfiókot.
- A Premium Storage-ot azonnali visszaállítással használó virtuális gépek biztonsági mentéséhez javasoljuk, hogy foglaljon *le 50%-os* szabad területet a teljes lefoglalt tárterületből, ami csak az első biztonsági mentéshez szükséges.  Az 50%-os szabad terület nem követelmény a biztonsági mentéshez az első biztonsági mentés befejezése után
- A tárfiókonkénti lemezszám korlátozása attól függ, hogy milyen gyakorisággal használják a lemezeket az IaaS virtuális gépen futó alkalmazások. Általános gyakorlatként, ha 5–10 vagy több lemez található egyetlen tárfiókon, akkor a terhelést úgy egyenlítheti ki, hogy egyes lemezeket külön tárfiókokra helyez át.
- Ha felügyelt lemezeket használó virtuális gépeket szeretne visszaállítani a PowerShell használatával, adja meg a ***TargetResourceGroupName*** további paramétert annak az erőforráscsoportnak a megadásához, amelybe a felügyelt lemezek vissza lesznek adva. További információ itt [található.](./backup-azure-vms-automation.md#restore-managed-disks)

## <a name="backup-costs"></a>Biztonsági mentési költségek

Az azure-beli virtuális gépek biztonsági Azure Backup a díjszabás [Azure Backup vonatkoznak.](https://azure.microsoft.com/pricing/details/backup/)

A számlázás csak az első sikeres biztonsági mentés befejezése után kezdődik el. Ezen a ponton megkezdődik a tárolás és a védett virtuális gépek számlázása. A számlázás addig folytatódik, amíg a virtuális gép biztonsági másolati adatai egy tárolóban vannak tárolva. Ha leállítja egy virtuális gép védelmét, de a virtuális gép biztonsági mentési adatai egy tárolóban vannak, a számlázás folytatódik.

Egy adott virtuális gép számlázása csak akkor áll le, ha a védelem le lett állítva, és az összes biztonsági mentési adatot törölték. Ha a védelem leáll, és nincsenek aktív biztonsági mentési feladatok, a virtuális gép legutóbbi sikeres biztonsági mentésének mérete lesz a havi számlán használt védett példány mérete.

A védett példányok mérete a virtuális gép *tényleges* méretétől függ. A virtuális gép mérete a virtuális gépen található összes adat összege, kivéve az ideiglenes tárolót. A díjszabás az adatlemezen tárolt tényleges adatokon alapul, nem pedig a virtuális géphez csatolt egyes adatlemezek támogatott maximális méretén.

Hasonlóképpen, a biztonsági másolatok tárára vonatkozó számla az Azure Backup-ban tárolt adatmennyiségen alapul, amely az egyes helyreállítási pontokkal kapcsolatos tényleges adatok összege.

Vehet például egy A2-standard méretű virtuális gépet, amely két további, 32 TB-os maximális méretű adatlemezzel rendelkezik. Az alábbi táblázat az egyes lemezeken tárolt tényleges adatokat mutatja be:

**Lemez** | **Maximális méret** | **Tényleges adatok**
--- | --- | ---
Operációsrendszer-lemez | 32 TB | 17 GB
Helyi/ideiglenes lemez | 135 GB | 5 GB (nem tartalmazza a biztonsági mentést)
1. adatlemez | 32 TB| 30 GB
2. adatlemez | 32 TB | 0 GB

Ebben az esetben a virtuális gép tényleges mérete 17 GB + 30 GB + 0 GB = 47 GB. Ez a védett példányméret (47 GB) lesz a havi számla alapja. A virtuális gépen található adatok mennyiségének növekedésével a számlázási változásokhoz használt védett példány mérete is egyezni fog.

## <a name="next-steps"></a>Következő lépések

- [Felkészülés az Azure-beli virtuális gépek biztonsági mentéséhez.](backup-azure-arm-vms-prepare.md)
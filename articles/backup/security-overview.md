---
title: A biztonsági funkciók áttekintése
description: Ismerkedjen meg Azure Backup biztonsági képességekkel, amelyek segítenek a biztonsági másolatok adatainak védelmében és a vállalat biztonsági igényeinek kielégítésében.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 9aa1909f1590b477d9a7f7a09ad0c2b1936e3e29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96325655"
---
# <a name="overview-of-security-features-in-azure-backup"></a>A Azure Backup biztonsági funkcióinak áttekintése

Az adatai védelme érdekében a legfontosabb lépések egyike, hogy megbízható biztonsági mentési infrastruktúrát biztosítson. De éppen olyan fontos, hogy az adatokat biztonságos módon biztonsági mentést készítsen, és a biztonsági mentések mindig védve legyenek. Azure Backup biztosít biztonságot a biztonsági mentési környezet számára – mindkettő, ha az adatok átvitel közben és nyugalmi állapotban vannak. Ez a cikk felsorolja Azure Backup biztonsági képességeit, amelyek segítenek a biztonsági másolatok adatainak védelmében és a vállalat biztonsági igényeinek kielégítésében.

## <a name="management-and-control-of-identity-and-user-access"></a>Az identitás-és felhasználói hozzáférés kezelése és felügyelete

A Recovery Services-tárolók által használt Storage-fiókok el vannak különítve, és a felhasználók nem férhetnek hozzá semmilyen kártékony célra. A hozzáférés csak Azure Backup felügyeleti műveleteken, például a visszaállításon keresztül engedélyezett. A Azure Backup lehetővé teszi az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](./backup-rbac-rs-vault.md)használatával történő részletes hozzáférés révén a felügyelt műveletek szabályozását. Az Azure RBAC lehetővé teszi a feladatok elkülönítését a csapaton belül, és csak a feladataik elvégzéséhez szükséges felhasználók számára biztosít hozzáférést.

A Azure Backup három [beépített szerepkört](../role-based-access-control/built-in-roles.md) biztosít a biztonságimásolat-kezelési műveletek vezérléséhez:

* Biztonsági mentési közreműködő – biztonsági másolatok létrehozása és kezelése, kivéve Recovery Services tár törlését és mások számára való hozzáférést biztosít
* Backup operátor – minden közreműködő a biztonsági mentés és a biztonsági mentési házirendek kezelése kivételével
* Backup Reader – engedélyek az összes biztonsági mentési kezelési művelet megtekintéséhez

További információ az [Azure szerepköralapú hozzáférés-vezérléséről Azure Backup kezeléséhez](./backup-rbac-rs-vault.md).

A Azure Backup számos, a szolgáltatásba beépített biztonsági vezérlőt tartalmaz a biztonsági rések megelőzésére, észlelésére és reagálására. További információ a [Azure Backup biztonsági vezérlőinek](./security-baseline.md).

## <a name="separation-between-guest-and-azure-storage"></a>A vendég és az Azure Storage közötti elkülönítés

A Azure Backup, amely magában foglalja a virtuális gépek biztonsági mentését és az SQL-t, és SAP HANA a virtuális gép biztonsági mentésében, a biztonsági mentési információ az Azure Storage-ban tárolódik, és a vendégnek nincs közvetlen hozzáférése a biztonsági mentési tárolóhoz vagy  A virtuális gép biztonsági mentése esetén a biztonsági mentés pillanatképének létrehozása és tárolása az Azure-hálón történik, ahol a vendég nem vesz részt az alkalmazás-konzisztens biztonsági másolatok űrlapsablon fokozatos leválasztása.  Az SQL és a SAP HANA használatával a biztonsági mentési bővítmény ideiglenes hozzáférést kap az adott blobokhoz való íráshoz.  Így még a feltört környezetben is a meglévő biztonsági mentéseket nem lehet illetéktelenül módosítani vagy törölni a vendég.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Az Azure virtuális gép biztonsági mentéséhez nem szükséges internetkapcsolat

Az Azure-beli virtuális gépekről készült biztonsági mentéshez a virtuális gép lemezéről az Recovery Services-tárolóba történő adatátvitelre van szükség. Azonban az összes szükséges kommunikáció és adatforgalom csak az Azure gerinces hálózaton történik, anélkül, hogy hozzá kellene férnie a virtuális hálózathoz. Ezért a biztonságos hálózatokon belül elhelyezett Azure-beli virtuális gépek biztonsági mentése nem igényli, hogy bármilyen IP-cím vagy teljes tartománynév számára engedélyezze a hozzáférést.

## <a name="private-endpoints-for-azure-backup"></a>Azure Backup magánhálózati végpontok

Mostantól [privát végpontokat](../private-link/private-endpoint-overview.md) is használhat az adatok biztonságos biztonsági mentésére a virtuális hálózaton belüli kiszolgálókról a Recovery Services-tárolóba. A privát végpont egy IP-címet használ a tár VNET, így nem kell közzétennie a virtuális hálózatokat a nyilvános IP-címekhez. Az Azure-beli virtuális gépeken futó SQL-és SAP HANA-adatbázisok biztonsági mentésére és visszaállítására privát végpontok használhatók. A MARS-ügynököt használó helyszíni kiszolgálókhoz is használható.

További információ a Azure Backup privát végpontokról [itt](./private-endpoints.md)olvasható.

## <a name="encryption-of-data"></a>Adattitkosítás

A titkosítás védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében. Az adattitkosítás a Azure Backup számos fázisában történik:

* Az Azure-on belül az Azure Storage és a tároló közötti adatátvitel [https-védelemmel](backup-support-matrix.md#network-traffic-to-azure)történik. Ezek az adatközpontok az Azure gerinc hálózatán maradnak.

* A biztonsági mentési adatai automatikusan titkosítva vannak a [platform által felügyelt kulcsokkal](backup-encryption.md), és nem szükséges explicit műveletet végrehajtani az engedélyezéshez. A biztonsági másolatok adatait a Azure Key Vault tárolt [ügyfelek által felügyelt kulcsok](encryption-at-rest-with-cmk.md) használatával is titkosíthatja. Ez a Recovery Services-tárolóba mentett összes munkaterhelésre vonatkozik.

* Azure Backup támogatja az olyan Azure-beli virtuális gépek biztonsági mentését és visszaállítását, amelyek az operációs rendszer/adatlemezei [Azure Disk Encryption (ADE)](backup-azure-vms-encryption.md#encryption-support-using-ade) és a [CMK titkosított lemezekkel titkosított virtuális gépekkel](backup-azure-vms-encryption.md#encryption-using-customer-managed-keys)rendelkeznek. További információért olvassa el a [titkosított Azure-beli virtuális gépek és a Azure Backup](./backup-azure-vms-encryption.md).

* Ha az adatok biztonsági mentést végeznek a helyszíni kiszolgálókról a MARS-ügynökkel, az adatok titkosítva lesznek a jelszóval, és csak azután, hogy a rendszer letölti a Azure Backup, és visszafejti azt Azure Backup. További információ a [biztonsági funkciókról a hibrid biztonsági másolatok védelméhez](#security-features-to-help-protect-hybrid-backups).

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>A véletlen törlésből származó biztonsági mentési adatok védelme

A Azure Backup biztonsági funkciókat biztosít a biztonsági mentési adatainak a törlés után még a védelme érdekében. Ha a felhasználó törli a virtuális gép biztonsági mentését, a törlést követően a biztonsági mentési adat 14 további napig megmarad, így a biztonsági mentési elem helyreállítása adatvesztés nélkül történik. A "Soft Delete" állapotban lévő biztonsági mentési adatok további 14 napos megőrzése nem jár semmilyen költséggel. [További információ a Soft delete](backup-azure-security-feature-cloud.md)szolgáltatásról.

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Gyanús tevékenység figyelése és riasztásai

A Azure Backup [beépített figyelési és riasztási képességeket](./backup-azure-monitoring-built-in-monitor.md) biztosít a Azure Backuphoz kapcsolódó események műveleteinek megtekintésére és konfigurálására. A [biztonsági mentési jelentések](./configure-reports.md) a használat nyomon követésére, a biztonsági mentések és a visszatárolások naplózására, valamint a különböző részletességi szinteken a főbb trendek azonosítására szolgálnak. A Azure Backup monitorozási és jelentéskészítési eszközeivel azonnal riasztást kaphat a jogosulatlan, gyanús vagy rosszindulatú tevékenységekről, amint azok bekövetkeznek.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Biztonsági funkciók a hibrid biztonsági másolatok védelme érdekében

A Azure Backup szolgáltatás a Microsoft Azure Recovery Services (MARS) ügynököt használja a fájlok, mappák, valamint a kötet vagy a rendszerállapot biztonsági mentésére és visszaállítására egy helyszíni számítógépről az Azure-ba. A MARS mostantól biztonsági funkciókat biztosít a hibrid biztonsági másolatok védelméhez. Ezek a funkciók a következők:

* A rendszer egy további hitelesítési réteget ad hozzá, amikor egy kritikus művelet, például a jelszó módosítása történik. Ez az ellenőrzés biztosítja, hogy ezeket a műveleteket csak azok a felhasználók tudják végrehajtani, akik rendelkeznek érvényes Azure-beli hitelesítő adatokkal. [További információ a támadásokat megakadályozó funkciókról](./backup-azure-security-feature.md#prevent-attacks).

* A törölt biztonsági mentési adatok a törlés időpontjától számítva további 14 napig megmaradnak. Ez egy adott időszakon belül gondoskodik az adathelyreállításról, így az adatvesztés még akkor sem, ha támadás történik. Emellett a rendszer a minimális helyreállítási pontok számát is megőrzi a sérült információk védelme érdekében. [További információ a törölt biztonsági másolatok adatainak helyreállításáról](./backup-azure-security-feature.md#recover-deleted-backup-data).

* A Microsoft Azure Recovery Services-(MARS-) ügynök használatával biztonsági mentést biztosító hozzáférési kód segítségével biztosítható, hogy az adatok titkosítva legyenek a feltöltés előtt Azure Backup és visszafejtése csak Azure Backup letöltése után. A hozzáférési kód adatai csak a jelszót létrehozó felhasználó és a hozzá konfigurált ügynök számára érhetők el. A szolgáltatás nem küldi el és nem osztja meg a szolgáltatást. Ez biztosítja az adatbiztonságot, mivel a véletlenül kitett, szándékosan (például a hálózaton lévő, a hálózatban található) adat nem használható a jelszó nélkül, és a jelszót a rendszer nem küldi el a hálózaton keresztül.

## <a name="compliance-with-standardized-security-requirements"></a>Megfelelőség a szabványosított biztonsági követelményekkel

Annak érdekében, hogy a szervezetek megfeleljenek az egyéni adatgyűjtésre és-felhasználásra vonatkozó nemzeti, regionális és iparági követelményeknek, Microsoft Azure & Azure Backup a minősítések és igazolások átfogó készletét kínálja. [Tekintse meg a megfelelőségi tanúsítványok listáját](compliance-offerings.md)

## <a name="next-steps"></a>Következő lépések

* [Biztonsági funkciók a Azure Backupt használó Felhőbeli munkaterhelések védelmének biztosításához](backup-azure-security-feature-cloud.md)
* [Biztonsági funkciók a Azure Backupt használó hibrid biztonsági másolatok védelméhez](backup-azure-security-feature.md)
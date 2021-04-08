---
title: A Hyper-V vész-helyreállítási architektúrája Azure Site Recovery
description: Ez a cikk áttekintést nyújt azokról az összetevőkről és architektúráról, amelyeket a rendszer a Azure Site Recovery szolgáltatással az Azure-ba irányuló helyszíni Hyper-V virtuális gépek (VMM nélkül) üzembe helyezéséhez használ.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: c5025b83619b505728bfdf5c4e1ccc81d3bb225e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654761"
---
# <a name="hyper-v-to-azure-disaster-recovery-architecture"></a>Hyper-V – Azure vész-helyreállítási architektúra


Ez a cikk a Hyper-V virtuális gépek (VM-EK) a helyszíni Hyper-V-gazdagépek és az Azure között a [Azure site Recovery](site-recovery-overview.md) szolgáltatással történő replikálása, áttelepítése és helyreállítása során használt architektúrát és folyamatokat ismerteti.

A Hyper-V-gazdagépek igény szerint kezelhetők System Center Virtual Machine Manager (VMM) privát felhőkben.



## <a name="architectural-components---hyper-v-without-vmm"></a>Architektúra-összetevők – Hyper-V VMM nélkül

A következő táblázat és ábra áttekintést nyújt az Azure-ba irányuló Hyper-V-replikációhoz használt összetevőkről, ha a Hyper-V-gazdagépeket nem a VMM felügyeli.

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Azure-előfizetés, Azure Storage-fiók és Azure-hálózat. | A helyszíni virtuálisgép-munkaterhelések replikált adatait a rendszer a Storage-fiókban tárolja. Az Azure-beli virtuális gépek a replikált számítási feladatok adataival jönnek létre, amikor a helyszíni helyről történik feladatátvétel.<br/><br/> Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**Hyper-V** | Site Recovery üzembe helyezés során a Hyper-V-gazdagépeket és-fürtöket Hyper-V-helyekre gyűjti. A Azure Site Recovery-szolgáltatót és a Recovery Services ügynököt mindegyik különálló Hyper-V gazdagépre, vagy minden Hyper-V fürtcsomóponton telepíti. | A Provider a Site Recoveryvel az interneten keresztül vezényli a replikációt. Az adatreplikációt a Recovery Services-ügynök kezeli.<br/><br/> A Provider és az Agent kommunikációja biztonságos, titkosított csatornákon történik. Ezenfelül az Azure-tárfiókba replikált adatok is titkosítást kapnak.
**Hyper-V virtuális gépek** | Egy vagy több Hyper-V-n futó virtuális gép. | Semmit nem kell explicit módon telepíteni a virtuális gépekre.


**Hyper-V – Azure architektúra (VMM nélkül)**

![A helyszíni Hyper-V-helyet az Azure architektúrára VMM nélkül bemutató ábra.](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)


## <a name="architectural-components---hyper-v-with-vmm"></a>Architektúra-összetevők – Hyper-V és VMM

A következő táblázat és ábra áttekintést nyújt az Azure-ba irányuló Hyper-V-replikációhoz használt összetevőkről, ha a Hyper-V-gazdagépeket a VMM-felhőkben kezelik.

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Azure-előfizetés, Azure Storage-fiók és Azure-hálózat. | A helyszíni virtuálisgép-munkaterhelések replikált adatait a rendszer a Storage-fiókban tárolja. Az Azure-beli virtuális gépek a replikált adatokkal jönnek létre, amikor a helyszíni helyről történik feladatátvétel.<br/><br/> Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**VMM-kiszolgáló** | A VMM-kiszolgáló egy vagy több, Hyper-V-gazdagépeket tartalmazó felhőt tartalmaz. | A Site Recovery szolgáltatót a VMM-kiszolgálóra kell telepítenie, hogy a replikációt a Site Recovery használatával hangolja össze, és regisztrálja a kiszolgálót a Recovery Services-tárolóban.
**Hyper-V gazdagép** | A VMM által felügyelt egy vagy több Hyper-V-gazdagép/-fürt. |  A Recovery Services-ügynököt minden egyes Hyper-V-gazdagépre vagy-fürtcsomóponton telepíti.
**Hyper-V virtuális gépek** | Hyper-V-gazdakiszolgálón futó egy vagy több virtuális gép. | A virtuális gépekre semmit nem kell explicit módon telepíteni.
**Hálózat** | A VMM-kiszolgálón beállított logikai- és virtuálisgép-hálózatok. A virtuálisgép-hálózatot a felhőhöz társított logikai hálózattal kell összekapcsolni. | A virtuálisgép-hálózatok az Azure-beli virtuális hálózatokra vannak leképezve. Ha az Azure-beli virtuális gépek a feladatátvétel után jönnek létre, a rendszer hozzáadja azokat a virtuálisgép-hálózathoz hozzárendelt Azure-hálózathoz.

**Hyper-V – Azure architektúra (VMM)**

![A helyszíni Hyper-V-helyet bemutató diagram az Azure-architektúrához a VMM használatával.](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)

## <a name="set-up-outbound-network-connectivity"></a>Kimenő hálózati kapcsolat beállítása

Ahhoz, hogy a Site Recovery a várt módon működjön, módosítania kell a kimenő hálózati kapcsolatot, hogy a környezet replikálása engedélyezve legyen.

> [!NOTE]
> A Site Recovery nem támogatja a hitelesítési proxy használatát a hálózati kapcsolat vezérléséhez.

### <a name="outbound-connectivity-for-urls"></a>Kimenő kapcsolat URL-címek esetén

Ha URL-alapú tűzfal-proxyt használ a kimenő kapcsolatok vezérléséhez, engedélyezze az alábbi URL-címek elérését:

| **Név**                  | **Kereskedelmi**                               | **Államigazgatás**                                 | **Leírás** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Tárolás                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Lehetővé teszi az adatok írását a virtuális gépről a forrásrégió gyorsítótárjának tárfiókjába. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Hitelesítést és engedélyezést biztosít a Site Recovery szolgáltatás URL-címeihez. |
| Replikáció               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Lehetővé teszi a virtuális gép és a Site Recovery szolgáltatás közötti kommunikációt. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Lehetővé teszi a virtuális gép számára a Site Recovery monitorozási és diagnosztikai adatainak írását. |


## <a name="replication-process"></a>Replikációs folyamat

![A Hyper-V és az Azure közötti replikálás folyamatát bemutató ábra](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Replikálási és helyreállítási folyamat**


### <a name="enable-protection"></a>Védelem engedélyezése

1. Miután engedélyezte a védelmet egy Hyper-V-alapú virtuális gép esetében az Azure Portalon vagy a helyszíni környezetben, elindul a **Védelem engedélyezése** feladat.
2. A feladat ellenőrzi, hogy a gép megfelel-e az előfeltételeknek, mielőtt meghívja a [CreateReplicationRelationship](/windows/win32/hyperv_v2/createreplicationrelationship-msvm-replicationservice) metódust, amely az Ön által megadott beállításoknak megfelelően beállítja a replikációt.
3. A feladat elindítja a kezdeti replikációt a [StartReplication](/windows/win32/hyperv_v2/startreplication-msvm-replicationservice) metódus meghívásával egy teljes körű virtuálisgép-replikáció elindítása céljából, majd a virtuális gépek virtuális lemezeit továbbítja az Azure-ba.
4. A feladatot a **feladatok** lapon követheti nyomon.      ![Képernyőfelvétel a feladatok lap feladatok listájáról. ](media/hyper-v-azure-architecture/image1.png) ![A védelem engedélyezése képernyő képernyőképe további részletekkel.](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Kezdeti adatreplikálás

1. A kezdeti replikáció indításakor a rendszer a [Hyper-V virtuális gép pillanatképének](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560637(v=ws.10)) pillanatképét is elvégzi.
2. A virtuális GÉPEN lévő virtuális merevlemezeket egyenként replikálja a rendszer, amíg az összes Azure-ba át nem másolja őket. A virtuális gép méretétől és a hálózati sávszélességtől függően ez eltarthat egy ideig. [Megtudhatja, hogyan](https://support.microsoft.com/kb/3056159) növelheti a hálózati sávszélességet.
3. Ha a lemez megváltozik, miközben a kezdeti replikálás folyamatban van, a Hyper-V replika replikációs nyomon követése a változásokat Hyper-V replikációs naplókként (. HRL) követi nyomon. Ezek a naplófájlok ugyanabban a mappában találhatók, mint a lemezek. Minden lemezhez tartozik egy. HRL fájl, amelyet a rendszer a másodlagos tárolóba továbbít. A pillanatkép- és a naplófájlok a kezdeti replikáció végrehajtása közben is lemezerőforrásokat használnak.
4. A kezdeti replikáció befejeztével a rendszer törli a virtuális gép pillanatképét.
5. A rendszer a naplózott lemezmódosításokat szinkronizálja, és egyesíti a szülőlemezzel.


### <a name="finalize-protection-process"></a>Védelmi folyamat véglegesítése

1. A kezdeti replikálás befejeződése után a **védelem véglegesítése a virtuális gép** feladatainak futtatásával végezhető el. Konfigurálja a hálózatot és más replikáció utáni beállításokat, hogy a virtuális gép védve legyen.
2. Ekkor megtekintheti a virtuális gép beállításait, és meggyőződhet arról, hogy készen áll a feladatátvételre. A virtuális gép vész-helyreállítási gyakorlata (feladatátvételi teszt) futtatásával ellenőrizheti, hogy az elvárt módon működik-e. 


## <a name="delta-replication"></a>Változásreplikáció

1. A kezdeti replikálást követően a replikációs házirendnek megfelelően elindul a különbözeti replikáció.
2. A Hyper-V replika-replikációs nyomkövetés nyomon követi a virtuális merevlemezek változásait. HRL-fájlként. Minden replikációra konfigurált lemezhez tartozik egy .hrl fájl.
3. A rendszer elküldi a naplót az ügyfél Storage-fiókjába. Amikor egy napló átkerül az Azure-ba, az elsődleges lemez változásai egy másik naplófájlban, ugyanabban a mappában vannak követve.
4. A kezdeti és a különbözeti replikáció során a Azure Portalban figyelheti a virtuális gépet.

### <a name="resynchronization-process"></a>Újraszinkronizálási folyamat

1. Ha nem sikerül a változások replikálása, és a teljes replikáció túl sok sávszélességet vagy időt venne igénybe, a rendszer a virtuális gépet megjelöli újraszinkronizálásra.
    - Ha például a .hrl-fájlok mérete eléri a lemezkapacitás 50%-át, a rendszer kijelöli a virtuális gépet újraszinkronizálásra.
    -  Alapértelmezés szerint az újraszinkronizálás úgy van ütemezve, hogy az munkaidőn kívül automatikusan fusson.
1.  Az Újraszinkronizálás csak a különbözeti adatokat küldi el.
    - Ez minimálisra csökkentheti a forrás és a cél virtuális gépek által a számítástechnikai ellenőrzőösszegekkel továbbított adatmennyiséget.
    - Egy rögzített blokkos adatdarabolási algoritmust használ, amelyben a forrás-és a célfájl rögzített adattömbökre van osztva.
    - Az egyes adattömbökhöz tartozó ellenőrzőösszegek jönnek létre. Ezeket a rendszer összehasonlítja azzal, hogy meghatározza, hogy a forrás mely blokkokat kell alkalmazni a célra.
2. Az újraszinkronizálás befejezését követően folytatódik a normál változásreplikálás.
3. Ha nem szeretné megvárni az alapértelmezett újraszinkronizálást a munkaidőn kívül, manuálisan is szinkronizálhatja a virtuális gépet. Ha például áramkimaradás történik. Ehhez a Azure Portal válassza ki a virtuális gépet > **Újraszinkronizálás** lehetőséget.

    ![Az újraszinkronizálási lehetőséget ábrázoló képernyőkép.](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Újrapróbálkozási folyamat

Ha hiba lép fel a replikáció során, a rendszer automatikusan újrapróbálkozik. Az újrapróbálkozás a táblázatban leírt módon van osztályozva.

**Kategória** | **Részletek**
--- | ---
**Helyreállíthatatlan hibák** | A rendszer nem kísérli meg a helyreállításukat. A virtuális gép állapota **Kritikusra** vált, és rendszergazdai beavatkozás szükséges.<br/><br/> Ilyen hibák például a törött VHD-lánc, a replika virtuális gép érvénytelen állapota, a hálózati hitelesítési hibák, az engedélyezési hibák és a virtuális gép nem található hibák (önálló Hyper-V-kiszolgálók esetén).
**Helyreállítható hibák** | A rendszer minden replikálási időközben újrapróbálkozik exponenciális visszalépéssel, amely az újrapróbálkozás időközét az első kísérlet kezdetétől számított 1, 2, 4, 8 és 10 percre növeli. Ha a hiba nem szűnik meg, a rendszer 30 percenként újrapróbálkozik. Ilyenek például a hálózati hibák, az alacsony lemezes hibák és az alacsony memória-feltételek.



## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

1. Futtathat tervezett vagy nem tervezett feladatátvételt a helyszíni Hyper-V virtuális gépekről az Azure-ra. Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés. Nem tervezett feladatátvétel futtatása, ha az elsődleges hely nem érhető el.
2. Elvégezheti egy gép feladatátadását, de létrehozhat több gép összehangolt feladatátadását tartalmazó helyreállítási terveket is.
3. Feladatátvételt futtat. A feladatátvétel első lépése után látnia kell a létrehozott replika virtuális gépeket az Azure-ban. Hozzárendelhet egy nyilvános IP-címet a virtuális géphez, ha szükséges.
4. Ezután véglegesíti a feladatátvételt, hogy megkezdje a munkaterhelések elérését a replika Azure-beli virtuális gépről.

Miután a helyszíni infrastruktúra újra működik, visszatérhet. A feladat-visszavétel három szakaszban fordul elő:

1. Indítsa el a tervezett feladatátvételt az Azure-ból a helyszíni helyre:
    - Az **állásidő csökkentése**: Ha ezt a beállítást használja, site Recovery a feladatátvétel előtt szinkronizálja az adatokat. Ellenőrzi a módosított adatblokkokat, és letölti azokat a helyszíni helyre, míg az Azure-beli virtuális gép folyamatosan fut, és minimalizálja az állásidőt. Ha manuálisan megadja, hogy a feladatátvétel befejeződik, az Azure-beli virtuális gép leáll, a rendszer minden végső változást átmásol, és elindul a feladatátvétel.
    - **Teljes letöltés**: ezzel a beállítással az adatokat szinkronizálja a rendszer a feladatátvétel során. Ez a lehetőség letölti a teljes lemezt. Gyorsabb, mert nincs ellenőrzőösszeg kiszámítva, de több állásidő is van. Akkor használja ezt a beállítást, ha a replika Azure-beli virtuális gépeket egy ideig, vagy ha a helyszíni virtuális gépet törölték.
    - **Virtuális gép létrehozása**: kiválaszthatja, hogy a feladat-visszavétel ugyanarra a virtuális gépre vagy egy másik virtuális gépre történjen. Azt is megadhatja, hogy Site Recovery hozza létre a virtuális gépet, ha még nem létezik.

2. A kezdeti szinkronizálás befejeződése után kiválaszthatja a feladatátvétel befejezését. A befejezést követően bejelentkezhet a helyszíni virtuális gépre, és megvizsgálhatja, hogy minden a várt módon működik-e. A Azure Portalban láthatja, hogy az Azure-beli virtuális gépek le lettek állítva.
3.  Ezután véglegesíti a feladatátvételt a befejezésig, és ismét megkezdi a munkaterhelések elérését a helyszíni virtuális gépről.
4. A munkaterhelések visszaállítása után engedélyezze a visszirányú replikálást, hogy a helyszíni virtuális gépek újra replikálódnak az Azure-ba.



## <a name="next-steps"></a>Következő lépések


[Ez az oktatóanyag](tutorial-prepare-azure.md) ismerteti a Hyper-V és az Azure közötti replikáció megkezdését.



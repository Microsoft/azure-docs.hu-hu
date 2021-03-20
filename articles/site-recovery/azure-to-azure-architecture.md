---
title: Azure-ról Azure-ra Azure Site Recovery vész-helyreállítási architektúrája
description: Az Azure-beli virtuális gépek Azure-régiói közötti vész-helyreállítást biztosító architektúra áttekintése, az Azure Site Recovery szolgáltatás használatával.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: raynew
ms.openlocfilehash: 64d1084fd7025c74676977f065062e5e94dabf1d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97652245"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Az Azure-ból Azure-ba történő vészhelyreállítás architektúrája


Ez a cikk azokat az architektúrát, összetevőket és folyamatokat ismerteti, amelyeket az Azure-beli virtuális gépek (VM-EK) vész-helyreállításának telepítésekor használ a [Azure site Recovery](site-recovery-overview.md) szolgáltatással. A vész-helyreállítási beállítással az Azure-beli virtuális gépek folyamatosan replikálódnak egy másik célcsoportra. Leállás esetén a virtuális gépek átadhatók a másodlagos régiónak, és onnan érhetik el azokat. Ha minden a szokásos módon fut, visszatérhet, és folytathatja a munkát az elsődleges helyen.



## <a name="architectural-components"></a>Az architektúra összetevői

Az Azure-beli virtuális gépek vész-helyreállításában részt vevő összetevőket a következő táblázat foglalja össze.

**Összetevő** | **Követelmények**
--- | ---
**A forrás régióban lévő virtuális gépek** | Egy másik Azure-beli virtuális gép egy [támogatott forrás-régióban](azure-to-azure-support-matrix.md#region-support).<br/><br/> A virtuális gépek bármely [támogatott operációs rendszert](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)futtatnak.
**Forrásként szolgáló virtuális gép tárolója** | Az Azure-beli virtuális gépek felügyelhetők, vagy nem felügyelt lemezeket osztanak szét a Storage-fiókok között.<br/><br/>[További információ a](azure-to-azure-support-matrix.md#replicated-machines---storage) támogatott Azure Storage-ról.
**Forrás virtuálisgép-hálózatok** | A virtuális gépek egy vagy több alhálózatban találhatók a forrás régiójában (VNet). [További](azure-to-azure-support-matrix.md#replicated-machines---networking) információ a hálózatkezelési követelményekről.
**Gyorsítótáras Storage-fiók** | A forrásoldali hálózatban szüksége lesz egy gyorsítótárbeli Storage-fiókra. A replikáció során a virtuális gépek módosításait a rendszer a gyorsítótárban tárolja, mielőtt elküldi őket a célként megadott tárhelyre.  A gyorsítótár-tárolási fiókoknak standard szintűnek kell lenniük.<br/><br/> A gyorsítótár használatával minimális hatással lehet a virtuális gépeken futó éles alkalmazásokra.<br/><br/> [További](azure-to-azure-support-matrix.md#cache-storage) információ a gyorsítótár tárolási követelményeiről. 
**Cél erőforrásai** | A cél erőforrásait a rendszer a replikáció során, valamint feladatátvétel esetén használja. A Site Recovery alapértelmezés szerint beállíthatja a célként megadott erőforrást, vagy létrehozhat/testreszabhatja őket.<br/><br/> Győződjön meg arról, hogy a célként megadott régióban virtuális gépeket hozhat létre, és hogy az előfizetése elegendő erőforrással rendelkezik a megcélzott régióban szükséges virtuálisgép-méretek támogatásához. 

![A forrás-és a cél replikálását bemutató ábra.](./media/concepts-azure-to-azure-architecture/enable-replication-step-1-v2.png)

## <a name="target-resources"></a>Cél erőforrásai

Amikor engedélyezi a replikációt egy virtuális gép számára, Site Recovery lehetőséget ad a célként megadott erőforrások automatikus létrehozására. 

**Célerőforrás** | **Alapértelmezett beállítás**
--- | ---
**Cél-előfizetés** | Ugyanaz, mint a forrás-előfizetés.
**Cél-erőforráscsoport** | Az erőforráscsoport, amelyhez a virtuális gépek a feladatátvételt követően tartoznak.<br/><br/> Bármely Azure-régióban lehet, kivéve a forrás-régiót.<br/><br/> A Site Recovery egy új erőforráscsoportot hoz létre a célként megadott régióban egy "ASR" utótaggal.<br/><br/>
**Cél VNet** | A virtuális hálózat (VNet), amelyben a replikált virtuális gépek a feladatátvétel után találhatók. A rendszer a forrás és a cél virtuális hálózatok között hozza létre a hálózati leképezést, és fordítva.<br/><br/> A Site Recovery egy új VNet és alhálózatot hoz létre az "ASR" utótaggal.
**Cél Storage-fiók** |  Ha a virtuális gép nem felügyelt lemezt használ, akkor ez az a Storage-fiók, amelybe a rendszer replikálja az adattárakat.<br/><br/> A Site Recovery egy új Storage-fiókot hoz létre a célként megadott régióban, hogy tükrözze a forrásként szolgáló Storage-fiókot.
**Replika által felügyelt lemezek** | Ha a virtuális gép felügyelt lemezt használ, akkor azokat a felügyelt lemezeket, amelyekhez az adatreplikációt végzi.<br/><br/> A Site Recovery replika felügyelt lemezeket hoz létre a tárolási régióban a forrás tükrözéséhez.
**Cél rendelkezésre állási csoportok** |  Az a rendelkezésre állási csoport, amelyben a replikáló virtuális gépek a feladatátvétel után találhatók.<br/><br/> Site Recovery létrehoz egy rendelkezésre állási készletet a célként megadott régióban az "ASR" utótaggal a forrás helyének rendelkezésre állási csoportjában található virtuális gépek esetében. Ha létezik egy rendelkezésre állási csoport, az használatban van, és a rendszer nem hoz létre újat.
**Cél rendelkezésre állási zónák** | Ha a célként megadott régió támogatja a rendelkezésre állási zónákat, akkor Site Recovery ugyanazt a zónát rendeli hozzá, mint amelyet a forrás régióban használt.

### <a name="managing-target-resources"></a>A cél erőforrásainak kezelése

A cél erőforrásait a következőképpen kezelheti:

- A cél beállításait a replikálás engedélyezésekor módosíthatja.
- A célhely beállításait a replikálás után is módosíthatja. Vegye figyelembe, hogy a célként megadott virtuális gép alapértelmezett SKU-jának megegyeznek a forrásként szolgáló virtuális gép SKU-jának (vagy a forrásként szolgáló virtuálisgép-SKU-hoz képest a következő legmegfelelőbb SKU-val). Hasonló más erőforrásokhoz, például a célként megadott erőforráscsoport, a célként megadott név és mások számára, a cél régió virtuális gép SKU-jának frissítése a replikáció előtt is lehetséges. A nem frissíthető erőforrás a rendelkezésre állási típus (egy példány, a készlet vagy a zóna). A beállítás módosításához le kell tiltania a replikációt, módosítania kell a beállítást, majd újra kell engedélyeznie. 


## <a name="replication-policy"></a>Replikációs szabályzat 

Az Azure-beli virtuális gépek replikálásának engedélyezésekor a Site Recovery alapértelmezés szerint létrehoz egy új replikációs házirendet a táblázatban összegzett alapértelmezett beállításokkal.

**Házirend-beállítás** | **Részletek** | **Alapértelmezett**
--- | --- | ---
**Helyreállítási pont megőrzése** | Meghatározza, hogy a Site Recovery mennyi ideig tart a helyreállítási pontok | 24 óra
**Alkalmazás-konzisztens pillanatkép gyakorisága** | Milyen gyakran Site Recovery egy alkalmazás-konzisztens pillanatképet. | Négy óránként

### <a name="managing-replication-policies"></a>Replikációs házirendek kezelése

Az alapértelmezett replikációs házirendek beállításait a következőképpen kezelheti és módosíthatja:
- A beállításokat a replikálás engedélyezése után módosíthatja.
- Bármikor létrehozhat egy replikációs szabályzatot, majd alkalmazhatja azt a replikálás engedélyezésekor.

### <a name="multi-vm-consistency"></a>Több virtuális gépre kiterjedő konzisztencia

Ha azt szeretné, hogy a virtuális gépek együtt replikálják őket, és megosztott összeomlás-konzisztens és alkalmazás-konzisztens helyreállítási pontokat biztosítanak a feladatátvétel során, akkor egyesítheti őket egy replikációs csoportba. A több virtuális gépre kiterjedő konzisztencia hatással van a számítási feladatok teljesítményére, és csak olyan munkaterheléseket futtató virtuális gépek esetében használható, amelyek az összes gépen konzisztencia szükségesek. 



## <a name="snapshots-and-recovery-points"></a>Pillanatképek és helyreállítási pontok

A helyreállítási pontok a virtuálisgép-lemezek egy adott időpontban vett pillanatképei alapján jönnek létre. Ha feladatátvételt végez egy virtuális gépen, egy helyreállítási pont használatával állíthatja vissza a virtuális gépet a célhelyen.

A feladatátvétel során általában biztosítani szeretnénk, hogy a virtuális gép ne legyen sérülés vagy adatvesztés nélkül, és hogy a virtuális gép adatai konzisztensek legyenek az operációs rendszer és a virtuális gépen futó alkalmazások esetében. Ez az elkészített Pillanatképek típusától függ.

A Site Recovery a következőképpen veszi fel a pillanatképeket:

1. Az Site Recovery alapértelmezés szerint az összeomlás-konzisztens pillanatképeket és az alkalmazással konzisztens pillanatképeket vesz igénybe, ha megadja a gyakoriságot.
2. A helyreállítási pontok a pillanatképek alapján jönnek létre, és a replikációs házirend megőrzési beállításoknak megfelelően tárolódnak.

### <a name="consistency"></a>Konzisztencia

A következő táblázat a konzisztencia különböző típusait ismerteti.

### <a name="crash-consistent"></a>Összeomlás – konzisztens

**Leírás** | **Részletek** | **Ajánlás**
--- | --- | ---
Az összeomlás-konzisztens Pillanatképek rögzítik a lemezen lévő, a pillanatkép elkészítéséhez szükséges adatok mennyiségét. Nem tartalmaz semmit a memóriában.<br/><br/> Tartalmazza a lemezen lévő adatok megfelelőjét, amely akkor jelenik meg, ha a virtuális gép összeomlott vagy a tápkábelt a kiszolgálóról húzta le a pillanattól kezdve, hogy a pillanatkép elkészítése megtörtént.<br/><br/> Az összeomlás-konzisztens érték nem garantálja az operációs rendszer vagy a virtuális gépen futó alkalmazások adatkonzisztenciáját. | A Site Recovery alapértelmezés szerint öt percenként hoz létre összeomlás-konzisztens helyreállítási pontokat. Ez a beállítás nem módosítható.<br/><br/>  | Napjainkban a legtöbb alkalmazás jól helyreállítható az összeomlás-konzisztens pontokból.<br/><br/> Az összeomlás-konzisztens helyreállítási pontok általában elegendőek az operációs rendszerek és az alkalmazások, például a DHCP-kiszolgálók és a nyomtatókiszolgálók replikálásához.

### <a name="app-consistent"></a>Alkalmazás – konzisztens

**Leírás** | **Részletek** | **Ajánlás**
--- | --- | ---
Az alkalmazással konzisztens helyreállítási pontok az alkalmazással konzisztens Pillanatképek alapján jönnek létre.<br/><br/> Az alkalmazás-konzisztens Pillanatképek tartalmazzák az összeomlás-konzisztens Pillanatképek összes adatát, valamint a memóriában lévő összes adatot és a folyamatban lévő tranzakciókat. | Az alkalmazással konzisztens Pillanatképek a Kötet árnyékmásolata szolgáltatást (VSS) használják:<br/><br/>   1.) Azure Site Recovery a csak másolás biztonsági mentési (VSS_BT_COPY) metódust használja, amely nem módosítja a Microsoft SQL tranzakciós naplójának biztonsági mentésének idejét és sorszámát. </br></br> 2) Ha pillanatképet kezdeményez, a VSS a köteten egy másolási írási (COW) műveletet hajt végre.<br/><br/>   3) mielőtt elvégezte a TEHENEt, a VSS tájékoztatja a gépen lévő összes alkalmazást, hogy a memóriájában tárolt adatok lemezre ürítése szükséges.<br/><br/>   4.) a VSS ezután lehetővé teszi a biztonsági mentési/vész-helyreállítási alkalmazás (ebben az esetben Site Recovery) számára a pillanatkép-adatok olvasását és a folytatást. | Az alkalmazással konzisztens Pillanatképek a megadott gyakoriságnak megfelelően készülnek. A gyakoriságnak mindig kisebbnek kell lennie, mint a helyreállítási pontok megőrzéséhez. Ha például megőrzi a helyreállítási pontokat a 24 órás alapértelmezett beállítással, a gyakoriságot 24 óránál rövidebb ideig kell beállítania.<br/><br/>Összetettebbek, és hosszabb időt is igénybe vehetik, mint az összeomlás-konzisztens Pillanatképek.<br/><br/> Hatással vannak a replikálásra engedélyezett virtuális gépeken futó alkalmazások teljesítményére. 

## <a name="replication-process"></a>Replikációs folyamat

Az Azure-beli virtuális gépek replikálásának engedélyezésekor a következők történnek:

1. A Site Recovery mobilitási szolgáltatás bővítménye automatikusan települ a virtuális gépre.
2. A bővítmény regisztrálja Site Recovery a virtuális gépet.
3. A virtuális gép folyamatos replikációja megkezdődik.  A lemez írásait a rendszer azonnal továbbítja a gyorsítótárbeli Storage-fiókba a forrás helyén.
4. Site Recovery feldolgozza a gyorsítótárban lévő adatokat, és elküldi azt a cél Storage-fiókba vagy a replikált felügyelt lemezekre.
5. Az adatfeldolgozást követően az összeomlás-konzisztens helyreállítási pontok öt percenként jönnek létre. Az alkalmazással konzisztens helyreállítási pontok a replikációs házirendben megadott beállításnak megfelelően jönnek létre.

![A replikációs folyamatot bemutató diagram, 2. lépés.](./media/concepts-azure-to-azure-architecture/enable-replication-step-2-v2.png)

**Replikációs folyamat**

## <a name="connectivity-requirements"></a>Kapcsolódási követelmények

 A replikált Azure-beli virtuális gépeknek kimenő kapcsolatra van szükségük. Site Recovery soha nem kell bejövő kapcsolatot létesítenie a virtuális géppel. 

### <a name="outbound-connectivity-urls"></a>Kimenő kapcsolat (URL-címek)

Ha a virtuális gépek kimenő hozzáférése URL-címekkel van vezérelve, engedélyezze ezeket az URL-címeket.

| **Név**                  | **Kereskedelmi**                               | **Államigazgatás**                                 | **Leírás** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Tárolás                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Lehetővé teszi az adatok írását a virtuális gépről a forrásrégió gyorsítótárjának tárfiókjába. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Hitelesítést és engedélyezést biztosít a Site Recovery szolgáltatás URL-címeihez. |
| Replikálás               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`     | Lehetővé teszi a virtuális gép és a Site Recovery szolgáltatás közötti kommunikációt. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Lehetővé teszi a virtuális gép számára a Site Recovery monitorozási és diagnosztikai adatainak írását. |
| Key Vault                 | `*.vault.azure.net`                        | `*.vault.usgovcloudapi.net`                  | Lehetővé teszi a hozzáférést az ADE-kompatibilis virtuális gépek replikálásának engedélyezéséhez a portálon keresztül |
| Azure Automation          | `*.automation.ext.azure.com`               | `*.azure-automation.us`                      | Lehetővé teszi a mobilitási ügynöknek a portálon keresztüli replikált elemek automatikus frissítésének engedélyezését |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Kimenő kapcsolat az IP-címtartományokhoz

Az IP-címeket használó virtuális gépek kimenő kapcsolatának vezérléséhez engedélyezze ezeket a címeket.
Vegye figyelembe, hogy a hálózati kapcsolatra vonatkozó követelmények részletei a [hálózatkezelési dokumentumban](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) találhatók. 

#### <a name="source-region-rules"></a>A forrásoldali régió szabályai

**Szabály** |  **Részletek** | **Szolgáltatáscímke**
--- | --- | --- 
HTTPS-kimenő engedélyezése: 443-es port | A forrás régióban lévő Storage-fiókoknak megfelelő tartományok engedélyezése | Storage.\<region-name>
HTTPS-kimenő engedélyezése: 443-es port | Azure Active Directorynak (Azure AD) megfelelő tartományok engedélyezése  | AzureActiveDirectory
HTTPS-kimenő engedélyezése: 443-es port | A célként megadott régióban található Events hubhoz tartozó tartományok engedélyezése. | EventsHub.\<region-name>
HTTPS-kimenő engedélyezése: 443-es port | Azure Site Recoverynak megfelelő tartományok engedélyezése  | AzureSiteRecovery
HTTPS-kimenő engedélyezése: 443-es port | Azure Key Vaultnak megfelelő tartományok engedélyezése (ez csak az ADE-kompatibilis virtuális gépek portálon keresztüli replikálásának engedélyezéséhez szükséges) | AzureKeyVault
HTTPS-kimenő engedélyezése: 443-es port | Azure Automation vezérlőnek megfelelő tartományok engedélyezése (ez csak a mobilitási ügynöknek a portálon keresztüli replikált elemek esetében történő automatikus frissítésének engedélyezéséhez szükséges) | GuestAndHybridManagement

#### <a name="target-region-rules"></a>A célként megadott régió szabályai

**Szabály** |  **Részletek** | **Szolgáltatáscímke**
--- | --- | --- 
HTTPS-kimenő engedélyezése: 443-es port | A cél régióban lévő Storage-fiókoknak megfelelő tartományok engedélyezése | Storage.\<region-name>
HTTPS-kimenő engedélyezése: 443-es port | Az Azure AD-nek megfelelő tartományok engedélyezése  | AzureActiveDirectory
HTTPS-kimenő engedélyezése: 443-es port | Az Events hub-nak megfelelő tartományok engedélyezése a forrás régióban. | EventsHub.\<region-name>
HTTPS-kimenő engedélyezése: 443-es port | Azure Site Recoverynak megfelelő tartományok engedélyezése  | AzureSiteRecovery
HTTPS-kimenő engedélyezése: 443-es port | Azure Key Vaultnak megfelelő tartományok engedélyezése (ez csak az ADE-kompatibilis virtuális gépek portálon keresztüli replikálásának engedélyezéséhez szükséges) | AzureKeyVault
HTTPS-kimenő engedélyezése: 443-es port | Azure Automation vezérlőnek megfelelő tartományok engedélyezése (ez csak a mobilitási ügynöknek a portálon keresztüli replikált elemek esetében történő automatikus frissítésének engedélyezéséhez szükséges) | GuestAndHybridManagement


#### <a name="control-access-with-nsg-rules"></a>Hozzáférés szabályozása NSG-szabályokkal

Ha a virtuális gép kapcsolatát úgy szabályozza, hogy az Azure-hálózatokra/alhálózatokra irányuló hálózati forgalmat [NSG-szabályok](../virtual-network/network-security-groups-overview.md)használatával szűri, vegye figyelembe a következő követelményeket:

- A forrásként szolgáló Azure-régió NSG-szabályainak engedélyezniük kell a kimenő hozzáférést a replikálási forgalom számára.
- Javasoljuk, hogy hozzon létre szabályokat tesztkörnyezetben, mielőtt éles környezetbe helyezné őket.
- Az egyes IP-címek engedélyezése helyett használja a [szolgáltatás címkéit](../virtual-network/network-security-groups-overview.md#service-tags) .
    - A szolgáltatás címkéi az IP-címek egy csoportját alkotják, amelyek a biztonsági szabályok létrehozásakor a bonyolultságuk csökkentése érdekében vannak összegyűjtve.
    - A Microsoft az idő múlásával automatikusan frissíti a szolgáltatás címkéit. 
 
További információ a Site Recovery [kimenő](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) kapcsolatairól és a [NSG-vel való kapcsolat szabályozásáról](concepts-network-security-group-with-site-recovery.md).


### <a name="connectivity-for-multi-vm-consistency"></a>Kapcsolat a több virtuális gépre kiterjedő konzisztencia érdekében

Ha engedélyezte a több virtuális gépre kiterjedő konzisztenciát, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással.
- Ellenőrizze, hogy nem blokkolja-e tűzfal a virtuális gépek között a 20004-es porton keresztül zajló belső kommunikációt.
- Ha Linux rendszerű virtuális gépeket szeretne egy replikációs csoport részévé tenni, győződjön meg arról, hogy a 20004-es port esetében a kimenő adatforgalom manuális megnyitása megtörtént-e az adott Linux-verzió útmutatásainak megfelelően.




## <a name="failover-process"></a>Feladatátvételi folyamat

Feladatátvétel kezdeményezése esetén a virtuális gépek a célként megadott erőforráscsoporthoz, a célként megadott alhálózatra, valamint a cél rendelkezésre állási csoportba kerülnek. Feladatátvétel során bármelyik helyreállítási pontot használhatja.

![A feladatátvételi folyamatot bemutató diagram a forrás-és a célként megadott környezetekben.](./media/concepts-azure-to-azure-architecture/failover-v2.png)

## <a name="next-steps"></a>Következő lépések

[Gyorsan replikálhat](azure-to-azure-quickstart.md) egy Azure-beli virtuális gépet egy másodlagos régióba.
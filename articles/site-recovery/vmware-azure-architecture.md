---
title: VMware virtuális gép vész-helyreállítási architektúrája Azure Site Recovery
description: Ez a cikk áttekintést nyújt azokról az összetevőkről és architektúráról, amelyeket a helyszíni VMware virtuális gépek vész-helyreállításának beállításakor használ az Azure-ba Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 5cf4dc5123040fd2af8efe54153867a8709fe1ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652228"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>VMware – Azure vész-helyreállítási architektúra

Ez a cikk azokat az architektúrákat és folyamatokat ismerteti, amelyeket a rendszer a helyszíni VMware-hely és az Azure között a [Azure site Recovery](site-recovery-overview.md) szolgáltatás használatával végez a helyreállítási replikáció, a feladatátvétel és a VMWare virtuális gépek (VM-EK) helyreállítási folyamatainak telepítésekor.


## <a name="architectural-components"></a>Az architektúra összetevői

Az alábbi táblázat és ábra áttekintést nyújt az Azure-ba irányuló VMware vész-helyreállításhoz használt összetevőkről.

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Azure-előfizetés, Azure Storage-fiók a gyorsítótárhoz, a felügyelt lemezekhez és az Azure-hálózathoz. | A helyszíni virtuális gépekről replikált adatok tárolása az Azure Storage-ban történik. Az Azure-beli virtuális gépek a replikált adatokkal jönnek létre, amikor feladatátvételt hajt végre a helyszínen az Azure-ba. Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**Konfigurációs kiszolgáló számítógépe** | Egyetlen helyszíni gép. Azt javasoljuk, hogy egy letöltött OVF-sablonból üzembe helyezhető VMware virtuális gépként futtassa.<br/><br/> A gép az összes helyszíni Site Recovery-összetevőt futtatja, amely tartalmazza a konfigurációs kiszolgálót, a Process Servert és a fő célkiszolgáló kiszolgálót. | **Konfigurációs kiszolgáló**: koordinálja a helyszíni és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.<br/><br/> **Process Server**: alapértelmezés szerint telepítve van a konfigurációs kiszolgálón. Replikációs adatkérést kap; a gyorsítótárazással, tömörítéssel és titkosítással optimalizálja azt. és elküldi az Azure Storage-nak. A folyamatkiszolgáló ezenfelül telepíti az Azure Site Recovery mobilitási szolgáltatást a replikálni kívánt virtuális gépekre, és elvégzi a helyszíni gépek automatikus felderítését. Az üzembe helyezés során további, különálló folyamat-kiszolgálókat adhat hozzá a replikációs forgalom nagyobb mennyiségének kezeléséhez.<br/><br/> **Fő célkiszolgáló**: alapértelmezés szerint telepítve van a konfigurációs kiszolgálón. Az Azure-beli feladat-visszavétel során kezeli a replikációs adatok kezelését. Nagyméretű központi telepítések esetén további, különálló fő célkiszolgáló adható hozzá a feladat-visszavételhez.
**VMware-kiszolgálók** | A VMware virtuális gépek helyszíni vSphere ESXi-kiszolgálókon futnak. A gazdagépek felügyeletéhez vCenter-kiszolgálót ajánlunk. | Site Recovery telepítése során VMware-kiszolgálókat ad hozzá a Recovery Services-tárolóhoz.
**Replikált gépek** | A mobilitási szolgáltatás minden replikált VMware virtuális gépen telepítve van. | Javasoljuk, hogy engedélyezze az automatikus telepítést a Process Serverről. Azt is megteheti, hogy manuálisan telepíti a szolgáltatást, vagy automatikus telepítési módszert használ, például Configuration Manager.

![A VMware – Azure replikálási architektúra kapcsolatait ábrázoló diagram.](./media/vmware-azure-architecture/arch-enhanced.png)

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

A helyszíni Azure Site Recovery infrastruktúra és az Azure-szolgáltatások közötti kommunikációra szűrni kívánt URL-címek teljes listájáért tekintse meg [a hálózati követelmények szakaszt az előfeltételek című cikkben](vmware-azure-deploy-configuration-server.md#prerequisites).

## <a name="replication-process"></a>Replikációs folyamat

1. Amikor engedélyezi a replikációt egy virtuális géphez, a kezdeti replikáció az Azure Storage-ba megkezdődik a megadott replikációs házirend használatával. Vegye figyelembe a következőket:
    - A VMware virtuális gépek esetében a replikáció a virtuális gépen futó mobilitási szolgáltatás ügynökének szinte folyamatos, közel folyamatos, a replikáláshoz.
    - A rendszer alkalmazza a replikációs házirend beállításait:
        - **RPO küszöbértéke**. Ez a beállítás nincs hatással a replikálásra. Segít a figyelésben. Egy esemény jön létre, és opcionálisan egy e-mailt küld, ha az aktuális RPO meghaladja a megadott küszöbértéket.
        - **Helyreállítási pont megőrzése**. Ezzel a beállítással megadható, hogy mennyi idő elteltével kell megszakítani a megszakítást. A Premium Storage maximális megőrzése 24 óra. Standard szintű tárolás esetén 72 óra. 
        - **Alkalmazás-konzisztens Pillanatképek**. Az alkalmazással konzisztens pillanatkép az alkalmazás igényeitől függően 1 – 12 óra lehet. A pillanatképek standard Azure Blob-Pillanatképek. A virtuális gépen futó mobilitási ügynök egy VSS-pillanatképet kér ennek a beállításnak megfelelően, és a könyvjelzőket, amelyek a replikálási adatfolyamban lévő alkalmazás-konzisztens pontként jelennek meg.

2. A forgalom az Azure Storage nyilvános végpontjait az interneten keresztül replikálja. Másik lehetőségként használhatja az Azure ExpressRoute-t a [Microsoft-partnerekkel](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). A helyek közötti virtuális magánhálózati (VPN) kapcsolaton keresztüli replikálása nem támogatott a helyszíni helyről az Azure-ba.
3. A kezdeti replikálási művelet biztosítja, hogy a rendszer az Azure-ba történő replikáció engedélyezésekor a gépen lévő teljes adatmennyiséget megkapja. A kezdeti replikálás befejeződése után a változásokat az Azure-ba replikálva megkezdődik. A rendszer a gépek nyomon követését a folyamat kiszolgálójára továbbítja.
4. A kommunikáció a következőképpen történik:

    - A virtuális gépek a replikációs felügyelet érdekében a HTTPS 443 bejövő porton kommunikálnak a helyszíni konfigurációs kiszolgálóval.
    - A konfigurációs kiszolgáló az Azure-ba irányuló replikációt a HTTPS 443 kimenő porton keresztül hangolja össze.
    - A virtuális gépek replikációs adatküldést küldenek a folyamat-kiszolgálónak (amely a konfigurációs kiszolgáló gépen fut) a HTTPS 9443 bejövő porton. Ez a port módosítható.
    - A Process Server replikációs adatokat fogad, optimalizálja és titkosítja, majd az Azure Storage-ba küldi az 443-as porton keresztül.
5. A replikációs adatnaplók először egy gyorsítótárbeli Storage-fiókba helyezik az Azure-ban. A rendszer feldolgozza ezeket a naplókat, és az adattárolást egy Azure-beli felügyelt lemez (ún. ASR-lemez) tárolja. A helyreállítási pontok ezen a lemezen jönnek létre.

![A VMware – Azure replikálási folyamatot bemutató ábra.](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="resynchronization-process"></a>Újraszinkronizálási folyamat

1. Időnként előfordulhat, hogy a kezdeti replikálás során vagy a különbözeti változások átvitele közben hálózati kapcsolati problémák merülhetnek fel a forrásszámítógép között a kiszolgáló vagy a Process Server között az Azure-ba történő feldolgozáshoz. Ezek egyike az Azure-ba irányuló adatátviteli hibákhoz vezethet.
2. Az adatintegritási problémák elkerülése és az adatátviteli költségek csökkentése érdekében Site Recovery megjelöli a gépet újraszinkronizálásra.
3. A gép az újraszinkronizálásra is kijelölhető olyan helyzetekben, mint az alábbi, hogy fenntartsa a forrásoldali gép és az Azure-ban tárolt adatmennyiség egységességét
    - Ha egy gép kényszerített leállítása
    - Ha egy gép olyan konfigurációs módosításokat hajt végre, mint például a lemezek átméretezése (a lemez méretét a 2 TB-ról 4 TB-ra módosítja)
4. Az Újraszinkronizálás csak a különbözeti adatokat küldi el az Azure-nak. A helyszíni és az Azure közötti adatátvitel a forrás-és az Azure-ban tárolt adatok között a számítástechnikai ellenőrzőösszegek révén csökkenthető.
5. Alapértelmezés szerint az újraszinkronizálás úgy van ütemezve, hogy az munkaidőn kívül automatikusan fusson. Ha nem szeretné megvárni az alapértelmezett újraszinkronizálást a munkaidőn kívül, manuálisan is szinkronizálhatja a virtuális gépet. Ehhez nyissa meg a Azure Portalt, válassza ki a virtuális gépet > **újraszinkronizálást**.
6. Ha az alapértelmezett újraszinkronizálási művelet munkaidőn kívül esik, és manuális beavatkozásra van szükség, akkor a rendszer hibát generál az adott gépen Azure Portal. Feloldhatja a hibát, és manuálisan is aktiválhatja az újraszinkronizálást.
7. Az Újraszinkronizálás befejezése után a különbözeti módosítások replikálása folytatódik.

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

## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

Miután beállította a replikálást, és elvégezte a vész-helyreállítási részletezést (feladatátvételi teszt) annak ellenőrzéséhez, hogy minden a vártnak megfelelően működik-e, futtathatja a feladatátvételt és a feladat-visszavételt.

1. A művelet végrehajtása egyetlen gépen meghiúsul, vagy létrehozhat egy helyreállítási tervet, amely egyszerre több virtuális gép feladatátvételét hajtja végre. Egy helyreállítási terv előnye, hogy az egyetlen gép feladatátvétele helyett a következőket használja:
    - Az alkalmazás-függőségek modellezéséhez az összes virtuális gépet egyetlen helyreállítási tervbe kell belefoglalni az alkalmazáson belül.
    - A manuális műveletekhez parancsfájlokat, Azure-runbookok és szüneteltetést is hozzáadhat.
2. A kezdeti feladatátvétel elindítása után véglegesíti azt az Azure-beli virtuális gép munkaterhelésének megkezdéséhez.
3. Ha az elsődleges helyszíni hely ismét elérhetővé válik, előkészítheti a feladat-visszavétel feladatait. A feladat-visszavétel érdekében be kell állítania egy feladat-visszavételi infrastruktúrát, beleértve a következőket:

    * **Ideiglenes Process Server az Azure-ban**: az Azure-ból való feladat-visszavételhez állítson be egy Azure-beli virtuális gépet, amely az Azure-ból történő replikáció kezelésére szolgáló folyamat-kiszolgálóként működik. Ez a virtuális gép a feladatok visszaadását követően törölhető.
    * **VPN-kapcsolat**: a feladat-visszavétel érdekében VPN-kapcsolatra (vagy ExpressRoute) van szükség az Azure-hálózatról a helyszíni helyre.
    * **Különálló fő célkiszolgáló**: alapértelmezés szerint a helyszíni VMWare virtuális gépen a konfigurációs kiszolgálóval telepített fő célkiszolgáló kezeli a feladat-visszavételt. Ha nagy mennyiségű forgalmat kell visszaadnia, hozzon létre egy külön helyszíni fő célkiszolgáló erre a célra.
    * **Feladat-visszavételi szabályzat**: A helyszíni helyre történő újbóli replikáláshoz feladat-visszavételi szabályzatra van szükség. A rendszer automatikusan létrehozza ezt a házirendet, amikor létrehoz egy replikációs házirendet a helyszínről az Azure-ba.
4. Az összetevők érvénybe lépése után a feladat-visszavétel három műveletben fordul elő:

    - 1. lépés: az Azure-beli virtuális gépek ismételt védetté tételével, hogy az Azure-ból vissza lehessen térni a helyszíni VMware virtuális gépekre.
    -  2. fázis: feladatátvétel futtatása a helyszíni helyre.
    - 3. fázis: a munkaterhelések visszaállítása után engedélyezze újra a helyszíni virtuális gépek replikálását.
    
 

![Az Azure-ból VMware-feladat-visszavételt ábrázoló ábra](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Következő lépések

[Ezt az oktatóanyagot](vmware-azure-tutorial.md) követve engedélyezheti a VMware – Azure replikálást.

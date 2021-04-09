---
title: Alkalmazási minták SQL Server virtuális gépeken | Microsoft Docs
description: Ez a cikk az Azure Virtual Machines SQL Server alkalmazási mintáit ismerteti. A megoldás a megfelelő alkalmazások architektúrájának és kialakításának alapja a megoldás-építészek és fejlesztők számára.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mathoma
ms.openlocfilehash: e877f775c6a7bc83ce6c4a559c9f74b606c952fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359489"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-on-azure-virtual-machines"></a>Alkalmazási minták és fejlesztési stratégiák az Azure-beli SQL Serverhoz Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Összegzés:
Annak meghatározása, hogy az Azure-környezetekben az SQL Server-alapú alkalmazásokhoz milyen alkalmazási minta vagy mintázatok használhatók, ezért fontos a tervezési döntés, amely alapos ismereteket igényel, hogy az Azure hogyan működik együtt a SQL Server és az egyes infrastruktúra-összetevőkkel. Az Azure infrastruktúra-szolgáltatások SQL Server segítségével könnyedén áttelepítheti, karbantarthatja és figyelheti a Windows Server rendszerre épülő meglévő SQL Server-alkalmazásait az Azure-ban futó virtuális gépekre.

Ebből a cikkből megtudhatja, hogy a megoldás-építészek és a fejlesztők megalapozzák a jó alkalmazások architektúráját és kialakítását, amelyeket a meglévő alkalmazások Azure-ba való áttelepítésekor, valamint az Azure-beli új alkalmazások fejlesztésekor követhetnek.

Minden egyes alkalmazási mintához egy helyszíni forgatókönyv, a hozzá tartozó felhőalapú megoldás és a kapcsolódó technikai javaslatok találhatók. Emellett a cikk az Azure-specifikus fejlesztési stratégiákat is ismerteti, hogy megfelelően tervezze meg alkalmazásait. A számos lehetséges alkalmazási minta miatt ajánlott, hogy az építészeknek és a fejlesztőknek az alkalmazásokhoz és a felhasználókhoz legmegfelelőbb mintát válasszák.

**Technikai Közreműködők:** Luis Carlos Vargas hering, a Sárvári, a

**Technikai felülvizsgálók:** Corey Sanders, Drew McDaniel, Narayan NIR, Mashkowski, Sanjay és Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Bevezetés
Számos különböző típusú n szintű alkalmazást fejleszthet, ha elválasztja a különböző számítógépeken lévő különböző alkalmazás-rétegek összetevőit, valamint külön összetevőket is. Tegyük fel például, hogy az ügyfélalkalmazás és az üzleti szabályok összetevői egy másik gépen, az előtér-webrétegben és az adatelérési rétegben található összetevők egy másik gépen, valamint egy másik gép háttérbeli adatbázis-szintje. Ez a fajta strukturálás segít elkülöníteni egymástól az egyes szinteket. Ha megváltoztatja, hogy honnan származik az adatok, nem kell módosítania az ügyfelet vagy a webalkalmazást, csak az adatelérési rétegek összetevőit.

Egy tipikus *n* szintű alkalmazás tartalmazza a megjelenítési szintet, az üzleti szintet és az adatréteget:

| Szint | Leírás |
| --- | --- |
| **Megjelenítés** |A *megjelenítési szint* (webes réteg, előtér-réteg) az a réteg, amelyben a felhasználók egy alkalmazással kommunikálnak. |
| **Üzleti** |Az *üzleti szint* (középső réteg) az a réteg, amelyet a megjelenítési réteg és az adatréteg használ egymással való kommunikációhoz, és a rendszer alapvető funkcióit tartalmazza. |
| **Adatok** |Az *adatcsomag* alapvetően az alkalmazáshoz tartozó (például egy SQL Server-t futtató kiszolgáló) tároló-kiszolgáló. |

Az alkalmazási rétegek leírják az alkalmazás funkcióinak és összetevőinek logikai csoportosításait; míg a rétegek a különböző fizikai kiszolgálókon, számítógépeken, hálózatokon vagy távoli telephelyeken lévő funkciók és összetevők fizikai eloszlását írják le. Az alkalmazások rétegei ugyanazon a fizikai számítógépen (ugyanazon a szinten) lehetnek, vagy különálló számítógépeken (n szintű) vannak elosztva, és az egyes rétegekben található összetevők a jól meghatározott felületek használatával kommunikálnak más rétegekben található összetevőkkel. Gondoljon arra, hogy a szint olyan fizikai eloszlási mintákra hivatkozik, mint például a kétrétegű, a háromrétegű és az n szintű. A **kétrétegű alkalmazási minta** két alkalmazási szintet tartalmaz: alkalmazáskiszolgáló és adatbázis-kiszolgáló. A közvetlen kommunikáció az alkalmazáskiszolgáló és az adatbázis-kiszolgáló között történik. Az alkalmazáskiszolgáló webes és üzleti rétegbeli összetevőket is tartalmaz. A háromrétegű **alkalmazási mintában** három alkalmazási réteg található: webkiszolgáló, alkalmazáskiszolgáló, amely az üzleti logikát és/vagy az üzleti réteg adatelérési összetevőit és az adatbázis-kiszolgálót tartalmazza. A webkiszolgáló és az adatbázis-kiszolgáló közötti kommunikáció az alkalmazáskiszolgáló felett történik. Az alkalmazási rétegekkel és rétegekkel kapcsolatos részletes információkért lásd: [Microsoft Application Architecture útmutató](/previous-versions/msp-n-p/ff650706(v=pandp.10)).

Mielőtt elkezdené a cikk elolvasását, ismernie kell a SQL Server és az Azure alapvető fogalmait. További információ: [SQL Server online könyvek](/sql/sql-server/), [SQL Server az Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md) és a [Azure.com](https://azure.microsoft.com/).

Ez a cikk számos olyan alkalmazási mintát ismertet, amelyek alkalmasak lehetnek az egyszerű alkalmazásokhoz, valamint a nagymértékben összetett vállalati alkalmazásokhoz. Az egyes minták részletezése előtt javasoljuk, hogy ismerkedjen meg az Azure-ban elérhető adattárolási szolgáltatásokkal, például az [Azure Storage](../../../storage/common/storage-introduction.md)-ban, a [Azure SQL Database](../../database/sql-database-paas-overview.md)és az [Azure-beli virtuális gépeken SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md). Az alkalmazásokra vonatkozó legjobb tervezési döntések meghozatalához meg kell ismernie, hogy mikor érdemes egyértelműen használni az adattárolási szolgáltatást.

### <a name="choose-sql-server-on-azure-virtual-machines-when"></a>Az Azure Virtual Machines SQL Server válassza a következőt:

* SQL Server-és Windows-vezérlésre van szükség. Ilyen lehet például a SQL Server verziója, a speciális gyorsjavítások, a teljesítmény konfigurációja stb.
* A SQL Server teljes kompatibilitása szükséges, és a meglévő alkalmazásokat át szeretné helyezni az Azure-ba.
* Szeretné kihasználni az Azure-környezet képességeit, de a Azure SQL Database nem támogatja az alkalmazás által igényelt összes funkciót. Ez a következő területeket tartalmazhatja:
  
  * **Adatbázis mérete**: a cikk frissítésének időpontjában SQL Database legfeljebb 1 TB adatból álló adatbázist támogat. Ha az alkalmazás több mint 1 TB-nyi adatra van szüksége, és nem szeretne egyéni horizontális megoldásokat megvalósítani, azt javasoljuk, hogy az Azure-beli virtuális gépeken SQL Server használjon. A legfrissebb információkért lásd: horizontális [felskálázás Azure SQL Database](/previous-versions/azure/dn495641(v=azure.100)), [DTU-alapú vásárlási modell](../../database/service-tiers-dtu.md)és [virtuális mag-alapú vásárlási modell](../../database/service-tiers-vcore.md)(előzetes verzió).
  * **HIPAA-megfelelőség**: az egészségügyi ügyfelek és a független szoftvergyártók (ISV-EK) az Azure-beli [Virtual Machines SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md) is választhatnak [Azure SQL Database](../../database/sql-database-paas-overview.md) helyett, mert az Azure Virtual Machines SQL Servert a HIPAA Business Associate Agreement (Baa) szabályozza. A megfelelőséggel kapcsolatos információkért lásd [: Microsoft Azure Adatvédelmi központ: megfelelőség](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Példány-szintű funkciók**: a SQL Database jelenleg nem támogatja az adatbázison kívül élő szolgáltatásokat (például a csatolt kiszolgálókat, az ügynök feladatait, a FileStream, a Service Broker stb.). További információ: [Azure SQL Database irányelvek és korlátozások](/previous-versions/azure/ff394102(v=azure.100)).

## <a name="1-tier-simple-single-virtual-machine"></a>1 szintű (egyszerű): egyetlen virtuális gép
Ebben az alkalmazási mintában a SQL Server alkalmazást és az adatbázist egy önálló virtuális gépre helyezi üzembe az Azure-ban. Ugyanez a virtuális gép tartalmazza az ügyfél/webalkalmazás, az üzleti összetevők, az adatelérési réteg és az adatbázis-kiszolgáló szolgáltatásait. A bemutató, az üzleti és az adatelérési kód logikailag el van különítve, de fizikailag egy egykiszolgálós gépen található. A legtöbb ügyfél ezzel az alkalmazási mintával kezdi meg a felskálázást, és további webes szerepköröket vagy virtuális gépeket ad hozzá a rendszerhez.

Ez az alkalmazási minta a következő esetekben hasznos:

* Egyszerű áttelepítést szeretne végezni az Azure platformra annak kiértékeléséhez, hogy a platform válaszol-e az alkalmazás követelményeire, vagy sem.
* Az azonos Azure-adatközpontban üzemeltetett összes alkalmazási szintet szeretné megőrizni a rétegek közötti késés csökkentése érdekében.
* Rövid ideig szeretné gyorsan kiépíteni a fejlesztési és tesztelési környezeteket.
* A különböző számítási feladatok szintjén szeretné elvégezni a terheléses tesztelést, de ugyanakkor nem szeretné, hogy az egész idő alatt sok fizikai gépet lehessen fenntartani és karbantartani.

Az alábbi ábra egy egyszerű helyszíni forgatókönyvet mutat be, és hogyan helyezheti üzembe a felhőben engedélyezett megoldást egyetlen virtuális gépen az Azure-ban.

![1 szintű alkalmazás mintája](./media/application-patterns-development-strategies/IC728008.png)

Ha az üzleti réteget (az üzleti logikát és az adatelérési összetevőket) ugyanazon a fizikai szinten helyezi üzembe, mint a megjelenítési réteg, maximalizálhatja az alkalmazások teljesítményét, kivéve, ha a méretezhetőség vagy a biztonsági probléma miatt külön szintet kell használnia.

Mivel ez nagyon gyakori minta a kezdéshez, előfordulhat, hogy az áttelepítéssel kapcsolatos következő cikk hasznos lehet az adatainak a SQL Server VMba való áthelyezéséhez: [adatbázis áttelepítése egy Azure-beli virtuális gépen lévő SQL Serverre](migrate-to-vm-from-sql-server.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>3 szint (egyszerű): több virtuális gép
Ebben az alkalmazási mintában egy 3 rétegbeli alkalmazást helyez üzembe az Azure-ban azáltal, hogy az egyes alkalmazások rétegeit egy másik virtuális gépen helyezi el. Ez rugalmas környezetet biztosít a könnyen méretezhető és Felskálázási forgatókönyvekhez. Ha az egyik virtuális gép tartalmazza az ügyfél-vagy webalkalmazást, a másik az üzleti összetevőket, a másik pedig az adatbázis-kiszolgálót tárolja.

Ez az alkalmazási minta a következő esetekben hasznos:

* Összetett adatbázis-alkalmazások áttelepítését szeretné elvégezni az Azure Virtual Machinesban.
* Azt szeretné, hogy a különböző alkalmazási rétegek különböző régiókban legyenek tárolva. Előfordulhat például, hogy olyan megosztott adatbázisokkal rendelkezik, amelyeket jelentéskészítési célokra több régióban telepítenek.
* Vállalati alkalmazásokat szeretne áthelyezni helyszíni virtualizált platformokról az Azure Virtual Machinesba. A vállalati alkalmazások részletes ismertetését lásd: [Mi az a vállalati alkalmazás](/previous-versions/visualstudio/aa267045(v=vs.60)).
* Rövid ideig szeretné gyorsan kiépíteni a fejlesztési és tesztelési környezeteket.
* A különböző számítási feladatok szintjén szeretné elvégezni a terheléses tesztelést, de ugyanakkor nem szeretné, hogy az egész idő alatt sok fizikai gépet lehessen fenntartani és karbantartani.

Az alábbi ábra azt mutatja be, hogyan helyezhet üzembe egy egyszerű, 3 rétegbeli alkalmazást az Azure-ban azáltal, hogy az egyes alkalmazások rétegeit egy másik virtuális gépen helyezi el.

![háromrétegű alkalmazás mintája](./media/application-patterns-development-strategies/IC728009.png)

Ebben az alkalmazási mintában az egyes rétegekben csak egy virtuális gép található. Ha több virtuális géppel rendelkezik az Azure-ban, javasoljuk, hogy állítson be egy virtuális hálózatot. Az [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) megbízható biztonsági határt hoz létre, és lehetővé teszi, hogy a virtuális gépek egymás között kommunikáljanak a magánhálózati IP-címeken keresztül. Emellett mindig győződjön meg arról, hogy az összes internetkapcsolat csak a bemutató szintjére mutat. Az alkalmazás mintájának követése után a hálózati biztonsági csoport szabályait a hozzáférés vezérléséhez kell kezelnie. További információ: [külső hozzáférés engedélyezése a virtuális géphez a Azure Portal használatával](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

A diagramon az internetes protokollok TCP, UDP, HTTP vagy HTTPS lehet.

> [!NOTE]
> A virtuális hálózatok beállítása az Azure-ban díjmentes. Azonban a helyszíni rendszerhez csatlakozó VPN-átjáróért kell fizetnie. Ez a díj a kapcsolatok kiépített és elérhető időtartamán alapul.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>kétrétegű és 3 szintű, prezentációs réteg kibővíthető
Ebben az alkalmazási mintában kétrétegű vagy 3 rétegbeli adatbázis-alkalmazást telepít az Azure Virtual Machinesba azáltal, hogy az egyes alkalmazások rétegeit egy másik virtuális gépen helyezi el. Emellett a beérkező ügyfelek kéréseinek nagyobb mennyisége miatt kibővítheti a megjelenítési szintet.

Ez az alkalmazási minta a következő esetekben hasznos:

* Vállalati alkalmazásokat szeretne áthelyezni helyszíni virtualizált platformokról az Azure Virtual Machinesba.
* A beérkező ügyfelek kéréseinek nagyobb mennyisége miatt ki szeretné bővíteni a megjelenítési szintet.
* Rövid ideig szeretné gyorsan kiépíteni a fejlesztési és tesztelési környezeteket.
* A különböző számítási feladatok szintjén szeretné elvégezni a terheléses tesztelést, de ugyanakkor nem szeretné, hogy az egész idő alatt sok fizikai gépet lehessen fenntartani és karbantartani.
* Olyan infrastrukturális környezetet szeretne, amely igény szerint fel-és leskálázást tud végezni.

Az alábbi ábra bemutatja, hogyan helyezheti el az alkalmazási rétegeket több virtuális gépen az Azure-ban a beérkező ügyfelek kéréseinek növelése miatt. Ahogy az ábrán is látható, Azure Load Balancer feladata a forgalom elosztása több virtuális gép között, és azt is, hogy melyik webkiszolgálót kívánja csatlakozni a szolgáltatáshoz. Ha a Load Balancer mögött található webkiszolgálók több példánya is biztosítja a megjelenítési szint magas rendelkezésre állását.

![Alkalmazás mintája – megjelenítési réteg kibővíthető](./media/application-patterns-development-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Ajánlott eljárások kétszintű, 3 vagy n szintű mintákhoz, amelyeken több virtuális gép található egy rétegben
Ajánlott olyan virtuális gépeket elhelyezni, amelyek ugyanahhoz a platformhoz tartoznak ugyanazon a felhőalapú szolgáltatásban és a rendelkezésre állási csoportban. Tegyük fel például, hogy a **CloudService1** és a **AvailabilitySet1** webkiszolgálókat, valamint a **CloudService2** és a **AvailabilitySet2** adatbázis-kiszolgálóinak készletét adja meg. Az Azure-beli rendelkezésre állási csoport lehetővé teszi a magas rendelkezésre állású csomópontok különálló tartalék tartományokra és frissítési tartományokra való elhelyezését.

Egy réteg több virtuálisgép-példányának kihasználása érdekében Azure Load Balancer kell konfigurálnia az alkalmazási rétegek között. Az egyes rétegekben a Load Balancer konfigurálásához hozzon létre egy elosztott terhelésű végpontot az egyes rétegekben lévő virtuális gépeken. Egy adott szinten először hozzon létre virtuális gépeket ugyanabban a felhőalapú szolgáltatásban. Ez biztosítja, hogy azonos nyilvános virtuális IP-címmel rendelkezzenek. Ezután hozzon létre egy végpontot az adott szinten található egyik virtuális gépen. Ezután rendeljen hozzá ugyanezt a végpontot az adott szinten lévő többi virtuális géphez a terheléselosztáshoz. Elosztott terhelésű készlet létrehozásával több virtuális gép között osztja el a forgalmat, és lehetővé teszi a Load Balancer számára, hogy eldöntse, melyik csomóponthoz kapcsolódjon, amikor a háttérbeli virtuális gép csomópontja meghibásodik. Például ha a terheléselosztó mögötti webkiszolgálók több példánya is biztosítja a megjelenítési szint magas rendelkezésre állását.

Ajánlott eljárásként mindig győződjön meg arról, hogy az összes internetkapcsolat először a bemutató szintjére mutat. A megjelenítési réteg hozzáfér az üzleti szinthez, majd az üzleti szint hozzáfér az adatréteghez. További információ a bemutató réteg elérésének engedélyezéséről: [külső hozzáférés engedélyezése a virtuális géphez a Azure Portal használatával](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Vegye figyelembe, hogy az Azure-beli Load Balancer a terheléselosztáshoz hasonló módon működik a helyszíni környezetben. További információ: terheléselosztás [Az Azure infrastruktúra-szolgáltatásokhoz](../../../virtual-machines/windows/tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Emellett azt javasoljuk, hogy az Azure Virtual Network használatával állítson be egy magánhálózati hálózatot a virtuális gépekhez. Ez lehetővé teszi, hogy többek között a magánhálózati IP-címen keresztül kommunikáljanak egymással. További információ: [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>2 szintű és 3 szintű, üzleti szintű kibővített
Ebben az alkalmazási mintában kétrétegű vagy 3 rétegbeli adatbázis-alkalmazást telepíthet az Azure Virtual Machinesba azáltal, hogy az egyes alkalmazások rétegeit egy másik virtuális gépen helyezi el. Emellett előfordulhat, hogy az alkalmazás összetettsége miatt érdemes több virtuális gépre terjeszteni az alkalmazáskiszolgáló összetevőit.

Ez az alkalmazási minta a következő esetekben hasznos:

* Vállalati alkalmazásokat szeretne áthelyezni helyszíni virtualizált platformokról az Azure Virtual Machinesba.
* Az alkalmazás összetettsége miatt az alkalmazáskiszolgáló összetevőit több virtuális gépre szeretné terjeszteni.
* Az üzleti logikát a helyszíni LOB (üzletági) alkalmazások Azure Virtual Machinesba kívánja áthelyezni. A LOB-alkalmazások olyan kritikus számítógépes alkalmazások összessége, amelyek létfontosságúak a vállalatok, például a nyilvántartás, az emberi erőforrások (HR), a bérszámfejtés, az Ellátásilánc-kezelés és az erőforrás-tervezési alkalmazások futtatásához.
* Rövid ideig szeretné gyorsan kiépíteni a fejlesztési és tesztelési környezeteket.
* A különböző számítási feladatok szintjén szeretné elvégezni a terheléses tesztelést, de ugyanakkor nem szeretné, hogy az egész idő alatt sok fizikai gépet lehessen fenntartani és karbantartani.
* Olyan infrastrukturális környezetet szeretne, amely igény szerint fel-és leskálázást tud végezni.

A következő ábra egy helyszíni forgatókönyvet és a felhőben engedélyezett megoldását mutatja be. Ebben az esetben az alkalmazási rétegeket több virtuális gépen helyezi el az Azure-ban az üzleti szint horizontális felskálázásával, amely az üzleti logikát és az adatelérési összetevőket tartalmazza. Ahogy az ábrán is látható, Azure Load Balancer feladata a forgalom elosztása több virtuális gép között, és azt is, hogy melyik webkiszolgálót kívánja csatlakozni a szolgáltatáshoz. Ha a Load Balancer mögött lévő alkalmazás-kiszolgálók több példánya is biztosítja az üzleti szint magas rendelkezésre állását. További információ: [ajánlott eljárások a kétrétegű, 3 vagy n szintű alkalmazásokhoz, amelyek egyetlen szinten több virtuális géppel rendelkeznek](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Alkalmazási minta az üzleti réteg felskálázásával](./media/application-patterns-development-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>kétrétegű és 3 szintű, bemutatási és üzleti szintű kibővíthető és HADR
Ebben az alkalmazási mintában kétrétegű vagy 3 rétegbeli adatbázis-alkalmazást telepíthet az Azure Virtual Machinesba a bemutató réteg (webkiszolgáló) és az üzleti réteg (alkalmazáskiszolgáló) összetevőinek több virtuális gépre való elosztásával. Emellett magas rendelkezésre állású és vész-helyreállítási (HADR) megoldásokat is megvalósíthat az Azure Virtual Machines-adatbázisokhoz.

Ez az alkalmazási minta a következő esetekben hasznos:

* SQL Server magas rendelkezésre állású és vész-helyreállítási képességek megvalósításával szeretné áthelyezni a vállalati alkalmazásokat a helyszíni virtualizált platformokról az Azure-ba.
* A beérkező ügyfélalkalmazások megnövekedett mennyisége és az alkalmazás összetettsége miatt ki szeretné bővíteni a megjelenítési szintet és az üzleti szintet.
* Rövid ideig szeretné gyorsan kiépíteni a fejlesztési és tesztelési környezeteket.
* A különböző számítási feladatok szintjén szeretné elvégezni a terheléses tesztelést, de ugyanakkor nem szeretné, hogy az egész idő alatt sok fizikai gépet lehessen fenntartani és karbantartani.
* Olyan infrastrukturális környezetet szeretne, amely igény szerint fel-és leskálázást tud végezni.

A következő ábra egy helyszíni forgatókönyvet és a felhőben engedélyezett megoldását mutatja be. Ebben a forgatókönyvben az Azure-ban több virtuális gépen is kibővítheti a megjelenítési szintet és az üzleti réteg összetevőit. Emellett magas rendelkezésre állású és vész-helyreállítási (HADR) technikákat is megvalósíthat az Azure-beli SQL Server-adatbázisokhoz.

Ha egy alkalmazás több példányát futtatja különböző virtuális gépeken, győződjön meg róla, hogy terheléselosztási kérelmek vannak a hálózaton. Ha több virtuális géppel rendelkezik, győződjön meg arról, hogy minden virtuális gép elérhető, és egy adott időpontban fut. Ha konfigurálja a terheléselosztást, Azure Load Balancer nyomon követi a virtuális gépek állapotát, és megfelelően irányítja a bejövő hívásokat az kifogástalan működésű virtuális gépek csomópontjaira. A virtuális gépek terheléselosztásának beállításával kapcsolatos információkért lásd: terheléselosztás az [Azure infrastruktúra-szolgáltatásokhoz](../../../virtual-machines/windows/tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). A webes és az alkalmazás-kiszolgálók egy terheléselosztó mögötti több példánya biztosítja a bemutató és az üzleti szintek magas rendelkezésre állását.

![Kibővíthető és magas rendelkezésre állás](./media/application-patterns-development-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Ajánlott eljárások az SQL-HADR igénylő alkalmazási mintákhoz
Ha SQL Server magas rendelkezésre állású és vész-helyreállítási megoldásokat állít be az Azure Virtual Machines-ban, a virtuális gépek virtuális hálózatának beállítása az [azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) használatával kötelező. A Virtual Network belüli virtuális gépek a szolgáltatás leállása után is stabil magánhálózati IP-címmel rendelkeznek majd, így elkerülhető a DNS-névfeloldáshoz szükséges frissítési idő. Emellett a virtuális hálózat lehetővé teszi, hogy a helyszíni hálózatot kiterjessze az Azure-ba, és megbízható biztonsági határt hozzon létre. Ha például az alkalmazás vállalati tartományi korlátozásokkal rendelkezik (például Windows-hitelesítés, Active Directory), az [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) beállítása szükséges.

Az Azure-ban üzemi kódokat futtató ügyfeleink többsége elsődleges és másodlagos replikákat tart fenn az Azure-ban.

A magas rendelkezésre állással és a vész-helyreállítási technikákkal kapcsolatos átfogó információk és oktatóanyagok: [magas rendelkezésre állás és vész-helyreállítás az Azure Virtual Machines SQL Server](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="2-tier-and-3-tier-using-azure-virtual-machines-and-cloud-services"></a>2 szintű és 3 réteg az Azure Virtual Machines és Cloud Services használatával
Ebben az alkalmazásban az Azure [Cloud Services](../../../cloud-services/cloud-services-choose-me.md) (webes és feldolgozói szerepkörök – szolgáltatásként szolgáló platform (IaaS)) és az [Azure Virtual Machines](../../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (az infrastruktúra-szolgáltatás ()) használatával kétrétegű vagy 3 rétegbeli alkalmazást telepíthet az Azure-ba. Az Azure- [Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) az Azure-ban az Azure-ban, az adatcsomagok esetében a megjelenítési szintek, az üzleti szintek és az Azure-beli [Virtual Machines](../../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) SQL Server használata előnyös az Azure-on futó legtöbb alkalmazás Ennek az az oka, hogy a Cloud Serviceson futó számítási példányok egyszerű felügyeletet, üzembe helyezést, figyelést és méretezhetőséget biztosítanak.

A Cloud Services az Azure fenntartja az infrastruktúrát, a rutin karbantartást végez, elhárítja az operációs rendszereket, és megkísérli a helyreállítást a szolgáltatás és a hardver meghibásodása miatt. Ha az alkalmazásnak az alkalmazás által használt példányok vagy virtuális gépek számának növelésével vagy csökkentésével kell kibővíteni, automatikusan és manuális méretezési lehetőségeket biztosítania a Cloud Service-projekt számára. Emellett a helyszíni Visual Studióval is üzembe helyezheti alkalmazását az Azure-beli Cloud Service-projektben.

Ha nem szeretné, hogy a bemutató/üzleti szinthez ne kelljen kiterjedt felügyeleti feladatokat felvennie, és az alkalmazás nem igényli a szoftver vagy az operációs rendszer bonyolult konfigurációját, használja az Azure Cloud Services. Ha Azure SQL Database nem támogatja az összes keresett funkciót, az adatcsomaghoz használja az Azure-beli virtuális gép SQL Server. Az Azure Cloud Serviceson futó alkalmazások futtatása és az Azure-Virtual Machines tárolt adattárolás ötvözi mindkét szolgáltatás előnyeit. Részletes összehasonlításért tekintse meg az Azure-beli [fejlesztési stratégiák összehasonlításával](#comparing-web-development-strategies-in-azure)foglalkozó témakör című szakaszát.

Ebben az alkalmazási mintában a megjelenítési csomag egy webes szerepkört tartalmaz, amely az Azure végrehajtási környezetben futó Cloud Services-összetevő, amely az IIS és a ASP.NET által támogatott webalkalmazás-programozáshoz van testreszabva. Az üzleti vagy a háttérrendszer-csomag feldolgozói szerepkört tartalmaz, amely az Azure végrehajtási környezetben futó Cloud Services-összetevő, és az általánosított fejlesztés esetében hasznos, és a webes szerepkörök esetében a háttérben történő feldolgozást is elvégezheti. Az adatbázis-csomag egy SQL Server virtuális gépen található az Azure-ban. A megjelenítési és az adatbázis-rétegek közötti kommunikáció közvetlenül vagy az üzleti szinten, a feldolgozói szerepkör összetevőin történik.

Ez az alkalmazási minta a következő esetekben hasznos:

* SQL Server magas rendelkezésre állású és vész-helyreállítási képességek megvalósításával szeretné áthelyezni a vállalati alkalmazásokat a helyszíni virtualizált platformokról az Azure-ba.
* Olyan infrastrukturális környezetet szeretne, amely igény szerint fel-és leskálázást tud végezni.
* A Azure SQL Database nem támogatja az alkalmazás vagy az adatbázis által igényelt összes funkciót.
* A különböző számítási feladatok szintjén szeretné elvégezni a terheléses tesztelést, de ugyanakkor nem szeretné, hogy az egész idő alatt sok fizikai gépet lehessen fenntartani és karbantartani.

A következő ábra egy helyszíni forgatókönyvet és a felhőben engedélyezett megoldását mutatja be. Ebben a forgatókönyvben a bemutató szintet a webes szerepkörök, a feldolgozói szerepkörök üzleti szintjei, de az Azure-beli virtuális gépek adatszintje helyezi el. Ha több példányban futtatja a megjelenítési szintet a különböző webes szerepkörökben, a többek között terheléselosztási kérelmeket biztosít. Ha az Azure-Cloud Services az Azure Virtual Machines kombinálja, javasoljuk, hogy állítsa be az [azure Virtual Networkt](../../../virtual-network/virtual-networks-overview.md) is. Az [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)segítségével állandó és állandó magánhálózati IP-címekkel rendelkezhet a felhőben lévő azonos felhőalapú szolgáltatásban. Miután meghatározta a virtuális hálózatokat a virtuális gépek és a Cloud Services számára, megkezdheti a kommunikációt egymás között a magánhálózati IP-cím használatával. Emellett a virtuális gépek és az Azure-beli webes és feldolgozói szerepkörök ugyanabban az [Azure-Virtual Networkban](../../../virtual-network/virtual-networks-overview.md) alacsony késést és biztonságosabb kapcsolatot biztosítanak. További információ: [What is a Cloud Service](../../../cloud-services/cloud-services-choose-me.md).

Ahogy az ábrán is látható, Azure Load Balancer több virtuális gép között osztja el a forgalmat, és meghatározza, hogy melyik webkiszolgáló vagy alkalmazáskiszolgáló csatlakozhat a szolgáltatáshoz. A webes és az alkalmazás-kiszolgálók egy terheléselosztó mögötti több példánya biztosítja a megjelenítési szint és az üzleti szint magas rendelkezésre állását. További információ: [ajánlott eljárások az SQL-HADR igénylő alkalmazási mintákhoz](#best-practices-for-application-patterns-requiring-sql-hadr).

![A diagram az Azure virtuális hálózatban lévő webes szerepkör példányaihoz csatlakoztatott helyszíni fizikai vagy virtuális gépeket mutatja be egy Azure Load Balancer használatával.](./media/application-patterns-development-strategies/IC728013.png)

Az alkalmazási minta megvalósításának egy másik megközelítése egy konszolidált webes szerepkör használata, amely a megjelenítési és az üzleti szintű összetevőket egyaránt tartalmazza az alábbi ábrán látható módon. Ez az alkalmazási minta olyan alkalmazások esetében hasznos, amelyek állapot-nyilvántartó kialakítást igényelnek. Mivel az Azure állapot nélküli számítási csomópontokat biztosít a webes és feldolgozói szerepkörökben, javasoljuk, hogy a munkamenet-állapot tárolásához a következő technológiák egyikével hozzon létre egy logikát: [Azure gyorsítótárazás](https://azure.microsoft.com/documentation/services/azure-cache-for-redis/), [azure Table Storage](../../../cosmos-db/tutorial-develop-table-dotnet.md) vagy [Azure SQL Database](../../database/sql-database-paas-overview.md).

![A diagram a konszolidált webes/feldolgozói szerepkörrel rendelkező helyszíni fizikai vagy virtuális gépeket mutatja be egy Azure-beli virtuális hálózatban.](./media/application-patterns-development-strategies/IC728014.png)

## <a name="pattern-with-azure-virtual-machines-azure-sql-database-and-azure-app-service-web-apps"></a>Minta az Azure Virtual Machines, Azure SQL Database és Azure App Service (Web Apps)
Az alkalmazás mintájának elsődleges célja, hogy bemutassa, hogyan egyesítheti az Azure infrastruktúra-szolgáltatást (IaaS) a megoldásban az Azure platform-Service Components (Pásti) összetevővel. Ez a minta a Azure SQL Database a viszonyítási adattárolásra összpontosít. A szolgáltatás nem tartalmazza az Azure-beli virtuális gépek SQL Serverét, amely az Azure-infrastruktúra szolgáltatási ajánlatának részét képezi.

Ebben az alkalmazási mintában egy adatbázis-alkalmazást helyez üzembe az Azure-ban azáltal, hogy a bemutatót és az üzleti szinteket ugyanabban a virtuális gépen helyezi el, és az adatbázishoz fér hozzá Azure SQL Database (SQL Database) kiszolgálókon. A bemutató réteg a hagyományos IIS-alapú webes megoldások használatával valósítható meg. Egy kombinált bemutatót és üzleti szintet is alkalmazhat [Azure app Service](https://azure.microsoft.com/documentation/services/app-service/web/)használatával.

Ez az alkalmazási minta a következő esetekben hasznos:

* Már rendelkezik az Azure-ban konfigurált meglévő SQL Database-kiszolgálóval, és gyorsan tesztelni szeretné az alkalmazást.
* Tesztelni szeretné az Azure-környezet képességeit.
* Rövid ideig szeretné gyorsan kiépíteni a fejlesztési és tesztelési környezeteket.
* Az üzleti logikája és az adatelérési összetevői egy webalkalmazásban önállóan is szerepelhetnek.

A következő ábra egy helyszíni forgatókönyvet és a felhőben engedélyezett megoldását mutatja be. Ebben a forgatókönyvben az alkalmazási rétegeket egyetlen virtuális gépre helyezi az Azure-ban, és a Azure SQL Databaseban lévő adatelérést.

![Vegyes alkalmazás mintája](./media/application-patterns-development-strategies/IC728015.png)

Ha a kombinált webes és alkalmazási szintet az Azure Web Apps használatával valósítja meg, javasoljuk, hogy egy webalkalmazás kontextusában tartsa a középső vagy az alkalmazási szintet dinamikus csatolású kódtáraként (DLL).

Emellett tekintse át az [Azure-ban a webes fejlesztési stratégiák összehasonlítása](#comparing-web-development-strategies-in-azure) című szakaszban ismertetett javaslatokat, amelyekkel további információkat tudhat meg a programozási módszerekről.

## <a name="n-tier-hybrid-application-pattern"></a>N szintű hibrid alkalmazás mintája
Az n szintű hibrid alkalmazási mintában az alkalmazást több, a helyszíni és az Azure-ban elosztott rétegben implementálhatja. Ezért létrehoz egy rugalmas és újrahasznosítható hibrid rendszerrendszert, amely lehetővé teszi egy adott réteg módosítását vagy hozzáadását a többi réteg módosítása nélkül. Ha a vállalati hálózatot szeretné kiterjeszteni a felhőre, használja az [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) szolgáltatást.

A hibrid alkalmazás mintája a következő esetekben hasznos:

* Olyan alkalmazásokat szeretne létrehozni, amelyek részben a felhőben, részben pedig a helyszínen futnak.
* Egy meglévő helyszíni alkalmazás néhány vagy összes elemét át szeretné telepíteni a felhőbe.
* Vállalati alkalmazásokat szeretne áthelyezni helyszíni virtualizált platformokról az Azure-ba.
* Olyan infrastrukturális környezetet szeretne, amely igény szerint fel-és leskálázást tud végezni.
* Rövid ideig szeretné gyorsan kiépíteni a fejlesztési és tesztelési környezeteket.
* Költséghatékony megoldást szeretne készíteni a vállalati adatbázis-alkalmazások biztonsági mentéséhez.

Az alábbi ábra egy n szintű hibrid alkalmazási mintát mutat be, amely a helyszíni és az Azure-ra is kiterjed. Ahogy az ábrán is látható, a helyszíni infrastruktúra [Active Directory Domain Services](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831484(v=ws.11)) tartományvezérlővel támogatja a felhasználók hitelesítését és engedélyezését. Vegye figyelembe, hogy a diagram egy olyan forgatókönyvet mutat be, amelyben az adatréteg egyes részei egy helyszíni adatközpontban élnek, míg az adatréteg egyes részei az Azure-ban is elérhetők. Az alkalmazás igényeitől függően számos más hibrid forgatókönyv is megvalósítható. Például megtarthatja a megjelenítési szintet és az üzleti szintet egy helyszíni környezetben, de az Azure-ban lévő adatrétegben is.

![N szintű alkalmazás mintája](./media/application-patterns-development-strategies/IC728016.png)

Az Azure-ban használhatja a Active Directory önálló Felhőbeli címtárként a szervezet számára, vagy integrálhatja a meglévő helyszíni Active Directoryt is a [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)használatával. Ahogy az ábrán is látható, az üzleti réteg összetevői több adatforráshoz is hozzáférhetnek, például az [Azure-ban SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md) egy privát belső IP-címen keresztül, a helyszíni SQL Server az [Azure-Virtual Network](../../../virtual-network/virtual-networks-overview.md)keresztül, vagy a .NET-keretrendszer adatszolgáltatói technológiáinak használatával [SQL Database](../../database/sql-database-paas-overview.md) . Ebben a diagramban Azure SQL Database egy opcionális adattárolási szolgáltatás.

Az n szintű hibrid alkalmazás mintájában a következő munkafolyamat valósítható meg a megadott sorrendben:

1. Azonosítsa azokat a vállalati adatbázis-alkalmazásokat, amelyeket a [Microsoft Assessment and Planning (MAP) eszközkészlet](https://microsoft.com/map)használatával kell áthelyezni a felhőbe. A MAP Toolkit leltár-és teljesítményadatokat gyűjt a virtualizációt fontolgató számítógépekről, és ajánlásokat biztosít a kapacitással és az értékeléssel kapcsolatos tervezéssel kapcsolatban.
2. Tervezze meg az Azure platformon szükséges erőforrásokat és konfigurációt, például a Storage-fiókokat és a virtuális gépeket.
3. Hálózati kapcsolat beállítása a helyi vállalati hálózat és az [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)között. A helyszíni vállalati hálózat és az Azure-beli virtuális gép közötti kapcsolat beállításához használja az alábbi két módszer egyikét:
   
   1. Hozzon létre kapcsolatot a helyszíni és az Azure között az Azure-beli virtuális gépek nyilvános végponti pontjain keresztül. Ez a módszer egyszerű beállítást biztosít, és lehetővé teszi SQL Server hitelesítés használatát a virtuális gépen. Emellett állítsa be a hálózati biztonsági csoport szabályait a virtuális gép nyilvános forgalmának szabályozására. További információ: [külső hozzáférés engedélyezése a virtuális géphez a Azure Portal használatával](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   2. Kapcsolat létesítése a helyszíni és az Azure között az Azure virtual private Network (VPN) alagúton keresztül. Ezzel a módszerrel kiterjesztheti a tartományi házirendeket egy Azure-beli virtuális gépre. Emellett beállíthatja a tűzfalszabályok beállításait, és Windows-hitelesítést is használhat a virtuális gépen. Az Azure jelenleg a biztonságos helyek közötti VPN-és pont – hely VPN-kapcsolatokat támogatja:
      
      * A biztonságos helyek közötti kapcsolat használatával hálózati kapcsolatot létesíthet a helyszíni hálózat és az Azure-beli virtuális hálózat között. Javasoljuk, hogy a helyszíni adatközpont-környezetét az Azure-hoz csatlakoztassa.
      * A biztonságos pont – hely kapcsolattal hálózati kapcsolatot létesíthet az Azure-beli virtuális hálózat és az Ön által futtatott önálló számítógépek között. Elsősorban fejlesztési és tesztelési célokra ajánlott.
      
      További információ az Azure-beli SQL Serverhoz való kapcsolódásról: [kapcsolódás SQL Server virtuális géphez az Azure](ways-to-connect-to-sql.md)-ban.
4. Olyan ütemezett feladatok és riasztások beállítása, amelyek a helyszíni adatok biztonsági mentését végzik az Azure-beli virtuálisgép-lemezeken. További információ: [SQL Server biztonsági mentés és visszaállítás az Azure Blob Storage szolgáltatással](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) , valamint [az Virtual Machines azure-beli SQL Server biztonsági mentése és visszaállítása](../../../azure-sql/virtual-machines/windows/backup-restore.md).
5. Az alkalmazás igényeitől függően a következő három gyakori forgatókönyv közül választhat:
   
   1. Megtarthatja a webkiszolgálót, az alkalmazáskiszolgáló és a nem bizalmas adatokat az Azure-beli adatbázis-kiszolgálókon, és megőrizheti a bizalmas adatokat a helyszínen.
   2. A webkiszolgáló és az alkalmazáskiszolgáló a helyszínen tartható, míg az adatbázis-kiszolgáló egy Azure-beli virtuális gépen.
   3. Az adatbázis-kiszolgáló, a webkiszolgáló és az alkalmazáskiszolgáló a helyszínen tartható, míg az adatbázis-replikák megmaradnak az Azure-beli virtuális gépeken. Ez a beállítás lehetővé teszi a helyszíni webkiszolgálók vagy a jelentéskészítő alkalmazások számára az adatbázis-replikák elérését az Azure-ban. Ezért elérheti a számítási feladatok egy helyszíni adatbázisban való csökkentését. Javasoljuk, hogy ezt a forgatókönyvet nagy mennyiségű olvasási feladatra és fejlesztési célra alkalmazza. Az adatbázis-replikák Azure-beli létrehozásával kapcsolatos információkért lásd: AlwaysOn rendelkezésre állási csoportok [magas rendelkezésre állású és vész-helyreállítási SQL Server Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md)-beli Virtual machines.

## <a name="comparing-web-development-strategies-in-azure"></a>Webes fejlesztési stratégiák összehasonlítása az Azure-ban
Többrétegű SQL Server-alapú alkalmazás az Azure-ban való megvalósításához és üzembe helyezéséhez a következő két programozási módszer közül választhat:

* Hozzon létre egy hagyományos webkiszolgálót (IIS-Internet Information Services) az Azure-ban, és nyissa meg az Azure Virtual Machines SQL Server adatbázisait.
* Felhőalapú szolgáltatás implementálása és üzembe helyezése az Azure-ban. Ezt követően győződjön meg arról, hogy a felhőalapú szolgáltatás hozzáférhet az Azure Virtual Machines SQL Server adatbázisaihoz. A felhőalapú szolgáltatások több webes és feldolgozói szerepkört is tartalmazhatnak.

Az alábbi táblázat a hagyományos webes fejlesztés és az Azure-Cloud Services és az Azure-Web Apps összehasonlítását ismerteti az Azure Virtual Machines SQL Server tekintetében. A tábla tartalmazza az Azure Web Apps-t, mivel az Azure-beli virtuális gépeken lévő SQL Server a nyilvános virtuális IP-cím vagy a DNS-név használatával lehet használni az Azure-beli Web Apps.

|  | Hagyományos webes fejlesztés az Azure Virtual Machines | Felhőalapú szolgáltatások az Azure-ban | Webes üzemeltetés az Azure Web Apps |
| --- | --- | --- | --- |
| **Alkalmazások migrálása a helyszíni környezetből** |Meglévő alkalmazások. |Az alkalmazásokhoz webes és feldolgozói szerepkörökre van szükség. |A már meglévő alkalmazások, azonban olyan önálló webes alkalmazások és webszolgáltatások számára készültek, amelyek gyors skálázhatóságot igényelnek. |
| **Fejlesztés és üzembe helyezés** |Visual Studio, WebMatrix, Visual Web Developer, webdeploy, FTP, TFS, IIS-kezelő, PowerShell. |Visual Studio, Azure SDK, TFS, PowerShell. Minden felhőalapú szolgáltatás két környezettel rendelkezik, amelyekkel üzembe helyezheti a szervizcsomagot és a konfigurációt: átmeneti és éles környezetben. Üzembe helyezhet egy felhőalapú szolgáltatást az átmeneti környezetben, hogy tesztelje azt, mielőtt előlépteti az éles környezetbe. |A Visual Studio, a WebMatrix, a Visual Web Developer, az FTP, a GIT, a BitBucket, a CodePlex, a DropBox, a GitHub, a Mercurial, a TFS, a web Deploy, a PowerShell. |
| **Felügyelet és beállítás** |Az alkalmazásra, az adatokra, a tűzfalszabályokra, a virtuális hálózatra és az operációs rendszerre vonatkozó adminisztratív feladatokért felelős. |Az alkalmazás, az adatok, a tűzfalszabályok és a virtuális hálózat adminisztratív feladataiért felelős. |Csak az alkalmazásért és az adatokért felelős a felügyeleti feladatokért. |
| **Magas rendelkezésre állás és vész-helyreállítás (HADR)** |Javasoljuk, hogy a virtuális gépeket ugyanabban a rendelkezésre állási csoportba helyezze, és ugyanabban a felhőalapú szolgáltatásban. A virtuális gépek ugyanazon rendelkezésre állási csoportba való tartása lehetővé teszi, hogy az Azure a magas rendelkezésre állású csomópontokat különálló tartalék tartományokra és frissítési tartományokra helyezze. Hasonlóképpen, a virtuális gépek ugyanabban a felhőalapú szolgáltatásban való megőrzése lehetővé teszi a terheléselosztást, és a virtuális gépek közvetlenül kommunikálhatnak egymással a helyi hálózaton egy Azure-adatközponton belül.<br/><br/>Ön felelős az Azure-Virtual Machines SQL Server magas rendelkezésre állású és vész-helyreállítási megoldásának végrehajtásához, hogy elkerülje az állásidőt. A támogatott HADR-technológiákkal kapcsolatban lásd: [a magas rendelkezésre állás és a vész-helyreállítás az Azure Virtual Machines SQL Server](business-continuity-high-availability-disaster-recovery-hadr-overview.md).<br/><br/>Ön felelős a saját adatai és alkalmazása biztonsági mentéséhez.<br/><br/>Az Azure áthelyezheti a virtuális gépeket, ha az adatközpontban lévő gazdagép hardveres problémák miatt meghiúsul. Emellett előfordulhat, hogy a virtuális gép tervezett leállása a gazdagép biztonsági vagy szoftverfrissítési frissítésekor. Ezért javasoljuk, hogy minden egyes alkalmazási szinten legalább két virtuális gépet fenntartsa a folyamatos rendelkezésre állás biztosítása érdekében. Az Azure nem biztosít SLA-t egyetlen virtuális géphez. |Az Azure kezeli az alapul szolgáló hardver vagy operációs rendszer szoftverének hibáit. Javasoljuk, hogy egy webes vagy feldolgozói szerepkör több példányát implementálja az alkalmazás magas rendelkezésre állásának biztosítása érdekében. További információ: [Cloud Services, Virtual Machines és Virtual Network szolgáltatói szerződés](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/).<br/><br/>Ön felelős a saját adatai és alkalmazása biztonsági mentéséhez.<br/><br/>Egy Azure-beli virtuális gépen SQL Server adatbázisban lévő adatbázisok esetében a leállás elkerülése érdekében a magas rendelkezésre állású és vész-helyreállítási megoldás végrehajtásáért felelős. A támogatott HDAR-technológiákkal kapcsolatban lásd: a magas rendelkezésre állás és a vész-helyreállítás az Azure Virtual Machines SQL Server.<br/><br/>**SQL Server adatbázis-tükrözés**: használat az Azure Cloud Services (webes/feldolgozói szerepkörök) használatával. SQL Server a virtuális gépek és a felhőalapú szolgáltatások projektje ugyanabban az Azure-Virtual Networkban lehet. Ha a SQL Server VM nem ugyanabban a Virtual Networkban van, létre kell hoznia egy SQL Server aliast, amely a SQL Server példányához irányítja a kommunikációt. Emellett az alias nevének meg kell egyeznie a SQL Server nevével. |A magas rendelkezésre állást az Azure feldolgozói szerepkörök, az Azure Blob Storage és a Azure SQL Database öröklik. Az Azure Storage például három replikát tart fenn az összes blob-, tábla-és üzenetsor-adathalmazon. Azure SQL Database egyszerre három replikát tart fenn – egy elsődleges replikát és két másodlagos replikát. További információ: [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) és [Azure SQL Database](../../database/sql-database-paas-overview.md).<br/><br/>Ha Azure-beli virtuális gépen SQL Servert használ az Azure-Web Apps adatforrásként, vegye figyelembe, hogy az Azure-Web Apps nem támogatja az Azure Virtual Network. Más szóval az Azure Web Appsról SQL Server az Azure-ba irányuló összes kapcsolatnak a virtuális gépek nyilvános végpontján át kell esnie. Ez bizonyos korlátozásokat eredményezhet a magas rendelkezésre állás és a vész-helyreállítási helyzetek esetében. Az Web Apps Azure-beli ügyfélalkalmazás például az adatbázis-tükrözéssel való SQL Server VMhoz való kapcsolódáskor nem tudott csatlakozni az új elsődleges kiszolgálóhoz, mert az adatbázis-tükrözéshez az Azure-Virtual Network beállítása szükséges az Azure-beli SQL Server gazdagépek között. Ezért a **SQL Server adatbázis-tükrözés** az Azure Web Apps használatával való használata jelenleg nem támogatott.<br/><br/>**SQL Server AlwaysOn rendelkezésre állási csoportok**: az Azure Web Apps Azure-beli virtuális gépekkel SQL Server való használatakor AlwaysOn rendelkezésre állási csoportokt állíthat be. A AlwaysOn rendelkezésre állási csoport figyelőjét azonban úgy kell beállítania, hogy a kommunikációt az elsődleges replikához a nyilvános terheléselosztási végpontokon keresztül irányítsa. |
| **Létesítmények közötti kapcsolatok** |Az Azure Virtual Network használatával kapcsolódhat a helyszíni hálózathoz. |Az Azure Virtual Network használatával kapcsolódhat a helyszíni hálózathoz. |Az Azure Virtual Network támogatott. További információ: [Web Apps Virtual Network Integration](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/). |
| **Méretezhetőség** |A méretezés a virtuális gépek méretének növelésével vagy további lemezek hozzáadásával érhető el. A virtuálisgép-méretekkel kapcsolatos további információkért lásd: [virtuális gépek méretei az Azure](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)-hoz.<br/><br/>**Adatbázis-kiszolgáló esetén: a** kibővített szolgáltatás adatbázis-particionálási módszerekkel és SQL Server alAlwaysOnon rendelkezésre állási csoportok használatával érhető el.<br/><br/>A nagy mennyiségű olvasási feladatokhoz több másodlagos csomóponton is használhatja a [AlwaysOn rendelkezésre állási csoportokt](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) , valamint SQL Server replikálást.<br/><br/>Nagy mennyiségű írási feladathoz több fizikai kiszolgálón is horizontális particionálási adatok valósíthatók meg, amelyek lehetővé teszik az alkalmazások méretezését.<br/><br/>Emellett a [SQL Server és az Adatfüggő útválasztás](/previous-versions/sql/sql-server-2005/administrator/cc966448(v=technet.10))használatával is megvalósítható a kibővíthető szolgáltatás. Az Adatfüggő útválasztással (DDR) együtt kell megvalósítani a particionálási mechanizmust az ügyfélalkalmazás, jellemzően az üzleti réteg rétegben, hogy az adatbázis-kérelmeket több SQL Server csomópontra irányítsa. Az üzleti csomag leképezéseket tartalmaz az adatparticionálás módjáról, valamint azt, hogy melyik csomópont tartalmazza az adathalmazt.<br/><br/>A virtuális gépeket futtató alkalmazásokat is méretezheti. További információ: az [alkalmazások skálázása](../../../cloud-services/cloud-services-how-to-scale-portal.md).<br/><br/>**Fontos megjegyzés**: az Azure automatikus **Méretezés** funkciója lehetővé teszi, hogy automatikusan növelje vagy csökkentse az alkalmazás által használt virtuális gépeket. Ez a szolgáltatás garantálja, hogy a végfelhasználói élmény negatív hatással van a csúcsidőben, a virtuális gépek pedig leállnak, ha az igény alacsony. Javasoljuk, hogy ha SQL Server virtuális gépeket is tartalmaz, ne adja meg a felhőalapú szolgáltatás autoskálázási beállítását. Ennek az az oka, hogy az Azure egy virtuális gép bekapcsolását teszi lehetővé, amikor az adott virtuális gépen lévő CPU-használat magasabb, mint egy küszöbérték, és ki kell kapcsolni a virtuális gépet, ha a CPU-használat alacsonyabb, mint. Az autoscale funkció olyan állapot nélküli alkalmazások esetében hasznos, mint a webkiszolgálók, ahol bármely virtuális gép a korábbi állapotokra mutató hivatkozások nélkül képes kezelni a munkaterhelést. Az autoscale szolgáltatás azonban nem használható olyan állapot-nyilvántartó alkalmazások esetében, mint például a SQL Server, ahol csak egy példány engedélyezi az adatbázisba való írást. |A vertikális felskálázás több webes és feldolgozói szerepkör használatával érhető el. A webes szerepkörökhöz és a feldolgozói szerepkörökhöz tartozó virtuálisgép-méretekről további információt a [méretek konfigurálása Cloud Services számára](../../../cloud-services/cloud-services-sizes-specs.md)című témakörben talál.<br/><br/>**Cloud Services** használatakor több szerepkört is megadhat a feldolgozás elosztásához, valamint az alkalmazás rugalmas skálázásának eléréséhez. Minden felhőalapú szolgáltatás egy vagy több webes szerepkört és/vagy feldolgozói szerepkört tartalmaz, amelyek mindegyike a saját alkalmazás fájljait és konfigurációját tartalmazza. A felhőalapú szolgáltatások felskálázásához növelje a szerepkörhöz üzembe helyezett szerepkör-példányok (virtuális gépek) számát, és bővítse a felhőalapú szolgáltatást a szerepkör-példányok számának csökkentésével. Részletes információkért lásd: [Azure-végrehajtási modellek](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>A kibővített Azure magas rendelkezésre állású támogatással [Cloud Services, Virtual Machines és Virtual Network szolgáltatói szerződés](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) és Load Balancer használatával érhető el.<br/><br/>Többrétegű alkalmazások esetében javasoljuk, hogy a webes/feldolgozói szerepköröket az Azure Virtual Network használatával összekösse az adatbázis-kiszolgáló virtuális gépekkel. Emellett az Azure terheléselosztást biztosít a virtuális gépek számára ugyanabban a felhőalapú szolgáltatásban, és elosztja a felhasználók kéréseit. Az ily módon csatlakoztatott virtuális gépek közvetlenül kommunikálhatnak egymással a helyi hálózaton egy Azure-adatközponton belül.<br/><br/>Az Azure Portalon és az ütemezett időpontokban is beállíthatja az **Autoskálázást** . További információkért lásd: [az automatikus skálázás konfigurálása felhőalapú szolgáltatásokhoz a portálon](../../../cloud-services/cloud-services-how-to-scale-portal.md). |Vertikális **fel-és leskálázás**: növelheti vagy csökkentheti a webhely számára fenntartott példány (VM) méretét.<br/><br/>Vertikális felskálázás: további fenntartott példányokat (virtuális gépeket) adhat hozzá a webhelyhez.<br/><br/>Beállíthatja az **Autoskálázást** a portálon, valamint az ütemterv időpontját. További információkért lásd: [Web Apps skálázása](../../../app-service/manage-scale-up.md). |

A programozási módszerek közötti választással kapcsolatos további információkért tekintse meg az [Azure Web Apps, a Cloud Services és a virtuális gépek: Mikor kell ezt használni](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="next-steps"></a>Következő lépések
Az Azure Virtual Machines SQL Server futtatásával kapcsolatos további információkért lásd: [SQL Server az Azure-on Virtual Machines áttekintés](sql-server-on-azure-vm-iaas-what-is-overview.md).
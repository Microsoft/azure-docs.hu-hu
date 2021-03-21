---
title: Azure Migrate készülék – gyakori kérdések
description: Választ kaphat a Azure Migrate berendezéssel kapcsolatos gyakori kérdésekre.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: 5a050d9aab9e8665c6048391488e57c9b4af10a5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043065"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate berendezés: gyakori kérdések

Ez a cikk az Azure Migrate berendezéssel kapcsolatos gyakori kérdésekre ad választ. Ha további kérdései vannak, tekintse meg a következő erőforrásokat:

- [Általános kérdések](resources-faq.md) a Azure Migrate
- A [felderítéssel, az értékeléssel és a függőségi vizualizációval](common-questions-discovery-assessment.md) kapcsolatos kérdések
- A [kiszolgálók áttelepítésével](common-questions-server-migration.md) kapcsolatos kérdések
- A [Azure Migrate fórumban](https://aka.ms/AzureMigrateForum) választ kaphat kérdéseire

## <a name="what-is-the-azure-migrate-appliance"></a>Mi a Azure Migrate készülék?

Az Azure Migrate készülék egy könnyű berendezés, amelyet a Azure Migrate: a kiszolgáló-értékelési eszköz a fizikai vagy virtuális kiszolgálók felderítésére és vizsgálatára használja a helyszíni vagy bármely felhőből. A Azure Migrate: a kiszolgáló áttelepítési eszköze a berendezést is használja a helyszíni VMware virtuális gépek ügynök nélküli áttelepítéséhez.

További információ az Azure Migrate készülékről:

- A berendezést virtuális gépre vagy fizikai gépre helyezik üzembe.
- A készülék felfedi a helyszíni gépeket, és folyamatosan küldi a gépi metaadatokat és teljesítményadatokat a Azure Migrate.
- A készülék felderítése ügynök nélkül történik. Nincs telepítve a felderített gépekre.

[További](migrate-appliance.md) információ a készülékről.

## <a name="how-can-i-deploy-the-appliance"></a>Hogyan helyezhetem üzembe a készüléket?

A készülék több módszer használatával is üzembe helyezhető:

- A készülék a VMware vagy a Hyper-V környezetben futó kiszolgálók sablonjának használatával telepíthető (a VMware-hez vagy [a virtuális merevlemezhez a Hyper-v-hez](how-to-set-up-appliance-hyper-v.md)készült[petesejtek sablonja](how-to-set-up-appliance-vmware.md) ).
- Ha nem szeretne sablont használni, telepítheti a készüléket VMware vagy Hyper-V környezetben egy [PowerShell-telepítő parancsfájl](deploy-appliance-script.md)használatával.
- Azure Government a készüléket egy PowerShell-telepítő parancsfájl használatával kell telepítenie. Tekintse át az üzembe helyezés lépéseit [itt](deploy-appliance-script-government.md).
- A helyszíni vagy más Felhőbeli fizikai vagy virtualizált kiszolgálók esetében mindig PowerShell-telepítő parancsfájl használatával telepítse a készüléket. Tekintse át az üzembe helyezés lépéseit [itt](how-to-set-up-appliance-physical.md).

## <a name="how-does-the-appliance-connect-to-azure"></a>Hogyan kapcsolódik a készülék az Azure-hoz?

A készülék az interneten keresztül vagy az Azure ExpressRoute segítségével csatlakozhat. 

- Győződjön meg arról, hogy a készülék képes csatlakozni ezekhez az [Azure URL-címekhez](./migrate-appliance.md#url-access). 
- A ExpressRoute-et Microsoft-partnerekkel is használhatja. A nyilvános társítás elavult, és nem érhető el az új ExpressRoute-áramkörökhöz.
- A privát társ-kezelés csak nem támogatott.


## <a name="does-appliance-analysis-affect-performance"></a>Befolyásolja a berendezések elemzése a teljesítményt?

A Azure Migrate készülék folyamatosan a teljesítményadatokat méri a helyszíni gépeken. Ez a profilkészítés szinte nem befolyásolja a profilkészítésen átadott gépek teljesítményét.

## <a name="can-i-harden-the-appliance-vm"></a>Megerősíthető a készülék virtuális gépe?

Ha a letöltött sablonnal hozza létre a berendezés virtuális gépet, hozzáadhat összetevőket (például víruskeresőt) a sablonhoz, ha a Azure Migrate berendezéshez szükséges kommunikációs és tűzfalszabályok nem állnak helyre.

## <a name="what-network-connectivity-is-required"></a>Milyen hálózati kapcsolat szükséges?

A készüléknek hozzá kell férnie az Azure URL-címeihez. [Tekintse át](migrate-appliance.md#url-access) az URL-listát.

## <a name="what-data-does-the-appliance-collect"></a>Milyen adatokat gyűjt a készülék?

A következő cikkekben talál információt arról, hogy az Azure Migrate készülék milyen adatokat gyűjt a virtuális gépeken:

- **VMWare virtuális gép**: [tekintse át](migrate-appliance.md#collected-data---vmware) az összegyűjtött adatokat.
- **Hyper-V virtuális gép**: [tekintse át](migrate-appliance.md#collected-data---hyper-v) az összegyűjtött adatokat.
- **Fizikai vagy virtuális kiszolgálók**: az összegyűjtött adatok [áttekintése](migrate-appliance.md#collected-data---physical) .

## <a name="how-is-data-stored"></a>Hogyan történik az adatok tárolása?

Az Azure Migrate készülék által gyűjtött adatokat az Azure-beli helyen tárolja a rendszer, ahol létrehozta a Azure Migrate projektet.

További információ az adatok tárolásáról:

- Az összegyűjtött adatokat a rendszer biztonságosan tárolja a Microsoft-előfizetések CosmosDB. Az Azure Migrate-projekt törlésekor a rendszer törli az adatvesztést. A tárterületet Azure Migrate kezeli. Nem választhat külön Storage-fiókot az összegyűjtött adatokhoz.
- Ha [függőségi vizualizációt](concepts-dependency-visualization.md)használ, a gyűjtött adatokat az Azure-előfizetésében létrehozott Azure log Analytics-munkaterületen tárolja a rendszer. Az adatai törlődnek, amikor törli a Log Analytics munkaterületet az előfizetésében. 

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Mekkora mennyiségű adatfeltöltés történik a folyamatos profilkészítés során?

A Azure Migrate elküldett adatok mennyisége több paramétertől függ. Például egy olyan Azure Migrate-projekt, amely 10 géppel rendelkezik (mindkettő egy lemezzel és egy hálózati adapterrel) körülbelül 50 MB adatmennyiséget küld naponta. Ez az érték közelítő; a tényleges érték a lemezek és hálózati adapterek adatpontjainak számától függ. Ha a gépek, lemezek vagy hálózati adapterek száma növekszik, az elküldett adatmennyiség növekedése nem lineáris.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Titkosítva van-e az adatok a nyugalmi és a átvitel során?

Igen, mindkettő esetén:

- A metaadatok biztonságos küldése a Azure Migrate szolgáltatásnak az interneten keresztül HTTPS használatával történik.
- A metaadatok egy [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) -adatbázisban és egy Microsoft-előfizetésben található [Azure Blob Storage](../storage/common/storage-service-encryption.md) -ban tárolódnak. A metaadatok titkosítva vannak a tárolás során.
- A függőségek elemzésének adatai is titkosítva vannak az átvitelben (biztonságos HTTPS-kapcsolaton keresztül). Ez az előfizetés egy Log Analytics munkaterületén van tárolva. Az adatok inaktív állapotban vannak titkosítva a függőségek elemzéséhez.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Hogyan kapcsolódik a készülék a vCenter Serverhoz?

Ezek a lépések azt írják le, hogyan kapcsolódik a készülék a VMware vCenter Serverhoz:

1. A berendezés a berendezés beállításakor megadott hitelesítő adatok használatával csatlakozik a vCenter Serverhoz (443-es port).
2. A készülék VMware PowerCLI használatával kérdezi le vCenter Server a vCenter Server által felügyelt virtuális gépekkel kapcsolatos metaadatok gyűjtését.
3. A berendezés konfigurációs adatokat gyűjt a virtuális gépekről (magok, memória, lemezek, hálózati adapterek) és az egyes virtuális gépek teljesítménybeli előzményeiről az elmúlt hónapban.
4. Az összegyűjtött metaadatokat a rendszer a Azure Migrate: kiszolgáló-értékelési eszközre (az interneten keresztül HTTPS protokollon keresztül) továbbítja az értékeléshez.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Csatlakozhat a Azure Migrate készülék több vCenter-kiszolgálóhoz?

Nem. Egy [Azure Migrate berendezés](migrate-appliance.md) és vCenter Server között egy-az-egyhez típusú hozzárendelés található. Több vCenter Server példányon futó virtuális gépek felderítéséhez több berendezést kell telepítenie. 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Rendelkezhet Azure Migrate-projekttel több készülékkel?

Egy projekthez több készülék is regisztrálva lehet. Egy készüléket azonban csak egyetlen projektben regisztrálhat.

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Csatlakozhat a Azure Migrate készülék/replikációs berendezés ugyanahhoz a vCenter?

Igen. Felveheti mind a Azure Migrate készüléket (az értékeléshez és az ügynök nélküli VMware áttelepítéshez), mind a replikációs berendezést (amely a VMware virtuális gépek ügynök-alapú áttelepítésére szolgál) ugyanarra a vCenter-kiszolgálóra. Azonban ügyeljen arra, hogy ne állítson be mindkét készüléket ugyanazon a virtuális gépen, és amely jelenleg nem támogatott.

## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Hány virtuális gépet vagy kiszolgálót tud felderíteni egy berendezéssel?

Akár 10 000 VMware virtuális gép, akár 5 000 Hyper-V virtuális gép, valamint akár 1000 fizikai kiszolgáló is felderíthető egyetlen berendezéssel. Ha több géppel rendelkezik a helyszíni környezetben, olvassa el a [Hyper-V felmérésének](scale-hyper-v-assessment.md)méretezését, [a VMware-értékelés méretezését](scale-vmware-assessment.md)és [a fizikai kiszolgáló értékelésének](scale-physical-assessment.md)méretezését ismertető részt.

## <a name="can-i-delete-an-appliance"></a>Törölhetek-e készüléket?

A készüléknek a projektből való törlése jelenleg nem támogatott.

A készülék törlésének egyetlen módja, ha törli a berendezéshez társított Azure Migrate projektet tartalmazó erőforráscsoportot.

Az erőforráscsoport törlése azonban törli az egyéb regisztrált berendezéseket, a felderített leltárt, az értékeléseket és a projekthez társított erőforráscsoport összes többi Azure-összetevőjét is.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Használhatom a készüléket egy másik előfizetéssel vagy projekttel?

Ha másik előfizetéssel vagy projekttel kívánja használni a készüléket, újra kell konfigurálnia a meglévő készüléket úgy, hogy futtatja a PowerShell-telepítő parancsfájlt az adott forgatókönyvhöz (VMware/Hyper-V/fizikai) a készülék számítógépén. A szkript törli a meglévő berendezés összetevőit és beállításait egy új berendezés üzembe helyezéséhez. Győződjön meg arról, hogy az újonnan telepített készülék Configuration Manager használatának megkezdése előtt törölni kell a böngésző gyorsítótárát.

Egy meglévő Azure Migrate-projekt kulcsát nem használhatja újra konfigurált berendezésen. Győződjön meg arról, hogy létrehoz egy új kulcsot a kívánt előfizetésből/projektből a készülék regisztrációjának befejezéséhez.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Be lehet állítani a készüléket egy Azure-beli virtuális gépen?

Nem. Ez a lehetőség jelenleg nem támogatott.

## <a name="can-i-discover-on-an-esxi-host"></a>Felfedezhető egy ESXi-gazdagépen is?

Nem. A VMware virtuális gépek felderítéséhez vCenter Server szükséges.

## <a name="how-do-i-update-the-appliance"></a>Hogyan frissíteni a készüléket?

Alapértelmezés szerint a készülék és a telepített ügynökök automatikusan frissülnek. A készülék 24 óránként ellenőrzi a frissítéseket. A sikertelen frissítések újrapróbálkoznak. 

Ezek az automatikus frissítések csak a készülék és a berendezés ügynökeit frissítik. Azure Migrate automatikus frissítések nem frissítik az operációs rendszert. Az operációs rendszer naprakészen tartása Windows-frissítések használatával.

## <a name="can-i-check-agent-health"></a>Ellenőrizhető az ügynök állapota?

Igen. A portálon nyissa meg az Azure Migrate: Server Assessment vagy Azure Migrate: Server áttelepítési eszköz **ügynök állapota** lapját. Itt megtekintheti a kapcsolat állapotát az Azure és a készüléken található felderítési és értékelési ügynökök között.

## <a name="can-i-add-multiple-server-credentials-on-vmware-appliance"></a>Több kiszolgálói hitelesítő adatot is Hozzáadhatok a VMware készüléken?

Igen, a szoftveres leltár (telepített alkalmazások felderítése), az ügynök nélküli függőségek elemzése és a SQL Server példányok és adatbázisok felderítése érdekében már több kiszolgáló hitelesítő adatait is támogatjuk. [További információ](tutorial-discover-vmware.md#provide-server-credentials) a hitelesítő adatok megadásáról a készülék Configuration Managerben.

## <a name="what-type-of-server-credentials-can-i-add-on-the-vmware-appliance"></a>Milyen típusú kiszolgálói hitelesítő adatok adhatók hozzá a VMware készüléken?
Megadhat tartomány/Windows (nem tartományi)/Linux (nem tartományi)/SQL Server hitelesítési hitelesítő adatokat a készülék Configuration Managerben. [További](add-server-credentials.md) információ a hitelesítő adatok megadásáról és kezeléséről.

## <a name="what-type-of-sql-server-connection-properties-are-supported-by-azure-migrate-for-sql-discovery"></a>Milyen típusú SQL Server-kapcsolatok tulajdonságait támogatja az SQL-felderítési Azure Migrate?
A Azure Migrate titkosítja Azure Migrate berendezés és a forrás SQL Server példányok közötti kommunikációt (a kapcsolat titkosítása tulajdonság értéke TRUE). Ezek a kapcsolatok a [TrustServerCertificate](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) -mel vannak titkosítva (igaz értékre állítva); az átviteli réteg az SSL használatával titkosítja a csatornát, és megkerüli a tanúsítványláncot a megbízhatóság ellenőrzéséhez. A berendezés-kiszolgálót úgy kell beállítani, hogy [megbízzon a tanúsítvány legfelső szintű hitelesítésszolgáltatóján](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

Ha nincs tanúsítvány kiépítve a kiszolgálón az indításkor, SQL Server létrehoz egy önaláírt tanúsítványt, amelyet a rendszer a bejelentkezési csomagok titkosítására használ. [További információ](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).


## <a name="next-steps"></a>Következő lépések

Olvassa el a [Azure Migrate áttekintést](migrate-services-overview.md).
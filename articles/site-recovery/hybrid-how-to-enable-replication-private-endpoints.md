---
title: A privát végpontokkal rendelkező helyszíni gépek replikálásának engedélyezése
description: Ez a cikk bemutatja, hogyan konfigurálhatja a helyszíni gépek replikálását a Site Recovery privát végpontjai használatával.
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: 7f10654e1c96f1756e5864d20fa2a6817385e994
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98629793"
---
# <a name="replicate-on-premises-machines-by-using-private-endpoints"></a>Helyszíni gépek replikálása privát végpontok használatával

A Azure Site Recovery lehetővé teszi, hogy az [Azure Private link](../private-link/private-endpoint-overview.md) privát végpontok használatával replikálja a helyszíni gépeket az Azure-beli virtuális hálózatra. A helyreállítási tárolóhoz való magánhálózati végpontok minden Azure kereskedelmi & kormányzati régióban támogatottak.

Ez a cikk a következő lépések végrehajtását ismerteti:

- Hozzon létre egy Azure Backup Recovery Services-tárolót a gépek elleni védelemhez.
- Felügyelt identitás engedélyezése a tárolóhoz. Adja meg a Storage-fiókok eléréséhez szükséges engedélyeket, hogy lehetővé váljon a helyszíniről az Azure-ba irányuló forgalom replikálása. A tárolóhoz való titkos kapcsolat eléréséhez felügyelt identitás-hozzáférésre van szükség.

- Hajtsa végre a magánhálózati végpontokhoz szükséges DNS-módosításokat.
- Privát végpontokat hozhat létre és hagyhat jóvá egy virtuális hálózaton belüli tárolóhoz.
- Hozzon létre privát végpontokat a Storage-fiókok számára. Ha szükséges, továbbra is engedélyezheti a nyilvános vagy tűzfalon keresztüli hozzáférést a tárterülethez. A Azure Site Recoveryhoz nem szükséges privát végpontot létrehozni a tároló eléréséhez.
  
A következő ábra a hibrid vész-helyreállítás replikációs munkafolyamatát mutatja be privát végpontokkal. A helyszíni hálózatban nem hozhatók létre privát végpontok. A privát hivatkozások használatához létre kell hoznia egy Azure-beli virtuális hálózatot (ennek a cikknek a *megkerülését* ), létre kell hoznia egy magánhálózati kapcsolatot a helyszíni és a megkerülési hálózat között, majd létre kell hoznia egy privát végpontot a mellőzési hálózaton. Kiválaszthatja a privát kapcsolatok bármely formáját.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="A Azure Site Recovery és privát végpontok architektúráját bemutató ábra.":::

## <a name="prerequisites-and-caveats"></a>Előfeltételek és kikötések

- A magánhálózati hivatkozások Site Recovery 9,35-es és újabb verzióiban támogatottak.
- Saját végpontokat csak olyan új Recovery Services-tárolók számára hozhat létre, amelyekhez nincs regisztrálva elem. Ezért létre kell hoznia egy privát végpontot, mielőtt bármely elemet felvesz a tárba. A díjszabással kapcsolatos információkért tekintse meg az [Azure Private link díjszabását](https://azure.microsoft.com/pricing/details/private-link/) .
- Amikor privát végpontot hoz létre egy tárolóhoz, a tároló zárolva van. Csak privát végpontokkal rendelkező hálózatokból érhető el.
- Azure Active Directory jelenleg nem támogatja a privát végpontokat. Ezért engedélyezni kell a kimenő hozzáférést a biztonságos Azure-beli virtuális hálózatról az IP-címekre és a teljes tartománynevek számára, amelyek szükségesek ahhoz, hogy a Azure Active Directory működjön egy adott régióban.
  Igény szerint használhatja a "Azure Active Directory" nevű hálózati biztonsági csoport címkéjét is, és Azure Firewall címkéket, hogy engedélyezze a hozzáférést a Azure Active Directoryhoz.
- Öt IP-cím szükséges abban a mellőzési hálózaton, amelyben létre kívánja hozni a privát végpontot. Amikor létrehoz egy magánhálózati végpontot a tárolóhoz, Site Recovery öt privát hivatkozást hoz létre a szolgáltatásokhoz való hozzáféréshez.
- Egy további IP-címnek kell lennie a mellőzési hálózaton a magánhálózati végponti kapcsolathoz egy cache Storage-fiókhoz. A helyszíni és a Storage-fiók végpontja között bármilyen csatlakozási módszert használhat. Használhatja például az internetet vagy az Azure- [ExpressRoute](../expressroute/index.yml). Privát hivatkozás létrehozása nem kötelező. Saját végpontokat csak általános célú v2-fiókokon hozhat létre a tároláshoz. A általános célú v2-fiókok adatátviteli díjszabásával kapcsolatos információkért tekintse meg az [Azure Page Blobs díjszabását](https://azure.microsoft.com/pricing/details/storage/page-blobs/) .

 ## <a name="create-and-use-private-endpoints-for-site-recovery"></a>Privát végpontok létrehozása és használata a site recoveryhez

 A következő szakaszok ismertetik, hogy milyen lépéseket kell tennie a virtuális hálózatokban a site recoveryhez szükséges privát végpontok létrehozásához és használatához.

> [!NOTE]
> Javasoljuk, hogy kövesse az alábbi lépéseket az itt látható sorrendben. Ha nem, akkor előfordulhat, hogy nem fogja tudni használni a privát végpontokat a tárolóban, és előfordulhat, hogy újra kell indítania a folyamatot egy új tárolóval.

### <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Services tároló tartalmazza a gépek replikációs információit. Site Recovery műveletek elindítására szolgál. További információ arról, hogyan hozhat létre egy Recovery Services-tárolót az Azure-régióban, ahol katasztrófa esetén szeretné felvenni a feladatátvételt: Recovery Services-tároló [létrehozása](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault).

### <a name="enable-the-managed-identity-for-the-vault"></a>Felügyelt identitás engedélyezése a tárolóhoz

A [felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md) lehetővé teszi a tároló számára a Storage-fiókok elérését. A követelményektől függően előfordulhat, hogy a Site Recoverynak hozzá kell férnie a cél tárolási és a gyorsítótár-és a naplófájl-fiókhoz. A felügyelt identitás elérésére akkor van szükség, ha a Private link Service-t használja a tárolóhoz.

1. Nyissa meg a Recovery Services-tárolót. Válassza ki az **identitás** elemet a **Beállítások** területen:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Képernyőkép, amely az Identity Settings oldalt jeleníti meg.":::

1. Módosítsa az **állapotot** **be értékre, majd** válassza a **Mentés** lehetőséget.

   A rendszer létrehoz egy objektumazonosító-azonosítót. A tár már regisztrálva van Azure Active Directoryban.

### <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Privát végpontok létrehozása a Recovery Services-tárolóhoz

A helyszíni forrásoldali hálózat számítógépeinek ellátásához egy privát végpontra van szükség a megkerülő hálózatban található tárolóhoz. Hozza létre a privát végpontot a Azure Portal privát kapcsolati központjának használatával, vagy [Azure PowerShell](../private-link/create-private-endpoint-powershell.md)használatával.

1. A Azure Portal keresési mezőben keressen rá a "privát hivatkozás" kifejezésre. Válassza ki a privát **hivatkozás** elemet a privát hivatkozás központjához való ugráshoz:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Képernyőkép, amely a privát kapcsolati központ Azure Portal keresésére mutat.":::

1. A bal oldali ablaktáblán válassza a **privát végpontok** lehetőséget. A **privát végpontok** lapon válassza a **Hozzáadás** lehetőséget a saját tárolóhoz tartozó privát végpont létrehozásának megkezdéséhez:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Képernyőkép, amely bemutatja, hogyan hozhat létre privát végpontot a privát kapcsolati központban.":::

1. A **privát végpont létrehozása** lapon adja meg a saját végponti kapcsolat létrehozásához szükséges adatokat.

   1. **Alapvető beállítások**. Adja meg a privát végpontok alapszintű részleteit. Használja a mellőzési hálózathoz használt régiót:

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Képernyőkép, amely az alapszintű lapot mutatja a privát végpontok létrehozásához.":::

   1. **Erőforrás**. Ezen a lapon meg kell adnia azt a platform-szolgáltatás típusú erőforrást, amelyhez létre kívánja hozni a kapcsolódást. A kiválasztott előfizetéshez tartozó **erőforrástípus** területen válassza a **Microsoft. recoveryservices szolgáltatónál/Vaults** lehetőséget. Válassza ki az Recovery Services-tároló nevét az **erőforrás** területen. Válassza a **Azure site Recovery** lehetőséget a **célként szolgáló alerőforrásként**.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Képernyőkép, amely az erőforrás fület jeleníti meg egy privát végponthoz való hivatkozáshoz.":::

   1. **Konfiguráció**. Ezen a lapon adhatja meg a mellőzési hálózatot és alhálózatot, ahol létre szeretné hozni a privát végpontot. 

      Az **Igen gombra** kattintva engedélyezheti az integrációt egy privát DNS-zónával.
      Válasszon egy meglévő DNS-zónát, vagy hozzon létre egy újat. Az **Igen** lehetőség kiválasztásával automatikusan összekapcsolja a zónát a mellőzési hálózattal. Ez a művelet hozzáadja azokat a DNS-rekordokat is, amelyek szükségesek az új IP-címek és a magánhálózati végponthoz létrehozott teljes tartománynevek DNS-feloldásához.

      Győződjön meg arról, hogy új DNS-zónát hoz létre minden olyan új magánhálózati végponthoz, amely ugyanahhoz a tárolóhoz csatlakozik. Ha meglévő privát DNS-zónát választ, a rendszer felülírja az előző CNAME rekordokat. A folytatás előtt tekintse meg a [privát végponti útmutatót](../private-link/private-endpoint-overview.md#private-endpoint-properties) .

      Ha a környezet rendelkezik sugaras és küllős modellel, csak egy privát végpontra és egy magánhálózati DNS-zónára van szükség a teljes telepítéshez. Ennek az az oka, hogy az összes virtuális hálózata már rendelkezik egymás közötti társítással. További információ: [magánhálózati végpont DNS-integrációja](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server).

      A magánhálózati DNS-zóna manuális létrehozásához kövesse a [saját DNS-zónák létrehozása és a DNS-rekordok manuális hozzáadása](#create-private-dns-zones-and-add-dns-records-manually)című témakör lépéseit.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Képernyőkép, amely a privát végpont konfigurációjának konfiguráció lapját jeleníti meg.":::

   1. **Címkék**. Igény szerint hozzáadhat címkéket a privát végponthoz.

   1. **A \+ Létrehozás áttekintése**. Az érvényesítés befejeződése után a **Létrehozás** gombra kattintva hozza létre a privát végpontot.

A privát végpont létrehozásakor a rendszer öt teljes tartománynevet (FQDN) ad hozzá a privát végponthoz. Ezek a hivatkozások lehetővé teszik, hogy a helyszíni hálózatban lévő gépek hozzáférjenek a megkerülési hálózaton keresztül, az összes szükséges Site Recovery-szolgáltatást a tároló környezetében. Ugyanazt a privát végpontot használhatja bármely Azure-gép védelméhez a megkerülési hálózatban és az összes társ hálózatban.

Az öt tartománynév a következő mintában van formázva:

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

### <a name="approve-private-endpoints-for-site-recovery"></a>Privát végpontok jóváhagyása a site recoveryhez

Ha létrehozza a privát végpontot, és Ön is a Recovery Services-tároló tulajdonosa, a korábban létrehozott privát végpontot néhány percen belül automatikusan jóváhagyjuk. Ellenkező esetben a tároló tulajdonosának jóvá kell hagynia a privát végpontot, mielőtt használni lehetne. A kért privát végponti kapcsolat jóváhagyásához vagy elutasításához nyissa meg a helyreállítási tároló lap **Beállítások** területén található **privát végponti kapcsolatok** csomópontot.

A folytatás előtt tekintse át a magánhálózati végpont erőforrását a kapcsolódás állapotának áttekintéséhez:

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="A tár privát Endpoint Connections lapját és a kapcsolatok listáját megjelenítő képernyőkép.":::

### <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>Választható Magánhálózati végpontok létrehozása a gyorsítótárbeli Storage-fiókhoz

Egy privát végpontot használhat az Azure Storage-hoz. A privát végpontok tárolási hozzáféréshez való létrehozása nem kötelező Azure Site Recovery replikáláshoz. Ha privát végpontot hoz létre a tárhelyhez, szüksége van egy privát végpontra a gyorsítótár-és a naplófájl-tároló fiókhoz a kikerülő virtuális hálózatban.

> [!NOTE]
> Ha a magánhálózati végpontok nincsenek engedélyezve a Storage-fiókon, a védelem még sikeres lesz. A replikálási forgalom azonban az interneten keresztül az Azure Site Recovery nyilvános végpontokra is áthaladhat. Annak érdekében, hogy a replikációs forgalom magánhálózati kapcsolaton keresztül történjen, a Storage-fióknak engedélyeznie kell a magánhálózati végpontokat.

> [!NOTE]
> A tárolóhoz tartozó magánhálózati végpontok csak általános célú v2 Storage-fiókokon hozhatók létre. A díjszabással kapcsolatos információkért lásd: az [Azure Page Blobs díjszabása](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

A [privát tárolók létrehozásával kapcsolatos útmutatást](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint) követve hozzon létre egy privát végponttal rendelkező Storage-fiókot. Ügyeljen arra, hogy az **Igen** lehetőséget válassza a **saját DNS-zóna integrálása** területen. Válasszon ki egy meglévő DNS-zónát, vagy hozzon létre egy újat.

### <a name="grant-required-permissions-to-the-vault"></a>Szükséges engedélyek megadása a tárolóhoz

A telepítéstől függően szükség lehet egy vagy több Storage-fiókra a cél Azure-régióban. Ezután adja meg a felügyelt identitás engedélyeit a Site Recovery által igényelt összes gyorsítótár-vagy naplózási tárolási fiók számára. Ebben az esetben előre létre kell hoznia a szükséges Storage-fiókokat.

A virtuális gépek replikálásának engedélyezése előtt a tár felügyelt identitásának a következő szerepkör-engedélyekkel kell rendelkeznie a Storage-fiók típusától függően.

- Resource Manager-alapú Storage-fiókok (szabványos típus):
  - [Közreműködő](../role-based-access-control/built-in-roles.md#contributor)
  - [Storage-blobadatok közreműködője](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Resource Manager-alapú Storage-fiókok (prémium típus):
  - [Közreműködő](../role-based-access-control/built-in-roles.md#contributor)
  - [Storage-blobadatok tulajdonosa](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Klasszikus Storage-fiókok:
  - [Klasszikus Storage-fiók közreműködői](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [A klasszikus Storage-fiók kulcs-kezelő szolgáltatásának szerepköre](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

Ezek a lépések azt írják le, hogyan adhat hozzá szerepkör-hozzárendelést a Storage-fiókhoz:

1. Nyissa meg a tárfiókot. A bal oldali ablaktáblán válassza a **hozzáférés-vezérlés (iam)** lehetőséget.

1. A **szerepkör-hozzárendelés hozzáadása** szakaszban válassza a **Hozzáadás** lehetőséget:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="A Storage-fiók hozzáférés-vezérlés (IAM) lapját megjelenítő képernyőkép.":::

1. A **szerepkör-hozzárendelés hozzáadása** oldalon a **szerepkör** listában válassza ki a szerepkört a szakasz elején lévő listából. Adja meg a tároló nevét, majd kattintson a **Mentés** gombra.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="A szerepkör-hozzárendelés hozzáadása lapot megjelenítő képernyőkép.":::

Az engedélyek hozzáadása után engedélyeznie kell a Microsoft megbízható szolgáltatásainak elérését. Válassza a **tűzfalak és virtuális hálózatok** lehetőséget, és jelölje be a **megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése a Storage-fiókhoz** **kivételeket**.

### <a name="protect-your-virtual-machines"></a>A virtuális gépek megóvása

Az előző feladatok befejezése után folytassa a helyszíni infrastruktúra beállításával. Folytassa a következő feladatok egyikének végrehajtásával: 

- [Konfigurációs kiszolgáló üzembe helyezése VMware-és fizikai gépeken](./vmware-azure-deploy-configuration-server.md)
- [A Hyper-V környezet beállítása replikáláshoz](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

A telepítés befejezése után engedélyezze a replikálást a forrásoldali gépeken. Ne állítsa be az infrastruktúrát addig, amíg a tárolóhoz tartozó magánhálózati végpontok nem jönnek létre a mellőzési hálózaton.

### <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Saját DNS-zónák létrehozása és a DNS-rekordok manuális hozzáadása

Ha nem választotta ki a saját DNS-zónával való integráció lehetőségét a privát végpont létrehozásakor a tárolóhoz, kövesse az ebben a szakaszban található lépéseket.

Hozzon létre egy privát DNS-zónát, amely engedélyezi a Site Recovery szolgáltatót (Hyper-V-gépek esetén) vagy a Process Servert (VMware/fizikai gépek esetén) a magánhálózati teljes tartománynevek magánhálózati IP-címekre való feloldásához.

1. Hozzon létre egy privát DNS-zónát.

   1. Keresse meg a "privát DNS-zóna" kifejezést a **minden szolgáltatás** keresési mezőben, majd válassza ki **saját DNS zónát** az eredmények között:

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Az Azure Portal új erőforrások lapján található privát DNS-zóna keresését bemutató képernyőkép.":::

   1. A **saját DNS zónák** lapon kattintson a **Hozzáadás** gombra egy új zóna létrehozásához.

   1. A **saját DNS-zóna létrehozása** lapon adja meg a szükséges adatokat. Adja meg a **privatelink.siterecovery.windowsazure.com** nevet a saját DNS-zóna neveként. Bármelyik erőforráscsoportot és előfizetést is kiválaszthatja.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="A saját DNS zóna létrehozása lap alapok lapját bemutató képernyőkép.":::

   1. A DNS-zóna áttekintéséhez és létrehozásához folytassa a **\+ create (létrehozás** ) lappal.

1. Kapcsolja össze a magánhálózati DNS-zónát a virtuális hálózattal.

   Most létre kell hoznia a megkerülő módon létrehozott magánhálózati DNS-zónát.

   1. Nyissa meg az előző lépésben létrehozott privát DNS-zónát, majd a bal oldali ablaktáblán lépjen a **virtuális hálózati kapcsolatok** elemre. Válassza a **Hozzáadás** lehetőséget.

   1. Adja meg a szükséges adatokat. Az **előfizetés** és a **virtuális hálózat** listában válassza a Mellőzés hálózatnak megfelelő részleteket. A többi mezőben hagyja meg az alapértelmezett értékeket.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="A virtuális hálózati kapcsolat hozzáadása lapot megjelenítő képernyőkép.":::

1. Adja hozzá a DNS-rekordokat.

   Most, hogy létrehozta a szükséges magánhálózati DNS-zónát és a privát végpontot, DNS-rekordokat kell hozzáadnia a DNS-zónához.

   > [!NOTE]
   > Ha egyéni DNS-zónát használ, ügyeljen arra, hogy hasonló bejegyzéseket hajtson végre a következő lépésben leírtak szerint.

   Ebben a lépésben a privát végponton lévő összes FQDN-re vonatkozó bejegyzéseket kell létrehoznia a saját DNS-zónájába.

   1. Nyissa meg a saját DNS-zónáját, és nyissa meg a bal oldali ablaktábla **Áttekintés** szakaszát. Rekord hozzáadásának megkezdéséhez **válassza a** rekordhalmaz lehetőséget.

   1. A **rekordazonosító hozzáadása** lapon adjon hozzá egy bejegyzést az egyes teljes tartománynevek és magánhálózati IP- **címek bejegyzéstípusként** . A teljes tartománynevek és IP-címek listáját a **privát végpont** oldalon **tekintheti** meg az Áttekintés lapon. Ahogy az alábbi képernyőképen is látható, a magánhálózati végpont első teljes tartományneve hozzá lesz adva a saját DNS-zónában lévő rekordhoz.

      Ezek a teljes tartománynevek megfelelnek a következő mintának: `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="A rekordazonosító hozzáadása lapot megjelenítő képernyőkép.":::

## <a name="next-steps"></a>Következő lépések

Most, hogy engedélyezte a saját végpontok használatát a virtuális gépek replikálásához, tekintse meg a további és a kapcsolódó információkat a következő cikkekben:

- [Helyszíni konfigurációs kiszolgáló üzembe helyezése](./vmware-azure-deploy-configuration-server.md)
- [Vészhelyreállítás beállítása a helyszíni Hyper-V virtuális gépekhez az Azure-ba](./hyper-v-azure-tutorial.md)
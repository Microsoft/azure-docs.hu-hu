---
title: Privát végpontok replikálásának engedélyezése Azure Site Recovery
description: Ez a cikk azt ismerteti, hogyan konfigurálható a virtuális gépek replikálása az egyik Azure-régióból a másikba Site Recovery használatával.
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.custom: references_regions
ms.openlocfilehash: 86f18be73966cb07489630191420b846622e45b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98629827"
---
# <a name="replicate-machines-with-private-endpoints"></a>Számítógépek replikálása privát végpontokkal

Azure Site Recovery lehetővé teszi, hogy az [Azure Private link](../private-link/private-endpoint-overview.md) privát végpontait használja a gépek elkülönített virtuális hálózaton belüli replikálásához. A helyreállítási tárolóhoz való magánhálózati végpontok minden Azure kereskedelmi & kormányzati régióban támogatottak.

Ez a cikk útmutatást nyújt a következő lépések végrehajtásához:

- Hozzon létre egy Azure Backup Recovery Services-tárolót a gépek elleni védelemhez.
- Engedélyezzen egy felügyelt identitást a tárolóhoz, és adja meg a szükséges engedélyeket az ügyfél-tárolási fiókok eléréséhez a forrásról a célhelyre történő adatforgalom replikálásához. A tár felügyelt identitás-hozzáférése akkor szükséges, ha magánhálózati kapcsolati hozzáférést állít be a tárolóhoz.
- DNS-módosítások szükségesek a privát végpontokhoz
- Privát végpontok létrehozása és jóváhagyása virtuális hálózaton belüli tárolóhoz
- Hozzon létre privát végpontokat a Storage-fiókok számára. Ha szükséges, továbbra is engedélyezheti a nyilvános vagy tűzfalon keresztüli hozzáférést a tárterülethez. A privát végpontok a tárolóhoz való hozzáféréshez való létrehozása nem kötelező a Azure Site Recovery.
  
Az alábbi hivatkozás azt mutatja be, hogyan változik a replikációs munkafolyamat a privát végpontokkal.

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/architecture.png" alt-text="A privát végpontokkal rendelkező Site Recovery hivatkozási architektúrája.":::

## <a name="prerequisites-and-caveats"></a>Előfeltételek és kikötések

- A magánhálózati végpontok csak olyan új Recovery Services tárolók számára hozhatók létre, amelyek nem rendelkeznek a tárolóban regisztrált elemekkel. Ezért a privát végpontokat **létre kell hozni ahhoz, hogy a rendszer minden elemet felvesz a tárba**. Tekintse át a [privát végpontok](https://azure.microsoft.com/pricing/details/private-link/)díjszabási struktúráját.
- Ha privát végpontot hoz létre egy tárolóhoz, a tároló zárolva van, és **nem érhető el a privát végpontokkal rendelkező hálózatokon kívüli hálózatokból**.
- A Azure Active Directory jelenleg nem támogatja a privát végpontokat. Ennek megfelelően az IP-címeket és a Azure Active Directory működéséhez szükséges teljes tartományneveket engedélyezni kell a biztonságos hálózatról a kimenő hozzáféréshez. A (z) "Azure Active Directory" hálózati biztonsági csoport címkével is rendelkezhet, és Azure Firewall címkéket is használhat a Azure Active Directoryhoz való hozzáférés engedélyezéséhez.
- A forrás-és a helyreállítási gépek alhálózatában **legalább hét IP-címnek kell** lennie. Amikor létrehoz egy magánhálózati végpontot a tárolóhoz, Site Recovery öt privát hivatkozást hoz létre a szolgáltatásokhoz való hozzáféréshez. Ha engedélyezi a replikálást, két további privát hivatkozást is feltesz a forrás-és a célcsoport párosításához.
- A forrás-és a helyreállítási alhálózatokban **egy további IP-cím szükséges** . Ez az IP-cím csak akkor szükséges, ha privát végpontokat kell használnia a cache Storage-fiókokhoz való csatlakozáshoz.
  A tárolóhoz tartozó magánhálózati végpontok csak általános célú v2 típuson hozhatók létre. Tekintse át a [GPv2-on történő adatátvitel](https://azure.microsoft.com/pricing/details/storage/page-blobs/)díjszabási struktúráját.

 ## <a name="creating-and-using-private-endpoints-for-site-recovery"></a>Privát végpontok létrehozása és használata Site Recoveryhoz

 Ez a szakasz a virtuális hálózatokon belüli Azure Site Recovery magánhálózati végpontok létrehozásával és használatával kapcsolatos lépésekkel foglalkozik.

> [!NOTE]
> Erősen ajánlott az alábbi lépéseket a megadott sorrendjében követni. Ennek elmulasztása miatt előfordulhat, hogy a tár nem tud privát végpontokat használni, és egy új tárolóval újra kell indítania a folyamatot.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Services-tároló egy olyan entitás, amely a gépek replikációs információit tartalmazza, és Site Recovery műveletek elindítására szolgál. További információ: [create a Recovery Services Vault](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault).

## <a name="enable-the-managed-identity-for-the-vault"></a>Engedélyezze a felügyelt identitást a tárolóhoz.

A [felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md) lehetővé teszi a tároló számára, hogy hozzáférjen az ügyfél Storage-fiókjaihoz. Site Recovery a forgatókönyvre vonatkozó követelménytől függően el kell érnie a forrás-és a tároló-, valamint a gyorsítótár-és a naplózási tárolási fiókokat.
A felügyelt identitáshoz való hozzáférés elengedhetetlen, ha a Private Links szolgáltatást használja a tárolóhoz.

1. Nyissa meg a Recovery Services-tárolót. Válassza az **identitás** elemet a _Beállítások_ területen.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Megjeleníti a Azure Portal és a Recovery Services lapot.":::

1. Módosítsa az **állapotot** _be értékre, majd_ válassza a **Mentés** lehetőséget.

1. Egy **objektumazonosító** jön létre, amely azt jelzi, hogy a tár már regisztrálva van Azure Active Directoryban.

## <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Privát végpontok létrehozása a Recovery Services-tárolóhoz

Az Azure-beli virtuális gépek feladatátvételének és feladat-visszavételének engedélyezéséhez két privát végpontra lesz szüksége a tárolóhoz. Egy privát végpont a forrásoldali hálózat számítógépeinek védelméhez, egy másik pedig a feladatátvételi hálózatban lévő feladatátviteli gépek ismételt védelméhez.

A telepítési folyamat során győződjön meg arról, hogy a cél régióban is létrehoz egy helyreállítási virtuális hálózatot.

Hozza létre a tároló első privát végpontját a forrásként szolgáló virtuális hálózaton a portálon található privát kapcsolati központ vagy a [Azure PowerShell](../private-link/create-private-endpoint-powershell.md)használatával. Hozza létre a tároló második privát végpontját a helyreállítási hálózaton belül. A következő lépésekkel hozhatja létre a magánhálózati végpontot a forrásoldali hálózaton. Ismételje meg ugyanezt az útmutatót a második privát végpont létrehozásához.

1. A Azure Portal keresési sávban keresse meg és válassza ki a "privát hivatkozás" lehetőséget. Ez a művelet a privát kapcsolati központba lép.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Megjeleníti a privát kapcsolati központ Azure Portal keresését.":::

1. A bal oldali navigációs sávon válassza a **privát végpontok** lehetőséget. A privát végpontok lapon válassza a **\+ Hozzáadás** lehetőséget a saját tárolóhoz tartozó privát végpont létrehozásához.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Egy privát végpont létrehozását mutatja be a privát kapcsolati központban.":::

1. Ha a "privát végpont létrehozása" felülettel rendelkezik, meg kell adnia a privát végponti kapcsolat létrehozásához szükséges adatokat.

   1. **Alapismeretek**: adja meg a privát végpontok alapszintű részleteit. A régiónak meg kell egyeznie a forrásoldali gépekkel.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Megjeleníti az alapszintű lapot, a projekt részleteit, az előfizetést és az egyéb kapcsolódó mezőket, amelyekkel létrehozhat egy privát végpontot a Azure Portal.":::

   1. **Erőforrás**: ezen a lapon megemlítheti azt a platform-szolgáltatás típusú erőforrást, amelyhez létre kívánja hozni a kapcsolatát. Válassza ki a _Microsoft. recoveryservices szolgáltatónál/Vaults_ elemet a kiválasztott előfizetéshez tartozó **erőforrástípus** alapján. Ezután válassza ki a Recovery Services-tároló nevét az **erőforráshoz** , és állítsa be a _Azure site Recoveryt_ **célként megadott alerőforrásként**.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Megjeleníti az Erőforrás lapot, az erőforrás típusát, az erőforrást és a cél alerőforrás mezőket a Azure Portal privát végpontjának csatolásához.":::

   1. **Konfiguráció**: a konfiguráció területen válassza ki azt a virtuális hálózatot és alhálózatot, ahol létre szeretné hozni a privát végpontot. Ez a virtuális hálózat azt a hálózatot mutatja, ahol a virtuális gép található. Az **Igen gombra** kattintva engedélyezheti a magánhálózati DNS-zónával való integrációt. Válasszon egy már létrehozott DNS-zónát, vagy hozzon létre egy újat. Az **Igen** lehetőség kiválasztásával automatikusan összekapcsolja a zónát a forrás virtuális hálózattal, és hozzáadja azokat a DNS-rekordokat, amelyek szükségesek az új IP-címek és a magánhálózati végponthoz létrehozott teljes tartománynevek DNS-feloldásához.

      Győződjön meg arról, hogy új DNS-zónát hoz létre minden olyan új magánhálózati végponthoz, amely ugyanahhoz a tárolóhoz csatlakozik. Ha meglévő privát DNS-zónát választ, a rendszer felülírja az előző CNAME rekordokat. A folytatás előtt tekintse meg a [privát végponti útmutatót](../private-link/private-endpoint-overview.md#private-endpoint-properties) .

      Ha a környezet rendelkezik sugaras és küllős modellel, csak egy privát végpontra és csak egy magánhálózati DNS-zónára van szükség a teljes beállításhoz, mivel az összes virtuális hálózata már engedélyezve van egymás között. További információ: [magánhálózati végpont DNS-integrációja](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server).

      A magánhálózati DNS-zóna manuális létrehozásához kövesse a [saját DNS-zónák létrehozása és a DNS-rekordok manuális hozzáadása](#create-private-dns-zones-and-add-dns-records-manually)című témakör lépéseit.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="A hálózati és DNS-integrációs mezőket tartalmazó Configuration (konfiguráció) lapot jeleníti meg a Azure Portal privát végpontjának konfigurálásához.":::

   1. **Címkék**: opcionálisan hozzáadhat címkéket a privát végponthoz.

   1. **A \+ Létrehozás áttekintése**: az ellenőrzés befejeződése után a **Létrehozás** gombra kattintva hozza létre a privát végpontot.

A magánhálózati végpont létrehozása után öt teljesen minősített tartománynevet ad hozzá a privát végponthoz. Ezek a hivatkozások lehetővé teszik a virtuális hálózatban lévő gépek számára, hogy hozzáférjenek a szükséges Site Recovery-szolgáltatásokhoz a tár környezetében. Később, amikor engedélyezi a replikálást, két további teljes tartománynevet ad hozzá ugyanahhoz a privát végponthoz.

Az öt tartománynevek a következő mintával vannak formázva:

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

## <a name="approve-private-endpoints-for-site-recovery"></a>Privát végpontok jóváhagyása Site Recovery

Ha a privát végpontot létrehozó felhasználó egyben a Recovery Services-tároló tulajdonosa is, a fent létrehozott privát végpontot néhány percen belül automatikusan jóváhagyjuk. Ellenkező esetben a tároló tulajdonosának jóvá kell hagynia a privát végpontot a használata előtt. A kért privát végponti kapcsolat jóváhagyásához vagy elutasításához a helyreállítási tár lapon a "beállítások" alatt válassza a **privát végponti kapcsolatok** lehetőséget.

A folytatás előtt nyissa meg a magánhálózati végpont erőforrását a kapcsolódás állapotának áttekintéséhez.

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Megjeleníti a tároló privát Endpoint Connections lapját és a Azure Portal kapcsolatainak listáját.":::

## <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>Választható Magánhálózati végpontok létrehozása a gyorsítótárbeli Storage-fiókhoz

Az Azure Storage-hoz egy privát végpontot lehet használni. A privát végpontok tárolási hozzáféréshez való létrehozása nem _kötelező_ Azure site Recovery replikáláshoz. Privát végpont tároláshoz való létrehozásakor a következő követelmények érvényesek:

- Szüksége van egy privát végpontra a forrás virtuális hálózatában található cache/log Storage-fiókhoz.
- A helyreállítási hálózatban a feladatátvétel alatt álló gépek ismételt védelmének időpontjában második privát végpontra van szükség. Ez a magánhálózati végpont a célként megadott régióban létrehozott új Storage-fiókhoz van hozzárendelve.

> [!NOTE]
> Ha a magánhálózati végpontok nincsenek engedélyezve a Storage-fiókon, a védelem még sikeres lesz. A replikálási forgalom azonban Azure Site Recovery nyilvános végpontokra lesz átirányítva. Annak érdekében, hogy a replikációs forgalom magánhálózati kapcsolaton keresztül történjen, a Storage-fióknak engedélyeznie kell a magánhálózati végpontokat.

> [!NOTE]
> A tárolóhoz tartozó magánhálózati végpont csak **általános célú v2** Storage-fiókokon hozható létre. A díjszabással kapcsolatos információkért lásd: a [standard oldal blob-árai](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Kövesse a [Private Storage létrehozásával kapcsolatos útmutatót](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint) a privát végponttal rendelkező Storage-fiók létrehozásához. Győződjön meg arról, hogy az **Igen** lehetőséget választja a magánhálózati DNS-zónával való integrációhoz. Válasszon egy már létrehozott DNS-zónát, vagy hozzon létre egy újat.

## <a name="grant-required-permissions-to-the-vault"></a>Szükséges engedélyek megadása a tárolóhoz

Ha a virtuális gépek felügyelt lemezeket használnak, csak a cache Storage-fiókok számára kell megadnia a felügyelt identitási engedélyeket. Ha a virtuális gépek nem felügyelt lemezeket használnak, meg kell adnia a felügyelt identitás engedélyeit a forrás, a gyorsítótár és a cél Storage-fiókok számára. Ebben az esetben előre létre kell hoznia a cél Storage-fiókot.

A virtuális gépek replikálásának engedélyezése előtt a tár felügyelt identitásának a következő szerepkör-engedélyekkel kell rendelkeznie a Storage-fiók típusától függően:

- Resource Manager-alapú Storage-fiókok (szabványos típus):
  - [Közreműködő](../role-based-access-control/built-in-roles.md#contributor)
  - [Storage-blobadatok közreműködője](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Resource Manager-alapú Storage-fiókok (prémium típus):
  - [Közreműködő](../role-based-access-control/built-in-roles.md#contributor)
  - [Storage-blobadatok tulajdonosa](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Klasszikus Storage-fiókok:
  - [Klasszikus Storage-fiók közreműködői](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [A klasszikus Storage-fiók kulcs-kezelő szolgáltatásának szerepköre](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

A következő lépések azt ismertetik, hogyan lehet szerepkör-hozzárendelést hozzáadni a Storage-fiókokhoz, egyszerre:

1. Nyissa meg a Storage-fiókot, és navigáljon az oldal bal oldalán található **hozzáférés-vezérlés (iam)** elemre.

1. A **hozzáférés-vezérlés (iam)** után a "szerepkör-hozzárendelés hozzáadása" mezőben válassza a **Hozzáadás** lehetőséget.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Megjeleníti a Storage-fiók hozzáférés-vezérlés (IAM) lapját, valamint a Azure Portal szerepkör-hozzárendelés hozzáadása gombját.":::

1. A "szerepkör-hozzárendelés hozzáadása" oldalon válassza ki a szerepkört a fenti listából a **szerepkör** legördülő menüből. Adja meg a tároló **nevét** , majd válassza a **Mentés** lehetőséget.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Megjeleníti a Storage-fiók hozzáférés-vezérlés (IAM) lapját, valamint a szerepkör kiválasztásának lehetőségeit, valamint azt, hogy az adott szerepkört a Azure Portalban adja meg.":::

Ezen engedélyek mellett az MS megbízható szolgáltatásoknak is hozzáférést kell biztosítani. A **kivételek** között válassza a "tűzfalak és virtuális hálózatok" lehetőséget, és jelölje be a "megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése a Storage-fiókhoz" jelölőnégyzetet.

## <a name="protect-your-virtual-machines"></a>A virtuális gépek megóvása

Az összes fenti konfiguráció befejezése után folytassa a replikáció engedélyezésével a virtuális gépeken. Az összes Site Recovery művelet további lépések nélkül működik, ha a DNS-integrációt használták a privát végpontok létrehozásakor a tárolón. Ha azonban a DNS-zónák létrehozása és konfigurálása manuálisan történik meg, további lépésekre van szükség a DNS-rekordok hozzáadásához a forrás és a cél DNS-zónákban a replikáció engedélyezése után. A részleteket és a lépéseket lásd: [privát DNS-zónák létrehozása és DNS-rekordok manuális hozzáadása](#create-private-dns-zones-and-add-dns-records-manually).

## <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Saját DNS-zónák létrehozása és a DNS-rekordok manuális hozzáadása

Ha nem választotta ki a saját DNS-zónával való integrációt a privát végpont létrehozásakor a tárolóhoz, kövesse az ebben a szakaszban ismertetett lépéseket.

Hozzon létre egy privát DNS-zónát, amely lehetővé teszi, hogy a mobilitási ügynök saját IP-címekre oldja fel a privát kapcsolatok teljes tartománynevét.

1. Privát DNS-zóna létrehozása

   1. Keresse meg a "saját DNS zóna" kifejezést a **minden szolgáltatás** keresési sávján, és válassza a legördülő menüből a "saját DNS zónák" lehetőséget.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Megjeleníti a &quot;privát DNS-zóna&quot; keresést a Azure Portal új erőforrások lapján.":::

   1. A "saját DNS zónák" lapon kattintson a **\+ Hozzáadás** gombra az új zóna létrehozásának megkezdéséhez.

   1. A "privát DNS-zóna létrehozása" lapon adja meg a szükséges adatokat. Adja meg a saját DNS-zóna nevét a következőképpen: `privatelink.siterecovery.windowsazure.com` . A létrehozáshoz bármelyik erőforráscsoportot és előfizetést kiválaszthatja.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Megjeleníti a saját DNS zóna létrehozása lap és a kapcsolódó projekt részleteinek alapjai lapot a Azure Portalban.":::

   1. A DNS-zóna áttekintéséhez és létrehozásához folytassa a **\+ create (létrehozás** ) lappal.

1. Privát DNS-zóna csatolása a virtuális hálózathoz

   A fent létrehozott magánhálózati DNS-zónákat most ahhoz a virtuális hálózathoz kell kapcsolni, ahol a kiszolgálók jelenleg vannak. A magánhálózati DNS-zónát előre kell összekapcsolni a célként megadott virtuális hálózattal is.

   1. Nyissa meg az előző lépésben létrehozott privát DNS-zónát, és navigáljon a lap bal oldalán található **virtuális hálózati kapcsolatokhoz** . Kattintson a **\+ Hozzáadás** gombra.

   1. Adja meg a szükséges adatokat. Az **előfizetés** és a **virtuális hálózat** mezőket meg kell adni annak a virtuális hálózatnak a megfelelő részleteivel, amelyen a kiszolgálók léteznek. A többi mezőnek a következőképpen kell maradnia:.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Megjeleníti a lapot, amely a Azure Portalban található hivatkozás nevével, előfizetéssel és kapcsolódó virtuális hálózattal rendelkező virtuális hálózati kapcsolatot adja hozzá.":::

1. DNS-rekordok hozzáadása

   Miután létrehozta a szükséges magánhálózati DNS-zónákat és a privát végpontokat, DNS-rekordokat kell hozzáadnia a DNS-zónákhoz.

   > [!NOTE]
   > Ha egyéni DNS-zónát használ, győződjön meg arról, hogy az alább leírtak szerint hasonló bejegyzéseket tesznek elérhetővé.

   Ez a lépés megköveteli, hogy a privát végponton lévő összes teljes tartománynévhez bejegyzéseket hozzon a saját DNS-zónájába.

   1. Nyissa meg a saját DNS-zónáját, és lépjen az oldal bal oldalán található **Áttekintés** szakaszra. A rekordok hozzáadásának megkezdéséhez válassza a **\+ Record set** (rekordhalmaz) lehetőséget.

   1. A megnyíló "rekord hozzáadása" lapon adjon _hozzá egy bejegyzést_ minden teljesen minősített tartománynévhez és magánhálózati IP-címhez. A teljes tartománynevek és IP-címek listája a "privát végpont" lapról szerezhető be az **áttekintésben**. Ahogy az alábbi példában is látható, a magánhálózati végpont első teljes tartományneve hozzá lesz adva a saját DNS-zónában lévő rekordhoz.

      Ezek a teljes tartománynevek megfelelnek a következő mintának: `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Megjeleníti a lapot, amely a teljes tartománynévhez hozzáadja a DNS-típusú rekordot a Azure Portal privát végpontjának.":::

   > [!NOTE]
   > A replikáció engedélyezése után két további teljes tartománynevet hoz létre a rendszer mindkét régióban a privát végpontokon. Győződjön meg arról, hogy az újonnan létrehozott, teljesen minősített tartománynevek számára is hozzáadja a DNS-rekordokat.

## <a name="next-steps"></a>Következő lépések

Most, hogy engedélyezte a saját végpontok használatát a virtuális gépek replikálásához, tekintse meg a további és kapcsolódó információkat a következő lapokon:

- [Azure-beli virtuális gépek replikálása másik Azure-régióba](./azure-to-azure-how-to-enable-replication.md)
- [Oktatóanyag: vész-helyreállítás beállítása Azure-beli virtuális gépekhez](./azure-to-azure-tutorial-enable-replication.md)
---
title: A Batch biztonsági és megfelelőségi ajánlott eljárásai
description: Ismerje meg az ajánlott eljárásokat és hasznos tippeket a Azure Batch-megoldásokkal való biztonság fokozásához.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: 6ec4a1d89ebaa9318986fc0d51e832652ba51683
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98723812"
---
# <a name="batch-security-and-compliance-best-practices"></a>A Batch biztonsági és megfelelőségi ajánlott eljárásai

Ez a cikk útmutatást és ajánlott eljárásokat biztosít a biztonság fokozásához Azure Batch használatakor.

Alapértelmezés szerint a Azure Batch fiókok nyilvános végponttal rendelkeznek, és nyilvánosan elérhetők. Azure Batch-készlet létrehozásakor a készletet egy Azure-beli virtuális hálózat egy adott alhálózatán kell kiépíteni. A Batch-készletben lévő virtuális gépek a Batch által létrehozott nyilvános IP-címeken keresztül érhetők el. A készletben lévő számítási csomópontok szükség esetén kommunikálhatnak egymással, például több példányos feladatok futtatásához, de a készlet csomópontjai nem tudnak kommunikálni a készleten kívüli virtuális gépekkel.

:::image type="content" source="media/security-best-practices/typical-environment.png" alt-text="Egy tipikus batch-környezetet ábrázoló diagram.":::

Számos szolgáltatás érhető el, amelyekkel biztonságosabb Azure Batch-telepítést hozhat létre. Korlátozhatja a csomópontokhoz való hozzáférést, és csökkentheti a csomópontok internetről való elérhetőségét azáltal, hogy [nyilvános IP-címek nélkül kiépíti a készletet](batch-pool-no-public-ip-address.md). A számítási csomópontok biztonságosan kommunikálhatnak más virtuális gépekkel vagy helyszíni hálózattal úgy, hogy [a készletet egy Azure-beli virtuális hálózat alhálózatán](batch-virtual-network.md)helyezik üzembe. Továbbá engedélyezheti a [virtuális hálózatok privát elérését](private-connectivity.md) egy Azure Private-hivatkozással működő szolgáltatásból.

:::image type="content" source="media/security-best-practices/secure-environment.png" alt-text="Az ábrán egy biztonságosabb batch-környezet látható.":::

## <a name="general-security-related-best-practices"></a>Általános biztonsággal kapcsolatos ajánlott eljárások

### <a name="pool-configuration"></a>Készlet konfigurációja

Számos biztonsági funkció csak a [virtuális gép konfigurációjának](nodes-and-pools.md#configurations)használatával konfigurált készletek esetében érhető el, és nem Cloud Services-konfigurációval rendelkező készletekhez. A virtuálisgép- [méretezési csoportokat](../virtual-machine-scale-sets/overview.md)használó virtuális gépek konfigurációs készleteit ajánlott használni, amikor csak lehetséges.

### <a name="batch-account-authentication"></a>Batch-fiók hitelesítése

A Batch-fiókhoz való hozzáférés két hitelesítési módszert támogat: a megosztott kulcsot és a [Azure Active Directoryt (Azure ad)](batch-aad-auth.md).

Javasoljuk, hogy a Batch-fiók hitelesítéséhez használja az Azure AD-t. Bizonyos batch-funkciók ezt a hitelesítési módszert igénylik, beleértve az itt tárgyalt biztonsággal kapcsolatos funkciókat is.

### <a name="batch-account-pool-allocation-mode"></a>Batch-fiók készletének lefoglalási módja

Batch-fiók létrehozásakor választhat két [készlet lefoglalási módja](accounts.md#batch-accounts)közül:

- **Batch szolgáltatás**: az alapértelmezett beállítás, ahol a mögöttes felhőalapú szolgáltatás vagy virtuálisgép-méretezési csoport a készlet-csomópontok foglalásához és kezeléséhez használt erőforrásai belső előfizetésekben jönnek létre, és nem közvetlenül láthatók a Azure Portal. Csak a Batch-készletek és-csomópontok láthatók. 
- **Felhasználói előfizetés**: a mögöttes felhőalapú szolgáltatás vagy virtuálisgép-méretezési csoport erőforrásai a Batch-fiókkal megegyező előfizetésben jönnek létre. Ezek az erőforrások ezért láthatók az előfizetésben, a megfelelő batch-erőforrások mellett.

A felhasználói előfizetési móddal a Batch virtuális gépek és egyéb erőforrások közvetlenül az előfizetésben jönnek létre a készlet létrehozásakor. A felhasználói előfizetési módot kötelező megadni, ha Azure Reserved VM Instances használatával szeretne batch-készleteket létrehozni, használja a Azure Policyt a virtuálisgép-méretezési csoport erőforrásaihoz, és/vagy kezelje az előfizetéshez tartozó fő kvótát (az előfizetés összes batch-fiókja között). Ha felhasználói előfizetési módban szeretne létrehozni Batch-fiókot, az előfizetését az Azure Batchben is regisztrálnia kell, és egy Azure Key Vaulttal is társítania kell.

## <a name="restrict-network-endpoint-access"></a>Hálózati végpontok hozzáférésének korlátozása

### <a name="batch-network-endpoints"></a>Batch hálózati végpontok

Vegye figyelembe, hogy alapértelmezés szerint a nyilvános IP-címekkel rendelkező végpontok a Batch-fiókokkal, a Batch-készletekkel és a készlet-csomópontokkal folytatott kommunikációra szolgálnak.

### <a name="batch-account-api"></a>Batch-fiók API

 Egy batch-fiók létrehozásakor létrejön egy nyilvános végpont, amely a fiók legtöbb műveletének meghívására szolgál a [REST API](/rest/api/batchservice/)használatával. A fiók végpontjának alap URL-címe van a formátum használatával `https://{account-name}.{region-id}.batch.azure.com` . A Batch-fiókhoz való hozzáférés biztonságos, a fiók végpontjának HTTPS-kapcsolaton keresztüli kommunikációval, valamint a megosztott kulccsal vagy Azure Active Directory (Azure AD) hitelesítéssel hitelesített minden kéréssel.

### <a name="azure-resource-manager"></a>Azure Resource Manager

A Batch-fiókokra vonatkozó műveletek mellett a [felügyeleti műveletek](/rest/api/batchmanagement/) egyetlen és több batch-fiókra is érvényesek. Ezek a felügyeleti műveletek Azure Resource Manageron keresztül érhetők el.

A Azure Resource Manageron keresztüli kötegelt felügyeleti műveletek HTTPS használatával vannak titkosítva, és minden kérést az Azure AD-hitelesítés használatával hitelesít.

### <a name="batch-pool-nodes"></a>Batch-készlet csomópontjai

A Batch szolgáltatás egy batch-csomóponti ügynökkel kommunikál, amely a készlet minden csomópontján fut. A szolgáltatás például arra utasítja a csomópont-ügynököt, hogy futtasson egy feladatot, leállítson egy feladatot, vagy beolvassa a feladathoz tartozó fájlokat. A csomópont-ügynökkel folytatott kommunikációt egy vagy több terheléselosztó teszi lehetővé, amely a készletben lévő csomópontok számától függ. A terheléselosztó továbbítja a kommunikációt a kívánt csomópontra, az egyes csomópontok pedig egyedi portszámmal kezelhetők. Alapértelmezés szerint a terheléselosztó a hozzájuk társított nyilvános IP-címekkel rendelkezik. Az RDP vagy az SSH használatával távolról is elérheti a készlet-csomópontokat (ez a hozzáférés alapértelmezés szerint engedélyezve van a terheléselosztó használatával történő kommunikáció esetén).

### <a name="restricting-access-to-batch-endpoints"></a>A Batch-végpontokhoz való hozzáférés korlátozása 

Számos lehetőség áll rendelkezésre a különböző batch-végpontokhoz való hozzáférés korlátozására, különösen akkor, ha a megoldás virtuális hálózatot használ. 

#### <a name="use-private-endpoints"></a>Privát végpontok használata

Az [Azure Private link](../private-link/private-link-overview.md) lehetővé teszi, hogy hozzáférjen az Azure Pásti-szolgáltatásokhoz és az Azure által üzemeltetett ügyfél-vagy partneri szolgáltatásokhoz a virtuális hálózat privát végpontján keresztül. A privát hivatkozás használatával korlátozhatja a Batch-fiókokhoz való hozzáférést a virtuális hálózatról vagy bármely társ virtuális hálózatról. A privát kapcsolatra leképezett erőforrások a VPN-en vagy az Azure-ExpressRoute keresztül is elérhetők a helyszínen keresztül.

A privát végpontok használatához a Batch-fiókot a létrehozáskor megfelelően kell konfigurálni. a nyilvános hálózati hozzáférés konfigurációját le kell tiltani. A létrehozást követően a saját végpontok létrehozhatók és társíthatók a Batch-fiókkal. További információ: [privát végpontok használata Azure batch fiókokkal](private-connectivity.md).

#### <a name="create-pools-in-virtual-networks"></a>Készletek létrehozása virtuális hálózatokban

A Batch-készletben lévő számítási csomópontok kommunikálhatnak egymással, például a többpéldányos feladatok futtatásához anélkül, hogy virtuális hálózatot (VNET) kellene igénybe venniük. Alapértelmezés szerint azonban a készlet csomópontjai nem tudnak kommunikálni a virtuális hálózat készletén kívüli virtuális gépekkel, és magánhálózati IP-címmel (például licenckiszolgálóra vagy fájlkiszolgálók) rendelkeznek.

Ha lehetővé szeretné tenni a számítási csomópontok számára a biztonságos kommunikációt más virtuális gépekkel vagy helyszíni hálózattal, beállíthatja, hogy a készlet egy Azure-VNET alhálózatában legyen.

Ha a készletek nyilvános IP-végpontokkal rendelkeznek, az alhálózatnak engedélyeznie kell a Batch szolgáltatástól érkező bejövő kommunikációt, hogy képes legyen a feladatok ütemezhetni és egyéb műveleteket végezni a számítási csomópontokon, valamint a kimenő kommunikációt az Azure Storage szolgáltatással vagy más erőforrásokkal való kommunikációra a munkaterhelés alapján. A virtuális gép konfigurációjában lévő készletek esetén a Batch hálózati biztonsági csoportokat (NSG) helyez el a számítási csomópontokhoz csatolt hálózati adapter szintjén. Ezeknek a NSG a következő szabályokkal kell rendelkezniük:

- Bejövő TCP-forgalom a Batch szolgáltatás IP-címeitől
- Bejövő TCP-forgalom a táveléréshez
- A virtuális hálózat bármely portjáról kimenő forgalom (az alhálózati szintű NSG-szabályok alapján módosítható)
- Kimenő forgalom bármely porton az interneten (az alhálózati szintű NSG-szabályok alapján módosítható)

Nem kell megadnia a NSG a virtuális hálózat alhálózatának szintjén, mert a Batch konfigurálja a saját NSG. Ha van olyan NSG társítva az alhálózathoz, ahol a Batch számítási csomópontok vannak telepítve, vagy ha egyéni NSG-szabályokat szeretne alkalmazni az alapértelmezett beállítások felülbírálására, akkor a NSG-t legalább a bejövő és kimenő biztonsági szabályokkal kell konfigurálni ahhoz, hogy a Batch szolgáltatás kommunikáljon a készlet-csomópontok és a készlet csomópontja között az Azure Storage-hoz.

További információ: [Azure batch készlet létrehozása egy virtuális hálózaton](batch-virtual-network.md).

#### <a name="create-pools-with-static-public-ip-addresses"></a>Statikus nyilvános IP-címmel rendelkező készletek létrehozása

Alapértelmezés szerint a készletekhez társított nyilvános IP-címek dinamikusak; a készlet létrehozásakor jönnek létre, és az IP-címek hozzáadhatók vagy eltávolíthatók a készlet átméretezése során. Ha a készlet csomópontjain futó feladatnak külső szolgáltatásokhoz kell hozzáférnie, előfordulhat, hogy a szolgáltatásokhoz való hozzáférésre csak bizonyos IP-címekre van szükség.  Ebben az esetben a dinamikus IP-címek nem kezelhetők.

A készlet létrehozása előtt létrehozhat statikus nyilvános IP-cím erőforrásokat a Batch-fiókkal megegyező előfizetésben. Ezeket a címeket a készlet létrehozásakor adhatja meg.

További információ: Azure Batch- [készlet létrehozása a megadott nyilvános IP-címekkel](create-pool-public-ip.md).

#### <a name="create-pools-without-public-ip-addresses"></a>Készletek létrehozása nyilvános IP-címek nélkül

Alapértelmezés szerint egy Azure Batch virtuálisgép-konfigurációs készlet összes számítási csomópontja egy vagy több nyilvános IP-címet kap. Ezeket a végpontokat a Batch szolgáltatás használja a feladatok és a számítási csomópontokkal folytatott kommunikációhoz, beleértve az internet felé irányuló kimenő hozzáférést is.

Ha korlátozni szeretné ezen csomópontok elérését, és csökkenti a csomópontok internetről való felderíthetővé tételét, nyilvános IP-címek nélkül is kiépítheti a készletet.

További információt a [készlet nyilvános IP-címek nélküli létrehozása](batch-pool-no-public-ip-address.md)című témakörben talál.

#### <a name="limit-remote-access-to-pool-nodes"></a>A távoli hozzáférés korlátozása a készlet csomópontjaira

Alapértelmezés szerint a Batch lehetővé teszi a hálózati kapcsolattal rendelkező csomópontok számára, hogy RDP vagy SSH használatával külsőleg csatlakozzanak egy batch-készletben lévő számítási csomóponthoz.

A csomópontok távoli elérésének korlátozásához használja az alábbi módszerek egyikét:

- Konfigurálja úgy a [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) , hogy megtagadja a hozzáférést. A készlethez a megfelelő hálózati biztonsági csoport (NSG) lesz társítva.
- [Nyilvános IP-címek nélkül](batch-pool-no-public-ip-address.md)hozza létre a készletet. Alapértelmezés szerint ezek a készletek nem érhetők el a VNet kívül.
- Társítson egy NSG a VNet, hogy megtagadja az RDP-vagy SSH-portok elérését.
- Ne hozzon létre felhasználókat a csomóponton. Csomópont-felhasználók nélkül a távelérés nem lesz lehetséges.

## <a name="encrypt-data"></a>Adatok titkosítása

### <a name="encrypt-data-in-transit"></a>Adatforgalom titkosítása

A Batch-fiók végpontjának (vagy Azure Resource Manager) minden kommunikációjának HTTPS protokollt kell használnia. A Batch- `https://` szolgáltatáshoz való csatlakozáskor az API-khoz megadott batch-fiók URL-címeit kell használnia.

A Batch szolgáltatással kommunikáló ügyfeleket úgy kell konfigurálni, hogy a következőt használják: Transport Layer Security (TLS) 1,2.

### <a name="encrypt-batch-data-at-rest"></a>Batch-adatok titkosítása a nyugalmi állapotban

A Batch API-k, például a tanúsítványok, a feladatok és a tevékenységek metaadatainak és a feladatok parancssora által meghatározott információk a Batch szolgáltatás által tárolt adatokon automatikusan titkosítva vannak. Alapértelmezés szerint a rendszer az egyes batch-fiókokra jellemző, Azure Batch platform által felügyelt kulcsokkal titkosítja ezeket az értékeket.

Ezeket az [ügyfeleket az ügyfél által felügyelt kulcsokkal](batch-customer-managed-key.md)is titkosíthatja. [Azure Key Vault](../key-vault/general/overview.md) a kulcs létrehozásához és tárolásához használható a Batch-fiókban regisztrált kulcs-azonosítóval.

### <a name="encrypt-compute-node-disks"></a>Számítási csomópont lemezek titkosítása

A Batch számítási csomópontjainak alapértelmezés szerint két lemeze van: egy operációsrendszer-lemez és a helyi ideiglenes SSD. A Batch által kezelt [fájlok és könyvtárak](files-and-directories.md) az ideiglenes SSD-meghajtón találhatók, amely a fájlok, például a feladat kimeneti fájljainak alapértelmezett helye. A Batch-feladattal rendelkező alkalmazások az alapértelmezett helyet használhatják az SSD-meghajtón vagy az operációsrendszer-lemezen.

A további biztonság érdekében titkosítsa ezeket a lemezeket az Azure Disk Encryption képességeinek egyikével:

- [Felügyelt lemezes titkosítás a platform által felügyelt kulcsokkal](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Titkosítás a gazdagépen a platform által felügyelt kulcs használatával](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Encryption](disk-encryption.md)

## <a name="securely-access-services-from-compute-nodes"></a>Biztonságos hozzáférés szolgáltatások a számítási csomópontokból

A Batch-csomópontok biztonságosan érhetik el [Azure Key Vault](../key-vault/general/overview.md)tárolt [hitelesítő adatokat és titkos kulcsokat](credential-access-key-vault.md) , amelyeket a feladatok alkalmazásai használhatnak más szolgáltatások eléréséhez. A rendszer egy tanúsítványt használ a készlet-csomópontok Key Vaulthoz való hozzáférésének biztosításához.

## <a name="governance-and-compliance"></a>Cégirányítás és megfelelőség

### <a name="compliance"></a>Megfelelőség

Az Azure a megfelelőségi [ajánlatok nagy portfólióját](https://azure.microsoft.com/overview/trusted-cloud/compliance)biztosítja, hogy az ügyfelek megfeleljenek a saját megfelelőségi kötelezettségeiknek a szabályozott iparágakban és piacokon. 

Ezek az ajánlatok különféle típusú garanciákat foglalnak magukban, beleértve a formális tanúsítványokat, a tanúsítványokat, az igazolásokat, az engedélyeket, valamint a független külső könyvvizsgáló cégek által létrehozott értékeléseket, valamint a Microsoft által készített szerződéses módosításokat, önértékeléseket és ügyfél-útmutatási dokumentumokat. Tekintse át a [megfelelőségi ajánlatok átfogó áttekintését](https://aka.ms/AzureCompliance) , és állapítsa meg, hogy melyek lehetnek a Batch-megoldásokhoz szükségesek.

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../governance/policy/overview.md) segít a szervezeti szabványok betartatásában és a megfelelőség nagy léptékű vizsgálatában. A Azure Policy gyakori használati esetei közé tartozik az erőforrás-konzisztencia megvalósítása, a szabályozások megfelelősége, a biztonság, a költséghatékonyság és a felügyelet.

A készlet lefoglalási módjától és a szabályzat hatálya alatt álló erőforrásoktól függően a következő módszerek egyikével használhatja a Azure Policy a Batch szolgáltatással:

- Közvetlenül a Microsoft.BatCH/batchAccounts erőforrás használatával. A Batch-fiók tulajdonságainak egy részhalmazát használhatja. Például a házirend tartalmazhatja a Batch-fiókok érvényes régióit, az engedélyezett készlet-kiosztási módot, valamint azt, hogy engedélyezve van-e egy nyilvános hálózat a fiókok számára.
- Közvetve a Microsoft. számítási/virtualMachineScaleSets erőforrás használatával. A felhasználói előfizetések készletének kiosztási móddal rendelkező batch-fiókjai rendelkezhetnek a Batch-fiók előfizetésében létrehozott virtuálisgép-méretezési csoport erőforrásain beállított házirenddel. Például engedélyezhető a virtuális gépek mérete, és bizonyos bővítmények futnak a készlet minden csomópontján.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a [Batch Azure biztonsági alaptervét](security-baseline.md).
- További [ajánlott eljárások a Azure batch](best-practices.md).
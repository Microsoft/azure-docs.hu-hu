---
title: Azure-alapú adattitkosítás – a REST-Azure biztonság
description: Ez a cikk áttekintést nyújt az Azure-beli adattitkosításról, az általános képességekről és az általános szempontokról.
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: mbaldwin
ms.openlocfilehash: 1318aaf50d4f05b8a999e5661318dc83997a2f02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96920778"
---
# <a name="azure-data-encryption-at-rest"></a>Az Azure-beli adattitkosítás inaktív állapotban

Microsoft Azure olyan eszközöket tartalmaz, amelyek a vállalat biztonsági és megfelelőségi igényeinek megfelelően védik az adatvédelmet. A tanulmány a következőkre összpontosít:

- Hogyan védi az adatok az egész Microsoft Azure
- Az adatvédelmi implementációban részt vevő különböző összetevőkről tárgyal
- A különböző kulcskezelő védelmi megközelítések előnyeit és hátrányait tekinti át.

A inaktív adatok titkosítása gyakori biztonsági követelmény. Az Azure-ban a szervezetek egy egyéni kulcskezelő megoldás kockázata vagy díja nélkül titkosíthatók az adatok. A szervezetek lehetővé teszik, hogy az Azure teljes mértékben kezelhesse a titkosítást a nyugalmi állapotban. Emellett a szervezetek különböző lehetőségekkel rendelkeznek a titkosítási vagy titkosítási kulcsok szoros kezeléséhez.

## <a name="what-is-encryption-at-rest"></a>Mi a titkosítás a REST-ben?

A titkosítás az adatok titkosságának védelme érdekében használt adatok biztonságos kódolása. Az Azure REST-alapú titkosítása szimmetrikus titkosítást használ a nagy adatmennyiségek gyors titkosításához és visszafejtéséhez egy egyszerű elméleti modell szerint:

- A szimmetrikus titkosítási kulcs használatával titkosíthatja az adattárakat a tárolóba való írás során.
- Ugyanazt a titkosítási kulcsot használja a rendszer a memóriában való használathoz readied.
- Előfordulhat, hogy az adatpartíciók particionálva vannak, és az egyes partíciók esetében különböző kulcsok is használhatók.
- A kulcsokat biztonságos helyen kell tárolni, az identitás-alapú hozzáférés-vezérléssel és a naplózási házirendekkel. Az adattitkosítási kulcsokat gyakran titkosítja Azure Key Vault a hozzáférés további korlátozása érdekében a kulcs titkosítási kulcsával.

A gyakorlatban a kulcsfontosságú felügyeleti és ellenőrzési forgatókönyvek, valamint a méretezési és rendelkezésre állási garanciák további szerkezeteket igényelnek. Az alábbiakban részletesen ismertetjük Microsoft Azure titkosítást a REST-fogalmakon és-összetevőkön.

## <a name="the-purpose-of-encryption-at-rest"></a>A titkosítás célja a nyugalmi állapotban

A inaktív adatok titkosítása adatvédelmet biztosít a tárolt adatok számára (nyugalmi állapotban). A REST-adatok elleni támadások közé tartozik az adatok tárolására szolgáló hardver fizikai hozzáférésének beszerzésére irányuló kísérlet, majd a benne foglalt adatok biztonsága. Ilyen támadás esetén előfordulhat, hogy a kiszolgáló merevlemeze helytelenül lett kezelve a karbantartás során, ami lehetővé teszi a támadó számára a merevlemez eltávolítását. Később a támadó a vezérlőbe helyezi a merevlemezt, hogy megpróbáljon hozzáférni az adathoz.

A inaktív adatok titkosítása úgy lett kialakítva, hogy a támadók ne férhessenek hozzá a titkosítatlan adatokhoz, mivel a lemezeken titkosítva vannak az adatok. Ha egy támadó titkosított adattal rendelkező merevlemezt szerez be, de nem a titkosítási kulcsokat, a támadónak le kell győznie a titkosítást az információk olvasásához. Ez a támadás sokkal összetettebb és erőforrás-felhasználást igényel, mint a titkosítatlan adatok merevlemezen való elérése. Emiatt a inaktív adatok titkosítása erősen ajánlott, és nagy prioritású követelmény számos szervezet számára.

A munkahelyen történő titkosításhoz a szervezetnek adatirányítási és megfelelőségi erőfeszítésekre is szüksége lehet. Az iparági és kormányzati rendeletek, például a HIPAA, a PCI és a FedRAMP, az adatvédelem és a titkosítási követelményekkel kapcsolatos konkrét óvintézkedéseket határoznak meg. A inaktív adatok titkosítása egy kötelező mérték, amely megfelel ezeknek a szabályozásoknak. További információ a Microsoft FIPS 140-2-hitelesítéssel kapcsolatos megközelítéséről: [Federal Information Processing standard (FIPS) kiadvány 140-2](/microsoft-365/compliance/offering-fips-140-2).

A megfelelőségi és szabályozási követelmények teljesítése mellett a inaktív titkosítás a védelem részletes védelmét is biztosítja. A Microsoft Azure megfelelő platformot biztosít a szolgáltatások, az alkalmazások és az adatkezeléshez. Emellett átfogó lehetőséget és fizikai biztonságot, adathozzáférés-vezérlést és auditálást is biztosít. Azonban fontos, hogy további "átfedésben" biztonsági intézkedéseket adjanak abban az esetben, ha az egyik másik biztonsági intézkedés meghibásodik, és a titkosítás inaktív állapotban van.

A Microsoft elkötelezte magát a különböző Cloud Services-beli Rest-lehetőségek titkosítására, és lehetővé teszi az ügyfeleknek a titkosítási kulcsok és a kulcsfontosságú használati naplók felügyeletét. Emellett a Microsoft az összes vásárlói adatok alapértelmezés szerint történő titkosítására is törekszik.

## <a name="azure-encryption-at-rest-components"></a>Azure encryption a REST-összetevőknél

A korábban leírtaknak megfelelően a titkosítás célja, hogy a lemezen tárolt adatok titkos titkosítási kulccsal legyenek titkosítva. Ennek a célnak a biztosításához meg kell adni a titkosítási kulcsok létrehozását, tárolását, hozzáférés-vezérlését és felügyeletét. Bár a részletek eltérőek lehetnek, az Azure-szolgáltatások titkosítása Rest-implementációkban az alábbi ábrán látható feltételek szerint olvasható.

![Összetevők](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

A titkosítási kulcsok és az azokhoz való hozzáférés-vezérlés tárolási helye a REST-modellben lévő titkosítási kulcs központi része. A kulcsoknak nagyon biztonságosnek kell lenniük, de a megadott felhasználók kezelhetik, és bizonyos szolgáltatások számára elérhetők. Az Azure-szolgáltatások esetében Azure Key Vault az ajánlott kulcstároló-megoldás, amely általános felügyeleti élményt nyújt a szolgáltatások között. A kulcsok tárolása és kezelése a kulcstartókban történik, és a Key vaulthoz való hozzáférés a felhasználók vagy szolgáltatások számára adható meg. Azure Key Vault támogatja a kulcsok ügyfelek általi létrehozását, illetve az ügyfelek által felügyelt titkosítási kulcsokban való használatra való adatimportálást.

### <a name="azure-active-directory"></a>Azure Active Directory

A Azure Key Vaultban tárolt kulcsok használatára vonatkozó engedélyek, amelyek a titkosításhoz vagy a titkosításhoz való hozzáféréshez szükségesek a REST-titkosításhoz és a visszafejtéshez Azure Active Directory fiókok számára is.

### <a name="key-hierarchy"></a>Kulcs hierarchiája

A REST-implementációban egynél több titkosítási kulcs van használatban. A titkosítási kulcs tárolása Azure Key Vault biztosítja a biztonságos kulcs-hozzáférést és a kulcsok központi felügyeletét. Azonban a szolgáltatás helyi hozzáférése a titkosítási kulcsokhoz hatékonyabb a tömeges titkosításhoz és a visszafejtéshez, mint a Key Vault összes adatművelettel való interakciója, ami erősebb titkosítást és jobb teljesítményt tesz lehetővé. Egyetlen titkosítási kulcs használatának korlátozása csökkenti annak a kockázatát, hogy a kulcs biztonsága sérül, valamint a kulcs cseréjének újratitkosításának költségeit. Az Azure-beli titkosítások a REST-modellekben a következő típusú kulcsokból állnak, hogy az összes ilyen szükségletet kezelni lehessen:

- **Adattitkosítási kulcs (adattitkosítási kulcsot)** – egy partíció vagy adatblokk titkosításához használt szimmetrikus AES256-kulcs.  Egyetlen erőforrás több partícióval és számos adattitkosítási kulccsal is rendelkezhet. Az egyes adatblokkok egy másik kulccsal való titkosítása nehezebbé teszi a titkosítási elemzési támadásokat. A DEKs való hozzáférésre az erőforrás-szolgáltató vagy az alkalmazás-példány szükséges, amely egy adott blokk titkosítását és visszafejtését végzi. Ha egy ADATTITKOSÍTÁSI kulcsot egy új kulccsal van lecserélve, csak a hozzá tartozó blokkban lévő adatmennyiséget kell újra titkosítani az új kulccsal.
- **Kulcs titkosítási kulcsa (KEK)** – az adattitkosítási kulcsok titkosításához használt titkosítási kulcs. Olyan kulcs-titkosítási kulcs használata, amely soha nem hagy Key Vault lehetővé teszi, hogy az adattitkosítási kulcsok titkosítva és szabályozva legyenek. Előfordulhat, hogy a KEK-hez hozzáférő entitás nem azonos a ADATTITKOSÍTÁSI kulcsot igénylő entitással. Az entitások a ADATTITKOSÍTÁSI kulcsot való hozzáférést az egyes ADATTITKOSÍTÁSI kulcsot adott partícióhoz való hozzáférésének korlátozására használhatják. Mivel a KEK a DEKs visszafejtéséhez szükséges, a KEK gyakorlatilag egyetlen pont, amellyel a DEKs hatékonyan törölhető a KEK törlésével.

A titkosítási kulcsokkal titkosított adattitkosítási kulcsokat a rendszer külön tárolja, és csak a kulcs titkosítási kulcshoz hozzáféréssel rendelkező entitások tudják visszafejteni ezeket az adattitkosítási kulcsokat. A Key Storage különböző modelljei támogatottak. További információért lásd az [adattitkosítási modelleket](encryption-models.md) .

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Inaktív adatok titkosítása a Microsoft Cloud Servicesben

Microsoft Cloud szolgáltatásokat mindhárom Felhőbeli modellben használják: IaaS, Pásti, SaaS. Az alábbiakban néhány példát láthat arra, hogyan illeszkednek az egyes modellekhez:

- A szoftver, amelyet kiszolgálóként vagy SaaS-ként, a felhő által nyújtott alkalmazásokkal (például Microsoft 365) rendelkező szoftverek.
- Azok a platform-szolgáltatások, amelyekkel az ügyfelek a felhőt használják alkalmazásaikban, például a Storage, az Analytics és a Service Bus funkcióinak használatával.
- Infrastruktúra-szolgáltatások vagy infrastruktúra-szolgáltatás (IaaS), amelyben az ügyfél üzembe helyezi a felhőben üzemeltetett operációs rendszereket és alkalmazásokat, és esetleg más felhőalapú szolgáltatásokat hasznosít.

### <a name="encryption-at-rest-for-saas-customers"></a>Inaktív adatok titkosítása SaaS-ügyfelek számára

A szolgáltatott szoftverként (SaaS) használó ügyfelek jellemzően inaktív titkosítással rendelkeznek, vagy az egyes szolgáltatásokban elérhetők. Microsoft 365 több lehetősége van arra, hogy az ügyfelek ellenőrizzék vagy engedélyezzék a titkosítást a nyugalmi állapotban. Microsoft 365 szolgáltatásokkal kapcsolatos információkért lásd: [titkosítás a Microsoft 365ban](/microsoft-365/compliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Inaktív titkosítás a Pásti-ügyfelek számára

A szolgáltatásként szolgáló platform (a Pásti) ügyfeleinek általában egy tárolási szolgáltatásban (például Blob Storage) vannak tárolva, de az alkalmazás-végrehajtási környezetben (például egy virtuális gépen) is gyorsítótárazva lehet vagy tárolhatók. Ha szeretné megtekinteni a titkosítást a rendelkezésre álló Rest-beállítások között, vizsgálja meg az alábbi táblázatot a használt tároló-és alkalmazás-platformok esetében.

### <a name="encryption-at-rest-for-iaas-customers"></a>Inaktív adatok titkosítása IaaS-ügyfelek számára

Az infrastruktúra-szolgáltatás (IaaS) ügyfelei számos szolgáltatást és alkalmazást használhatnak. A IaaS Services lehetővé teszi, hogy az Azure-ban üzemeltetett virtuális gépeken és a virtuális merevlemezeken is engedélyezze a titkosítást Azure Disk Encryption használatával.

#### <a name="encrypted-storage"></a>Titkosított tároló

A Pástihoz hasonlóan a IaaS-megoldások más Azure-szolgáltatásokat is használhatnak, amelyek a nyugalmi állapotban titkosított adatok tárolására használhatók. Ezekben az esetekben engedélyezheti a titkosítást az összes felhasznált Azure-szolgáltatás által biztosított Rest-támogatásban. Az alábbi táblázat felsorolja a főbb tárolási, szolgáltatás-és alkalmazás-platformokat, valamint a REST titkosítású modellt.

#### <a name="encrypted-compute"></a>Titkosított számítás

A Managed Disks, a pillanatképek és a képek a szolgáltatás által felügyelt kulccsal vannak titkosítva Storage Service Encryption használatával. A REST-alapú teljes titkosítási megoldás biztosítja, hogy az adatok soha ne legyenek megmaradva titkosítatlan formában. A virtuális gépek adatainak feldolgozásakor a Windows-lapozófájl vagy a Linux-lapozófájl, egy összeomlási memóriakép vagy egy alkalmazásnapló is megmarad. Annak biztosítása érdekében, hogy az adatok titkosítva legyenek, a IaaS-alkalmazások az Azure IaaS virtuális gépen (Windows vagy Linux) és virtuális lemezen is használhatják a Azure Disk Encryption.

#### <a name="custom-encryption-at-rest"></a>Egyéni titkosítás nyugalmi állapotban

Azt javasoljuk, hogy ha lehetséges, a IaaS alkalmazások Azure Disk Encryption és titkosítást használjanak a felhasznált Azure-szolgáltatások által biztosított Rest-beállításokban. Bizonyos esetekben – például a szabálytalan titkosítási követelmények vagy a nem Azure-alapú tárolás – esetében előfordulhat, hogy egy IaaS-alkalmazás fejlesztője maga is implementálja a titkosítást. A IaaS-megoldások fejlesztői a bizonyos Azure-összetevők kihasználása révén jobban integrálhatók az Azure felügyeleti és vevői elvárásaival. Konkrétan a fejlesztőknek az Azure Key Vault szolgáltatást kell használniuk a biztonságos kulcstároló biztosításához, valamint az ügyfeleket a legtöbb Azure platform-szolgáltatáshoz tartozó konzisztens felügyeleti lehetőségekkel. Emellett az egyéni megoldások Azure-Managed szolgáltatás-identitások használatával engedélyezhetik a szolgáltatásfiókok számára a titkosítási kulcsok elérését. A Azure Key Vault-és felügyelt szolgáltatásbeli identitásokkal kapcsolatos fejlesztői információkért tekintse meg a megfelelő SDK-kat.

## <a name="azure-resource-providers-encryption-model-support"></a>Azure Resource Providers titkosítási modell támogatása

Microsoft Azure szolgáltatások a REST-modellekben egy vagy több titkosítást támogatnak. Egyes szolgáltatások esetében azonban előfordulhat, hogy egy vagy több titkosítási modell nem alkalmazható. Az ügyfél által felügyelt kulcsfontosságú forgatókönyveket támogató szolgáltatások esetében csak a kulcs titkosítási kulcsainak Azure Key Vault által támogatott kulcstárolók egy részhalmazát támogatják. Emellett a szolgáltatások a különböző időpontokban is támogathatják az ilyen forgatókönyvek és a kulcsfontosságú típusok támogatását. Ez a szakasz a nagy Azure-beli adattárolási szolgáltatásokra vonatkozó írás időpontjában a REST-támogatás titkosítását ismerteti.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Az Azure-alapú infrastruktúra-szolgáltatás (IaaS) funkcióit használó bármely ügyfél a IaaS-alapú virtuális gépek és Azure Disk Encryption lemezek számára is lehetővé teheti a titkosítást. Az Azure Disk Encryption szolgáltatással kapcsolatos további információkért tekintse meg a [Azure Disk Encryption dokumentációját](./azure-disk-encryption-vms-vmss.md).

#### <a name="azure-storage"></a>Azure Storage tárterület

Az összes Azure Storage-szolgáltatás (blob Storage, üzenetsor-tárolás, Table Storage és Azure Files) támogatja a kiszolgálóoldali titkosítást a nyugalmi állapotban; néhány szolgáltatás emellett az ügyfél által felügyelt kulcsokat és az ügyféloldali titkosítást is támogatja.

- Kiszolgálóoldali: minden Azure Storage-szolgáltatás alapértelmezés szerint a szolgáltatás által felügyelt kulcsok használatával engedélyezi a kiszolgálóoldali titkosítást, amely transzparens az alkalmazás számára. További információ: [Azure Storage Service encryption for](../../storage/common/storage-service-encryption.md)inaktív adatok. Az Azure Blob Storage és a Azure Files támogatja az RSA 2048 bites ügyfél által felügyelt kulcsokat Azure Key Vault. További információ: [Storage Service encryption ügyfél által felügyelt kulcsok használata Azure Key Vaultban](../../storage/common/customer-managed-keys-configure-key-vault.md).
- Ügyféloldali: az Azure-Blobok,-táblák és-várólisták támogatják az ügyféloldali titkosítást. Az ügyféloldali titkosítás használatakor az ügyfelek titkosítják az adataikat, és feltöltik az adataikat titkosított blobként. A kulcskezelőt az ügyfél végzi. További információ: [ügyféloldali titkosítás és Azure Key Vault Microsoft Azure Storagehoz](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Azure SQL Database

A Azure SQL Database jelenleg a Microsoft által felügyelt szolgáltatás-és ügyféloldali titkosítási helyzetekben támogatja a REST titkosítást.

A kiszolgálói titkosítás támogatását jelenleg a transzparens adattitkosítás nevű SQL-szolgáltatás nyújtja. Ha egy Azure SQL Database ügyfél lehetővé teszi, hogy a rendszer automatikusan létrehozza és felügyeli a TDE kulcsot. A inaktív adatok titkosítása az adatbázis és a kiszolgáló szintjén is engedélyezhető. A 2017. júniusi [transzparens adattitkosítás (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) alapértelmezés szerint engedélyezve van az újonnan létrehozott adatbázisokon. Azure SQL Database támogatja az RSA 2048-bites ügyfél által felügyelt kulcsokat Azure Key Vault. További információ: [transzparens adattitkosítás bring your own Key támogatással a Azure SQL Database és az Adattárházhoz](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql).

A Azure SQL Database-adat ügyféloldali titkosítása támogatott a [Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) funkción keresztül. A Always Encrypted az ügyfél által létrehozott és tárolt kulcsot használja. Az ügyfelek a főkulcsot egy Windows tanúsítványtárolóban, Azure Key Vaultban vagy egy helyi hardveres biztonsági modulban tárolhatják. A SQL Server Management Studio használatával az SQL-felhasználók kiválaszthatják, hogy milyen kulcsot szeretnének használni az oszlop titkosításához.

## <a name="conclusion"></a>Összegzés

Az Azure-szolgáltatásokon belül tárolt ügyféladatok védelme kiemelkedő fontosságú a Microsoft számára. Az összes Azure-beli üzemeltetett szolgáltatás elkötelezett amellett, hogy titkosítást biztosítson a REST-beállításokban. Az Azure-szolgáltatások a szolgáltatás által felügyelt kulcsokat, az ügyfél által felügyelt kulcsokat vagy az ügyféloldali titkosítást támogatják. Az Azure-szolgáltatások széles körben javítják a titkosítást a REST rendelkezésre állása során, és az új lehetőségek az előzetes verzióra és az általános elérhetőségre vannak tervezve a következő hónapokban.

## <a name="next-steps"></a>Következő lépések

- A szolgáltatás által felügyelt kulcsokkal és az ügyfél által felügyelt kulcsokkal kapcsolatos további tudnivalókért tekintse meg az [adattitkosítási modelleket](encryption-models.md) .
- Ismerje meg, hogyan használja az Azure a [kettős titkosítást](double-encryption.md) az adattitkosítással kapcsolatos fenyegetések enyhítésére.
- Ismerje meg, hogy a Microsoft Hogyan biztosítja a [platform integritását és biztonságát](platform.md) a hardver-és belső vezérlőprogram-kiépítési, integrációs, operacionalizálási és javítási folyamatokat áthaladó gazdagépek számára.

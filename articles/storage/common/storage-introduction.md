---
title: Bevezetés az Azure Storage használatába – Felhőalapú tárolás az Azure-ban | Microsoft Docs
description: Az alapvető Azure Storage platform a Microsoft felhőalapú tárolási megoldása. Az Azure Storage magas rendelkezésre állású, biztonságos, tartós, méretezhető és redundáns adatobjektum-tárolást biztosít.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: b49e780714bd5e0662c6cb7ef208c473a8b45ccc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96498149"
---
# <a name="introduction-to-the-core-azure-storage-services"></a>Az alapszintű Azure Storage szolgáltatás bemutatása

Az Azure Storage platform a Microsoft felhőalapú tárolási megoldása a modern adattárolási forgatókönyvekhez. Az alapszintű tárolási szolgáltatások egy nagymértékben méretezhető objektumot biztosítanak az adatobjektumokhoz, a lemezes tároláshoz az Azure Virtual Machines (VM) szolgáltatáshoz, a felhőhöz tartozó fájlrendszer-szolgáltatást, a megbízható üzenetküldést biztosító üzenetküldési tárolót és egy NoSQL-tárolót. A szolgáltatások a következők:

- **Tartós és magas rendelkezésre állású.** A redundancia biztosítja az adatok biztonságát átmeneti hardverhiba esetén. Az adatközpontok vagy földrajzi helyek adatait replikálhatja is a helyi vagy természeti katasztrófák elleni további védelem érdekében. Az így replikált adatok magas rendelkezésre állásúak maradnak váratlan meghibásodás esetén is.
- **Biztonságos.** Az Azure Storage-fiókba írt összes adathalmazt a szolgáltatás titkosítja. Az Azure Storage használatával részletesen szabályozhatja, hogy ki férhet hozzá az adatokhoz.
- **Méretezhető.** Az Azure Storage nagymértékben skálázható, hogy megfeleljen a mai alkalmazások adattárolási és teljesítménybeli igényeinek.
- **Felügyelt.** Az Azure kezeli a hardveres karbantartást, a frissítéseket és a kritikus problémákat.
- **Hozzáférhető.** Az Azure Storage tárterületen lévő adatok a világon bárhonnan elérhetők HTTP- vagy HTTPS-kapcsolaton keresztül. A Microsoft számos nyelven biztosít ügyféloldali kódtárakat az Azure Storage-hoz, többek között a .NET, a Java, a Node.js, a Python, a PHP, a Ruby, a Go és mások számára, valamint egy érett REST API. Az Azure Storage támogatja az Azure PowerShell és az Azure CLI szkriptjeit. Az Azure Portal és az Azure Storage Explorer emellett egyszerű vizuális megoldásokat nyújt az adatok használatához.  

## <a name="core-storage-services"></a>Alapszintű tárolási szolgáltatások

Az Azure Storage platform a következő adatszolgáltatásokat tartalmazza:

- [Azure Blobs](../blobs/storage-blobs-introduction.md): Nagymértékben skálázható objektumtároló szöveges és bináris adatokhoz. A Data Lake Storage Gen2on keresztül big data Analitika támogatását is magában foglalja.
- [Azure Files](../files/storage-files-introduction.md): felügyelt fájlmegosztás a Felhőbeli vagy helyszíni központi telepítésekhez.
- [Azure Queues](../queues/storage-queues-introduction.md): Az alkalmazások összetevői között megbízható üzenetkezelést biztosító üzenettároló.
- [Azure Tables](../tables/table-storage-overview.md): A strukturált adatok séma nélküli tárolására szolgáló NoSQL-tároló.
- [Azure-lemezek](../../virtual-machines/managed-disks-overview.md): az Azure-beli virtuális gépek blokkolási szintű tárolási kötetei.

Mindegyik szolgáltatás tárfiókon keresztül érhető el. Első lépésként lásd: [Tárfiók létrehozása](storage-account-create.md).

## <a name="example-scenarios"></a>Példaforgatókönyvek

A következő táblázat összehasonlítja a fájlokat, a blobokat, a lemezeket, a várólistákat és a táblázatokat, és megjeleníti az egyes forgatókönyvek példáit.

| Szolgáltatás | Leírás | A következő esetekben használja |
|--------------|-------------|-------------|
| **Azure Files** |A teljes körűen felügyelt felhőalapú fájlmegosztást biztosít, amely bárhonnan elérhető az iparági szabványnak megfelelő SMB protokollon keresztül.<br><br>Azure-fájlmegosztást csatlakoztathat a felhőből vagy a helyszíni Windows, Linux és macOS rendszerű környezetekről. | Olyan alkalmazást szeretne átemelni és átállítani a felhőbe, amely már a natív fájlrendszer API-kat használja az Azure-ban futó más alkalmazások közötti adatmegosztáshoz.<br/><br/>Szeretné cserélni vagy kiegészíteni a helyszíni fájlkiszolgálók vagy NAS-eszközöket.<br><br> Olyan fejlesztési és hibakeresési eszközöket szeretne tárolni, amelyeknek számos virtuális gépről kell elérniük. |
| **Azure Blobs** | Lehetővé teszi a strukturálatlan adatmennyiségek tárolását és elérését a blokkos Blobok nagy méretekben.<br/><br/>A [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) is támogatja a vállalati Big Data elemzési megoldásokhoz. | Azt szeretné, hogy az alkalmazás támogassa a folyamatos átvitelt és a véletlenszerű hozzáférési forgatókönyveket.<br/><br/>Bárhonnan elérhetővé szeretné tenni az alkalmazásadatok elérését.<br/><br/>Az Azure-ban szeretne létrehozni egy vállalati adattavat, és big data elemzést hajt végre. |
| **Azure Disks** | Lehetővé teszi az adatok tartós tárolását és elérését egy csatlakoztatott virtuális merevlemezről. | A natív fájlrendszerű API-kat használó alkalmazások "átemelése" és "eltolása" szükséges az állandó lemezekre való olvasáshoz és az adatíráshoz.<br/><br/>Olyan adatok tárolására van szükség, amelyek nem szükségesek azon a virtuális gépen kívülről való hozzáféréshez, amelyhez a lemez csatlakoztatva van. |
| **Azure-üzenetsorok** | Lehetővé teszi az alkalmazások összetevői közötti aszinkron üzenetsor-kezelést. | El szeretné választani az alkalmazás-összetevőket, és aszinkron üzenetküldést használ a közöttük való kommunikációhoz.<br><br>A Queue Storage és a Service Bus Queues használata esetén a következő témakörben talál útmutatást: [Storage Queues and Service Bus Queues – összehasonlítás és kontrasztos](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md). |
| **Azure-táblák** | Lehetővé teszi a strukturált NoSQL-alapú adattárolást a felhőben, amely egy kulcs/attribútum-tárolót biztosít a séma nélküli kialakításhoz. | Rugalmas adatkészleteket szeretne tárolni, például a webalkalmazásokhoz, a címjegyzékekhez, az eszköz adataihoz vagy a szolgáltatás által igényelt más típusú metaadatokhoz tartozó felhasználói adatokat. <br/><br/>Ha a Table Storage és a Azure Cosmos DB Table API használatát ismerteti, tekintse meg a [fejlesztés a Azure Cosmos DB Table API és az Azure Table Storage](../../cosmos-db/table-support.md)szolgáltatással című témakört. |

## <a name="blob-storage"></a>Blob Storage

Az Azure Blob Storage a Microsoft felhőalapú objektumtárolási megoldása. A Blob Storage nagy mennyiségű strukturálatlan adat, például szöveg vagy bináris adatok tárolására van optimalizálva.

A Blob Storage a következőkhöz ideális:

- Képek vagy dokumentumok közvetlen szolgáltatása a böngészők számára.
- Fájlok tárolása megosztott hozzáféréshez.
- Video- és hangtartalom streamelése.
- Adattárolás biztonsági mentésekhez és helyreállításhoz, vészhelyreállításhoz és archiváláshoz.
- Adattárolás helyszíni vagy az Azure-ban üzemeltetett szolgáltatásban való elemzéshez.

A Blob Storage tárolóban lévő objektumok a világon bárhonnan elérhetők HTTP- vagy HTTPS-kapcsolaton keresztül. A felhasználók vagy ügyfélalkalmazások URL-eken, az [Azure Storage REST API-n](/rest/api/storageservices/blob-service-rest-api), az [Azure PowerShellen](/powershell/module/azure.storage), az [Azure CLI-n](/cli/azure/storage) vagy Azure Storage-ügyfélkódtárakon keresztül érhetik el a blobokat. A tároló ügyfélkódtárai több nyelven érhetők el, beleértve a [.NET](/dotnet/api/overview/azure/storage), [Java](/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) és [Ruby](https://azure.github.io/azure-storage-ruby) nyelveket.

További információ a blob Storage-ról: [Bevezetés a blob Storage](../blobs/storage-blobs-introduction.md)-ba.

## <a name="azure-files"></a>Azure Files

A [Azure Files](../files/storage-files-introduction.md) lehetővé teszi, hogy olyan, a szabványos SMB protokoll használatával elérhető hálózati fájlmegosztás beállítása legyen elérhető. Ez azt jelenti hogy ugyanazokhoz a fájlokhoz több virtuális gép is hozzáférhet olvasási és írási jogosultsággal. A fájlokat a REST-felület vagy a Storage klienskódtáraival is olvashatja.

Az egyik dolog, ami megkülönbözteti az Azure Files szolgáltatást a fájlok a vállalati fájlmegosztásokon való tárolásától, hogy a fájlokat a világ bármely pontjáról elérheti a rájuk mutató URL-címekkel, amelyek közös hozzáférésű jogosultságkód- (SAS-) tokenekkel rendelkeznek. SAS-tokeneket Ön is létrehozhat: ezek határozott ideig biztosítanak egyedi hozzáférést a privát objektumokhoz.

A fájlmegosztások számos gyakori forgatókönyvhöz használhatók:

- Számos helyszíni alkalmazás használ fájlmegosztásokat. Ezzel a szolgáltatással könnyebb migrálni azokat az alkalmazásokat, amelyek az Azure-ban osztják meg az adatokat. Ha a fájlmegosztást ugyanarra a meghajtóhelyre csatlakoztatja, amelyet a helyszíni alkalmazás használ, az alkalmazás fájlmegosztáshoz kapcsolódó része minimális vagy semennyi módosítással használható.

- A konfigurációs fájlok fájlmegosztásokon tárolhatók, és több virtuális gépről is elérhetők. A több fejlesztő által egy csoportban használt eszközök és segédprogramok is tárolhatók fájlmegosztásokon, így mindenki elérheti őket, és mindenki ugyanazt a verziót használhatja.

- Az erőforrás-naplók, a metrikák és az összeomlási memóriaképek csupán három példát mutatnak a fájlmegosztásba való írásra és a később feldolgozott vagy elemzett adatokra.

További információ az Azure Filesról: [Bevezetés az Azure Files használatába](../files/storage-files-introduction.md).

Egyes SMB-funkciók nem alkalmazhatók a felhőre. További információ: [Az Azure file Service által nem támogatott szolgáltatások](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).

## <a name="queue-storage"></a>Queue Storage

Az Azure Queue szolgáltatás üzenetek tárolására és lehívására használható. Az üzenetsor üzenetei egyenként legfeljebb 64 KB méretűek lehetnek, és az üzenetsor akár több millió üzenetet is tartalmazhat. Az üzenetsorok általában aszinkron feldolgozásra szánt üzenetek listáit tárolják.

Tegyük fel például, hogy biztosítani szeretné az ügyfelei számára, hogy képeket tölthessenek fel, az egyes képekhez pedig miniatűröket szeretne létrehozni. Választhatná azt, hogy az ügyfeleknek a feltöltéskor várniuk kelljen a miniatűrök létrehozására. Alternatív megoldásként azonban használhat üzenetsort is. Amikor az ügyfél befejezi a feltöltést, írjon egy üzenetet a várólistára. Ezután egy Azure-függvénnyel hívja le az üzenetet az üzenetsorból, és hozza létre a miniatűrt. Ennek az eljárásnak minden egyes része külön méretezhető, és így szabadabban hangolhatja a felhasználási céloknak megfelelően.

További információ az Azure Queuesról: [Bevezetés az Azure Queues használatába](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Table Storage

Az Azure Table Storage mostantól az Azure Cosmos DB része. Az Azure Table Storage dokumentációját lásd: [Az Azure Table Storage áttekintése](../tables/table-storage-overview.md). A meglévő Azure Table Storage-szolgáltatáson kívül elérhető egy új Azure Cosmos DB Table API-ajánlat, amely teljesítményoptimalizált táblákat, globális elosztást és automatikus másodlagos indexeket is biztosít. Ha többet szeretne megtudni, és próbálja ki az új prémium szintű élményt, tekintse meg [Azure Cosmos DB Table API](../../cosmos-db/table-introduction.md).

További információ a Table Storage-ról: [Az Azure Table Storage áttekintése](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Lemezes tárolás

Az Azure Managed Disk egy virtuális merevlemez (VHD). Úgy gondolhatja, mint egy fizikai lemez egy helyszíni kiszolgálón, de virtualizált. Az Azure által felügyelt lemezek blobként tárolódnak, amelyek az Azure-ban véletlenszerűen kiválasztott IO Storage-objektumok. A felügyelt lemezeket nevezzük felügyelt, mivel ez egy absztrakt lap Blobok, blob-tárolók és Azure Storage-fiókok esetében. A felügyelt lemezekkel mindössze annyit kell tennie, hogy kiépíti a lemezt, az Azure pedig gondoskodik a többiről.

A felügyelt lemezekkel kapcsolatos további információkért lásd: [Bevezetés az Azure Managed Disks](../../virtual-machines/managed-disks-overview.md)szolgáltatásba.

## <a name="types-of-storage-accounts"></a>A tárfiókok típusai

Az Azure Storage számos különböző típusú Storage-fiókot kínál. Mindegyik típus támogatja a különböző funkciókat, és saját díjszabási modellel rendelkezik. További információ a Storage-fiókok típusairól: az [Azure Storage-fiók áttekintése](storage-account-overview.md).

## <a name="secure-access-to-storage-accounts"></a>Biztonságos hozzáférés a Storage-fiókokhoz

Minden Azure Storage-kérelemnek engedélyezve kell lennie. Az Azure Storage a következő engedélyezési módszereket támogatja:

- **Azure Active Directory (Azure AD) integráció a blob-és üzenetsor-adatkezeléshez.** Az Azure Storage támogatja az Azure AD-vel való hitelesítést és engedélyezést a blob-és üzenetsor-szolgáltatásokhoz az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával. A kérések Azure AD-vel való engedélyezése ajánlott a kiváló biztonság és a könnyű használat érdekében. További információ: az [Azure-blobok és-várólisták hozzáférésének engedélyezése Azure Active Directory használatával](storage-auth-aad.md).
- **Azure AD-hitelesítés az Azure Files SMB-en keresztül.** Azure Files támogatja az SMB-(Server Message Block) identitás-alapú hitelesítést Azure Active Directory Domain Services (Azure AD DS) vagy helyszíni Active Directory Domain Services (előzetes verzió) használatával. A tartományhoz csatlakoztatott Windows rendszerű virtuális gépek Azure AD-beli hitelesítő adatokkal érhetik el az Azure-fájlmegosztást. További információ: [Azure Files identitás-alapú hitelesítés támogatásának áttekintése az SMB-hozzáféréshez](../files/storage-files-active-directory-overview.md) és a [Azure Files központi telepítésének megtervezéséhez](../files/storage-files-planning.md#identity).
- **Hitelesítés megosztott kulccsal.** A Azure Storage Blob, fájlok, üzenetsor és Table Services támogatja a megosztott kulccsal való hitelesítést. A megosztott kulcsos hitelesítést használó ügyfelek minden, a Storage-fiók elérési kulcsával aláírt kérelemmel továbbítanak egy fejlécet. További információ: [Engedélyezés megosztott kulccsal](/rest/api/storageservices/authorize-with-shared-key).
- **Hitelesítés közös hozzáférésű aláírásokkal (SAS).** A közös hozzáférésű aláírás (SAS) egy olyan karakterlánc, amely egy olyan biztonsági jogkivonatot tartalmaz, amely egy tárolási erőforrás URI azonosítójának hozzáfűzésére használható. A biztonsági jogkivonat olyan korlátozásokat ágyaz be, mint az engedélyek és a hozzáférés intervalluma. További információ: [Shared Access Signatures (SAS) használata](storage-sas-overview.md).
- **A tárolók és Blobok névtelen hozzáférése.** Előfordulhat, hogy a tároló és a Blobok nyilvánosan elérhetők. Ha megadja, hogy a tároló vagy a blob nyilvános, akkor bárki névtelenül olvashatja. nincs szükség hitelesítésre. További információ: [Névtelen olvasási hozzáférés tárolók és Blobok kezelésére](../blobs/anonymous-read-access-configure.md).

## <a name="encryption"></a>Titkosítás

Az alapszintű tárolási szolgáltatásokhoz két alapvető fajta titkosítás érhető el. A biztonsággal és a titkosítással kapcsolatos további információkért lásd az [Azure Storage biztonsági útmutatóját](../blobs/security-recommendations.md).

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

Az Azure Storage-titkosítás megvédi és védi az adatait, hogy megfeleljen a szervezeti biztonsági és megfelelőségi kötelezettségeknek. Az Azure Storage automatikusan titkosítja az összes adathalmazt a Storage-fiók megőrzése előtt, és visszafejti azt a lekérés előtt. A titkosítás, a visszafejtés és a kulcskezelő folyamat átlátható a felhasználók számára. Az ügyfelek a Azure Key Vault használatával is dönthetnek saját kulcsaik kezeléséhez. További információ: az [Azure Storage titkosítása inaktív adatokhoz](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Ügyféloldali titkosítás

Az Azure Storage ügyféloldali kódtárai módszerekkel titkosítják az adatokat az ügyfél könyvtárából, mielőtt elküldené a hálózaton, és visszafejti a választ. Az ügyféloldali titkosításon keresztül titkosított adatok az Azure Storage szolgáltatásban is titkosítva vannak. Az ügyféloldali titkosítással kapcsolatos további információkért lásd: [ügyféloldali titkosítás az Azure Storage-hoz készült .net-](storage-client-side-encryption.md)tel.

## <a name="redundancy"></a>Redundancia

Az Azure Storage több példányban tárolja az adatait, így biztosítva, hogy az adatai tartósak legyenek. A Storage-fiók beállításakor ki kell választania egy redundancia beállítást. További információ: [Azure Storage-redundancia](./storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="transfer-data-to-and-from-azure-storage"></a>Adatok átvitele az Azure Storage-ba és onnan

Az Azure Storage-ba vagy az-ba való adatáthelyezésre több lehetőség is rendelkezésre áll. A választott lehetőség az adatkészlet méretétől és a hálózati sávszélességtől függ. További információ: Azure- [alapú megoldás kiválasztása adatátvitelhez](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Díjszabás

Az adattárolásra és-hozzáférésre vonatkozó döntések meghozatalakor figyelembe kell vennie az érintett költségeket is. További információ: az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).

## <a name="storage-apis-libraries-and-tools"></a>Storage API-k, kódtárak és eszközök

A Storage-fiókokban lévő erőforrásokat bármilyen, HTTP/HTTPS-kérelmeket elvégező nyelven érheti el. Emellett az alapvető Azure Storage-szolgáltatások számos népszerű nyelvhez biztosítanak programozási kódtárakat. Ezek a kódtárak sok szempontból leegyszerűsítik az Azure Storage használatát, mivel számos részletet kezelnek (például a szinkron és aszinkron hívás, műveletek kötegelése, kivételek kezelése, automatikus újrapróbálkozások, működési viselkedés stb.). Jelenleg a következő nyelvekhez és platformokhoz érhetők el kódtárak (a továbbiak összeállítása folyamatban):

### <a name="azure-storage-data-api-and-library-references"></a>Az Azure Storage adat API-ja és kódtárhivatkozásai

- [Azure Storage-REST API](/rest/api/storageservices/)
- [Azure Storage ügyféloldali kódtára a .NET-hez](/dotnet/api/overview/azure/storage)
- [Azure Storage ügyféloldali kódtár Java/Android rendszerhez](/java/api/overview/azure/storage)
- [Azure Storage ügyféloldali kódtár a Node.jshoz ](/javascript/api/overview/azure/storage-overview)
- [Az Azure Storage ügyféloldali kódtára a Pythonhoz](https://github.com/Azure/azure-storage-python)
- [Az Azure Storage ügyféloldali kódtára a PHP-hez](https://github.com/Azure/azure-storage-php)
- [Azure Storage ügyféloldali kódtár a Rubyhoz](https://github.com/Azure/azure-storage-ruby)
- [Azure Storage ügyféloldali kódtár a C++ nyelvhez](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Az Azure Storage felügyeleti API-ja és kódtárhivatkozásai

- [A Storage erőforrás-szolgáltató REST API-ja](/rest/api/storagerp/)
- [Storage erőforrás-szolgáltató ügyfél a .NET-hez](/dotnet/api/overview/azure/storage/management)
- [A Storage szolgáltatásfelügyelet REST API-ja](/previous-versions/azure/reference/ee460790(v=azure.100))

### <a name="azure-storage-data-movement-api-and-library-references"></a>Az Azure Storage adatátviteli API-ja és kódtárhivatkozásai

- [A Storage Import/Export szolgáltatás REST API-ja](/rest/api/storageimportexport/)
- [A Storage adatátviteli ügyféloldali kódtára a .NET-hez](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Eszközök és segédprogramok

- [Azure PowerShell-parancsmagok a Storage-hoz](/powershell/module/az.storage)
- [Azure CLI-parancsmagok a Storage-hoz](/cli/azure/storage)
- [AzCopy Command-Line segédprogram](https://aka.ms/downloadazcopy)
- Az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) a Microsoft ingyenes, önálló alkalmazása, amellyel vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
- [Azure Storage-sablonok Azure Resource Manager](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

## <a name="next-steps"></a>Következő lépések

Az alapvető Azure Storage-szolgáltatásokkal való üzembe helyezéssel kapcsolatban lásd: [Storage-fiók létrehozása](storage-account-create.md).
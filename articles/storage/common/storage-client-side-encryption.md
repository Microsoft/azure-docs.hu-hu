---
title: Titkosítás Client-Side a .NET-tel a Microsoft Azure Storagehoz | Microsoft Docs
description: Az Azure Storage .NET-hez készült ügyféloldali kódtára támogatja az ügyféloldali titkosítást és az Azure Key Vault-integrációt az Azure Storage-alkalmazások maximális biztonsága tekintetében.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/16/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 02607c219cf39a20a40854632e961b3ce199d0d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588256"
---
# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>Titkosítás és Azure Key Vault Client-Side Microsoft Azure Storage

[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Áttekintés

Az [Azure Storage .net-hez készült ügyféloldali kódtára](/dotnet/api/overview/azure/storage) támogatja az ügyfeleken belüli adattitkosítást az Azure Storage-ba való feltöltés előtt, és az adattitkosítást az ügyfélre való letöltéskor. A függvénytár támogatja a Storage-fiókok kulcsának felügyeletéhez [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) integrációt is.

A Blobok ügyféloldali titkosítással és Azure Key Vault használatával történő titkosításának folyamatán alapuló lépésenkénti oktatóanyagért lásd: [Blobok titkosítása és visszafejtése Microsoft Azure Storage a Azure Key Vault használatával](../blobs/storage-encrypt-decrypt-blobs-key-vault.md).

A Java-alapú ügyféloldali titkosítással kapcsolatban lásd: [ügyféloldali titkosítás a javával Microsoft Azure Storage](storage-client-side-encryption-java.md).

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Titkosítás és visszafejtés a boríték-technikán keresztül

A titkosítási és visszafejtési folyamatok követik a burkológörbe technikáját.

### <a name="encryption-via-the-envelope-technique"></a>Titkosítás a boríték-technikán keresztül

A titkosítás a burkológörbe technikán keresztül a következő módon működik:

1. Az Azure Storage ügyféloldali kódtára létrehoz egy Content encryption Key (CEK) kulcsot, amely egy egyszer használatos szimmetrikus kulcs.
2. A felhasználói adatai titkosítva vannak a CEK használatával.
3. Ezután a CEK (titkosított) a kulcs titkosítási kulcs (KEK) használatával burkolta. A KEK-et egy kulcs-azonosító azonosítja, és lehet egy aszimmetrikus kulcspár vagy egy szimmetrikus kulcs, és kezelhető helyileg, vagy az Azure Key Vaultban tárolható.

    Maga a Storage ügyféloldali kódtár soha nem fér hozzá a KEK-hez. A könyvtár meghívja a Key Vault által biztosított kulcs-körbefuttatási algoritmust. A felhasználók dönthetnek úgy, hogy egyéni szolgáltatókat használnak a kulcsok becsomagolásához/kicsomagolásához, ha szükséges.

4. Ezt követően a rendszer feltölti a titkosított fájlokat az Azure Storage szolgáltatásba. A burkolt kulcsot, valamint néhány további titkosítási metaadatot metaadatokként (blobon) vagy a titkosított adatokkal (Üzenetsor-üzenetek és tábla entitások) interpolált módon tárolja a rendszer.

### <a name="decryption-via-the-envelope-technique"></a>Visszafejtés a boríték-technikán keresztül

A burkológörbe technikán keresztüli visszafejtés a következő módon működik:

1. Az ügyféloldali kódtár feltételezi, hogy a felhasználó helyileg vagy az Azure Key Vaultban kezeli a kulcs titkosítási kulcsát (KEK). A felhasználónak nem kell tudnia a titkosításhoz használt konkrét kulcsot. Ehelyett egy olyan kulcs-feloldót lehet beállítani és használni, amely a kulcsok különböző kulcs-azonosítóit oldja fel.
2. Az ügyféloldali kódtár letölti a titkosított adatok mellett a szolgáltatásban tárolt összes titkosítási anyagot is.
3. Ezután a burkolt tartalom titkosítási kulcsát (CEK) a rendszer kicsomagolja (visszafejtve) a kulcs titkosítási kulcs (KEK) használatával. Ebben az esetben az ügyféloldali kódtár nem fér hozzá a KEK-hez. Egyszerűen meghívja az egyéni vagy Key Vault szolgáltató kicsomagolási algoritmusát.
4. A titkosító kulcs (CEK) használatával visszafejti a titkosított felhasználói adatokat.

## <a name="encryption-mechanism"></a>Titkosítási mechanizmus

A Storage ügyféloldali kódtára [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) -t használ a felhasználói adattartalom titkosításához. Pontosabban, AES-sel rendelkező [titkosítási blokkoló (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) mód. Az egyes szolgáltatások némileg eltérően működnek, ezért ezeket itt fogjuk megbeszélni.

### <a name="blobs"></a>Blobok

Az ügyféloldali kódtár jelenleg csak a teljes Blobok titkosítását támogatja. A letöltések esetében a teljes és a tartományra vonatkozó letöltések is támogatottak.

A titkosítás során az ügyfél-függvénytár 16 bájtos véletlenszerű inicializálási vektort (IV) állít elő, amely egy 32 bájtos véletlenszerű tartalom-titkosítási kulccsal (CEK), valamint a Blobok adatainak ezen információk használatával történő titkosítását is elvégezheti. A burkolt CEK és néhány további titkosítási metaadat ezután blob-metaadatokként tárolódik a szolgáltatás titkosított blobja mellett.

> [!WARNING]
> Ha a blobhoz saját metaadatokat szerkeszt vagy tölt fel, gondoskodnia kell arról, hogy a metaadatok megmaradjanak. Ha a metaadatok nélkül tölt fel új metaadatokat, a burkolt CEK, IV és egyéb metaadatok elvesznek, és a blob tartalma soha nem lesz lekérdezhető.

Egy teljes blob letöltésekor a burkolt CEK kicsomagolása és használata a IV-vel együtt (ebben az esetben a blob-metaadatokban tárolódik), hogy visszaállítsa a visszafejtett adatokat a felhasználók számára.

A titkosított blob tetszőleges tartományának letöltése magában foglalja a felhasználók által megadott tartomány beállítását, hogy egy kis mennyiségű további adat kerüljön beszerzésre, amely a kért tartomány sikeres visszafejtéséhez használható.

Az összes blob-típus (Blobok, blobok és hozzáfűző Blobok) titkosítása/visszafejtése a séma használatával lehetséges.

### <a name="queues"></a>Üzenetsorok

Mivel a üzenetsor-üzenetek bármilyen formátumúak lehetnek, az ügyféloldali kódtár egyéni formátumot határoz meg, amely tartalmazza az inicializálási vektort (IV) és a titkosított tartalom titkosítási kulcsát (CEK) az üzenet szövegeként.

A titkosítás során az ügyféloldali kódtár a 16 bájtos véletlenszerű CEK, valamint a 32 bájtos véletlenszerű adatmennyiséget, valamint az üzenetsor-üzenet szövegének boríték-titkosítását használja ezen információk alapján. A burkolt CEK és néhány további titkosítási metaadat hozzá lesz adva a titkosított üzenetsor-üzenethez. Ezt a módosított üzenetet (alább látható) a szolgáltatás tárolja.

```xml
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

A visszafejtés során a rendszer kinyeri a beburkolt kulcsot az üzenetsor-üzenetből, és csomagolja ki. A rendszer kinyeri a IV-t az üzenetsor-üzenetből is, és a nem burkolt kulccsal együtt használja az üzenetsor-üzenet adatainak visszafejtéséhez. Vegye figyelembe, hogy a titkosítási metaadatok kis méretűek (500 bájtnál), így amíg a várólista-üzenet 64 kb-korlátja megesik, a hatásnak kezelhető kell lennie. Vegye figyelembe, hogy a titkosított üzenet Base64 kódolású lesz, ahogy az a fenti kódrészletben is látható, amely az elküldött üzenet méretét is kibővíti.

### <a name="tables"></a>Táblázatok

> [!NOTE]
> A Table service csak a 9. x verzióban támogatott az Azure Storage ügyféloldali kódtáraban.

Az ügyféloldali kódtár támogatja a INSERT és a Replace műveletekhez tartozó entitás-tulajdonságok titkosítását.

> [!NOTE]
> Az egyesítés jelenleg nem támogatott. Mivel a tulajdonságok egy részhalmaza korábban egy másik kulccsal lett titkosítva, egyszerűen az új tulajdonságok egyesítése és a metaadatok frissítése adatvesztést eredményezhet. Az egyesítéshez szükség van további szolgáltatási hívásokra, hogy beolvassa a szolgáltatásból a már meglévő entitást, vagy egy új kulcsot használjon egy tulajdonságban, amelyek közül mindkettő nem alkalmas a teljesítményre.

A tábla adattitkosítása a következőképpen működik:  

1. A felhasználók a titkosítani kívánt tulajdonságokat határozzák meg.
2. Az ügyféloldali kódtár létrehoz egy véletlenszerű inicializálási vektort (IV) 16 bájttal, valamint egy véletlenszerű Content encryption Key (CEK) 32 bájtot minden entitáshoz, és a borítékok titkosítását is végrehajtja az egyes tulajdonságokon, hogy az új IV/tulajdonságot származtatva titkosítsa. A titkosított tulajdonság bináris adatként van tárolva.
3. A burkolt CEK és néhány további titkosítási metaadatot a rendszer két további fenntartott tulajdonságként tárolja. Az első fenntartott tulajdonság (_ClientEncryptionMetadata1) egy karakterlánc-tulajdonság, amely a IV, a verzió és a becsomagolt kulcs adatait tartalmazza. A második fenntartott tulajdonság (_ClientEncryptionMetadata2) egy bináris tulajdonság, amely a titkosított tulajdonságokkal kapcsolatos információkat tartalmazza. A második tulajdonság (_ClientEncryptionMetadata2) adatai titkosítva vannak.
4. A titkosításhoz szükséges további fenntartott tulajdonságok miatt a felhasználók mostantól csak 250 egyéni tulajdonságokkal rendelkezhetnek 252 helyett. Az entitás teljes méretének 1 MB-nál kisebbnek kell lennie.

Vegye figyelembe, hogy csak a karakterlánc-tulajdonságok titkosíthatók. Ha más típusú tulajdonságokat kíván titkosítani, azokat karakterlánccá kell konvertálni. A titkosított karakterláncok a szolgáltatásban bináris tulajdonságokként tárolódnak, és a visszafejtés után vissza lesznek konvertálva karakterlánccá.

A táblák esetében a titkosítási házirenden kívül a felhasználóknak meg kell adniuk a titkosítani kívánt tulajdonságokat. Ezt megteheti egy [EncryptProperty] attribútum megadásával (a TableEntity-ből származó POCO-entitások esetén) vagy a kérési beállításokban található titkosítási feloldóval. A titkosítási feloldó egy olyan delegált, amely egy partíciós kulcsot, egy sor kulcsot és egy tulajdonság nevét veszi át, és egy logikai értéket ad vissza, amely jelzi, hogy a tulajdonságot titkosítani kell-e. A titkosítás során az ügyféloldali kódtár ezeket az információkat fogja használni annak eldöntéséhez, hogy a tulajdonságot titkosítani kell-e a drótba való írás során. A delegált emellett a tulajdonságok titkosítását is lehetővé teszi. (Ha például X, akkor titkosítsa az A tulajdonságot, máskülönben az A és B tulajdonságokat titkosítja.) Vegye figyelembe, hogy az entitások olvasása vagy lekérdezése során nem szükséges megadnia ezeket az információkat.

### <a name="batch-operations"></a>Batch-műveletek

A Batch-műveletekben ugyanezt a KEK-et fogja használni a Batch-művelet összes sorában, mert az ügyféloldali kódtár csak egy Options objektumot (és így egy házirendet/KEK-t) engedélyez a Batch-műveletekben. Az ügyféloldali kódtár azonban belsőleg létrehozza az új véletlenszerű IV és véletlenszerű CEK a kötegben. A felhasználók úgy is dönthetnek, hogy a kötegben lévő összes művelethez különböző tulajdonságokat titkosítanak. ehhez a viselkedést a titkosítási feloldóban kell meghatározni.

### <a name="queries"></a>Lekérdezések

> [!NOTE]
> Mivel az entitások titkosítva vannak, nem futtathat olyan lekérdezéseket, amelyek egy titkosított tulajdonságra szűrnek.  Ha megpróbálják, az eredmények helytelenek lesznek, mivel a szolgáltatás nem titkosított adattal próbálta összehasonlítani a titkosított adatmennyiséget.
>
> A lekérdezési műveletek végrehajtásához meg kell adnia egy kulcs-feloldót, amely képes az eredményhalmaz összes kulcsának feloldására. Ha a lekérdezési eredményben szereplő entitás nem oldható fel szolgáltatóhoz, akkor az ügyféloldali kódtár hibát jelez. A kiszolgálóoldali kivetítéseket végrehajtó lekérdezések esetében az ügyféloldali kódtár alapértelmezés szerint a kijelölt oszlopokhoz adja hozzá a speciális titkosítási metaadatok tulajdonságait (_ClientEncryptionMetadata1 és _ClientEncryptionMetadata2).

## <a name="azure-key-vault"></a>Azure Key Vault

Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A Azure Key Vault használatával a felhasználók titkosítják a kulcsokat és a titkos kulcsokat (például a hitelesítési kulcsokat, a Storage-fiók kulcsait, az adattitkosítási kulcsokat). PFX-fájlok és jelszavak) a hardveres biztonsági modulok (HSM-EK) által védett kulcsok használatával. További információ: [Mi az Azure Key Vault?](../../key-vault/general/overview.md).

A Storage ügyféloldali kódtár az Alapkönyvtár Key Vault felületét használja ahhoz, hogy közös keretrendszert biztosítson az Azure-ban a kulcsok kezeléséhez. A felhasználók igénybe vehetik az általuk nyújtott további előnyökre Key Vault kódtárakat, például az egyszerű és a zökkenőmentes szimmetrikus/RSA helyi és a Felhőbeli kulcstartók hasznos funkcióit, valamint segítséget nyújtanak az összesítéshez és a gyorsítótárazáshoz.

### <a name="interface-and-dependencies"></a>Felület és függőségek

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Két szükséges csomag Key Vault integrációhoz:

* Az Azure. Core tartalmazza a `IKeyEncryptionKey` és a `IKeyEncryptionKeyResolver` felületet. A Storage ügyféloldali kódtára a .NET-hez már meg van határozva függőségként.
* Az Azure. Security. kulcstartó. Keys (v4. x) tartalmazza a Key Vault REST-ügyfelet, valamint az ügyféloldali titkosításhoz használt kriptográfiai ügyfeleket.

A Key Vault a nagy értékű főkulcsok számára készült, és az egyes Key Vault sávszélesség-szabályozási korlátját szem előtt tartva tervezték. Az Azure. Security. kulcstartó. Keys 4.1.0 nem rendelkezik olyan `IKeyEncryptionKeyResolver` implementációval, amely támogatja a kulcsok gyorsítótárazását. A gyorsítótárazásnak a szabályozás miatt szükségesnek kell lennie, [ezt a mintát](/samples/azure/azure-sdk-for-net/azure-key-vault-proxy/) követheti egy gyorsítótárazási réteg behelyezése egy `Azure.Security.KeyVault.Keys.Cryptography.KeyResolver` példányba.

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Három Key Vault csomag létezik:

* A Microsoft. Azure. kulcstartó. Core tartalmazza a Rendszerállapotkulcsot és a IKeyResolver. Ez egy kis csomag, amely nem rendelkezik függőségekkel. A Storage ügyféloldali kódtára a .NET-hez definiálja függőségként.
* A Microsoft. Azure. kulcstartó (v3. x) tartalmazza a Key Vault REST-ügyfelet.
* A Microsoft. Azure. kulcstartó. Extensions (v3. x) olyan bővítményi kódot tartalmaz, amely titkosítási algoritmusok, valamint egy RSAKey és egy SymmetricKey implementációit tartalmazza. Ez az alapszintű és a kulcstartó névtertől függ, és funkciókat biztosít az összesített feloldó definiálásához (ha a felhasználók több Key providert kívánnak használni) és a gyorsítótárazási kulcs feloldóját. Bár a Storage ügyféloldali kódtár nem függ közvetlenül ettől a csomagtól, ha a felhasználók Azure Key Vault szeretnék tárolni a kulcsaikat, vagy hogy a Key Vault-bővítményeket használják a helyi és a felhőalapú titkosítási szolgáltatók felhasználásához, erre a csomagra lesz szükségük.

A Key Vault a nagy értékű főkulcsok számára készült, és az egyes Key Vault sávszélesség-szabályozási korlátját szem előtt tartva tervezték. Ha Key Vault használatával ügyféloldali titkosítást végez, az előnyben részesített modell a titokként tárolt szimmetrikus főkulcsok használata Key Vault és helyi gyorsítótárban. A felhasználóknak a következőket kell tenniük:

1. Hozzon létre egy titkos kulcsot, és töltse fel Key Vaultba.
2. A titkos kulcs alapazonosítójának használatával oldja fel a titkosítás jelenlegi verzióját, és gyorsítótárazza ezeket az információkat helyileg. CachingKeyResolver használata a gyorsítótárazáshoz; a felhasználók nem várhatóan saját gyorsítótárazási logikát implementálnak.
3. Használja a gyorsítótárazási feloldót bemenetként a titkosítási házirend létrehozásakor.

A v11 Key Vault használatáról további információt a [v11 titkosítási kód mintáit](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples)ismertető témakörben talál.

---

## <a name="best-practices"></a>Ajánlott eljárások

A titkosítási támogatás csak a .NET-hez készült Storage ügyféloldali kódtáraban érhető el. Windows Phone-telefon és Windows-futtatókörnyezet jelenleg nem támogatja a titkosítást.

> [!IMPORTANT]
> Az ügyféloldali titkosítás használatakor vegye figyelembe ezeket a fontos pontokat:
>
> * Titkosított blobba való olvasáskor vagy az azokból való íráskor használja az egész blob feltöltési parancsokat és a tartomány/teljes blob letöltési parancsot. Kerülje a titkosított blobba való írást olyan protokollok használatával, mint a Put blokk, a letiltási lista, a lapok írása, az oldalak törlése vagy a hozzáfűzési blokk; Ellenkező esetben előfordulhat, hogy a titkosított blob sérült, és nem olvasható.
> * A táblákhoz hasonló korlátozás létezik. Ügyeljen arra, hogy ne frissítse a titkosított tulajdonságokat a titkosítási metaadatok frissítése nélkül.
> * Ha a titkosított blobon beállítja a metaadatokat, felülírhatja a titkosítással kapcsolatos metaadatokat a visszafejtéshez, mivel a metaadatok beállítása nem adalékanyag. Ez a pillanatképek esetében is igaz. Kerülje a metaadatok megadását egy titkosított blob pillanatképének létrehozása közben. Ha be kell állítani a metaadatokat, először hívja meg a **FetchAttributes** metódust, hogy lekérje a jelenlegi titkosítási metaadatokat, és elkerülje a párhuzamos írásokat a metaadatok beállításakor.
> * Engedélyezze a **RequireEncryption** tulajdonságot az alapértelmezett kérési beállításokban azon felhasználók számára, akik csak titkosított adattal működnek. További információért lásd alább.

## <a name="client-api--interface"></a>Ügyfél API/Interface

A felhasználók csak egy kulcsot, csak egy feloldót vagy mindkettőt biztosíthatnak. A kulcsok azonosítása a kulcs azonosítójának használatával történik, és a becsomagolás/kicsomagolás logikáját biztosítja. A rendszer feloldókat használ a kulcsok feloldására a visszafejtési folyamat során. Definiál egy feloldási metódust, amely egy kulcs azonosítóját adja vissza. Ez lehetővé teszi a felhasználók számára, hogy több helyen felügyelt kulcsok közül választhatnak.

* A titkosításhoz mindig a kulcsot használja a rendszer, és a kulcs hiánya hibát eredményez.
* Visszafejtéshez:
  * Ha a kulcs meg van adva, és az azonosítója megegyezik a szükséges kulcs-azonosítóval, a rendszer ezt a kulcsot használja a visszafejtéshez. Ellenkező esetben a rendszer megkísérli a feloldót. Ha nincs feloldó ehhez a kísérlethez, a rendszer hibát jelez.
  * Ha meg van adva a kulcs, a kulcs feloldója meghívásra kerül. Ha a feloldó meg van adva, de nem rendelkezik leképezéssel a kulcs-azonosítóhoz, a rendszer hibát jelez.

### <a name="requireencryption-mode-v11-only"></a>RequireEncryption mód (csak v11)

A felhasználók opcionálisan engedélyezhetik a művelet módját, ahol a feltöltéseket és a letöltéseket titkosítani kell. Ebben a módban az adatok titkosítási házirend nélkül tölthetők fel, vagy a szolgáltatásban nem titkosított adatok letöltése sikertelen lesz az ügyfélen. A kérési beállítások objektum **RequireEncryption** tulajdonsága ezt a viselkedést vezérli. Ha az alkalmazás az Azure Storage-ban tárolt összes objektumot titkosítani fogja, akkor a **RequireEncryption** tulajdonságot a szolgáltatás ügyféloldali objektumához tartozó alapértelmezett kérési beállításoknál állíthatja be. Állítsa be például a **CloudBlobClient. DefaultRequestOptions. RequireEncryption** értéket az **igaz** értékre, hogy titkosítást igényel az adott ügyfélalkalmazás által végrehajtott összes blob-művelethez.

### <a name="blob-service-encryption"></a>Titkosítás Blob service

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Hozzon létre egy **ClientSideEncryptionOptions** objektumot, és állítsa be az ügyfél-létrehozáshoz a **SpecializedBlobClientOptions**. API-alapon nem állíthatók be titkosítási beállítások. Minden mást az ügyféloldali kódtár fog kezelni belsőleg.

```csharp
// Your key and key resolver instances, either through KeyVault SDK or an external implementation
IKeyEncryptionKey key;
IKeyEncryptionKeyResolver keyResolver;

// Create the encryption options to be used for upload and download.
ClientSideEncryptionOptions encryptionOptions = new ClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Set the encryption options on the client options
BlobClientOptions options = new SpecializedBlobClientOptions() { ClientSideEncryption = encryptionOptions };

// Get your blob client with client-side encryption enabled.
// Client-side encryption options are passed from service to container clients, and container to blob clients.
// Attempting to construct a BlockBlobClient, PageBlobClient, or AppendBlobClient from a BlobContainerClient
// with client-side encryption options present will throw, as this functionality is only supported with BlobClient.
BlobClient blob = new BlobServiceClient(connectionString, options).GetBlobContainerClient("my-container").GetBlobClient("myBlob");

// Upload the encrypted contents to the blob.
blob.Upload(stream);

// Download and decrypt the encrypted contents from the blob.
MemoryStream outputStream = new MemoryStream();
blob.DownloadTo(outputStream);
```

A titkosítási beállítások alkalmazásához nem szükséges **BlobServiceClient** . Átadhatók olyan **BlobContainerClient** / **BlobClient** konstruktoroknak is, amelyek elfogadják a **BlobClientOptions** objektumokat.

Ha egy kívánt **BlobClient** objektum már létezik, de ügyféloldali titkosítási beállítások nélkül van, egy bővítményi metódus létezik az objektum egy példányának létrehozásához a megadott **ClientSideEncryptionOptions**. Ez a kiterjesztési módszer elkerüli az új **BlobClient** -objektum elejétől való felépítési terhelést.

```csharp
using Azure.Storage.Blobs.Specialized;

// Your existing BlobClient instance and encryption options
BlobClient plaintextBlob;
ClientSideEncryptionOptions encryptionOptions;

// Get a copy of plaintextBlob that uses client-side encryption
BlobClient clientSideEncryptionBlob = plaintextBlob.WithClientSideEncryptionOptions(encryptionOptions);
```

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Hozzon létre egy **BlobEncryptionPolicy** objektumot, és állítsa be a kérési beállítások között (API-ban vagy ügyféloldali szinten a **DefaultRequestOptions** használatával). Minden mást az ügyféloldali kódtár fog kezelni belsőleg.

```csharp
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

// Set the encryption policy on the request options.
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Upload the encrypted contents to the blob.
blob.UploadFromStream(stream, size, null, options, null);

// Download and decrypt the encrypted contents from the blob.
MemoryStream outputStream = new MemoryStream();
blob.DownloadToStream(outputStream, null, options, null);
```

---

### <a name="queue-service-encryption"></a>Titkosítás Queue szolgáltatás

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Hozzon létre egy **ClientSideEncryptionOptions** objektumot, és állítsa be az ügyfél-létrehozáshoz a **SpecializedQueueClientOptions**. API-alapon nem állíthatók be titkosítási beállítások. Minden mást az ügyféloldali kódtár fog kezelni belsőleg.

```csharp
// Your key and key resolver instances, either through KeyVault SDK or an external implementation
IKeyEncryptionKey key;
IKeyEncryptionKeyResolver keyResolver;

// Create the encryption options to be used for upload and download.
ClientSideEncryptionOptions encryptionOptions = new ClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Set the encryption options on the client options
QueueClientOptions options = new SpecializedQueueClientOptions() { ClientSideEncryption = encryptionOptions };

// Get your queue client with client-side encryption enabled.
// Client-side encryption options are passed from service to queue clients.
QueueClient queue = new QueueServiceClient(connectionString, options).GetQueueClient("myQueue");

// Send an encrypted queue message.
queue.SendMessage("Hello, World!");

// Download queue messages, decrypting ones that are detected to be encrypted
QueueMessage[] queue.ReceiveMessages(); 
```

A titkosítási beállítások alkalmazásához nem szükséges **QueueServiceClient** . A **QueueClientOptions** objektumokat fogadó **QueueClient** -konstruktorokban is átadhatók.

Ha egy kívánt **QueueClient** objektum már létezik, de ügyféloldali titkosítási beállítások nélkül van, egy bővítményi metódus létezik az objektum egy példányának létrehozásához a megadott **ClientSideEncryptionOptions**. Ez a kiterjesztési módszer elkerüli az új **QueueClient** -objektum elejétől való felépítési terhelést.

```csharp
using Azure.Storage.Queues.Specialized;

// Your existing QueueClient instance and encryption options
QueueClient plaintextQueue;
ClientSideEncryptionOptions encryptionOptions;

// Get a copy of plaintextQueue that uses client-side encryption
QueueClient clientSideEncryptionQueue = plaintextQueue.WithClientSideEncryptionOptions(encryptionOptions);
```

Egyes felhasználók olyan várólistákkal rendelkezhetnek, amelyekben nem sikerült az összes fogadott üzenet visszafejtése, és a kulcsnak vagy a feloldónak el kell dobnia. Ebben az esetben a fenti példa utolsó sora jelenik meg, és a fogadott üzenetek egyike sem lesz elérhető. Ezekben a forgatókönyvekben az alosztály **QueueClientSideEncryptionOptions** használhatók az ügyfelek titkosítási lehetőségeinek biztosításához. Olyan Event **DecryptionFailed** tesz elérhetővé, amely akkor aktiválódik, amikor egy üzenetsor-üzenetet nem lehet visszafejteni, amíg legalább egy hívást felvettek az eseményre. Az önálló sikertelen üzenetek így kezelhetők, és a rendszer kiszűri a **ReceiveMessages** által visszaadott utolsó **QueueMessage []** .

```csharp
// Create your encryption options using the sub-class.
QueueClientSideEncryptionOptions encryptionOptions = new QueueClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Add a handler to the DecryptionFailed event.
encryptionOptions.DecryptionFailed += (source, args) => {
   QueueMessage failedMessage = (QueueMessage)source;
   Exception exceptionThrown = args.Exception;
   // do something
};

// Use these options with your client objects.
QueueClient queue = new QueueClient(connectionString, queueName, new SpecializedQueueClientOptions()
{
   ClientSideEncryption = encryptionOptions
});

// Retrieve 5 messages from the queue.
// Assume 5 messages come back and one throws during decryption.
QueueMessage[] messages = queue.ReceiveMessages(maxMessages: 5).Value;
Debug.Assert(messages.Length == 4)
```

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Hozzon létre egy **QueueEncryptionPolicy** objektumot, és állítsa be a kérési beállítások között (API-ban vagy ügyféloldali szinten a **DefaultRequestOptions** használatával). Minden mást az ügyféloldali kódtár fog kezelni belsőleg.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

 // Add message
 QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
 queue.AddMessage(message, null, null, options, null);

 // Retrieve message
 CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);
```

---

### <a name="table-service-encryption-v11-only"></a>Table service titkosítás (csak v11)

A titkosítási szabályzat létrehozása és a kérési beállítások megadása mellett meg kell adnia egy **EncryptionResolver** a **TableRequestOptions**-ben, vagy az entitáson a [EncryptProperty] attribútumot kell beállítania.

#### <a name="using-the-resolver"></a>A feloldó használata

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

 TableRequestOptions options = new TableRequestOptions()
 {
    EncryptionResolver = (pk, rk, propName) =>
     {
        if (propName == "foo")
         {
            return true;
         }
         return false;
     },
     EncryptionPolicy = policy
 };

 // Insert Entity
 currentTable.Execute(TableOperation.Insert(ent), options, null);

 // Retrieve Entity
 // No need to specify an encryption resolver for retrieve
 TableRequestOptions retrieveOptions = new TableRequestOptions()
 {
    EncryptionPolicy = policy
 };

 TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
 TableResult result = currentTable.Execute(operation, retrieveOptions, null);
```

#### <a name="using-attributes"></a>Attribútumok használata

Ahogy fent említettük, ha az entitás TableEntity valósít meg, akkor a tulajdonságok a [EncryptProperty] attribútummal is megadhatók a **EncryptionResolver** megadása helyett.

```csharp
[EncryptProperty]
 public string EncryptedProperty1 { get; set; }
```

## <a name="encryption-and-performance"></a>Titkosítás és teljesítmény

Vegye figyelembe, hogy a tárolási adatokat a rendszer további teljesítménybeli terhelést eredményez. A tartalmi kulcsot és a IV-t elő kell állítani, a tartalmat titkosítani kell, és további metaadatokat kell formázni és feltölteni. Ez a terhelés a titkosított adatmennyiségtől függően eltérő lesz. Javasoljuk, hogy az ügyfelek mindig tesztelje az alkalmazásaikat a fejlesztés során.

## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: Blobok titkosítása és visszafejtése Microsoft Azure Storage használatával Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)
* Töltse le az [Azure Storage ügyféloldali kódtárat a .net NuGet-csomaghoz](https://www.nuget.org/packages/WindowsAzure.Storage)
* A Azure Key Vault NuGet [Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [Client](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)és [Extensions](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/) csomagok letöltése  
* A [Azure Key Vault dokumentációjának](../../key-vault/general/overview.md) felkeresése
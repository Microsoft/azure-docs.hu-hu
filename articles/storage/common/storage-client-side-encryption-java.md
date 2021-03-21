---
title: Client-Side titkosítás a Javával Microsoft Azure Storagehoz | Microsoft Docs
description: A Javához készült Azure Storage ügyféloldali kódtára támogatja az ügyféloldali titkosítást és az Azure Key Vault az Azure Storage-alkalmazások maximális biztonságához.
services: storage
author: tamram
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-java
ms.openlocfilehash: 78baaa3f794bed870b40fb3975f6b80ff37e90f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043728"
---
# <a name="client-side-encryption-and-azure-key-vault-with-java-for-microsoft-azure-storage"></a>Client-Side titkosítás és Azure Key Vault Javával Microsoft Azure Storage
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Áttekintés
A [Javához készült Azure Storage ügyféloldali kódtára](https://search.maven.org/artifact/com.azure/azure-storage-blob-cryptography) támogatja az ügyfeleken belüli adattitkosítást az Azure Storage-ba való feltöltés előtt, és az adatvisszafejtést az ügyfélre való letöltés során. A függvénytár támogatja a Storage-fiókok kulcsának felügyeletéhez [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) integrációt is.

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
Az ügyféloldali kódtár jelenleg csak a teljes Blobok titkosítását támogatja. A titkosítás akkor támogatott, ha a felhasználók a **feltöltés** _ metódusait vagy a _ *openOutputStream** metódust használják. A letöltések esetében a teljes és a tartományra vonatkozó letöltések is támogatottak.  

A titkosítás során az ügyfél-függvénytár 16 bájtos véletlenszerű inicializálási vektort (IV) állít elő, amely egy 32 bájtos véletlenszerű tartalom-titkosítási kulccsal (CEK), valamint a Blobok adatainak ezen információk használatával történő titkosítását is elvégezheti. A burkolt CEK és néhány további titkosítási metaadat ezután blob-metaadatokként tárolódik a szolgáltatás titkosított blobja mellett.

> [!WARNING]
> Ha a blobhoz saját metaadatokat szerkeszt vagy tölt fel, gondoskodnia kell arról, hogy a metaadatok megmaradjanak. Ha a metaadatok nélkül tölt fel új metaadatokat, a becsomagolt CEK, IV és egyéb metaadatok elvesznek, és a blob tartalma soha nem lesz lekérdezhető.
> 
> 

A titkosított Blobok letöltése magában foglalja a teljes blob tartalmának lekérését a **letöltési** / **openInputStream** kényelmi módszereinek használatával. A burkolt CEK nincs becsomagolva és együtt használva a IV (ebben az esetben a blob-metaadatokban tárolt), hogy visszaállítsa a visszafejtett adatokat a felhasználók számára.

Egy tetszőleges tartomány (**downloadRange** metódus) a titkosított blobban való letöltése magában foglalja a felhasználók által megadott tartomány beállítását, hogy egy kis mennyiségű további adat kapjon segítséget, amely a kért tartomány sikeres visszafejtéséhez használható.  

Az összes blob-típus (Blobok, blobok és hozzáfűző Blobok) titkosítása/visszafejtése a séma használatával lehetséges.

### <a name="queues"></a>Üzenetsorok
Mivel a üzenetsor-üzenetek bármilyen formátumúak lehetnek, az ügyféloldali kódtár egyéni formátumot határoz meg, amely tartalmazza az inicializálási vektort (IV) és a titkosított tartalom titkosítási kulcsát (CEK) az üzenet szövegeként.  

A titkosítás során az ügyféloldali kódtár a 16 bájtos véletlenszerű CEK, valamint a 32 bájtos véletlenszerű adatmennyiséget, valamint az üzenetsor-üzenet szövegének boríték-titkosítását használja ezen információk alapján. A burkolt CEK és néhány további titkosítási metaadat hozzá lesz adva a titkosított üzenetsor-üzenethez. Ezt a módosított üzenetet (alább látható) a szolgáltatás tárolja.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

A visszafejtés során a rendszer kinyeri a beburkolt kulcsot az üzenetsor-üzenetből, és csomagolja ki. A rendszer kinyeri a IV-t az üzenetsor-üzenetből is, és a nem burkolt kulccsal együtt használja az üzenetsor-üzenet adatainak visszafejtéséhez. Vegye figyelembe, hogy a titkosítási metaadatok kis méretűek (500 bájtnál), így amíg a várólista-üzenet 64 kb-korlátja megesik, a hatásnak kezelhető kell lennie.

### <a name="tables"></a>Táblázatok
Az ügyféloldali kódtár támogatja a INSERT és a Replace műveletekhez tartozó entitás-tulajdonságok titkosítását.

> [!NOTE]
> Az egyesítés jelenleg nem támogatott. Mivel a tulajdonságok egy részhalmaza korábban egy másik kulccsal lett titkosítva, egyszerűen az új tulajdonságok egyesítése és a metaadatok frissítése adatvesztést eredményezhet. Az egyesítéshez szükség van további szolgáltatási hívásokra, hogy beolvassa a szolgáltatásból a már meglévő entitást, vagy egy új kulcsot használjon egy tulajdonságban, amelyek közül mindkettő nem alkalmas a teljesítményre.
> 
> 

A tábla adattitkosítása a következőképpen működik:  

1. A felhasználók a titkosítani kívánt tulajdonságokat határozzák meg.  
2. Az ügyféloldali kódtár létrehoz egy véletlenszerű inicializálási vektort (IV) 16 bájttal, valamint egy véletlenszerű Content encryption Key (CEK) 32 bájtot minden entitáshoz, és a borítékok titkosítását is végrehajtja az egyes tulajdonságokon, hogy az új IV/tulajdonságot származtatva titkosítsa. A titkosított tulajdonság bináris adatként van tárolva.  
3. A burkolt CEK és néhány további titkosítási metaadatot a rendszer két további fenntartott tulajdonságként tárolja. Az első fenntartott tulajdonság (_ClientEncryptionMetadata1) egy karakterlánc-tulajdonság, amely a IV, a verzió és a becsomagolt kulcs adatait tartalmazza. A második fenntartott tulajdonság (_ClientEncryptionMetadata2) egy bináris tulajdonság, amely a titkosított tulajdonságokkal kapcsolatos információkat tartalmazza. A második tulajdonság (_ClientEncryptionMetadata2) adatai titkosítva vannak.  
4. A titkosításhoz szükséges további fenntartott tulajdonságok miatt a felhasználók mostantól csak 250 egyéni tulajdonságokkal rendelkezhetnek 252 helyett. Az entitás teljes méretének 1 MB-nál kisebbnek kell lennie.  
   
   Vegye figyelembe, hogy csak a karakterlánc-tulajdonságok titkosíthatók. Ha más típusú tulajdonságokat kíván titkosítani, azokat karakterlánccá kell konvertálni. A titkosított karakterláncok a szolgáltatásban bináris tulajdonságokként tárolódnak, és a visszafejtés után vissza lesznek konvertálva karakterlánccá.
   
   A táblák esetében a titkosítási házirenden kívül a felhasználóknak meg kell adniuk a titkosítani kívánt tulajdonságokat. Ezt megteheti a [titkosítás] attribútum megadásával (a TableEntity származó POCO-entitások esetében) vagy a kérési beállítások titkosítási feloldóval. A titkosítási feloldó egy olyan delegált, amely egy partíciós kulcsot, egy sor kulcsot és egy tulajdonság nevét veszi át, és egy logikai értéket ad vissza, amely jelzi, hogy a tulajdonságot titkosítani kell-e. A titkosítás során az ügyféloldali kódtár ezeket az információkat fogja használni annak eldöntéséhez, hogy a tulajdonságot titkosítani kell-e a drótba való írás során. A delegált emellett a tulajdonságok titkosítását is lehetővé teszi. (Ha például X, akkor titkosítsa az A tulajdonságot, máskülönben az A és B tulajdonságokat titkosítja.) Vegye figyelembe, hogy az entitások olvasása vagy lekérdezése során nem szükséges megadnia ezeket az információkat.

### <a name="batch-operations"></a>Batch-műveletek
A Batch-műveletekben ugyanezt a KEK-et fogja használni a Batch-művelet összes sorában, mert az ügyféloldali kódtár csak egy Options objektumot (és így egy házirendet/KEK-t) engedélyez a Batch-műveletekben. Az ügyféloldali kódtár azonban belsőleg létrehozza az új véletlenszerű IV és véletlenszerű CEK a kötegben. A felhasználók úgy is dönthetnek, hogy a kötegben lévő összes művelethez különböző tulajdonságokat titkosítanak. ehhez a viselkedést a titkosítási feloldóban kell meghatározni.

### <a name="queries"></a>Lekérdezések
> [!NOTE]
> Mivel az entitások titkosítva vannak, nem futtathat olyan lekérdezéseket, amelyek egy titkosított tulajdonságra szűrnek.  Ha megpróbálják, az eredmények helytelenek lesznek, mivel a szolgáltatás nem titkosított adattal próbálta összehasonlítani a titkosított adatmennyiséget.
> 
> 
> A lekérdezési műveletek végrehajtásához meg kell adnia egy kulcs-feloldót, amely képes az eredményhalmaz összes kulcsának feloldására. Ha a lekérdezési eredményben szereplő entitás nem oldható fel szolgáltatóhoz, akkor az ügyféloldali kódtár hibát jelez. A kiszolgálóoldali kivetítéseket végrehajtó lekérdezések esetében az ügyféloldali kódtár alapértelmezés szerint hozzáadja a speciális titkosítási metaadatok tulajdonságait (_ClientEncryptionMetadata1 és _ClientEncryptionMetadata2) a kijelölt oszlopokhoz.

## <a name="azure-key-vault"></a>Azure Key Vault
Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A Azure Key Vault használatával a felhasználók titkosítják a kulcsokat és a titkos kulcsokat (például a hitelesítési kulcsokat, a Storage-fiók kulcsait, az adattitkosítási kulcsokat). PFX-fájlok és jelszavak) a hardveres biztonsági modulok (HSM-EK) által védett kulcsok használatával. További információ: [Mi az Azure Key Vault?](../../key-vault/general/overview.md).

A Storage ügyféloldali kódtára a Key Vault Core könyvtárat használja, hogy az Azure-ban közös keretrendszert biztosítson a kulcsok kezeléséhez. A felhasználók a Key Vault Extensions Library használatának további előnyeit is igénybe vehetik. A bővítmények könyvtára hasznos funkciókat biztosít az egyszerű és zökkenőmentes szimmetrikus/RSA helyi és Felhőbeli kulcsos szolgáltatók, valamint az Összesítés és a gyorsítótárazás terén.

### <a name="interface-and-dependencies"></a>Felület és függőségek
Három Key Vault csomag létezik:  

* Az Azure-kulcstartó-Core tartalmazza a Rendszerállapotkulcsot és a IKeyResolver. Ez egy kis csomag, amely nem rendelkezik függőségekkel. A Java Storage ügyféloldali kódtára a függőségként határozza meg.
* Az Azure-kulcstartó tartalmazza a Key Vault REST-ügyfelet.  
* Azure-kulcstartó – a bővítmények a titkosítási algoritmusok, valamint az RSAKey és a SymmetricKey implementációit tartalmazó bővítményi kódokat tartalmaznak. Ez az alapszintű és a kulcstartó névtertől függ, és funkciókat biztosít az összesített feloldó definiálásához (ha a felhasználók több Key providert kívánnak használni) és a gyorsítótárazási kulcs feloldóját. Bár a Storage ügyféloldali kódtár nem függ közvetlenül ettől a csomagtól, ha a felhasználók Azure Key Vault szeretnék tárolni a kulcsaikat, vagy hogy a Key Vault-bővítményeket használják a helyi és a felhőalapú titkosítási szolgáltatók felhasználásához, erre a csomagra lesz szükségük.  
  
  A Key Vault a nagy értékű főkulcsok számára készült, és az egyes Key Vault sávszélesség-szabályozási korlátját szem előtt tartva tervezték. Ha Key Vault használatával ügyféloldali titkosítást végez, az előnyben részesített modell a titokként tárolt szimmetrikus főkulcsok használata Key Vault és helyi gyorsítótárban. A felhasználóknak a következőket kell tenniük:  

1. Hozzon létre egy titkos kulcsot, és töltse fel Key Vaultba.  
2. A titkos kulcs alapazonosítójának használatával oldja fel a titkosítás jelenlegi verzióját, és gyorsítótárazza ezeket az információkat helyileg. CachingKeyResolver használata a gyorsítótárazáshoz; a felhasználók nem várhatóan saját gyorsítótárazási logikát implementálnak.  
3. Használja a gyorsítótárazási feloldót bemenetként a titkosítási házirend létrehozásakor.
   Key Vault használattal kapcsolatos további információkért tekintse meg a titkosítási kód mintáit.

## <a name="best-practices"></a>Ajánlott eljárások
A titkosítás támogatása csak a Java Storage ügyféloldali kódtáraban érhető el.

> [!IMPORTANT]
> Az ügyféloldali titkosítás használatakor vegye figyelembe ezeket a fontos pontokat:
> 
> * Titkosított blobba való olvasáskor vagy az azokból való íráskor használja az egész blob feltöltési parancsokat és a tartomány/teljes blob letöltési parancsot. Kerülje a titkosított blobba való írást olyan protokollok használatával, mint a Put blokk, a letiltási lista, a lapok írása, az oldalak törlése vagy a hozzáfűzési blokk; Ellenkező esetben előfordulhat, hogy a titkosított blob sérült, és nem olvasható.
> * A táblákhoz hasonló korlátozás létezik. Ügyeljen arra, hogy ne frissítse a titkosított tulajdonságokat a titkosítási metaadatok frissítése nélkül.  
> * Ha a titkosított blobon beállítja a metaadatokat, felülírhatja a titkosítással kapcsolatos metaadatokat a visszafejtéshez, mivel a metaadatok beállítása nem adalékanyag. Ez a pillanatképek esetében is igaz. Kerülje a metaadatok megadását egy titkosított blob pillanatképének létrehozása közben. Ha be kell állítani a metaadatokat, először hívja meg a **downloadAttributes** metódust, hogy lekérje a jelenlegi titkosítási metaadatokat, és elkerülje a párhuzamos írásokat a metaadatok beállításakor.  
> * Engedélyezze a **requireEncryption** jelzőt az alapértelmezett kérési beállításokban azon felhasználók számára, akik csak titkosított adattal működnek. További információért lásd alább.  
> 
> 

## <a name="client-api--interface"></a>Ügyfél API/Interface
Egy EncryptionPolicy objektum létrehozásakor a felhasználók csak a kulcsot (Rendszerállapotkulcsot-t), csak a feloldót (IKeyResolver) vagy mindkettőt tudják biztosítani. A Rendszerállapotkulcsot az alapszintű kulcs típusa, amelyet a rendszer a kulcs azonosítójának használatával azonosít, és amely a burkoló/kicsomagolási logikát biztosítja. A IKeyResolver egy kulcs feloldására szolgál a visszafejtési folyamat során. Definiál egy ResolveKey metódust, amely egy Rendszerállapotkulcsot ad vissza. Ez lehetővé teszi a felhasználók számára, hogy több helyen felügyelt kulcsok közül választhatnak.

* A titkosításhoz mindig a kulcsot használja a rendszer, és a kulcs hiánya hibát eredményez.  
* Visszafejtéshez:  
  
  * Ha meg van adva a kulcs, a kulcs feloldója meghívásra kerül. Ha a feloldó meg van adva, de nem rendelkezik leképezéssel a kulcs-azonosítóhoz, a rendszer hibát jelez.  
  * Ha a feloldó nincs megadva, de a kulcs meg van adva, akkor a rendszer akkor használja a kulcsot, ha annak azonosítója megegyezik a szükséges kulcs-azonosítóval. Ha az azonosító nem egyezik, a rendszer hibát jelez.  
    
    A [titkosítási minták](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) a Blobok, a várólisták és a táblák részletes, végpontok közötti forgatókönyveit mutatják be Key Vault integrációval együtt.

### <a name="requireencryption-mode"></a>RequireEncryption mód
A felhasználók opcionálisan engedélyezhetik a művelet módját, ahol a feltöltéseket és a letöltéseket titkosítani kell. Ebben a módban az adatok titkosítási házirend nélkül tölthetők fel, vagy a szolgáltatásban nem titkosított adatok letöltése sikertelen lesz az ügyfélen. A kérési beállítások objektum **requireEncryption** jelzője vezérli ezt a viselkedést. Ha az alkalmazás az Azure Storage-ban tárolt összes objektumot titkosítani fogja, akkor a **requireEncryption** tulajdonságot a szolgáltatás ügyféloldali objektumához tartozó alapértelmezett kérési beállításoknál állíthatja be.   

Használja például a **CloudBlobClient. getDefaultRequestOptions (). setRequireEncryption (true)** értéket, ha titkosítást kíván használni az adott ügyfélalkalmazás által végrehajtott összes blob-művelethez.

### <a name="blob-service-encryption"></a>Titkosítás Blob service
Hozzon létre egy **BlobEncryptionPolicy** objektumot, és állítsa be a kérési beállítások között (API-ban vagy ügyféloldali szinten a **DefaultRequestOptions** használatával). Minden mást az ügyféloldali kódtár fog kezelni belsőleg.

# <a name="java-v12"></a>[Java V12](#tab/java)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="java-v8"></a>[Java V8](#tab/java8)

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

// Set the encryption policy on the request options.
BlobRequestOptions options = new BlobRequestOptions();
options.setEncryptionPolicy(policy);

// Upload the encrypted contents to the blob.
blob.upload(stream, size, null, options, null);

// Download and decrypt the encrypted contents from the blob.
ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
blob.download(outputStream, null, options, null);
```
---

### <a name="queue-service-encryption"></a>Titkosítás Queue szolgáltatás
Hozzon létre egy **QueueEncryptionPolicy** objektumot, és állítsa be a kérési beállítások között (API-ban vagy ügyféloldali szinten a **DefaultRequestOptions** használatával). Minden mást az ügyféloldali kódtár fog kezelni belsőleg.

# <a name="java-v12"></a>[Java V12](#tab/java)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="java-v8"></a>[Java V8](#tab/java8)

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

// Add message
QueueRequestOptions options = new QueueRequestOptions();
options.setEncryptionPolicy(policy);

queue.addMessage(message, 0, 0, options, null);

// Retrieve message
CloudQueueMessage retrMessage = queue.retrieveMessage(30, options, null);
```
---

### <a name="table-service-encryption"></a>Titkosítás Table service
A titkosítási szabályzat létrehozása és a kérési beállítások megadása mellett meg kell adnia egy **EncryptionResolver** a **TableRequestOptions**-ben, vagy a [titkosítás] attribútumot kell megadnia az entitás kiolvasóján és szetterén.

### <a name="using-the-resolver"></a>A feloldó használata

# <a name="java-v12"></a>[Java V12](#tab/java)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="java-v8"></a>[Java V8](#tab/java8)

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

TableRequestOptions options = new TableRequestOptions()
options.setEncryptionPolicy(policy);
options.setEncryptionResolver(new EncryptionResolver() {
    public boolean encryptionResolver(String pk, String rk, String key) {
        if (key == "foo")
        {
            return true;
        }
        return false;
    }
});

// Insert Entity
currentTable.execute(TableOperation.insert(ent), options, null);

// Retrieve Entity
// No need to specify an encryption resolver for retrieve
TableRequestOptions retrieveOptions = new TableRequestOptions()
retrieveOptions.setEncryptionPolicy(policy);

TableOperation operation = TableOperation.retrieve(ent.PartitionKey, ent.RowKey, DynamicTableEntity.class);
TableResult result = currentTable.execute(operation, retrieveOptions, null);
```
---

### <a name="using-attributes"></a>Attribútumok használata
Ahogy azt fentebb említettük, ha az entitás TableEntity valósít meg, akkor a **EncryptionResolver** megadása helyett a [titkosítás] attribútummal rendezheti a tulajdonságok beolvasóját és a szetteret.

# <a name="java-v12"></a>[Java V12](#tab/java)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="java-v8"></a>[Java V8](#tab/java8)

```java
private string encryptedProperty1;

@Encrypt
public String getEncryptedProperty1 () {
    return this.encryptedProperty1;
}

@Encrypt
public void setEncryptedProperty1(final String encryptedProperty1) {
    this.encryptedProperty1 = encryptedProperty1;
}
```
---

## <a name="encryption-and-performance"></a>Titkosítás és teljesítmény

Vegye figyelembe, hogy a tárolási adatokat a rendszer további teljesítménybeli terhelést eredményez. A tartalmi kulcsot és a IV-t elő kell állítani, a tartalmat titkosítani kell, és további metaadatokat kell formázni és feltölteni. Ez a terhelés a titkosított adatmennyiségtől függően eltérő lesz. Javasoljuk, hogy az ügyfelek mindig tesztelje az alkalmazásaikat a fejlesztés során.

## <a name="next-steps"></a>Következő lépések

* Töltse le az [Azure Storage ügyféloldali kódtárat a Java Maven-csomaghoz](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)  
* Az [Azure Storage ügyféloldali kódtár letöltése a githubról a Java-forráskódhoz](https://github.com/Azure/azure-storage-java)
* Töltse le a Azure Key Vault Maven-függvénytárat a Java Maven-csomagokhoz:
  * [](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core) Alapcsomag
  * [Ügyfél](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault) -csomag
* A [Azure Key Vault dokumentációjának](../../key-vault/general/overview.md) felkeresése

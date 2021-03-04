---
title: Ügyféloldali titkosítás Python-val
titleSuffix: Azure Storage
description: A Pythonhoz készült Azure Storage ügyféloldali kódtára támogatja az ügyféloldali titkosítást az Azure Storage-alkalmazások maximális biztonsága mellett.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: ffdfd4dc8a81587d757e3f9853f1bb34e0b93c0d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043745"
---
# <a name="client-side-encryption-with-python"></a>Ügyféloldali titkosítás Python-val

[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Áttekintés
A [Pythonhoz készült Azure Storage ügyféloldali kódtára](https://pypi.python.org/pypi/azure-storage) támogatja az ügyfeleken belüli adattitkosítást az Azure Storage-ba való feltöltés előtt, és az adatvisszafejtést az ügyfélre való letöltés során.

> [!NOTE]
> Az Azure Storage Python-könyvtára előzetes verzióban érhető el.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Titkosítás és visszafejtés a boríték-technikán keresztül
A titkosítási és visszafejtési folyamatok követik a burkológörbe technikáját.

### <a name="encryption-via-the-envelope-technique"></a>Titkosítás a boríték-technikán keresztül
A titkosítás a burkológörbe technikán keresztül a következő módon működik:

1. Az Azure Storage ügyféloldali kódtára létrehoz egy Content encryption Key (CEK) kulcsot, amely egy egyszer használatos szimmetrikus kulcs.
2. A felhasználói adatai titkosítva vannak a CEK használatával.
3. Ezután a CEK (titkosított) a kulcs titkosítási kulcs (KEK) használatával burkolta. A KEK-et egy kulcs-azonosító azonosítja, és lehet aszimmetrikus kulcspár vagy egy helyileg felügyelt szimmetrikus kulcs.
   Maga a Storage ügyféloldali kódtár soha nem fér hozzá a KEK-hez. A könyvtár meghívja a KEK által biztosított Key wrapper algoritmust. A felhasználók dönthetnek úgy, hogy egyéni szolgáltatókat használnak a kulcsok becsomagolásához/kicsomagolásához, ha szükséges.
4. Ezt követően a rendszer feltölti a titkosított fájlokat az Azure Storage szolgáltatásba. A burkolt kulcsot, valamint néhány további titkosítási metaadatot metaadatokként (blobon) vagy a titkosított adatokkal (Üzenetsor-üzenetek és tábla entitások) interpolált módon tárolja a rendszer.

### <a name="decryption-via-the-envelope-technique"></a>Visszafejtés a boríték-technikán keresztül
A burkológörbe technikán keresztüli visszafejtés a következő módon működik:

1. Az ügyféloldali kódtár feltételezi, hogy a felhasználó helyileg kezeli a kulcs titkosítási kulcsát (KEK). A felhasználónak nem kell tudnia a titkosításhoz használt konkrét kulcsot. Ehelyett a kulcs feloldója, amely feloldja a különböző kulcs-azonosítókat a kulcsokhoz, beállítható és használható.
2. Az ügyféloldali kódtár letölti a titkosított adatok mellett a szolgáltatásban tárolt összes titkosítási anyagot is.
3. Ezután a burkolt tartalom titkosítási kulcsát (CEK) a rendszer kicsomagolja (visszafejtve) a kulcs titkosítási kulcs (KEK) használatával. Ebben az esetben az ügyféloldali kódtár nem fér hozzá a KEK-hez. Egyszerűen meghívja az egyéni szolgáltató kicsomagolási algoritmusát.
4. A titkosító kulcs (CEK) használatával visszafejti a titkosított felhasználói adatokat.

## <a name="encryption-mechanism"></a>Titkosítási mechanizmus
A Storage ügyféloldali kódtára [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) -t használ a felhasználói adattartalom titkosításához. Pontosabban, AES-sel rendelkező [titkosítási blokkoló (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) mód. Az egyes szolgáltatások némileg eltérően működnek, ezért ezeket itt fogjuk megbeszélni.

### <a name="blobs"></a>Blobok
Az ügyféloldali kódtár jelenleg csak a teljes Blobok titkosítását támogatja. A titkosítás csak akkor támogatott, ha a felhasználók a **create*** metódusokat használják. A letöltések esetében a teljes és a tartományra vonatkozó letöltések is támogatottak, és a feltöltés és a letöltés párhuzamos is elérhető.

A titkosítás során az ügyfél-függvénytár 16 bájtos véletlenszerű inicializálási vektort (IV) állít elő, amely egy 32 bájtos véletlenszerű tartalom-titkosítási kulccsal (CEK), valamint a Blobok adatainak ezen információk használatával történő titkosítását is elvégezheti. A burkolt CEK és néhány további titkosítási metaadat ezután blob-metaadatokként tárolódik a szolgáltatás titkosított blobja mellett.

> [!WARNING]
> Ha a blobhoz saját metaadatokat szerkeszt vagy tölt fel, gondoskodnia kell arról, hogy a metaadatok megmaradjanak. Ha a metaadatok nélkül tölt fel új metaadatokat, a becsomagolt CEK, IV és egyéb metaadatok elvesznek, és a blob tartalma soha nem lesz lekérdezhető.
> 
> 

A titkosított Blobok letöltése magában foglalja a teljes blob tartalmának lekérését a **Get*** kényelmi módszerek használatával. A burkolt CEK nincs becsomagolva és együtt használva a IV (ebben az esetben a blob-metaadatokban tárolt), hogy visszaállítsa a visszafejtett adatokat a felhasználók számára.

Egy tetszőleges tartomány (**Get*** metódusok, amelyekben az átadott tartománybeli paraméterek) letöltése a titkosított blobban magában foglalja a felhasználók által megadott tartomány beállítását, hogy a kért tartomány sikeres visszafejtéséhez használható, kis mennyiségű további adat legyen elérhető.

A blobok és a Blobok csak akkor titkosíthatók/visszafejthetők, ha ezt a sémát használják. A hozzáfűző Blobok titkosítása jelenleg nem támogatott.

### <a name="queues"></a>Üzenetsorok
Mivel a üzenetsor-üzenetek bármilyen formátumúak lehetnek, az ügyféloldali kódtár egyéni formátumot határoz meg, amely tartalmazza az inicializálási vektort (IV) és a titkosított tartalom titkosítási kulcsát (CEK) az üzenet szövegeként.

A titkosítás során az ügyféloldali kódtár a 16 bájtos véletlenszerű CEK, valamint a 32 bájtos véletlenszerű adatmennyiséget, valamint az üzenetsor-üzenet szövegének boríték-titkosítását használja ezen információk alapján. A burkolt CEK és néhány további titkosítási metaadat hozzá lesz adva a titkosított üzenetsor-üzenethez. Ezt a módosított üzenetet (alább látható) a szolgáltatás tárolja.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

A visszafejtés során a rendszer kinyeri a beburkolt kulcsot az üzenetsor-üzenetből, és csomagolja ki. A rendszer kinyeri a IV-t az üzenetsor-üzenetből is, és a nem burkolt kulccsal együtt használja az üzenetsor-üzenet adatainak visszafejtéséhez. Vegye figyelembe, hogy a titkosítási metaadatok kis méretűek (500 bájtnál), így amíg a várólista-üzenet 64 kb-korlátja megesik, a hatásnak kezelhető kell lennie.

### <a name="tables"></a>Táblák
Az ügyféloldali kódtár támogatja a INSERT és a Replace műveletekhez tartozó entitás-tulajdonságok titkosítását.

> [!NOTE]
> Az egyesítés jelenleg nem támogatott. Mivel a tulajdonságok egy részhalmaza korábban egy másik kulccsal lett titkosítva, egyszerűen az új tulajdonságok egyesítése és a metaadatok frissítése adatvesztést eredményezhet. Az egyesítéshez szükség van további szolgáltatási hívásokra, hogy beolvassa a szolgáltatásból a már meglévő entitást, vagy egy új kulcsot használjon egy tulajdonságban, amelyek közül mindkettő nem alkalmas a teljesítményre.
> 
> 

A tábla adattitkosítása a következőképpen működik:

1. A felhasználók a titkosítani kívánt tulajdonságokat határozzák meg.
2. Az ügyféloldali kódtár létrehoz egy véletlenszerű inicializálási vektort (IV) 16 bájttal, valamint egy véletlenszerű Content encryption Key (CEK) 32 bájtot minden entitáshoz, és a borítékok titkosítását is végrehajtja az egyes tulajdonságokon, hogy az új IV/tulajdonságot származtatva titkosítsa. A titkosított tulajdonság bináris adatként van tárolva.
3. A burkolt CEK és néhány további titkosítási metaadatot a rendszer két további fenntartott tulajdonságként tárolja. Az első fenntartott tulajdonság ( \_ ClientEncryptionMetadata1) egy karakterlánc-tulajdonság, amely a IV, a verzió és a becsomagolt kulcs adatait tartalmazza. A második fenntartott tulajdonság ( \_ ClientEncryptionMetadata2) egy bináris tulajdonság, amely a titkosított tulajdonságokkal kapcsolatos információkat tartalmazza. Az ebben a második tulajdonságban ( \_ ClientEncryptionMetadata2) található információk titkosítva vannak.
4. A titkosításhoz szükséges további fenntartott tulajdonságok miatt a felhasználók mostantól csak 250 egyéni tulajdonságokkal rendelkezhetnek 252 helyett. Az entitás teljes méretének 1 MB-nál kisebbnek kell lennie.

   Vegye figyelembe, hogy csak a karakterlánc-tulajdonságok titkosíthatók. Ha más típusú tulajdonságokat kíván titkosítani, azokat karakterlánccá kell konvertálni. A titkosított karakterláncok a szolgáltatásban bináris tulajdonságokként tárolódnak, és vissza lesznek alakítva karakterlánccá (nyers karakterláncok, EdmType. STRING típussal nem EntityProperties) a visszafejtés után.

   A táblák esetében a titkosítási házirenden kívül a felhasználóknak meg kell adniuk a titkosítani kívánt tulajdonságokat. Ezt úgy teheti meg, hogy ezeket a tulajdonságokat a TableEntity objektumokban tárolja, és a EdmType. STRING és a titkosítás értéke TRUE (igaz) értékre van állítva, vagy a tableservice objektumon állítja be a encryption_resolver_function. A titkosítási feloldó egy olyan függvény, amely egy partíciós kulcsot, egy sor kulcsot és egy tulajdonságnév-nevet használ, és egy logikai értéket ad vissza, amely jelzi, hogy a tulajdonságot titkosítani kell-e. A titkosítás során az ügyféloldali kódtár ezeket az információkat fogja használni annak eldöntéséhez, hogy a tulajdonságot titkosítani kell-e a drótba való írás során. A delegált emellett a tulajdonságok titkosítását is lehetővé teszi. (Ha például X, akkor titkosítsa az A tulajdonságot, máskülönben az A és B tulajdonságokat titkosítja.) Vegye figyelembe, hogy az entitások olvasása vagy lekérdezése során nem szükséges megadnia ezeket az információkat.

### <a name="batch-operations"></a>Batch-műveletek
Az egyik titkosítási szabályzat a köteg összes sorára érvényes. Az ügyféloldali kódtár belsőleg hozza meg a kötegben egy új véletlenszerű IV és véletlenszerű CEK. A felhasználók úgy is dönthetnek, hogy a kötegben lévő összes művelethez különböző tulajdonságokat titkosítanak. ehhez a viselkedést a titkosítási feloldóban kell meghatározni.
Ha egy köteget a tableservice batch () metódussal hoz létre, akkor a rendszer automatikusan alkalmazza a tableservice titkosítási házirendjét a kötegre. Ha a köteget explicit módon hozza létre a konstruktor meghívásával, a titkosítási házirendet paraméterként kell átadni, és a köteg élettartama előtt változatlan marad.
Vegye figyelembe, hogy az entitások titkosítva vannak, mivel a köteg titkosítási házirendje segítségével vannak beszúrva a kötegbe (az entitások nem lesznek titkosítva a Batch tableservice titkosítási házirendjének használatával történő véglegesítése közben).

### <a name="queries"></a>Lekérdezések
> [!NOTE]
> Mivel az entitások titkosítva vannak, nem futtathat olyan lekérdezéseket, amelyek egy titkosított tulajdonságra szűrnek.  Ha megpróbálják, az eredmények helytelenek lesznek, mivel a szolgáltatás nem titkosított adattal próbálta összehasonlítani a titkosított adatmennyiséget.
> 
> 
> A lekérdezési műveletek végrehajtásához meg kell adnia egy kulcs-feloldót, amely képes az eredményhalmaz összes kulcsának feloldására. Ha a lekérdezési eredményben szereplő entitás nem oldható fel szolgáltatóhoz, akkor az ügyféloldali kódtár hibát jelez. A kiszolgálóoldali kivetítéseket végrehajtó lekérdezések esetében az ügyféloldali kódtár alapértelmezés szerint hozzáadja a speciális titkosítási metaadatok tulajdonságait ( \_ ClientEncryptionMetadata1 és \_ ClientEncryptionMetadata2) a kijelölt oszlopokhoz.
> 
> [!IMPORTANT]
> Az ügyféloldali titkosítás használatakor vegye figyelembe ezeket a fontos pontokat:
> 
> * Titkosított blobba való olvasáskor vagy az azokból való íráskor használja az egész blob feltöltési parancsokat és a tartomány/teljes blob letöltési parancsot. Kerülje a titkosított blobba való írást olyan protokollok használatával, mint például a Put blokk, a tiltási lista, az írási lapok vagy a törlési lapok használata. Ellenkező esetben előfordulhat, hogy a titkosított blob sérült, és nem olvasható.
> * A táblákhoz hasonló korlátozás létezik. Ügyeljen arra, hogy ne frissítse a titkosított tulajdonságokat a titkosítási metaadatok frissítése nélkül.
> * Ha a titkosított blobon beállítja a metaadatokat, felülírhatja a titkosítással kapcsolatos metaadatokat a visszafejtéshez, mivel a metaadatok beállítása nem adalékanyag. Ez a pillanatképek esetében is igaz. Kerülje a metaadatok megadását egy titkosított blob pillanatképének létrehozása közben. Ha a metaadatokat be kell állítani, ügyeljen arra, hogy először hívja meg a **get_blob_metadata** metódust az aktuális titkosítási metaadatok beszerzéséhez, és ne legyenek egyidejű írások a metaadatok beállításakor.
> * Engedélyezze a **require_encryption** jelzőt a szolgáltatás objektumon olyan felhasználók számára, akik csak titkosított adattal működnek. További információért lásd alább.

A Storage ügyféloldali kódtára a megadott KEK-és kulcs-feloldót várja a következő felület megvalósításához. A Python KEK-felügyelet [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) támogatása függőben van, és ha elkészült, integrálva lesz ebbe a könyvtárba.

## <a name="client-api--interface"></a>Ügyfél API/Interface
Miután létrehozta a Storage Service-objektumot (például blockblobservice), a felhasználó értékeket rendelhet a titkosítási házirendet alkotó mezőkhöz: key_encryption_key, key_resolver_function és require_encryption. A felhasználók csak KEK-et, csak feloldót vagy mindkettőt adhatnak meg. a key_encryption_key az alapszintű kulcs típusa, amelyet a rendszer a kulcs-azonosító használatával azonosít, és amely a burkoló/kicsomagolási logikát biztosítja. key_resolver_function a kulcsok feloldására szolgál a visszafejtési folyamat során. Egy érvényes KEK-t ad vissza, amely egy kulcs-azonosítót adott meg. Ez lehetővé teszi a felhasználók számára, hogy több helyen felügyelt kulcsok közül választhatnak.

A KEK-nek a következő módszereket kell megvalósítania az adattitkosítás sikeres elvégzéséhez:

* wrap_key (CEK): a megadott CEK (bájt) becsomagolása a felhasználó által választott algoritmus használatával. A beburkolt kulcsot adja vissza.
* get_key_wrap_algorithm (): a kulcsok becsomagolásához használt algoritmust adja vissza.
* get_kid (): a jelen KEK karakterlánc-kulcsának azonosítóját adja vissza.
  A KEK-nek a következő módszereket kell megvalósítania az adattitkosítás sikeres visszafejtéséhez:
* unwrap_key (CEK, algoritmus): a megadott CEK kicsomagolt formáját adja vissza a karakterlánc által megadott algoritmus használatával.
* get_kid (): egy karakterlánc-kulcs azonosítóját adja vissza ehhez a KEK-hez.

A kulcs feloldójának legalább olyan metódust kell megvalósítani, amely a kulcs azonosítója alapján visszaadja a fenti illesztőfelület megvalósításának megfelelő KEK-t. Csak ezt a metódust kell hozzárendelni a szolgáltatás objektum key_resolver_function tulajdonságához.

* A titkosításhoz mindig a kulcsot használja a rendszer, és a kulcs hiánya hibát eredményez.
* Visszafejtéshez:

  * Ha meg van adva a kulcs, a kulcs feloldója meghívásra kerül. Ha a feloldó meg van adva, de nem rendelkezik leképezéssel a kulcs-azonosítóhoz, a rendszer hibát jelez.
  * Ha a feloldó nincs megadva, de a kulcs meg van adva, akkor a rendszer akkor használja a kulcsot, ha annak azonosítója megegyezik a szükséges kulcs-azonosítóval. Ha az azonosító nem egyezik, a rendszer hibát jelez.

    A titkosítási minták az Azure. Storage. Samples-ben részletesebben szemléltetik a Blobok, a várólisták és a táblák részletes, végpontok közötti forgatókönyveit.
      A KEK és a kulcs feloldójának mintául szolgáló implementációi a minta fájljaiban, illetve a kulcstárolóban is elérhetők.

### <a name="requireencryption-mode"></a>RequireEncryption mód
A felhasználók opcionálisan engedélyezhetik a művelet módját, ahol a feltöltéseket és a letöltéseket titkosítani kell. Ebben a módban az adatok titkosítási házirend nélkül tölthetők fel, vagy a szolgáltatásban nem titkosított adatok letöltése sikertelen lesz az ügyfélen. A szolgáltatás objektum **require_encryption** jelzője vezérli ezt a viselkedést.

### <a name="blob-service-encryption"></a>Titkosítás Blob service
Állítsa be a titkosítási házirend mezőket a blockblobservice objektumra. Minden mást az ügyféloldali kódtár fog kezelni belsőleg.

# <a name="python-v12"></a>[Python V12](#tab/python)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_block_blob_service.key_encryption_key = kek
my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

# Upload the encrypted contents to the blob.
my_block_blob_service.create_blob_from_stream(
    container_name, blob_name, stream)

# Download and decrypt the encrypted contents from the blob.
blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)
```
---

### <a name="queue-service-encryption"></a>Titkosítás Queue szolgáltatás
Állítsa be a titkosítási házirend mezőket a queueservice objektumra. Minden mást az ügyféloldali kódtár fog kezelni belsőleg.

# <a name="python-v12"></a>[Python V12](#tab/python)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_queue_service.key_encryption_key = kek
my_queue_service.key_resolver_funcion = key_resolver.resolve_key

# Add message
my_queue_service.put_message(queue_name, content)

# Retrieve message
retrieved_message_list = my_queue_service.get_messages(queue_name)
```
---

### <a name="table-service-encryption"></a>Titkosítás Table service
A titkosítási szabályzat létrehozása és a kérési beállítások megadása mellett meg kell adnia egy **encryption_resolver_function** a **tableservice**, vagy a titkosítás attribútumot a EntityProperty kell beállítania.

### <a name="using-the-resolver"></a>A feloldó használata

# <a name="python-v12"></a>[Python V12](#tab/python)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Define the encryption resolver_function.


def my_encryption_resolver(pk, rk, property_name):
    if property_name == 'foo':
        return True
    return False


# Set the KEK and key resolver on the service object.
my_table_service.key_encryption_key = kek
my_table_service.key_resolver_funcion = key_resolver.resolve_key
my_table_service.encryption_resolver_function = my_encryption_resolver

# Insert Entity
my_table_service.insert_entity(table_name, entity)

# Retrieve Entity
# Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
my_table_service.get_entity(
    table_name, entity['PartitionKey'], entity['RowKey'])
```
---

### <a name="using-attributes"></a>Attribútumok használata
A fentiekben leírtaknak megfelelően a tulajdonságot a rendszer egy EntityProperty objektumban tárolja, és beállítja a titkosítást.

# <a name="python-v12"></a>[Python V12](#tab/python)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```
---

## <a name="encryption-and-performance"></a>Titkosítás és teljesítmény
Vegye figyelembe, hogy a tárolási adatokat a rendszer további teljesítménybeli terhelést eredményez. A tartalmi kulcsot és a IV-t elő kell állítani, a tartalmat titkosítani kell, és további metaadatokat kell formázni és feltölteni. Ez a terhelés a titkosított adatmennyiségtől függően eltérő lesz. Javasoljuk, hogy az ügyfelek mindig tesztelje az alkalmazásaikat a fejlesztés során.

## <a name="next-steps"></a>Következő lépések
* Töltse le az [Azure Storage ügyféloldali kódtárat a Java PyPi-csomaghoz](https://pypi.python.org/pypi/azure-storage)
* Töltse le az [Azure Storage ügyféloldali kódtárat a githubról a Python forráskódjának kódjából](https://github.com/Azure/azure-storage-python)

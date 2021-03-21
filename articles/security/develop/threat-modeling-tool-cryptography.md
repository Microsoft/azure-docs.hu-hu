---
title: Titkosítás – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Ismerje meg a Threat Modeling Toolban elérhető fenyegetések titkosításának enyhítését. Tekintse meg a kockázatcsökkentő információkat és a kód megjelenítése példákat.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 6af4fba8ddf50b795d847a2c7b4e2fbc02fe593f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97587179"
---
# <a name="security-frame-cryptography--mitigations"></a>Biztonsági keret: titkosítás | Enyhítését 

| Termék/szolgáltatás | Cikk |
| --------------- | ------- |
| **Webalkalmazás** | <ul><li>[Csak a jóváhagyott szimmetrikus blokk titkosítási algoritmusok és a kulcsok hosszának használata](#cipher-length)</li><li>[Jóváhagyott blokk titkosítási módok és inicializálási vektorok használata szimmetrikus titkosításhoz](#vector-ciphers)</li><li>[Jóváhagyott aszimmetrikus algoritmusok, kulcsok hosszának és kitöltésének használata](#padding)</li><li>[Jóváhagyott véletlenszerű számú generátorok használata](#numgen)</li><li>[Ne használjon szimmetrikus adatfolyam-titkosítást](#stream-ciphers)</li><li>[Jóváhagyott MAC/HMAC/kulcsos kivonatoló algoritmusok használata](#mac-hash)</li><li>[Csak jóváhagyott titkosítási kivonatoló függvények használata](#hash-functions)</li></ul> |
| **Adatbázis** | <ul><li>[Erős titkosítási algoritmusok használata az adatbázisban tárolt adattitkosításhoz](#strong-db)</li><li>[A SSIS-csomagokat titkosítani és digitálisan alá kell írni](#ssis-signed)</li><li>[Digitális aláírás hozzáadása a kritikus adatbázis-biztonságos elemek migrálására](#securables-db)</li><li>[Az SQL Server EKM használata a titkosítási kulcsok védeleméhez](#ekm-keys)</li><li>[AlwaysEncrypted szolgáltatás használata, ha a titkosítási kulcsokat nem szabad felmutatni az adatbázismotor számára](#keys-engine)</li></ul> |
| **IoT-eszköz** | <ul><li>[Titkosítási kulcsok biztonságos tárolása a IoT-eszközön](#keys-iot)</li></ul> | 
| **IoT Cloud Gateway** | <ul><li>[A IoT Hub hitelesítéshez elegendő hosszúságú véletlenszerű szimmetrikus kulcs létrehozása](#random-hub)</li></ul> | 
| **Dynamics CRM Mobile-ügyfél** | <ul><li>[Győződjön meg arról, hogy van olyan eszköz-felügyeleti szabályzat, amely használatához PIN-kód szükséges, és lehetővé teszi a távoli törlést](#pin-remote)</li></ul> | 
| **Dynamics CRM Outlook-ügyfél** | <ul><li>[Győződjön meg arról, hogy van olyan eszköz-felügyeleti házirend, amely PIN-kód/jelszó/automatikus zárolást igényel, és titkosítja az összes adathalmazt (például BitLocker).](#bitlocker)</li></ul> | 
| **Identity Server** | <ul><li>[Ellenőrizze, hogy az aláíró kulcsok át lesznek-e tekerve az Identity Server használatakor](#rolled-server)</li><li>[Győződjön meg arról, hogy a kriptográfiailag erős ügyfél-azonosító, az ügyfél titkos kulcsa az Identity Serverben van használatban](#client-server)</li></ul> | 

## <a name="use-only-approved-symmetric-block-ciphers-and-key-lengths"></a><a id="cipher-length"></a>Csak a jóváhagyott szimmetrikus blokk titkosítási algoritmusok és a kulcsok hosszának használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>A termékeknek csak azokat a szimmetrikus blokkos titkosítási algoritmusokat és a hozzájuk tartozó kulcs hosszait kell használniuk, amelyeket kifejezetten jóváhagytak a szervezet kriptográfiai tanácsadója. A Microsoft által jóváhagyott szimmetrikus algoritmusok a következő blokk-rejtjeleket tartalmazzák:</p><ul><li>Az AES-128, AES-192 és AES-256 új kód esetén elfogadható</li><li>A meglévő kóddal való visszamenőleges kompatibilitás érdekében a három kulcsos 3DES elfogadható</li><li>Szimmetrikus blokk titkosítási algoritmust használó termékek esetén:<ul><li>Az új kód Advanced Encryption Standard (AES) szükséges</li><li>A három kulcsos Triple adattitkosítási szabvány (3DES) a meglévő kódban megengedett a visszamenőleges kompatibilitás érdekében.</li><li>Az összes többi blokk-titkosítás, például az RC2, a DES, a 2. kulcs, a DESX és a csíkoshasú csak a régi adattitkosítás visszafejtésére használható, és ha titkosításhoz használják, le kell cserélni.</li></ul></li><li>A szimmetrikus blokk titkosítási algoritmusok esetében az 128 bitek minimális hosszának megadása kötelező. A csak az új kódokhoz ajánlott blokk titkosítási algoritmus az AES (AES-128, AES-192 és AES-256) esetén elfogadható.</li><li>A három kulcsos 3DES jelenleg elfogadható, ha már használatban van meglévő kódban; az AES-re való áttérés ajánlott. A DES, a DESX, az RC2 és a csíkoshasú már nem tekinthető biztonságosnak. Ezek az algoritmusok csak a meglévő adatvisszafejtéshez használhatók a visszamenőleges kompatibilitás érdekében, és az adatmennyiséget ajánlott blokk-rejtjel használatával újra titkosítani kell.</li></ul><p>Vegye figyelembe, hogy az összes szimmetrikus blokkos titkosítást jóváhagyott titkosítási móddal kell használni, amely a megfelelő inicializálási vektor (IV) használatát igényli. A megfelelő IV, általában egy véletlenszerű szám, és soha nem állandó érték</p><p>Az örökölt vagy más módon nem jóváhagyott kriptográfiai algoritmusok és kisebb kulcsok használata a meglévő információk olvasásához (az új adatmennyiségek megírása helyett) a szervezet titkosítási Tanácsa általi felülvizsgálata után is engedélyezhető. Ebben az esetben azonban kivételt kell megadnia ehhez a követelményhöz. Emellett a vállalati telepítésekben a termékeknek figyelembe kell venniük a figyelmeztetési rendszergazdákat, ha gyenge titkosítást használnak az adatolvasáshoz. Az ilyen figyelmeztetéseknek magyarázó és végrehajthatónak kell lenniük. Bizonyos esetekben célszerű lehet a gyenge titkosítás használatát Csoportházirend vezérelni</p><p>Engedélyezett .NET-algoritmusok a felügyelt titkosítási rugalmasság érdekében (preferencia sorrendben)</p><ul><li>AesCng (FIPS-kompatibilis)</li><li>AuthenticatedAesCng (FIPS-kompatibilis)</li><li>AESCryptoServiceProvider (FIPS-kompatibilis)</li><li>AESManaged (nem FIPS-kompatibilis)</li></ul><p>Vegye figyelembe, hogy ezek az algoritmusok egyike sem adható `SymmetricAlgorithm.Create` meg a vagy `CryptoConfig.CreateFromName` metódusok segítségével anélkül, hogy módosítani kellene a machine.config fájlt. Azt is vegye figyelembe, hogy a .net 3,5-es verzió előtti AES a named `RijndaelManaged` , és `AesCng` `AuthenticatedAesCng` >érhető el a Codeplex-en keresztül, és az alapul szolgáló operációs rendszerhez a CNG szükséges.</p>

## <a name="use-approved-block-cipher-modes-and-initialization-vectors-for-symmetric-ciphers"></a><a id="vector-ciphers"></a>Jóváhagyott blokk titkosítási módok és inicializálási vektorok használata szimmetrikus titkosításhoz

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Az összes szimmetrikus blokkos titkosítást jóváhagyott szimmetrikus titkosítási móddal kell használni. Az egyetlen jóváhagyott mód a CBC és a CTS. Az elektronikus kódú (EKB) üzemmódot nem szabad elkerülni; az EKB használatához a szervezete kriptográfiai Tanácsának felülvizsgálata szükséges. Az OFB, a CFB, a CTR, a CCM és a GCM, illetve bármely más titkosítási mód használatát a szervezet kriptográfiai testületének kell áttekintenie. Ha ugyanazt az inicializálási vektort (IV) szeretné újból használni a "streaming titkosítási módokban", például a CTR-ben, akkor a titkosított adatmegjelenítést okozó blokkos titkosítási algoritmusokat. Az összes szimmetrikus blokkos titkosítást a megfelelő inicializálási vektor (IV) használatával is fel kell használni. A megfelelő IV titkosítási szempontból erős, véletlenszerű szám, és soha nem állandó érték. |

## <a name="use-approved-asymmetric-algorithms-key-lengths-and-padding"></a><a id="padding"></a>Jóváhagyott aszimmetrikus algoritmusok, kulcsok hosszának és kitöltésének használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>A tiltott titkosítási algoritmusok használata jelentős kockázatot jelent a termékbiztonság számára, és el kell kerülni. A termékeknek kizárólag azokat a titkosítási algoritmusokat és a hozzájuk tartozó kulcs hosszait és kitöltését kell használniuk, amelyeket a szervezet kriptográfiai Tanácsa explicit módon jóváhagyott.</p><ul><li>**RSA –** titkosításhoz, kulcscserehez és aláíráshoz is használható. Az RSA-titkosítás csak a OAEP vagy az RSA-KEM kitöltési módokat kell használnia. A meglévő kód a PKCS #1 v 1.5 kitöltési módot is használhatja a kompatibilitáshoz. A null kitöltés használata explicit módon tiltott. A kulcsok >= 2048 BITS szükségesek az új kódhoz. A meglévő kód a szervezet kriptográfiai Tanácsa általi felülvizsgálat után csak a visszamenőleges kompatibilitás érdekében támogatja a kulcsok < 2048 bitet. A kulcsok < 1024 bitek csak a régi adatok visszafejtéséhez vagy ellenőrzéséhez használhatók, és ha titkosítási vagy aláírási műveletekhez használják őket, le kell cserélni.</li><li>**ECDSA –** csak aláírás esetén használható. A ECDSA >= 256 bites kulcsokkal kell rendelkeznie az új kódokhoz. A ECDSA-alapú aláírásoknak a három NIST által jóváhagyott görbe egyikét kell használniuk (P-256, P-384 vagy P521). A alaposan elemzett görbék csak a szervezet titkosítási táblájának áttekintése után használhatók.</li><li>**ECDH –** csak kulcscsere esetén használható. A ECDH >= 256 bites kulcsokkal kell rendelkeznie az új kódokhoz. A ECDH-alapú kulcscserenek a három NIST által jóváhagyott görbe egyikét kell használnia (P-256, P-384 vagy P521). A alaposan elemzett görbék csak a szervezet titkosítási táblájának áttekintése után használhatók.</li><li>A **DSA –** a szervezete kriptográfiai táblájának felülvizsgálata és jóváhagyása után is elfogadható lehet. Forduljon a biztonsági tanácsadóhoz, és ütemezze a szervezet titkosítási Tanácsának felülvizsgálatát. Ha jóváhagyja a DSA használatát, vegye figyelembe, hogy a 2048-nál kisebb hosszúságú kulcsok használatát meg kell tiltania. A CNG a 2048 bites és nagyobb hosszúságú kulcsokat támogatja a Windows 8 rendszertől kezdve.</li><li>**Diffie-Hellman –** csak a munkamenet-kulcsok kezeléséhez használható. A kulcs hosszának >= 2048 bit szükséges az új kódhoz. A meglévő kód a szervezet kriptográfiai Tanácsa általi felülvizsgálat után csak a visszamenőleges kompatibilitás érdekében < 2048 biteket támogatja. A kulcsok < 1024 bitek nem használhatók.</li><ul>

## <a name="use-approved-random-number-generators"></a><a id="numgen"></a>Jóváhagyott véletlenszerű számú generátorok használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>A termékeknek jóváhagyott véletlenszerű számú generátort kell használniuk. A bájtján elvégeznek függvények, például a Rand C futásidejű függvény, a .NET-keretrendszer System. Random vagy a rendszerfüggvények, például a GetTickCount, soha nem használhatók ilyen kódban. A kettős elliptikus görbe Random Number Generator (DUAL_EC_DRBG) algoritmus használata tiltott</p><ul><li>**CNG –** BCryptGenRandom (csak akkor használja a BCRYPT_USE_SYSTEM_PREFERRED_RNG jelzőt, ha a hívó a 0 értéknél nagyobb mennyiségű IRQL fut, PASSIVE_LEVEL])</li><li>**CAPI –** cryptGenRandom</li><li>**Win32/64 –** RtlGenRandom (az új implementációknak a BCryptGenRandom vagy a CryptGenRandom) * rand_s * SystemPrng (kernel módban) kell használniuk</li><li>**. NET-** RNGCryptoServiceProvider vagy RNGCng</li><li>**Windows áruházbeli alkalmazások –** Windows. Security. kriptográfia. CryptographicBuffer. GenerateRandom vagy. GenerateRandomNumber</li><li>**Apple OS X (10.7 +)/iOS (2.0 +) –** int SecRandomCopyBytes (SecRandomRef véletlenszerű, size_t darabszám, uint8_t \* bájt)</li><li>**Apple OS X (<10,7) –** Véletlenszerű számok beolvasása a/dev/Random használatával</li><li>**Java (például Google Android Java Code) –** Java. Security. SecureRandom osztály. Vegye figyelembe, hogy az Android 4,3 (zselés Bean) esetében a fejlesztőknek követniük kell az Android ajánlott megkerülő megoldását, és frissíteniük kell alkalmazásaikat, hogy explicit módon inicializálják az PRNG-t a/dev/urandom vagy a/dev/Random</li></ul>|

## <a name="do-not-use-symmetric-stream-ciphers"></a><a id="stream-ciphers"></a>Ne használjon szimmetrikus adatfolyam-titkosítást

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | A szimmetrikus adatfolyam-titkosítások (például az RC4) nem használhatók. A szimmetrikus adatfolyamok titkosítása helyett a termékeknek egy blokkos titkosítást, azaz legalább 128 bitet tartalmazó kulcs hosszúságú AES-t kell használniuk. |

## <a name="use-approved-machmackeyed-hash-algorithms"></a><a id="mac-hash"></a>Jóváhagyott MAC/HMAC/kulcsos kivonatoló algoritmusok használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>A termékek csak a jóváhagyott üzenet-hitelesítési kód (MAC) vagy a hash-alapú üzenethitelesítő kód (HMAC) algoritmusokat használják.</p><p>Az üzenet-hitelesítési kód (MAC) egy üzenethez csatolt információ, amely lehetővé teszi a címzett számára, hogy ellenőrizze a küldő hitelességét és az üzenet integritását titkos kulccsal. A kivonatoló MAC ([HMAC](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) vagy a [Block-Cipher-alapú Mac](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) használata engedélyezett, ha az összes alapul szolgáló kivonatoló vagy szimmetrikus titkosítási algoritmus is jóvá van hagyva; Ez jelenleg magában foglalja a HMAC-SHA2 függvényeket (HMAC-SHA256, HMAC-SHA384 és HMAC-SHA512), valamint a CMAC/OMAC1 és a OMAC2 blokk rejtjel-alapú Mac gépeket (ezek az AES-alapúak).</p><p>A HMAC-SHA1 használata engedélyezhető a platformok kompatibilitása szempontjából, de kivételt kell beírnia az eljárásba, és a szervezete titkosítási felülvizsgálatán át kell esnie. A HMACs kevesebb mint 128 bit-re való csonkítása nem engedélyezett. Ha az ügyfél módszereit használja a kulcsok és az Adatkivonatok kivonatolására, a használat előtt el kell végeznie a szervezet titkosítási Tanácsának felülvizsgálatát.</p>|

## <a name="use-only-approved-cryptographic-hash-functions"></a><a id="hash-functions"></a>Csak jóváhagyott titkosítási kivonatoló függvények használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>A termékeknek az SHA-2 kivonatoló algoritmust (SHA256, SHA384 és SHA512) kell használniuk. Ha rövidebb kivonatra van szükség, például egy 128 bites kimeneti hosszt ahhoz, hogy illeszkedjen egy rövidebb MD5-kivonattal kialakított adatszerkezethez, a termékcsoportok a SHA2-kivonatok egyikét (általában SHA256) is lerövidítik. Vegye figyelembe, hogy a SHA384 a SHA512 csonkolt verziója. A titkosítási kivonatok 128-nál kevesebb biten való csonkítása nem engedélyezett. Az új kód nem használhatja a MD2, a MD4, az MD5, az SHA-0, az SHA-1 vagy az RIPEMD kivonatoló algoritmust. Ezekhez az algoritmusokhoz a kivonatoló ütközések számítási feloldhatók, ami gyakorlatilag megszakítja azokat.</p><p>Engedélyezett .NET-kivonatoló algoritmusok a felügyelt titkosítási rugalmassághoz (preferencia sorrendben):</p><ul><li>SHA512Cng (FIPS-kompatibilis)</li><li>SHA384Cng (FIPS-kompatibilis)</li><li>SHA256Cng (FIPS-kompatibilis)</li><li>SHA512Managed (nem FIPS-kompatibilis) (használja a SHA512 as algoritmus nevét a HashAlgorithm. Create vagy CryptoConfig. CreateFromName) hívásokban.</li><li>SHA384Managed (nem FIPS-kompatibilis) (használja a SHA384 as algoritmus nevét a HashAlgorithm. Create vagy CryptoConfig. CreateFromName) hívásokban.</li><li>SHA256Managed (nem FIPS-kompatibilis) (használja a SHA256 as algoritmus nevét a HashAlgorithm. Create vagy CryptoConfig. CreateFromName) hívásokban.</li><li>SHA512CryptoServiceProvider (FIPS-kompatibilis)</li><li>SHA256CryptoServiceProvider (FIPS-kompatibilis)</li><li>SHA384CryptoServiceProvider (FIPS-kompatibilis)</li></ul>| 

## <a name="use-strong-encryption-algorithms-to-encrypt-data-in-the-database"></a><a id="strong-db"></a>Erős titkosítási algoritmusok használata az adatbázisban tárolt adattitkosításhoz

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Titkosítási algoritmus kiválasztása](/sql/relational-databases/security/encryption/choose-an-encryption-algorithm) |
| **Lépések** | A titkosítási algoritmusok olyan adatátalakításokat határoznak meg, amelyeket jogosulatlan felhasználók nem könnyen visszafordítanak. SQL Server lehetővé teszi, hogy a rendszergazdák és a fejlesztők több algoritmus közül választhatnak, többek között a DES, a Triple DES, az TRIPLE_DES_3KEY, az RC2, az RC4, a 128 bites RC4, a DESX, a 128 bites AES, a 192 bites AES és a 256 bites AES |

## <a name="ssis-packages-should-be-encrypted-and-digitally-signed"></a><a id="ssis-signed"></a>A SSIS-csomagokat titkosítani és digitálisan alá kell írni

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [A digitális aláírásokkal, a](/sql/integration-services/security/identify-the-source-of-packages-with-digital-signatures) [fenyegetésekkel és a sebezhetőségek enyhítésével (Integration Services)](/sql/integration-services/security/security-overview-integration-services) rendelkező csomagok forrásainak azonosítása |
| **Lépések** | A csomag forrása a csomagot létrehozó személy vagy szervezet. A csomagok ismeretlen vagy nem megbízható forrásból való futtatása kockázatos lehet. A SSIS-csomagok jogosulatlan illetéktelen módosításának megakadályozása érdekében a digitális aláírásokat kell használni. Emellett a csomagok titkosságának biztosításához a tárolás/átvitel során a SSIS-csomagokat titkosítani kell |

## <a name="add-digital-signature-to-critical-database-securables"></a><a id="securables-db"></a>Digitális aláírás hozzáadása a kritikus adatbázis-biztonságos elemek migrálására

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [ALÁÍRÁS hozzáadása (Transact-SQL)](/sql/t-sql/statements/add-signature-transact-sql) |
| **Lépések** | Azokban az esetekben, amikor a biztonságos adatbázis integritását ellenőrizni kell, a digitális aláírásokat kell használni. Az adatbázis biztonságos elemek migrálására, például tárolt eljárás, függvény, szerelvény vagy trigger lehet digitálisan aláírva. Alább látható egy példa arra, ha ez hasznos lehet: tegyük fel, hogy egy ISV (független szoftvergyártó) támogatta az egyik ügyfele számára elérhető szoftvert. A támogatás megkezdése előtt az ISV-nak biztosítania kell, hogy a szoftverben biztonságos adatbázis nem módosult véletlenül vagy rosszindulatú kísérlet útján. Ha a biztonságos digitális aláírással rendelkezik, az ISV ellenőrizheti a digitális aláírását, és ellenőrizheti annak integritását.| 

## <a name="use-sql-server-ekm-to-protect-encryption-keys"></a><a id="ekm-keys"></a>Az SQL Server EKM használata a titkosítási kulcsok védeleméhez

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [SQL Server bővíthető kulcskezelő (EKM)](/sql/relational-databases/security/encryption/extensible-key-management-ekm), [bővíthető kulcskezelő Azure Key Vault használatával (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server) |
| **Lépések** | SQL Server bővíthető kulcskezelő szolgáltatás lehetővé teszi, hogy az adatbázisfájlok tárolására szolgáló titkosítási kulcsok egy off-Box eszközön, például egy intelligens kártyán, USB-eszközön vagy EKM/HSM-modulban legyenek tárolva. Ez az adatvédelmet is lehetővé teszi az adatbázis-rendszergazdáktól (a sysadmin csoport tagjai kivételével). Az adattitkosítás olyan titkosítási kulcsok használatával végezhető el, amelyekhez csak az adatbázis-felhasználó fér hozzá a külső EKM/HSM-modulhoz. |

## <a name="use-alwaysencrypted-feature-if-encryption-keys-should-not-be-revealed-to-database-engine"></a><a id="keys-engine"></a>AlwaysEncrypted szolgáltatás használata, ha a titkosítási kulcsokat nem szabad felmutatni az adatbázismotor számára

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | SQL Azure, helyszíni |
| **Attribútumok**              | SQL-verzió – V12, MsSQL2016 |
| **Hivatkozások**              | [Always Encrypted (adatbázismotor)](/sql/relational-databases/security/encryption/always-encrypted-database-engine) |
| **Lépések** | A Always Encrypted a bizalmas adatok, például a hitelkártya-számok vagy a nemzeti azonosító számok (például az Egyesült államokbeli társadalombiztosítási számok) védelmére szolgáló szolgáltatás, amely Azure SQL Database vagy SQL Server adatbázisokban van tárolva. Always Encrypted lehetővé teszi az ügyfelek számára a bizalmas adatok titkosítását az ügyfélalkalmazások belsejében, és soha ne fedje fel a titkosítási kulcsokat az adatbázismotor számára (SQL Database vagy SQL Server). Ennek eredményeképpen a Always Encrypted elkülöníti az adattulajdonost (és megtekintheti) és az adatkezelést végzőket (de nem rendelkezik hozzáféréssel) |

## <a name="store-cryptographic-keys-securely-on-iot-device"></a><a id="keys-iot"></a>Titkosítási kulcsok biztonságos tárolása a IoT-eszközön

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Eszköz operációs rendszer – Windows IoT Core, Device connectivity – Azure IoT Device SDK-k |
| **Hivatkozások**              | [TPM a Windows IoT Core](/windows/iot-core/secure-your-device/TPM)-ban, [TPM beállítása a Windows IoT Core-on](/windows/iot-core/secure-your-device/setuptpm), [Azure IoT Device SDK TPM](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Lépések** | A szimmetrikus vagy a tanúsítvány titkos kulcsa biztonságos a hardveres védett tárolóban, például a TPM-vagy intelligenskártya-chipek esetében. A Windows 10 IoT Core támogatja a TPM felhasználóját, és több kompatibilis TPM is használható: https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpm . A belső vezérlőprogram vagy a különálló TPM használata javasolt. A szoftveres TPM csak fejlesztési és tesztelési célokra használható. Ha a TPM elérhetővé válik, és a kulcsok be vannak építve, a jogkivonatot létrehozó kódot írás nélkül kell megírni. | 

### <a name="example"></a>Példa
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Ahogy látható, az eszköz elsődleges kulcsa nem szerepel a kódban. Ehelyett a rendszer a 0. tárolóhelyen tárolja a TPM-ben. A TPM-eszköz egy rövid élettartamú SAS-tokent hoz létre, amelyet aztán a IoT Hubhoz való kapcsolódáshoz használ. 

## <a name="generate-a-random-symmetric-key-of-sufficient-length-for-authentication-to-iot-hub"></a><a id="random-hub"></a>A IoT Hub hitelesítéshez elegendő hosszúságú véletlenszerű szimmetrikus kulcs létrehozása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT Cloud Gateway | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Átjáró választása – Azure IoT Hub |
| **Hivatkozások**              | N/A  |
| **Lépések** | IoT Hub tartalmaz egy eszköz-identitási beállításjegyzéket, és egy eszköz kiépítés közben automatikusan létrehoz egy véletlenszerű szimmetrikus kulcsot. Javasoljuk, hogy az Azure IoT Hub Identity Registry ezen funkcióját használja a hitelesítéshez használt kulcs létrehozásához. A IoT Hub az eszköz létrehozásakor is lehetővé teszi az adott kulcs megadását. Ha a rendszer a IoT Hubon kívül hozza létre a kulcsot az eszköz kiépítés során, akkor azt javasoljuk, hogy hozzon létre egy véletlenszerű szimmetrikus kulcsot vagy legalább 256 bitet. |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-use-pin-and-allows-remote-wiping"></a><a id="pin-remote"></a>Győződjön meg arról, hogy van olyan eszköz-felügyeleti szabályzat, amely használatához PIN-kód szükséges, és lehetővé teszi a távoli törlést

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM Mobile-ügyfél | 
| **SDL-fázis**               | Üzembe helyezés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Győződjön meg arról, hogy van olyan eszköz-felügyeleti szabályzat, amely használatához PIN-kód szükséges, és lehetővé teszi a távoli törlést |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-pinpasswordauto-lock-and-encrypts-all-data-eg-bitlocker"></a><a id="bitlocker"></a>Győződjön meg arról, hogy van olyan eszköz-felügyeleti házirend, amely PIN-kód/jelszó/automatikus zárolást igényel, és titkosítja az összes adathalmazt (például BitLocker).

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM Outlook-ügyfél | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Győződjön meg arról, hogy van olyan eszköz-felügyeleti házirend, amely PIN-kód/jelszó/automatikus zárolást igényel, és titkosítja az összes adathalmazt (például BitLocker). |

## <a name="ensure-that-signing-keys-are-rolled-over-when-using-identity-server"></a><a id="rolled-server"></a>Ellenőrizze, hogy az aláíró kulcsok át lesznek-e tekerve az Identity Server használatakor

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identity Server | 
| **SDL-fázis**               | Üzembe helyezés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Identity Server – kulcsok, aláírások és titkosítás](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Lépések** | Ellenőrizze, hogy az aláíró kulcsok át lesznek-e tekerve az Identity Server használatakor. A hivatkozások szakaszban található hivatkozás azt mutatja be, hogy ezt hogyan kell megtervezni anélkül, hogy a rendszer kiesést okozna az Identity Serveren alapuló alkalmazásokhoz. |

## <a name="ensure-that-cryptographically-strong-client-id-client-secret-are-used-in-identity-server"></a><a id="client-server"></a>Győződjön meg arról, hogy a kriptográfiailag erős ügyfél-azonosító, az ügyfél titkos kulcsa az Identity Serverben van használatban

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identity Server | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>Győződjön meg arról, hogy a kriptográfiai szempontból erős ügyfél-azonosító, az ügyfél titkos kulcsa használatban van az Identity Serverben. A következő irányelveket kell használni az ügyfél-azonosító és a titkos kulcs létrehozásakor:</p><ul><li>Véletlenszerű GUID létrehozása ügyfél-AZONOSÍTÓként</li><li>Titkosítási szempontból véletlenszerű 256 bites kulcs létrehozása titokként</li></ul>|
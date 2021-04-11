---
title: Biztonsági gyakorlatok gyártók számára – Azure IoT Device kiépítési szolgáltatás
description: Az OEM-ek és az eszközök általános biztonsági eljárásainak áttekintése, amelyek előkészítik az eszközöket az Azure IoT Device kiépítési szolgáltatásban (DPS) való regisztrálásra.
author: timlt
ms.author: timlt
ms.date: 3/02/2020
ms.topic: conceptual
ms.service: iot-dps
ms.custom: iot-p0-scenario, iot-devices-deviceOEM
ms.reviewer: nberdy
ms.openlocfilehash: dad46e21b35225855d787c367d112362fc95a463
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107010541"
---
# <a name="security-practices-for-azure-iot-device-manufacturers"></a>Az Azure IoT-eszközök gyártóinak biztonsági eljárásai
Ahogy egyre több gyártó bocsát ki IoT-eszközöket, hasznos lehet az általános gyakorlatokra vonatkozó útmutatást azonosítani. Ez a cikk az Azure IoT Device kiépítési szolgáltatással (DPS) való használatra vonatkozó ajánlott biztonsági eljárásokat foglalja össze.  

> [!div class="checklist"]
> * Az eszköz hitelesítési beállításainak kiválasztása
> * Tanúsítványok telepítése IoT-eszközökön
> * Platformmegbízhatósági modul (TPM) integrálása a gyártási folyamatba

## <a name="selecting-device-authentication-options"></a>Az eszköz hitelesítési beállításainak kiválasztása
A IoT-eszközök biztonsági mértékének végső célja, hogy biztonságos IoT-megoldást hozzon létre. Az olyan problémák, mint például a hardveres korlátozások, a díjak és a biztonsági szakértelem szintje minden kiválasztott lehetőségre hatással van. Ezen túlmenően a biztonsági módszer azt is befolyásolja, hogy a IoT-eszközök hogyan csatlakoznak a felhőhöz. Noha a [IoT-biztonság több eleme](https://www.microsoft.com/research/publication/seven-properties-highly-secure-devices/) is van, az összes ügyfél által felmerülő kulcsfontosságú elem a használandó hitelesítési típus. 

Három széles körben használt hitelesítési típus X. 509 tanúsítvány, platformmegbízhatósági modul (TPM) és szimmetrikus kulcs. Míg más hitelesítési típusok léteznek, a legtöbb ügyfél, akik az Azure IoT-megoldásokat építenek, a három típus egyikét használják. Ennek a cikknek a többi része az egyes hitelesítési típusok használatának előnyeit és hátrányait is felhasználja.

### <a name="x509-certificate"></a>X. 509 tanúsítvány
Az X. 509 tanúsítványok olyan digitális identitások, amelyek a hitelesítéshez használhatók. Az X. 509 tanúsítvány szabványát az [IETF RFC 5280](https://tools.ietf.org/html/rfc5280)dokumentációja ismerteti. Az Azure IoT kétféleképpen hitelesítheti a tanúsítványokat:
- Ujjlenyomat. Az ujjlenyomat-algoritmus egy tanúsítványon fut egy hexadecimális karakterlánc létrehozásához. A generált karakterlánc a tanúsítvány egyedi termékazonosító. 
- HITELESÍTÉSSZOLGÁLTATÓI hitelesítés egy teljes lánc alapján. A tanúsítványlánc az összes tanúsítvány hierarchikus listája, amely a végfelhasználói (EE-) tanúsítványok hitelesítéséhez szükséges. Egy EE-tanúsítvány hitelesítéséhez szükség van a láncban lévő összes tanúsítvány hitelesítésére, beleértve a megbízható legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT is. 

Az X. 509 előnyei:
- Az X. 509 az Azure IoT által támogatott legbiztonságosabb hitelesítési típus.
- Az X. 509 magas szintű vezérlést tesz lehetővé a Tanúsítványkezelők szempontjából.
- Az X. 509 alapú hitelesítési megoldások biztosításához számos gyártó érhető el.

Az X. 509 hátránya:
- Számos ügyfélnek előfordulhat, hogy külső szállítókat kell használnia a tanúsítványokhoz.
- A Tanúsítványkezelő költséges lehet, és a megoldás teljes költsége is felhasználható.
- A tanúsítvány életciklus-kezelése nehéz lehet, ha a logisztika nem jól ismert. 

### <a name="trusted-platform-module-tpm"></a>Platformmegbízhatósági modul (TPM)
A TPM, más néven [ISO/IEC 11889](https://www.iso.org/standard/66510.html), egy standard a titkosítási kulcsok biztonságos létrehozásához és tárolásához. A TPM egy virtuális vagy fizikai I/O-eszközre is hivatkozik, amely a szabványt megvalósító modulokkal kommunikál. A TPM-eszközök különálló hardverként, integrált hardverként, belső vezérlőprogram-alapú modulként vagy szoftveres modulként is létezhetnek. 

Két fő különbség van a TPM és a szimmetrikus kulcsok között: 
- A TPM-chipek X. 509 tanúsítványokat is tárolhatnak.
- A DPS-ben a TPM-igazolás a TPM-kiterjesztési kulcsot (EK) használja, aszimmetrikus hitelesítés formájában. Aszimmetrikus hitelesítés esetén a rendszer nyilvános kulcsot használ a titkosításhoz, és egy külön titkos kulcsot használ a visszafejtéshez. Ezzel szemben a szimmetrikus kulcsok szimmetrikus hitelesítést használnak, ahol a titkos kulcsot a titkosítás és a visszafejtés is használja. 

A TPM előnyei:
- A TPM számos Windows-eszközön szabványos hardverként szerepel, az operációs rendszer beépített támogatásával. 
- A TPM-igazolás könnyebben biztonságossá válik, mint a közös hozzáférésű aláírás (SAS) jogkivonat-alapú szimmetrikus kulcsának igazolása.
- Egyszerűen lemondhatja és megújíthatja vagy leállíthatja az eszköz hitelesítő adatait. A DPS automatikusan legörgeti a IoT Hub hitelesítő adatokat, amikor a TPM-eszköz kiépítése esedékes.

A TPM hátránya: 
- A TPM bonyolultak, és nehéz lehet használni. 
- A TPM szolgáltatással való alkalmazásfejlesztés nehéz, ha fizikai TPM vagy minőségi emulátor van.
- Előfordulhat, hogy újra kell terveznie az eszköz tábláját, hogy tartalmazza a TPM modult a hardverben. 
- Ha az EK-t a TPM modulban állítja be, a megsemmisíti a TPM identitását, és létrehoz egy újat. Bár a fizikai lapka ugyanaz marad, új identitással rendelkezik a IoT-megoldásban.

### <a name="symmetric-key"></a>Szimmetrikus kulcs
A szimmetrikus kulcsokkal a rendszer ugyanazt a kulcsot használja az üzenetek titkosítására és visszafejtésére. Ennek eredményeképpen ugyanazt a kulcsot ismeri az eszköz és a hitelesítést végző szolgáltatás is. Az Azure IoT támogatja az SAS-jogkivonat-alapú szimmetrikus kulcsok kapcsolatait. A szimmetrikus kulcsos hitelesítéshez jelentős tulajdonosi felelősségre van szükség a kulcsok biztonságossá tételéhez és az X. 509 hitelesítéssel egyenlő szintű biztonság eléréséhez. Szimmetrikus kulcsok használata esetén az ajánlott eljárás a kulcsok védelme a hardveres biztonsági modul (HSM) használatával.

Profik a szimmetrikus kulcshoz:
- A szimmetrikus kulcsok használatával megkezdheti a hitelesítés első lépéseit.
- A szimmetrikus kulcsok használata egyszerűsíti a folyamatot, mert nincs külön a létrehozáshoz. 

A szimmetrikus kulcs hátránya: 
- A szimmetrikus kulcsok jelentős mértékű erőfeszítést tesznek a kulcsok biztonságossá tételéhez. Ugyanaz a kulcs van megosztva az eszköz és a felhő között, ami azt jelenti, hogy a kulcsot két helyen kell védeni. Ezzel szemben a TPM és az X. 509 tanúsítványokkal kapcsolatos kihívás igazolja, hogy a nyilvános kulcs birtokosa nem fedi fel a titkos kulcsot.
- A szimmetrikus kulcsok megkönnyítik a gyenge biztonsági eljárások követését. A szimmetrikus kulcsokkal kapcsolatos gyakori tendencia, hogy a titkosítatlan kulcsokat az eszközökön kódolja. Habár ez a gyakorlat kényelmes, a kulcsok sebezhetővé válik. A szimmetrikus kulcs biztonságos tárolásával csökkentheti a kockázatokat az eszközön. Ha azonban a prioritása végső soron a kényelem helyett a biztonság, az X. 509 tanúsítványokat vagy a TPM-t használja a hitelesítéshez. 

### <a name="shared-symmetric-key"></a>Megosztott szimmetrikus kulcs
A szimmetrikus kulcsos hitelesítés a megosztott szimmetrikus kulcsként ismert változata. Ez a módszer minden eszközön ugyanazt a szimmetrikus kulcsot használja. A javaslat célja, hogy elkerülje a megosztott szimmetrikus kulcsok használatát az eszközökön. 

Pro megosztott szimmetrikus kulcshoz:
- Egyszerűen megvalósítható és olcsó a méretezéshez. 

A megosztott szimmetrikus kulcs hátránya: 
- Nagyon sebezhető a támadásokkal szemben. Az egyszerű megvalósítás előnyeit messze felülmúlja a kockázat. 
- Az eszközöket bárki megszemélyesítheti, ha megszerezik a megosztott kulcsot.
- Ha egy olyan megosztott szimmetrikus kulcsra támaszkodik, amely biztonságban van, akkor a rendszer valószínűleg elveszíti az eszközök felügyeletét. 

### <a name="making-the-right-choice-for-your-devices"></a>Az eszközök megfelelő választásának kihasználása
A hitelesítési módszer kiválasztásához ügyeljen arra, hogy az egyedi gyártási folyamat minden megközelítésének előnyeit és költségeit figyelembe vegye.  Az eszközök hitelesítéséhez általában egy fordított kapcsolat áll fenn egy adott megközelítés biztonságossá tétele és a megfelelő működésük között.  

## <a name="installing-certificates-on-iot-devices"></a>Tanúsítványok telepítése IoT-eszközökön
Ha X. 509 tanúsítványokat használ a IoT-eszközök hitelesítéséhez, ez a szakasz útmutatást nyújt ahhoz, hogyan integrálhatja a tanúsítványokat a gyártási folyamatba. Több döntést kell meghoznia.  Ezek közé tartoznak a gyakori tanúsítvány-változókra vonatkozó döntések, a tanúsítványok létrehozása és a telepítésének időpontja. 

Ha jelszavakat használ, megkérdezheti, hogy miért nem használhatja ugyanazt a tanúsítványt az összes eszközön, ugyanúgy, ahogy az összes eszközön ugyanazt a jelszót fogja tudni használni. Először ugyanazt a jelszót kell használnia, bárhol is veszélyes. A gyakorlat a vállalatok számára jelentős DDoS-támadásokat tett elérhetővé, beleértve a DNS-t az USA keleti partján, néhány évvel ezelőtt. Soha ne használja ugyanazt a jelszót, akár személyes fiókkal is. Másodszor, a tanúsítvány nem jelszó, hanem egyedi identitás. A jelszó olyan titkos kód, amelyet bárki használhat egy biztonságos épületben lévő ajtó megnyitására.  Ez az a dolog, amit tud, és a jelszó megadásával bárki megszerezheti a bejáratot.  A tanúsítvány olyan, mint az illesztőprogram licence a fotóval és egyéb adatokkal, amelyeket egy őr számára jeleníthet meg, hogy biztonságos épületbe kerüljön. Az Ön számára van kötve.  Ha a Gárda pontosan megfelel az illesztőprogram-licencekkel rendelkező személyeknek, csak a licencét (identitását) használhatja a bejárathoz. 

### <a name="variables-involved-in-certificate-decisions"></a>A tanúsítvány határozataiban érintett változók
Vegye figyelembe a következő változókat, és azt, hogy mindegyik hogyan befolyásolja a teljes gyártási folyamatot. 

#### <a name="where-the-certificate-root-of-trust-comes-from"></a>Honnan származik a tanúsítvány gyökerének megbízhatósága
A nyilvános kulcsokra épülő infrastruktúra (PKI) kezelése költséges és összetett lehet.  Különösen, ha a vállalata nem rendelkezik a PKI felügyeletével. A következő lehetőségek közül választhat:
- Használjon harmadik féltől származó PKI-t. Köztes aláíró tanúsítványokat külső gyártótól származó tanúsítványokból is vásárolhat. Használhat privát hitelesítésszolgáltatót (CA) is. 
- Saját felügyeletű PKI használata. Megtarthatja saját PKI-rendszerét, és létrehozhat saját tanúsítványokat.
- Használja a [Azure Sphere](https://azure.microsoft.com/services/azure-sphere/) biztonsági szolgáltatást. Ez a beállítás csak Azure Sphere eszközökre vonatkozik. 

#### <a name="where-certificates-are-stored"></a>A tanúsítványok tárolási helye
Van néhány tényező, amely hatással van a tanúsítványok tárolásának helye szerinti döntésre. Ezek a tényezők közé tartoznak az eszköz típusa, a várt haszonkulcsok (akár biztonságos tárterület is biztosítható), az eszköz képességei és a meglévő biztonsági technológiák az eszközön, amelyet használhat. Vegye figyelembe a következő lehetőségeket:
- Hardveres biztonsági modulban (HSM). A HSM használata kifejezetten ajánlott. Győződjön meg arról, hogy az eszköz Vezérlőpultján már telepítve van-e a HSM. Ha tudja, hogy nem rendelkezik HSM-rel, akkor működjön együtt a hardver gyártójával, és azonosítsa az igényeinek megfelelő HSM-et.
- A lemez biztonságos helyén, például egy megbízható végrehajtási környezetben (TEE).
- A helyi fájlrendszerben vagy tanúsítványtárolóban. Például a Windows-tanúsítványtároló. 

#### <a name="connectivity-at-the-factory"></a>Kapcsolat a gyárban
A gyári kapcsolat határozza meg, hogyan és mikor kapja meg az eszközökön telepítendő tanúsítványokat. A kapcsolódási lehetőségek a következők:
- Connectivity. A kapcsolat optimális, így egyszerűsíti a tanúsítványok helyi generálásának folyamatát. 
- Nincs kapcsolat. Ebben az esetben egy HITELESÍTÉSSZOLGÁLTATÓ aláírt tanúsítványát használja az eszközök tanúsítványainak helyi és offline létrehozásához. 
- Nincs kapcsolat. Ebben az esetben az idő előtt létrehozott tanúsítványokat is beszerezheti. Vagy használhat offline PKI-t a tanúsítványok helyi létrehozásához.

#### <a name="audit-requirement"></a>Naplózási követelmény
A létrehozott eszközök típusától függően lehet, hogy szabályozási követelményt kell létrehoznia ahhoz, hogy az eszköz-identitások hogyan vannak telepítve az eszközökön. Az auditálás jelentős termelési költségeket jelent. Tehát a legtöbb esetben csak szükség esetén hajtsa végre. Ha nem biztos benne, hogy van-e szükség naplózásra, forduljon a vállalat jogi részlegéhez. A naplózási lehetőségek a következők: 
- Nem érzékeny iparág. Nincs szükség naplózásra.
- Érzékeny iparág. A tanúsítványokat a megfelelőségi tanúsítvány követelményeinek megfelelően a biztonságos helyiségbe kell telepíteni. Ha a tanúsítványok telepítéséhez biztonságos helyiségre van szükség, akkor valószínűleg már tisztában van azzal, hogy a tanúsítványok hogyan lesznek telepítve az eszközökön. Valószínűleg már rendelkezik egy naplózási rendszerrel. 

#### <a name="length-of-certificate-validity"></a>A tanúsítvány érvényességének hossza
Az illesztőprogram-licencekhez hasonlóan a tanúsítványok a létrehozáskor beállított lejárati dátummal rendelkeznek. A tanúsítvány érvényességi időtartamának lehetősége:
- Nem szükséges megújítani.  Ez a módszer hosszú megújítási időszakot használ, így soha nem kell megújítani a tanúsítványt az eszköz élettartama alatt. Habár ez a megközelítés kényelmes, az is kockázatos.  Csökkentheti a kockázatot a biztonságos tárterület, például egy HSM használatával az eszközökön. Az ajánlott eljárás azonban a hosszú élettartamú tanúsítványok használatának elkerülése.
- Megújításra van szükség.  Az eszköz élettartama alatt meg kell újítania a tanúsítványt. A tanúsítvány érvényességi időtartama függ a környezettől, és szüksége lesz egy megújítási stratégiára.  A stratégiának tartalmaznia kell a tanúsítványok beszerzésének helyét, valamint azt, hogy az eszközök milyen típusú működés közben használják a megújítási folyamatokat. 

### <a name="when-to-generate-certificates"></a>Mikor kell tanúsítványokat előállítani
A gyár internetkapcsolati képességei hatással lesznek a tanúsítványok generálásának folyamatára. A tanúsítványok létrehozásához több lehetőség közül választhat: 

- Előre betöltött tanúsítványok.  Egyes HSM-szállítók olyan prémium szintű szolgáltatást kínálnak, amelyben a HSM-szállító telepíti a tanúsítványokat az ügyfél számára. Első lépésként a HSM-gyártó hozzáférést biztosít egy aláíró tanúsítványhoz. Ezután a HSM-szállító telepíti az aláíró tanúsítvánnyal aláírt tanúsítványokat az ügyfél által megvásárolt összes HSM-hez. Az összes ügyfélnek telepítenie kell a HSM-t az eszközre. A szolgáltatás pedig növeli a költségeket, segít a gyártási folyamat egyszerűsítésében.  És feloldja azt a kérdést, hogy mikor telepítse a tanúsítványokat.
- Eszköz által generált tanúsítványok.  Ha az eszközök belsőleg állítanak elő tanúsítványokat, akkor a nyilvános X. 509 tanúsítványt ki kell bontania az eszközről a DPS-ben való regisztráláshoz. 
- Csatlakoztatott gyár.  Ha a gyár rendelkezik kapcsolattal, akkor bármikor létrehozhat tanúsítványokat, amikor csak szüksége van rájuk.
- Offline gyár saját PKI-vel. Ha a gyár nem rendelkezik kapcsolattal, és a saját PKI-t offline támogatással használja, akkor a tanúsítványok szükség esetén létrehozhatók.
- Offline üzem külső PKI-mel. Ha a gyár nem rendelkezik kapcsolattal, és egy külső gyártótól származó PKI-t használ, akkor az idő előtt elő kell készítenie a tanúsítványokat. És a tanúsítványokat a kapcsolattal rendelkező helyről kell előállítani. 

### <a name="when-to-install-certificates"></a>Mikor kell telepíteni a tanúsítványokat
Miután létrehozta a tanúsítványokat a IoT-eszközökhöz, ezeket telepítheti az eszközökön. 

Ha előre betöltött tanúsítványokat használ HSM-sel, a folyamat egyszerűsített. Miután a HSM telepítve lett az eszközön, az eszköz kódja hozzáférhet. Ezután hívja meg a HSM API-kat a HSM-ben tárolt tanúsítvány eléréséhez. Ez a módszer a legalkalmasabb a gyártási folyamathoz. 

Ha nem használ előre betöltött tanúsítványt, a termelési folyamat részeként telepítenie kell a tanúsítványt. A legegyszerűbb módszer a tanúsítvány telepítése a HSM-ben a kezdeti belső vezérlőprogram rendszerképének megkezdése előtt. A folyamatnak hozzá kell adnia egy lépést a rendszerkép telepítéséhez az egyes eszközökön. A lépés elvégzése után a végső minőségi ellenőrzéseket és az egyéb lépéseket is futtathatja az eszköz csomagolása és szállítása előtt. 

Elérhetők olyan szoftvereszközök, amelyek lehetővé teszik a telepítési folyamat és a végső minőség-ellenőrzés egyetlen lépésben történő futtatását. Ezeket az eszközöket módosíthatja tanúsítvány létrehozásához, illetve egy tanúsítvány egy előre generált tanúsítványtárolóból való lekéréséhez. Ezután a szoftver telepítheti a tanúsítványt, amelyre telepítenie kell. Az ilyen típusú szoftvereszközök lehetővé teszik a termelési minőség gyártásának méretezését. 

Miután telepítette a tanúsítványokat az eszközökre, a következő lépés az eszközök a [DPS](about-iot-dps.md)-vel való regisztrálásának megismerése. 

## <a name="integrating-a-tpm-into-the-manufacturing-process"></a>A TPM integrálása a gyártási folyamatba
Ha TPM-t használ a IoT-eszközök hitelesítéséhez, ez a szakasz útmutatást nyújt. Az útmutató a széles körben használt TPM 2,0-eszközökre vonatkozik, amelyeken a kivonatoló hitelesítésre szolgáló HMAC-kulcs támogatása támogatott. A TPM-chipek TPM-specifikációja egy ISO szabvány, amelyet a Trusted Computing Group tart fenn. A TPM-mel kapcsolatos további információkért tekintse meg a [tpm 2,0](https://trustedcomputinggroup.org/tpm-library-specification/) és az [ISO/IEC 11889](https://www.iso.org/standard/66510.html)specifikációit. 

### <a name="taking-ownership-of-the-tpm"></a>A TPM tulajdonjogának átvétele
Az eszközök TPM-lapka használatával történő előállításának kritikus lépése a TPM tulajdonjogának átvétele. Erre a lépésre azért van szükség, hogy meg tudja adni az eszköz tulajdonosának kulcsát. Az első lépés a jóváhagyó kulcs (EK) kibontása az eszközről. A következő lépés az, hogy ténylegesen tulajdonjogot igényeljen. A megvalósítás módja attól függ, hogy melyik TPM-t és operációs rendszert használja. Ha szükséges, forduljon a TPM gyártójához vagy az eszköz operációs rendszerének fejlesztőhöz, és határozza meg, hogyan igényelhet tulajdonjogot. 

A gyártási folyamat során különböző időpontokban kinyerheti az EK-t és a jogcím tulajdonosát, ami rugalmasságot biztosít. Számos gyártó kihasználja ezt a rugalmasságot egy hardveres biztonsági modul (HSM) hozzáadásával az eszközök biztonságának növelése érdekében. Ez a szakasz útmutatást nyújt az EK kibontásához, amikor a TPM tulajdonjogának igénylését, valamint a lépések gyártási ütemtervbe való integrálásának szempontjait ismerteti. 

> [!IMPORTANT]
> A következő útmutató feltételezi, hogy diszkrét, belső vezérlőprogram vagy integrált TPM-t használ. Azokon a helyeken, ahol alkalmazható, az útmutató megjegyzéseket ad hozzá a nem diszkrét vagy a szoftveres TPM használatával. Ha szoftveres TPM-t használ, további lépések is előfordulhatnak, amelyeket ez az útmutató nem tartalmaz. A szoftver TPM számos implementációja van, amelyek túlmutatnak ennek a cikknek a hatókörén.  Általánosságban elmondható, hogy a szoftver TPM-et a következő általános gyártási idővonalba integrálja. Bár a szoftveresen emulált TPM alkalmas a prototípus-készítésre és a tesztelésre, nem tud ugyanolyan szintű biztonságot biztosítani, mint a különálló, belső vezérlőprogram vagy integrált TPM. Általános gyakorlatként Kerülje a szoftveres TPM használatát éles környezetben.

### <a name="general-manufacturing-timeline"></a>Általános gyártási ütemterv
A következő idővonal azt mutatja be, hogy a TPM hogyan halad egy éles folyamaton, és végződik egy eszközön. Minden gyártási folyamat egyedi, és ez az ütemterv a leggyakoribb mintákat mutatja. Az idősor útmutatást nyújt arra vonatkozóan, hogy mikor kell bizonyos műveleteket végezni a kulcsokkal. 

#### <a name="step-1-tpm-is-manufactured"></a>1. lépés: a TPM előállítása
- Ha TPM vásárol a gyártótól az eszközökön való használatra, tekintse meg, hogy szeretné-e kinyerni a nyilvános bejegyzések kulcsait (EK_pubs). Ez akkor hasznos, ha a gyártó megadja a leszállított eszközökkel rendelkező EK_pubs listáját. 
    > [!NOTE]
    > A TPM gyártójának írási hozzáférést adhat a regisztrációs listához, ha megosztott hozzáférési házirendeket használ a kiépítési szolgáltatásban.  Ez a megközelítés lehetővé teszi, hogy hozzáadja a TPM a regisztrációs listához.  Ez azonban a gyártási folyamat elején, és megbízhatósági kapcsolatot igényel a TPM-gyártóban. Ezt a saját kockázatával teheti meg. 

- Ha a TPM az eszközök gyártóinak való értékesítésére gyárt, érdemes megfontolnia az ügyfelek számára EK_pubs listáját a fizikai TPM együtt.  A EK_pubs elmenti az ügyfeleket a folyamatba. 
- Ha a TPM a saját eszközeivel kívánja használni, akkor határozza meg, hogy a folyamat melyik pontja a legalkalmasabb a EK_pub kinyeréséhez. A EK_pub kinyerhető az idősor bármely hátralévő pontján. 

#### <a name="step-2-tpm-is-installed-into-a-device"></a>2. lépés: a TPM telepítve van egy eszközre
Az éles folyamat ezen pontján tudnia kell, hogy melyik DPS-példányt fogja használni az eszköz. Ennek eredményeképpen hozzáadhat eszközöket a beléptetési listához az automatikus kiépítés érdekében. További információ az eszközök automatikus kiépítés: DPS- [dokumentáció](about-iot-dps.md).
- Ha még nem bontotta ki a EK_pub, most már jó ideje van. 
- A TPM telepítési folyamata alapján ez a lépés is jó alkalom a TPM tulajdonjogának átvételére. 

#### <a name="step-3-device-has-firmware-and-software-installed"></a>3. lépés: az eszközön telepítve van a belső vezérlőprogram és a szoftver
A folyamat ezen pontján telepítse a DPS-ügyfelet az azonosító hatókörével és a kiépítés globális URL-címével együtt.
- Most az utolsó lehetőség a EK_pub kinyerésére. Ha egy harmadik fél telepíti a szoftvert az eszközére, érdemes először kibontani a EK_pub.
- A gyártási folyamat ezen pontja ideális a TPM tulajdonjogának átvételéhez.  
    > [!NOTE]
    > Ha szoftveres TPM-t használ, akkor telepítheti most.  A EK_pub kibontása egy időben.

#### <a name="step-4-device-is-packaged-and-sent-to-the-warehouse"></a>4. lépés: az eszköz becsomagolva és elküldése a raktárba
Egy eszköz esetenként akár egy évig is eltarthat egy adott raktárban, mielőtt üzembe helyezi és kiépíti a DPS-t.  Ha egy eszköz hosszú ideig ül egy raktárban az üzembe helyezés előtt, előfordulhat, hogy az eszközt telepítő ügyfeleknek frissíteniük kell a belső vezérlőprogram, a szoftver vagy a lejárt hitelesítő adatokat.

#### <a name="step-5-device-is-installed-into-the-location"></a>5. lépés: az eszköz telepítve van a helyre
Miután az eszköz megérkezett a végső helyükre, az automatikus kiépítés a DPS használatával történik.

További információ: [kiépítés](about-iot-dps.md#provisioning-process) és TPM- [igazolás](concepts-tpm-attestation.md). 

## <a name="resources"></a>Források

A jelen cikkben ajánlott biztonsági eljárások mellett az Azure IoT olyan erőforrásokat biztosít, amelyek segítséget nyújtanak a biztonságos hardver kiválasztásában és a biztonságos IoT-telepítések létrehozásában: 
- Az Azure IoT [biztonsági javaslatai](../iot-fundamentals/security-recommendations.md) az üzembe helyezési folyamat irányításához. 
- A [Azure Security Center](https://azure.microsoft.com/services/security-center/) egy olyan szolgáltatást kínál, amely segít a biztonságos IoT-telepítések létrehozásában. 
- A hardveres környezet kiértékelésével kapcsolatos segítségért tekintse [meg a IoT-biztonság felmérését](https://download.microsoft.com/download/D/3/9/D3948E3C-D5DC-474E-B22F-81BA8ED7A446/Evaluating_Your_IOT_Security_whitepaper_EN_US.pdf)ismertető tanulmányt. 
- A biztonságos hardver kiválasztásával kapcsolatos segítségért tekintse [meg a IoT üzemelő példányának megfelelő biztonságos hardvert](https://download.microsoft.com/download/C/0/5/C05276D6-E602-4BB1-98A4-C29C88E57566/The_right_secure_hardware_for_your_IoT_deployment_EN_US.pdf). 
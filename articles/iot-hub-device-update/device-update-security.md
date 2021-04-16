---
title: Biztonsági beállítások eszközfrissítése Azure IoT Hub | Microsoft Docs
description: Az eszközök eszközfrissítésének IoT Hub az eszközök biztonságos frissítését.
author: lichris
ms.author: lichris
ms.date: 4/15/2021
ms.topic: conceptual
ms.service: iot-hub
ms.openlocfilehash: b10049e03e26cfe8da2bd57cc9f69dd933af706b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567298"
---
# <a name="device-update-security-model"></a>Eszközfrissítési biztonsági modell

A IoT Hub eszközfrissítés biztonságos módszert kínál az eszköz belső vezérlőprogramjának, rendszerképének és alkalmazásának frissítésére az IoT-eszközökön. A munkafolyamat egy teljes körű biztonságos csatornát biztosít egy teljes felügyeleti modellel, amely segítségével az eszközök igazolják, hogy a frissítés megbízható, módosítatlan és szándékos.

Az eszközfrissítési munkafolyamat minden lépése különböző biztonsági funkciókkal és folyamatokkal van védve, hogy a folyamat minden lépése biztonságos módon adja át a következőt. Az eszközfrissítési ügyfél azonosítja és megfelelően kezeli az illegitimate frissítési kéréseket. Az ügyfél minden letöltést ellenőriz annak érdekében, hogy a tartalom megbízható, módosítatlan és szándékos legyen.

## <a name="for-solution-operators"></a>Megoldáskezelők számára

Mivel a megoldáskezelők frissítéseket importálnak az eszközfrissítési példányukba, a szolgáltatás feltölti és ellenőrzi a frissítési bináris fájlokat, hogy egy rosszindulatú felhasználó ne módosította vagy cserélte-e fel őket. Az ellenőrzés után a Device Update [](./update-manifest.md) szolgáltatás létrehoz egy belső frissítési jegyzékfájlt, amely az importálási jegyzékből és más metaadatokból származó fájl-hashekkel rendelkezik. Ezt a frissítési jegyzékfájlt ezután aláírja az eszközfrissítési szolgáltatás.

Miután betöltötte a szolgáltatást, és az Azure-ban tárolta, az Azure Storage szolgáltatás automatikusan titkosítja a bináris fájlok frissítését és a kapcsolódó ügyfél-metaadatokat. Az eszközfrissítési szolgáltatás nem biztosít automatikusan további titkosítást, de lehetővé teszi a fejlesztők számára a tartalom titkosítását, mielőtt a tartalom elérné az eszközfrissítési szolgáltatást.

Amikor a megoldáskezelő egy eszköz frissítését kéri, a rendszer aláírt üzenetet küld a védett IoT Hub az eszközre. A kérelem aláírását az eszköz eszközfrissítési ügynöke ellenőrzi hitelesként. 

Az eredményül kapott bináris letöltések a frissítési jegyzék aláírásának ellenőrzésével vannak biztosítva. A frissítési jegyzékfájl tartalmazza a bináris fájl hasheit, így ha a jegyzék megbízhatónak lett va, a Device Update-ügynök megbízik a hashében, és megfelel a bináris fájloknak. Miután letöltötte és ellenőrizte a frissítési bináris adatokat, biztonságosan átadjuk az eszköz telepítőjéhez.

## <a name="for-device-builders"></a>Eszközszerkesztők számára

Annak érdekében, hogy az eszközfrissítési szolgáltatás egyszerű, kis teljesítményű eszközökre skálázjon le, a biztonsági modell nyers aszimmetrikus kulcsokat és nyers aláírásokat használ. JSON-alapú formátumokat, például JSON webtokeneket és JSON-& használják.

### <a name="securing-update-content-via-the-update-manifest"></a>A frissítési tartalom biztonságossá tétele a frissítési jegyzéken keresztül

A frissítési jegyzék két aláírással van ellenőrizve. Az aláírások aláírókulcsokból és  gyökérkulcsokból álló struktúra *használatával vannak létrehozva.*

Az eszközfrissítési ügynök beágyazott nyilvános kulcsokkal rendelkezik, amelyek minden eszközfrissítéssel kompatibilis eszközhöz használhatók. Ezek a *gyökérkulcsok.* A megfelelő titkos kulcsokat a Microsoft vezérli.

A Microsoft létrehoz egy nyilvános/titkos kulcspárt is, amely nem része az eszközfrissítési ügynöknek, vagy amely az eszközön van tárolva. Ez az *aláírókulcs.*

Amikor frissítést importál az IoT Hub eszközfrissítéseibe, és a szolgáltatás generálja a frissítési jegyzékfájlt, a szolgáltatás aláírja a jegyzékfájlt az aláírókulccsal, és magában foglalja magát az aláírókulcsot, amelyet egy gyökérkulcs ír alá. Amikor a frissítési jegyzékfájlt elküldi az eszköznek, az eszközfrissítési ügynök a következő aláírási adatokat kapja meg:

1. Maga az aláírási érték.
2. A jelentés létrehozásához használt #1.
3. A kulcs létrehozásához használt aláírókulcs nyilvános #1.
4. A nyilvános aláírókulcs aláírása a #3.
5. A kulcs létrehozásához használt legfelső szintű kulcs nyilvános #3.
6. Az adatok létrehozásához használt #4.

Az eszközfrissítési ügynök a fent megadott információk alapján ellenőrzi, hogy a nyilvános aláírókulcs aláírása a legfelső szintű kulccsal van-e aláírva. Az eszközfrissítési ügynök ezután ellenőrzi, hogy a frissítési jegyzék aláírását az aláírókulcs írja-e alá. Ha az összes aláírás helyes, a frissítési jegyzék megbízhatónak fog lenni a Device Update Agent számára. Mivel a frissítési jegyzékfájl magában foglalja a frissítési fájloknak megfelelő fájl-hashelőket, a frissítési fájlok akkor is megbízhatók, ha a hashek megegyeznek.

A legfelső szintű és aláírókulcsok lehetővé teszik, hogy a Microsoft rendszeres időközönként legördülje az aláírókulcsot, ami biztonsági ajánlott eljárás.

### <a name="json-web-signature-jws"></a>JSON webaláírás (JWS)

A a segítségével gondoskodik arról, hogy a által tartalmazott információkat `updateManifestSignature` `updateManifest` ne módosítják illetéktelenül. A `updateManifestSignature` előállítása JSON webes aláírással és JSON-webkulcsokkal, lehetővé téve a forrás ellenőrzését. Az aláírás egy Base64Url kódolású sztring, három szakaszsal, "." karakterekkel.  Tekintse meg [a jws_util.h](https://github.com/Azure/iot-hub-device-update/tree/main/src/utils/jws_utils) segítő metódusokat a JSON-kulcsok és -jogkivonatok ellenőrzéséhez és ellenőrzéséhez.

A JSON Web Signature egy széles körben használt [ajánlott IETF-szabvány](https://tools.ietf.org/html/rfc7515) tartalom JSON-alapú adatstruktúrák használatával való aláírásához. Ezzel biztosíthatja az adatok integritását az adatok aláírásának ellenőrzéséhez. További információt a JSON Web Signature (JWS) [RFC 7515 (JWS) RFC 7515 webhelyén talál.](https://www.rfc-editor.org/info/rfc7515)

### <a name="json-web-token"></a>JSON-webtoken

A JSON Web Tokens egy nyílt, iparági [szabványnak](https://tools.ietf.org/html/rfc7519) megfelelő módszer a jogcímek biztonságos ábrázolása két fél között.

### <a name="root-keys"></a>Legfelső szintű kulcsok

Minden eszközfrissítési eszköz gyökérkulcsokat tartalmaz. Ezek a kulcsok az eszközfrissítés összes aláírásának megbízhatósági gyökerét biztosítják. Minden aláírást ezen legfelső szintű kulcsok egyikében kell összefűzni, hogy megbízhatónak minősülnek.

A legfelső szintű kulcsok készlete idővel megváltozik, mivel biztonsági okokból rendszeresen cserélendők az aláírókulcsok. Ennek eredményeképpen az eszközfrissítési ügynök szoftverének frissítenie kell magát a legújabb legfelső szintű kulcsokkal. 

### <a name="signatures"></a>Aláírások

Minden aláíráshoz a legfelső szintű kulcsok egyikében aláírt aláírási (nyilvános) kulcs fog igazodni. Az aláírás azonosítja az aláírókulcs aláírásához használt legfelső szintű kulcsot. 

Az eszközfrissítési ügynöknek először ellenőriznie kell az aláírásokat annak ellenőrzésével, hogy az aláíró (nyilvános) kulcs aláírása megfelelő, érvényes és a jóváhagyott legfelső szintű kulcsok egyikével van aláírva. Az aláírókulcs sikeres ellenőrzése után maga az aláírás is érvényesíthető a már megbízhatónak bizonyult aláíró nyilvános kulccsal.

Az aláírókulcsok a legfelső szintű kulcsoknál sokkal gyorsabb ütemben vannak elforgatva, ezért különböző aláírókulcsokkal aláírt üzenetekre számít. 

Az aláírókulcsok visszavonását az eszközfrissítési szolgáltatás kezeli, így a felhasználók nem próbálkoznak az aláírókulcsok gyorsítótárazása során. Mindig használja az aláíráshoz tartozó aláírókulcsot.

### <a name="receiving-updates"></a>Frissítések fogadása

Az eszközfrissítési ügynök által fogadott frissítési kérelmek aláírt frissítési jegyzékfájlt (UM) tartalmaznak. Az ügynöknek ellenőriznie kell, hogy az UM aláírása megfelelő és érintetlen-e. Ez annak ellenőrzését jelenti, hogy az UM-aláírás aláírókulcsát egy megfelelő legfelső szintű kulcs írta alá. Ha ez megtörtént, az ügynök ellenőrzi az UM-aláírást az aláírókulcson.

Az UM-aláírás ellenőrzése után az eszközfrissítési ügynök "igazságforrásként" megbízik benne. Minden további biztonsági megbízhatóság ebből a forrásból ered. 

Az UM a letölteni és telepíteni kívánt tartalom URL-címeit és fájl-hasheiit tartalmazza. Miután az ügynök letöltött egy frissítési bináris fájlt, ellenőriznie kell a frissítést az UM-fájl kivonatán. Ez tranzitív megbízhatósági modellt biztosít a letöltések érvényesítéséhez. Nem csupán biztosítja, hogy a tartalom érintetlen (nem módosult), hanem azt is megerősíti, hogy a letöltött tartalom valóban az volt, amit le kellett volna tölteni. 

### <a name="securing-the-device"></a>Az eszköz biztonságossá tétele

Fontos, hogy az eszközfrissítéshez kapcsolódó biztonsági eszközök megfelelően védettek és védettek az eszközön. Az eszközöket, például a gyökérkulcsokat védeni kell a módosításokkal szemben. Ezt többféleképpen is meg lehet tenni, például biztonsági eszközök (TPM, SGX, HSM, egyéb biztonsági eszközök) használatával, vagy akár szoftveres kódolással az eszközfrissítési ügynökben. Az utóbbi megköveteli, hogy az eszközfrissítési ügynök kódja digitálisan alá legyen írva, és hogy a rendszer kódintegritás-támogatása védve legyen az ügynökkód rosszindulatú módosításával szemben.

További biztonsági intézkedések lehetnek indokoltak, például annak biztosítása, hogy az összetevőről az összetevőre való leosztás biztonságos módon történik. Például regisztrálhat egy adott elkülönített fiókot a különböző összetevők futtatásához. A hálózatalapú kommunikáció (például a REST API hívások) korlátozása csak a localhostra.

**[Következő lépés: További információ arról, hogyan használja az eszközfrissítés az Azure RBAC-t](.\device-update-control-access.md)**
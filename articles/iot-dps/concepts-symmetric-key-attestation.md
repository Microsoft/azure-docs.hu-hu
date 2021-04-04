---
title: Azure IoT Hub Device Provisioning Service – szimmetrikus kulcs igazolása
description: Ez a cikk áttekintést nyújt a szimmetrikus kulcs igazolásáról a IoT Device kiépítési szolgáltatás (DPS) használatával.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: devx-track-csharp
ms.openlocfilehash: 994c2c3124d6822f047af942268ad7a401d5a976
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90531559"
---
# <a name="symmetric-key-attestation"></a>Szimmetrikus kulcsú igazolás

Ez a cikk az identitás-igazolási folyamatot ismerteti, amikor szimmetrikus kulcsokat használ az eszköz kiépítési szolgáltatásához. 

A szimmetrikus kulcs igazolása egyszerű módszer egy eszköz kiépítési szolgáltatási példánnyal való hitelesítésére. Ez az igazolási módszer a "Hello World" felhasználói élményt jelöli olyan fejlesztők számára, akik még nem ismerik az eszközök üzembe helyezését, vagy nincsenek szigorú biztonsági követelmények. Az eszköz tanúsítványa [TPM](concepts-tpm-attestation.md) vagy [X. 509 tanúsítvány](concepts-x509-attestation.md) használatával biztonságosabb, és szigorúbb biztonsági követelményekhez kell használni őket.

A szimmetrikus kulcsok beléptetése nagyszerű módszert kínál a régi eszközök számára, korlátozott biztonsági funkciókkal a felhőbe az Azure IoT keresztül. Az örökölt eszközökkel történő szimmetrikus kulcs-igazolással kapcsolatos további információkért lásd: [a szimmetrikus kulcsok használata örökölt eszközökkel](how-to-legacy-device-symm-key.md).


## <a name="symmetric-key-creation"></a>Szimmetrikus kulcs létrehozása

Alapértelmezés szerint a Device kiépítési szolgáltatás új szimmetrikus kulcsokat hoz létre, amelyek alapértelmezett hossza 64 bájt, ha az új regisztrációk a **kulcsok automatikus létrehozása** lehetőséggel lettek mentve.

![Szimmetrikus kulcsok automatikus generálása](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

A beállítás letiltásával saját szimmetrikus kulcsokat is megadhat a regisztrációhoz. A saját szimmetrikus kulcsok megadásakor a kulcsoknak 16 bájt és 64 bájt közötti kulcs hosszúságú kell lenniük. A szimmetrikus kulcsokat is érvényes Base64 formátumban kell megadni.



## <a name="detailed-attestation-process"></a>Részletes igazolási folyamat

A szimmetrikus kulcs igazolása az eszköz kiépítési szolgáltatásával az eszközök azonosítására szolgáló IoT-hubok által támogatott [biztonsági jogkivonatok](../iot-hub/iot-hub-devguide-security.md#security-token-structure) használatával történik. Ezek a biztonsági jogkivonatok [közös hozzáférésű aláírási (SAS-) tokenek](../service-bus-messaging/service-bus-sas.md). 

Az SAS-jogkivonatok olyan kivonatoló *aláírással* rendelkeznek, amely a szimmetrikus kulccsal lett létrehozva. Az eszköz kiépítési szolgáltatása újra létrehozza az aláírást annak ellenőrzéséhez, hogy az igazolás során bemutatott biztonsági jogkivonat hiteles-e vagy sem.

Az SAS-jogkivonatok formátuma a következő:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Az egyes tokenek összetevői:

| Érték | Leírás |
| --- | --- |
| aláírás |Egy HMAC-SHA256 aláírási karakterlánc. Az egyéni regisztrációk esetében ez az aláírás a szimmetrikus kulcs (elsődleges vagy másodlagos) segítségével történik a kivonat végrehajtásához. A beléptetési csoportok esetében a rendszer a beléptetési csoport kulcsa alapján származtatott kulcsot használja a kivonat végrehajtásához. A kivonatot az űrlap egy üzenete hajtja végre: `URL-encoded-resourceURI + "\n" + expiry` . **Fontos**: a kulcsot a HMAC-sha256 számítás végrehajtásához a Base64-ből kell dekódolni. Emellett az aláírás eredményének URL-kódolású kell lennie. |
| ResourceURI |A jogkivonattal elérhető regisztrációs végpont URI-ja, amely az eszköz kiépítési szolgáltatási példányának hatókör-azonosítójával kezdődik. Például: `{Scope ID}/registrations/{Registration ID}` |
| lejárta |UTF8-karakterláncok a 00:00:00-es, 1970-os UTC-kor óta eltelt idő másodpercben. |
| {URL-kódolt-resourceURI} |Kisbetűs URL-cím – a kisbetűs erőforrás URI-ja kódolása |
| PolicyName |Annak a megosztott hozzáférési házirendnek a neve, amelyre ez a jogkivonat hivatkozik. A szimmetrikus kulcs igazolásával való kiépítés során használt szabályzat neve **regisztráció**. |

Ha egy eszköz egyéni regisztrációt tanúsít, az eszköz az egyéni beléptetési bejegyzésben definiált szimmetrikus kulcs használatával hozza létre az SAS-jogkivonat kivonatos aláírását.

A SAS-tokent létrehozó kód példákat a [biztonsági jogkivonatok](../iot-hub/iot-hub-devguide-security.md#security-token-structure)című témakörben talál.

Az Azure IoT C SDK támogatja a szimmetrikus kulcsú tanúsítványhoz tartozó biztonsági jogkivonatok létrehozását. Ha például az Azure IoT C SDK-val egyéni regisztrációt tanúsít, tekintse meg [a szimulált eszköz kiépítése szimmetrikus kulcsokkal](quick-create-simulated-device-symm-key.md)című témakört.


## <a name="group-enrollments"></a>Csoportos regisztrációk

A csoportos regisztrációk szimmetrikus kulcsait az eszközök nem használják közvetlenül a kiépítés során. Ehelyett a beléptetési csoporthoz tartozó eszközök származtatott eszköz kulcsát használva. 

Először egyedi regisztrációs azonosítót kell megadni minden olyan eszközhöz, amely regisztrál egy regisztrációs csoportot. A regisztrációs AZONOSÍTÓhoz tartozó érvényes karakterek a kisbetűs alfanumerikus és kötőjel ("-"). Ennek a regisztrációs AZONOSÍTÓnak olyan egyedinek kell lennie, amely azonosítja az eszközt. Előfordulhat például, hogy egy örökölt eszköz nem támogat számos biztonsági funkciót. Az örökölt eszközön csak egy MAC-címe vagy sorozatszáma lehet elérhető az eszköz egyedi azonosításához. Ebben az esetben a regisztrációs azonosító a következőhöz hasonló MAC-címről és sorozatszámból állhat:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Ez a pontos példa a [örökölt eszközök kiépítése a szimmetrikus kulcsok használatával](how-to-legacy-device-symm-key.md) című cikkben található.

Miután meghatározta a regisztrációs azonosítót az eszközhöz, a beléptetési csoport szimmetrikus kulcsa a regisztrációs azonosító [HMAC sha256](https://wikipedia.org/wiki/HMAC) kivonatának kiszámítására szolgál egy származtatott eszköz kulcsának létrehozásához. A regisztrációs azonosító kivonatolása a következő C#-kóddal végezhető el:

```csharp
using System; 
using System.Security.Cryptography; 
using System.Text;  

public static class Utils 
{ 
    public static string ComputeDerivedSymmetricKey(byte[] masterKey, string registrationId) 
    { 
        using (var hmac = new HMACSHA256(masterKey)) 
        { 
            return Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(registrationId))); 
        } 
    } 
} 
```

```csharp
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

Az eredményül kapott eszköz kulcsát a rendszer az igazoláshoz használandó SAS-jogkivonat létrehozásához használja. Egy regisztrációs csoportban lévő összes eszköznek egy egyedi származtatott kulcsból generált biztonsági jogkivonat használatával kell tanúsítani. A beléptetési csoport szimmetrikus kulcsát nem lehet közvetlenül az igazoláshoz használni.

#### <a name="installation-of-the-derived-device-key"></a>A származtatott eszköz kulcsának telepítése

Ideális esetben az eszköz kulcsai a gyárban vannak származtatva és telepítve. Ez a módszer garantálja, hogy a csoport kulcsa soha nem szerepel az eszközre központilag telepített szoftverek között. Ha az eszközhöz MAC-vagy sorozatszám-azonosító van rendelve, a kulcs származtatható és befecskendezhető az eszközre, de a gyártó úgy dönt, hogy tárolja azt.

Vegye figyelembe a következő diagramot, amely egy gyári eszköz kulcsait jeleníti meg, az egyes eszközök regisztrációs AZONOSÍTÓinak a csoportos beléptetési kulccsal (**K**) való kivonatolásával. 

![Gyári eszközökhöz rendelt kulcsok](./media/concepts-symmetric-key-attestation/key-diversification.png)

Az egyes eszközök identitását a gyári regisztrációs azonosító és a származtatott eszköz kulcsa képviseli. Az eszköz kulcsát a rendszer soha nem másolja egy másik helyre, és a csoport kulcsát soha nem tárolja egy eszköz.

Ha az eszköz kulcsai nincsenek telepítve a gyárban, a [hardveres biztonsági modul HSM](concepts-service.md#hardware-security-module) -et kell használnia az eszköz identitásának biztonságos tárolásához.

## <a name="next-steps"></a>Következő lépések

Most, hogy megértette a szimmetrikus kulcs igazolását, tekintse meg az alábbi cikkeket:

* [Rövid útmutató: Szimmetrikus kulcs kiosztása szimulált eszköz számára](quick-create-simulated-device-symm-key.md)
* [Ismerje meg a kiépítés fogalmait](about-iot-dps.md#provisioning-process)
* [Az automatikus kiépítés használatának első lépései](./quick-setup-auto-provision.md) 

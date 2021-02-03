---
title: Különálló érzékelők kezelése
description: Megtudhatja, hogyan kezelheti az egyes érzékelőket, beleértve az aktiválási fájlok kezelését, a biztonsági mentéseket és a különálló érzékelők frissítését.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: b35851bae8db39392d10a302d5f1059ba3ace696
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508760"
---
# <a name="manage-individual-sensors"></a>Különálló érzékelők kezelése

Ez a cikk az egyes érzékelők kezelését ismerteti. A feladatok közé tartozik az aktiválási fájlok kezelése, a biztonsági mentések végrehajtása és a különálló érzékelők frissítése.

A helyszíni felügyeleti konzolról is végezhet bizonyos érzékelő-felügyeleti feladatokat, ahol egyszerre több érzékelő is kezelhető.

Az érzékelő bevezetéséhez és regisztrálásához használja a Azure Portal.

## <a name="manage-sensor-activation-files"></a>Érzékelő-aktiválási fájlok kezelése

Az érzékelőt az Azure Defender IoT vette fel a Azure Portal. Minden érzékelő egy helyileg csatlakoztatott vagy egy felhőhöz csatlakoztatott érzékelőként lett előkészítve.

A rendszer minden egyes üzembe helyezett érzékelőre feltölt egy egyedi aktiválási fájlt. További információ arról, hogy mikor és hogyan használhatók az új fájlok: [új aktiválási fájlok feltöltése](#upload-new-activation-files). Ha nem tudja feltölteni a fájlt, tekintse meg az [aktiválási fájl feltöltésének hibáit](#troubleshoot-activation-file-upload)ismertető témakört.

### <a name="about-activation-files-for-locally-connected-sensors"></a>Tudnivalók a helyileg csatlakoztatott érzékelőkhöz tartozó aktiválási fájlokról

A helyileg csatlakoztatott érzékelők egy Azure-előfizetéshez vannak társítva. A helyileg csatlakoztatott érzékelőkhöz tartozó aktiválási fájl lejárati dátumot tartalmaz. Egy hónappal ezen időpont előtt egy figyelmeztető üzenet jelenik meg az érzékelő konzoljának tetején. A figyelmeztetés addig marad, amíg nem frissítette az aktiválási fájlt.

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="A rendszerbeállítások képernyőképe.":::

Továbbra is dolgozhat a Defender for IoT szolgáltatással, még akkor is, ha az aktiválási fájl lejárt. 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>Tudnivalók a felhőalapú érzékelőkhöz kapcsolódó aktiválási fájlokról

A felhővel összekapcsolt érzékelők az IoT hub Defender szolgáltatásával vannak társítva. Ezek az érzékelők nem korlátozódnak az aktiválási fájl időtartamára. A felhőalapú érzékelőkhöz tartozó aktiválási fájl a IoT hub Defender-hoz való csatlakozás biztosítására szolgál.

### <a name="upload-new-activation-files"></a>Új aktiválási fájlok feltöltése

Előfordulhat, hogy a következő esetekben fel kell töltenie egy új aktiválási fájlt egy bekészített érzékelőre:

- Egy aktiválási fájl lejár egy helyileg csatlakoztatott érzékelőn. 

- Más szenzor-felügyeleti módban szeretne dolgozni. 

- Egy új Defender for IoT hub-t szeretne hozzárendelni egy felhőhöz csatlakoztatott érzékelőhöz.

Új aktiválási fájl hozzáadása:

1. Nyissa meg az **érzékelő kezelése** lapot.

2. Válassza ki azt az érzékelőt, amelyhez új aktiválási fájlt szeretne feltölteni.

3. Törölje.

4. Az érzékelőt újra be kell készíteni a bevezetési **oldalról az új** mód vagy egy új Defender for IoT hub alkalmazásba.

5. Töltse le az aktiválási fájlt az **aktiválási fájl letöltése** lapról.

6. Mentse a fájlt.

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="Töltse le az aktiválási fájlt a Defender for IoT hub webhelyről.":::

7. Jelentkezzen be a Defender for IoT szenzor-konzolra.

8. Az érzékelő konzolon válassza a **Rendszerbeállítások**  >  **újraaktiválása** lehetőséget.

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="A rendszerbeállítások képernyőn válassza újra az aktiválást.":::

9. Válassza a **feltöltés** lehetőséget, és válassza ki a mentett fájlt.

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="Töltse fel a mentett fájlt.":::

10. Válassza az **aktiválás** lehetőséget.

### <a name="troubleshoot-activation-file-upload"></a>Az aktiválási fájl feltöltésének hibája

Hibaüzenet jelenik meg, ha az aktiválási fájlt nem lehet feltölteni. Előfordulhat, hogy a következő események történtek:

- **Helyileg csatlakoztatott érzékelők esetén**: az aktiválási fájl érvénytelen. Ha a fájl érvénytelen, nyissa meg a Defender for IoT portált. Az **érzékelő kezelése** lapon válassza ki az érvénytelen fájllal rendelkező érzékelőt, és töltsön le egy új aktiválási fájlt.

- **Felhőhöz csatlakoztatott érzékelők esetén**: az érzékelő nem tud csatlakozni az internethez. Keresse meg az érzékelő hálózati konfigurációját. Ha az érzékelő webproxyn keresztül csatlakozik az internethez, ellenőrizze, hogy a proxykiszolgáló megfelelően van-e konfigurálva az **érzékelő hálózati konfiguráció** képernyőjén. Ellenőrizze, hogy \* a. Azure-Devices.net:443 engedélyezett-e a tűzfalban és/vagy a proxyban. Ha a helyettesítő karakterek használata nem támogatott, vagy ha további szabályozásra van szüksége, akkor a IoT hub-hoz tartozó egyedi teljes tartománynevet meg kell nyitni a tűzfalban és/vagy a proxyban. Részletekért lásd: [referenciák IoT hub végpontok](../iot-hub/iot-hub-devguide-endpoints.md).  

- **Felhőhöz csatlakoztatott érzékelők esetén**: az aktiválási fájl érvényes, de a IoT Defender visszautasította azt. Ha nem tudja elhárítani ezt a problémát, egy másik aktiválást is letölthet a Defender IoT-portál **Sensor Management** oldaláról. Ha ez nem működik, forduljon a Microsoft ügyfélszolgálatahoz.

## <a name="manage-certificates"></a>Tanúsítványok kezelése

Az érzékelő telepítése után létrejön egy helyi önaláírt tanúsítvány, amely az érzékelő webalkalmazás elérésére szolgál. Amikor első alkalommal jelentkezik be az érzékelőbe, a rendszergazda felhasználók számára SSL/TLS-tanúsítványt kell megadnia.  Az első telepítéssel kapcsolatos további információkért lásd: [Bejelentkezés és az érzékelő aktiválása](how-to-activate-and-set-up-your-sensor.md).

Ez a cikk a tanúsítványok frissítéséről, a tanúsítvány CLI-parancsainak használatáról, valamint a támogatott tanúsítványokról és tanúsítvány-paraméterekről tartalmaz információkat.

### <a name="about-certificates"></a>Információ a tanúsítványokról

A IoT készült Azure Defender SSL/TLS-tanúsítványokat használ a következőhöz:

- A HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítvány feltöltésével teljesítheti a szervezet által kért bizonyos tanúsítvány-és titkosítási követelményeket.

- A felügyeleti konzol és a csatlakoztatott érzékelők, valamint a felügyeleti konzol és a magas rendelkezésre állású felügyeleti konzol közötti ellenőrzés engedélyezése. Az érvényesítések kiértékelése a visszavont tanúsítványok listáján és a tanúsítvány lejárati dátumán történik. *Ha az ellenőrzés sikertelen, a felügyeleti konzol és az érzékelő közötti kommunikáció leállt, és a konzolon egy érvényesítési hiba jelenik meg*. Ez a beállítás alapértelmezés szerint engedélyezve van a telepítés után.

 Harmadik féltől származó továbbítási szabályok, például a SYSLOG, a splunk vagy a ServiceNow számára továbbított riasztási információk. vagy a Active Directoryval való kommunikáció nincs érvényesítve.

#### <a name="ssl-certificates"></a>SSL-tanúsítványok

A Defender for IoT szenzor és a helyszíni felügyeleti konzol SSL-és TLS-tanúsítványokat használ a következő függvényekhez: 

 - Biztonságos kommunikáció a felhasználók és a készülék webkonzolja között. 
 
 - Biztonságos kommunikáció a REST API az érzékelő és a helyszíni felügyeleti konzolon.
 
 - Biztonságos kommunikáció az érzékelők és a helyszíni felügyeleti konzol között. 

A telepítés után a készülék létrehoz egy helyi önaláírt tanúsítványt, hogy engedélyezze az előzetes hozzáférést a webkonzolhoz. A vállalati SSL és a TLS-tanúsítványok a parancssori eszköz használatával telepíthetők [`cyberx-xsense-certificate-import`](#cli-commands) . 

 > [!NOTE]
 > Az integrációk és a továbbítási szabályok esetében, ahol a berendezés a munkamenet ügyfele és kezdeményezője, a rendszer bizonyos tanúsítványokat használ, és nem kapcsolódik a rendszertanúsítványokhoz.  
 >
 >Ezekben az esetekben a rendszer általában a-kiszolgálótól fogadja a tanúsítványokat, vagy aszimmetrikus titkosítást használ, ha az integráció beállításához egy adott tanúsítvány lesz megadva.

A berendezések egyedi tanúsítványfájl használatát is használhatják. Ha cserélnie kell egy tanúsítványt, feltöltött;

- A 10,0-es verzióból a tanúsítvány a rendszerbeállítások menüből lehet lecserélve.

- Az 10,0-es verziónál korábbi verziók esetén az SSL-tanúsítvány a parancssori eszköz használatával cserélhető le.

### <a name="update-certificates"></a>Tanúsítványok frissítése

Az érzékelő rendszergazdájának felhasználói frissíthetik a tanúsítványokat.

Tanúsítvány frissítése:  

1. Válassza a **Rendszerbeállítások** lehetőséget.

1. Válassza az **SSL/TLS-tanúsítványok lehetőséget.**
1. Törölje vagy szerkessze a tanúsítványt, és adjon hozzá egy újat.

    - Adja meg a tanúsítvány nevét.
    
    - Töltse fel a CRT-fájlt és a kulcsot tartalmazó fájlt, és adjon meg egy jelszót.
    - Szükség esetén töltse fel a PEM-fájlt.

Az érvényesítési beállítás módosítása:

1. A **tanúsítvány-ellenőrzés engedélyezése** váltógomb engedélyezése vagy letiltása.

1. Kattintson a **Mentés** gombra.

Ha a beállítás engedélyezve van, és az érvényesítés sikertelen, a felügyeleti konzol és az érzékelő közötti kommunikáció leállt, és a konzolon egy érvényesítési hiba jelenik meg.

### <a name="certificate-support"></a>Tanúsítvány-támogatás

A következő tanúsítványok támogatottak:

- Magán-és nagyvállalati kulcsokra épülő infrastruktúra (privát PKI)

- Nyilvános kulcsokra épülő infrastruktúra (nyilvános PKI) 

- Helyileg generált a készüléken (helyileg önaláírt). 

> [!IMPORTANT]
> Nem javasoljuk önaláírt tanúsítványok használatát. Az ilyen típusú kapcsolatok nem biztonságosak, és csak tesztelési környezetekben használhatók. Mivel a tanúsítvány tulajdonosa nem érvényesíthető, és a rendszer biztonsága nem tartható fenn, az önaláírt tanúsítványok soha nem használhatók éles hálózatokhoz.

### <a name="supported-ssl-certificates"></a>Támogatott SSL-tanúsítványok 

A következő paraméterek támogatottak. 

**Tanúsítvány CRT**

- A tartománynévhez tartozó elsődleges tanúsítványfájl

- Aláírási algoritmus = SHA256RSA
- Aláírás-kivonatoló algoritmus = SHA256
- Érvényességi idő = érvényes korábbi dátum
- Érvényes: = érvényes jövőbeli dátum
- Nyilvános kulcs = RSA 2048 BITS (minimum) vagy 4096 bit
- CRL terjesztési pontja = a. CRL fájl URL-címe
- A tulajdonos CN = URL-cím lehet helyettesítő tanúsítvány; például: Sensor. contoso. <span> com vagy *. contoso. <span> com
- Tárgy (C) ountry = meghatározott, például USA
- Tulajdonos (OU) szervezeti egység = meghatározott, például contoso Labs
- Tárgy (O) rvezet = meghatározva, például: contoso Inc.

**Kulcsfájl**

- A CSR létrehozásakor generált kulcsfájl.

- RSA 2048 BITS (minimum) vagy 4096 bit.

 > [!Note]
 > A 4096bits kulcs hosszának használata:
 > - Az SSL-kézfogás az egyes kapcsolatok elején lassabb lesz.  
 > - A kézfogások során a CPU-használat növekedése növekszik. 

**Tanúsítványlánc**

- A HITELESÍTÉSSZOLGÁLTATÓ által megadott köztes tanúsítványfájl (ha van ilyen)

- A kiszolgáló tanúsítványát kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítványnak először a fájlban kell lennie, amelyet a többinek a gyökérig kell megadnia. 
- Tartalmazhat táska-attribútumokat is.

**Jelszót**

- Egy kulcs támogatott.

- A tanúsítvány importálásakor be kell állítani.

A más paraméterekkel rendelkező tanúsítványok is működhetnek, de a Microsoft nem támogatja őket.

#### <a name="encryption-key-artifacts"></a>Titkosítási kulcs összetevői

**. PEM – tanúsítvány-tároló fájl**

A Privacy Enhanced Mail (PEM) fájlok az e-mailek védelméhez használt általános fájltípusok voltak. Napjainkban a PEM-fájlok tanúsítványokkal vannak ellátva, és x509 ASN1 kulcsokat használnak.  

A tároló-fájl a 1421 – 1424, a csak nyilvános tanúsítványt tartalmazó tároló formátuma alapján van meghatározva. Például az Apache telepíti, a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt, a fájlokat, az SSL-t vagy a TANÚSÍTVÁNYokat. Ez magában foglalhatja a teljes tanúsítványláncot, beleértve a nyilvános kulcsokat, a titkos kulcsokat és a főtanúsítványokat is.  

Emellett a CSR-t PKCS10 formátumban is kódolhatja, amely a PEM-ba fordítható le.

**. CERT. cer. CRT – tanúsítvány-tároló fájl**

Egy `.pem` `.der` másik kiterjesztéssel rendelkező vagy formázott fájl. A Windows Intéző tanúsítványként ismeri fel a fájlt. A `.pem`   Windows Intéző nem ismeri fel a fájlt.

**. Key – titkos kulcs fájlja**

A kulcsfájl formátuma azonos a PEM-fájllal, de más kiterjesztéssel rendelkezik.

#### <a name="additional-commonly-available-key-artifacts"></a>További általánosan elérhető főbb összetevők

**. CSR – tanúsítvány-aláírási kérelem**.  

Ez a fájl a hitelesítésszolgáltatók számára történő küldéshez használatos. A tényleges formátum a PKCS10, amely az RFC 2986-ben van meghatározva, és tartalmazhat néhányat vagy a kért tanúsítvány legfontosabb részleteit. Például a tárgy, a szervezet és az állapot. Ez a HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítvány nyilvános kulcsa, és a rendszer visszaküldi a tanúsítványt.  

A visszaadott tanúsítvány a nyilvános tanúsítvány, amely magában foglalja a nyilvános kulcsot, de nem a titkos kulcsot. 

**. PKCS12/pfx-profil. pfx. P12 – jelszó tároló**. 

Az RSA által eredetileg az Public-Key titkosítási szabványokban (PKCS) definiált, a 12 változatot eredetileg a Microsoft fejlesztette ki, később pedig RFC 7292-ként lett elküldve.  

A tároló formátumához a nyilvános és a privát tanúsítvány párokat egyaránt tartalmazó jelszó szükséges. `.pem`   A fájlokkal ellentétben ez a tároló teljesen titkosítva van.  

Az OpenSSL használatával egy `.pem`   nyilvános és titkos kulccsal rendelkező fájlba is bekapcsolhatja a következőt: `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**. der – bináris kódolású PEM**.

Az ASN. 1 szintaxis bináris fájlban való kódolásának módja egy `.pem`   fájlon keresztül történik, amely csak egy Base64 kódolású `.der` fájl. 

Az OpenSSL konvertálhatja ezeket a fájlokat a következőre `.pem` :  `openssl x509 -inform der -in to-convert.der -out converted.pem` .  

A Windows a fájlokat tanúsítványfájlként fogja felismerni. Alapértelmezés szerint a Windows a tanúsítványokat `.der` más kiterjesztésű formázott fájlként fogja exportálni.  

**. CRL – visszavont tanúsítványok listája**.  
A hitelesítésszolgáltatók ezeket a fájlokat a lejárat előtt előállítják a tanúsítványok megszüntetéséhez.
 
##### <a name="cli-commands"></a>Parancssori felületi parancsok

`cyberx-xsense-certificate-import`Tanúsítványok importálásához használja a CLI-parancsot. Az eszköz használatához a tanúsítványokat a megnyerő vagy a wget eszközzel kell feltölteni az eszközre.

A parancs a következő bemeneti jelzőket támogatja:

- `-h`: A parancssori Súgó szintaxisát jeleníti meg.

- `--crt`: Egy tanúsítványfájl elérési útja (. CRT kiterjesztés).

- `--key`:  \* . key fájl. A kulcs hosszának legalább 2 048 bitenek kell lennie.

- `--chain`: Tanúsítványlánc-fájl elérési útja (nem kötelező).

- `--pass`: A tanúsítvány titkosításához használt jelszó (nem kötelező).

- `--passphrase-set`: Default = `False` , nincs használatban. Állítsa az értékre az `True` előző tanúsítvánnyal megadott előző jelszó használatára (nem kötelező).

A CLI-parancs használatakor:

- Ellenőrizze, hogy a tanúsítvány fájljai olvashatók-e a készüléken.

- Ellenőrizze, hogy a tanúsítványban szereplő tartománynév és az IP-cím megegyezik-e az informatikai részleg által tervezett konfigurációval.

### <a name="use-openssl-to-manage-certificates"></a>A tanúsítványok kezelése az OpenSSL használatával

A tanúsítványokat a következő parancsokkal kezelheti:

| Leírás | CLI-parancs |
|--|--|
| Új titkos kulcs és tanúsítvány-aláírási kérelem létrehozása | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| Önaláírt tanúsítvány létrehozása | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| Tanúsítvány-aláírási kérelem (CSR) létrehozása egy meglévő titkos kulcshoz | `openssl req -out CSR.csr -key privateKey.key -new` |
| Tanúsítvány-aláírási kérelem létrehozása egy meglévő tanúsítvány alapján | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| Jelszó eltávolítása titkos kulcsból | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

Ha ellenőriznie kell a tanúsítványon, a CSR-en vagy a titkos kulcson belüli adatokat, használja a következő parancsokat:

| Leírás | CLI-parancs |
|--|--|
| Tanúsítvány-aláírási kérelem (CSR) keresése | `openssl req -text -noout -verify -in CSR.csr` |
| Titkos kulcs keresése | `openssl rsa -in privateKey.key -check` |
| Tanúsítvány keresése | `openssl x509 -in certificate.crt -text -noout`  |

Ha hibaüzenet jelenik meg arról, hogy a titkos kulcs nem egyezik a tanúsítvánnyal, vagy ha egy helyre telepített tanúsítvány nem megbízható, a következő parancsokkal javítsa ki a hibát;

| Leírás | CLI-parancs |
|--|--|
| A nyilvános kulcs MD5-kivonatának ellenőrzése annak biztosításához, hogy az megfelel-e a CSR vagy a titkos kulcsnak | 1. `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> 2. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> 3. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

Ha a tanúsítványokat és a kulcsokat különböző formátumokra szeretné átalakítani, hogy azok kompatibilisek legyenek a kiszolgálók vagy szoftverek bizonyos típusaival, használja a következő parancsokat:

| Leírás | CLI-parancs |
|--|--|
| DER-fájl konvertálása (. CRT. cer. der) a PEM-ba  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| PEM-fájl átalakítása DER-re | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| PKCS # 12 fájl konvertálása (. pfx. P12), amely egy titkos kulcsot és a PEM-tanúsítványokat tartalmaz. | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />A Hozzáadás `-nocerts` gombra kattintva csak a titkos kulcsot kell kiadnia, vagy a Hozzáadás lehetőséggel `-nokeys` csak a tanúsítványokat kell kiadnia. |
| PEM-tanúsítványfájl és egy titkos kulcs átalakítása PKCS # 12 (. pfx. P12) fájlba | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

## <a name="connect-a-sensor-to-the-management-console"></a>Érzékelő összekötése a Felügyeleti konzollal

Ez a szakasz az érzékelő és a helyszíni felügyeleti konzol közötti kapcsolat biztosítását ismerteti. Ezt akkor kell megtennie, ha gapped hálózaton dolgozik, és az érzékelőtől az eszköz-és riasztási adatokat szeretné elküldeni a felügyeleti konzolra. Ez a kapcsolódás azt is lehetővé teszi, hogy a felügyeleti konzol leküldje a rendszerbeállításokat az érzékelőre, és más felügyeleti feladatokat hajtson végre az érzékelőn.

A kapcsolódáshoz:

1. Jelentkezzen be a helyszíni felügyeleti konzolba.

2. Válassza a **Rendszerbeállítások** lehetőséget.

3. Az **érzékelő beállítása – a kapcsolatok karakterlánca** szakaszban másolja az automatikusan generált kapcsolatok karakterláncát.

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="Másolja a kapcsolatok karakterláncát a képernyőről."::: 

4. Jelentkezzen be az érzékelő konzolra.

5. A bal oldali ablaktáblán válassza a **Rendszerbeállítások** elemet.

6. Válassza a **felügyeleti konzolon a kapcsolatok** lehetőséget.

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="Képernyőfelvétel a felügyeleti konzol kapcsolatai párbeszédpanelről.":::

7. Illessze be a kapcsolati karakterláncot a **kapcsolati karakterlánc** mezőbe, és válassza a **Csatlakoztatás** lehetőséget.

8. A helyszíni felügyeleti konzol **hely kezelése** ablakában rendelje hozzá az érzékelőt egy zónához.

## <a name="change-the-name-of-a-sensor"></a>Egy érzékelő nevének módosítása

Módosíthatja az érzékelő konzoljának nevét. Az új név megjelenik a konzol webböngészőjében, a különböző konzolablak-ablakok között, valamint a hibaelhárítási naplókban.

Az érzékelő nevének megváltoztatásának folyamata a helyileg csatlakoztatott érzékelők és a felhőhöz csatlakoztatott érzékelők esetében változik. Az alapértelmezett név az **érzékelő**.

### <a name="change-the-name-of-a-locally-connected-sensor"></a>Helyileg csatlakoztatott érzékelő nevének módosítása

A név módosítása:

1. A konzol bal oldali panelének alján válassza ki az aktuális érzékelő címkéjét.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="Képernyőkép, amely az érzékelő címkéjét jeleníti meg.":::

1. Az **érzékelő nevének szerkesztése** párbeszédpanelen adjon meg egy nevet.

1. Kattintson a **Mentés** gombra. Az új név lesz alkalmazva.

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>Felhőhöz csatlakoztatott érzékelő nevének módosítása

Ha az érzékelőt felhőalapú csatlakoztatott érzékelőként regisztrálták, az érzékelő nevét a regisztráció során hozzárendelt név határozza meg. A nevet az első bejelentkezéskor feltöltött aktiválási fájl tartalmazza. Az érzékelő nevének módosításához fel kell töltenie egy új aktiválási fájlt.

A név módosítása:

1. Az Azure Defender for IoT portálon nyissa meg az **érzékelő kezelése** lapot.

1. Törölje az érzékelőt az **érzékelő kezelése** ablakból.

1. Regisztrálja újra az új nevet.

1. Töltse le az új aktiválási fájlt.

1. Jelentkezzen be az érzékelőbe, és töltse fel az új aktiválási fájlt.

## <a name="update-the-sensor-network-configuration"></a>Az érzékelő hálózati konfigurációjának frissítése

Az érzékelő hálózati konfigurációja az érzékelő telepítésekor lett meghatározva. Módosíthatók a konfigurációs paraméterek. Beállíthatja a proxy konfigurációját is.

Ha új IP-címet hoz létre, előfordulhat, hogy újra be kell jelentkeznie.

A konfiguráció módosítása:

1. Az oldalsó menüben válassza a **Rendszerbeállítások** elemet.

2. A **Rendszerbeállítások** ablakban válassza a **hálózat** lehetőséget.

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="Konfigurálja a hálózati beállításokat.":::

3. Állítsa be a paramétereket az alábbiak szerint:

    | Paraméter | Leírás |
    |--|--|
    | IP-cím | Az érzékelő IP-címe |
    | Alhálózati maszk | A maszk címe |
    | Alapértelmezett átjáró | Az alapértelmezett átjáró címe |
    | DNS | A DNS-kiszolgáló címe |
    | Hostname (Gazdanév) | Az érzékelő állomásneve |
    | Proxy | Proxy gazdagép és port neve |

4. Kattintson a **Mentés** gombra.

## <a name="synchronize-time-zones-on-the-sensor"></a>Időzónák szinkronizálása az érzékelőn

Az érzékelő időpontját és régióját beállíthatja úgy, hogy az összes felhasználó ugyanazt az időt és régiót láthassa.

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="Adja meg az időt és a régiót.":::

| Paraméter | Leírás |
|--|--|
| Időzóna | A következő időzóna-definíciója:<br />– Riasztások<br />-Trendek és statisztika widgetek<br />– Adatbányászati jelentések<br />   Kockázatfelmérési jelentések<br />-Támadási vektorok |
| Dátumformátum | Válasszon a következő formátumok közül:<br />-nn/hh/éééé óó: PP: mm<br />-HH/nn/éééé óó: PP: mm<br />-éééé/hh/nn óó: PP: mm |
| Dátum és idő | Megjeleníti az aktuális dátumot és a helyi időpontot a kiválasztott formátumban.<br />Ha például a tényleges helye Amerika és New York, az időzóna pedig Európa és Berlin között van beállítva, az idő a berlini helyi idő szerint jelenik meg. |

Az érzékelő időpontjának beállítása:

1. Az oldalsó menüben válassza a **Rendszerbeállítások** elemet.

2. A **Rendszerbeállítások** ablakban válassza az **idő & regionális** lehetőséget.

3. Állítsa be a paramétereket, majd válassza a **Mentés** lehetőséget.

## <a name="set-up-backup-and-restore-files"></a>Biztonsági mentési és visszaállítási fájlok beállítása

A rendszer biztonsági mentését naponta 3:00-kor hajtjuk végre. Az adatmentést az érzékelő egy másik lemeze tárolja. Az alapértelmezett hely a `/var/cyberx/backups` .

Ezt a fájlt automatikusan átviheti a belső hálózatra.

> [!NOTE]
> - A biztonsági mentési és a visszaállítási eljárás csak a verziók között végezhető el.
> - Egyes architektúrákban a biztonsági mentés le van tiltva. Engedélyezheti a `/var/cyberx/properties/backup.properties` fájlban.

Ha a helyszíni felügyeleti konzol segítségével vezérli az érzékelőt, az érzékelők biztonsági mentési ütemezésével gyűjtheti össze ezeket a biztonsági másolatokat, és tárolhatja azokat a felügyeleti konzolon vagy egy külső biztonsági mentési kiszolgálón.

**A biztonsági másolat készítése:** Konfigurációk és adathalmazok.

**Mi a biztonsági mentés:** PCAP fájlok és naplók. A PCAPs és a naplók biztonsági mentését és visszaállítását manuálisan végezheti el.

Az érzékelő biztonságimásolat-fájljai a következő formátumban lesznek automatikusan elnevezve: `<sensor name>-backup-version-<version>-<date>.tar` . Például: `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`.

A biztonsági mentés konfigurálása:

- Jelentkezzen be egy rendszergazdai fiókba, és írja be a következőt: `$ sudo cyberx-xsense-system-backup` .

A legújabb biztonságimásolat-fájl visszaállítása:

- Jelentkezzen be egy rendszergazdai fiókba, és írja be a következőt: `$ sudo cyberx-xsense-system-restore` .

A biztonsági másolat mentése külső SMB-kiszolgálóra:

1. Hozzon létre egy megosztott mappát a külső SMB-kiszolgálón.

    Szerezze be az SMB-kiszolgáló eléréséhez szükséges mappa elérési útját, felhasználónevét és jelszavát.

2. Az érzékelőben készítse el a biztonsági másolatok könyvtárát:

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. Szerkesztés `fstab` : 

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. Az SMB-kiszolgáló megosztásához szükséges hitelesítő adatok szerkesztése és létrehozása:

    `sudo nano /etc/samba/user` 

5. Adja hozzá a következőket:

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. A könyvtár csatlakoztatása:

    `sudo mount -a`

7. Konfigurálja a biztonsági mentési könyvtárat a Defender for IoT érzékelő megosztott mappájába:  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>Érzékelők visszaállítása

A biztonsági másolatokat az érzékelő konzolról és a parancssori felületről állíthatja vissza.

**Visszaállítás a konzolról:**

- Válassza a **rendszerkép visszaállítása** lehetőséget az érzékelő **rendszerbeállításai** ablakában.

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="A gomb kiválasztásával állítsa vissza a képet.":::

A konzolon megjelennek a visszaállítási hibák.

**Visszaállítás a parancssori felület használatával:**

- Jelentkezzen be egy rendszergazdai fiókba, és írja be a következőt: `$ sudo cyberx-management-system-restore` .

## <a name="update-a-standalone-sensor-version"></a>Önálló érzékelő verziójának frissítése

Az alábbi eljárás azt ismerteti, hogyan frissíthető egy önálló érzékelő az érzékelő konzol használatával. A frissítési folyamat körülbelül 30 percet vesz igénybe.

1. Nyissa meg az [Azure Portal](https://portal.azure.com/).

2. Nyissa meg a Defender for IoT.

3. Nyissa meg a **frissítések** lapot.

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="Képernyőkép a IoT Defender frissítések oldaláról.":::

4. Válassza a **Letöltés** lehetőséget az **érzékelők** szakaszban, és mentse a fájlt.

5. Az érzékelő konzol oldalsávján válassza a **Rendszerbeállítások** elemet.

6. **A verziófrissítés ablaktáblán** válassza a **frissítés** lehetőséget.

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="Képernyőkép a frissítés panelről.":::

7. Válassza ki azt a fájlt, amelyet a Defender for IoT **Updates** lapon töltött le.

8. Ekkor elindul a frissítési folyamat, amely alatt a rendszer kétszer újraindul. Az első újraindítás után (a frissítési folyamat befejezése előtt) megnyílik a rendszer a bejelentkezési ablakon. A bejelentkezést követően megjelenik a verziófrissítés verziója az oldalsáv bal alsó részén.

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="A bejelentkezés után megjelenő verziófrissítési verzió képernyőképe.":::

## <a name="forward-sensor-failure-alerts"></a>Továbbítási érzékelők sikertelen riasztásai 

A riasztásokat harmadik feleknek továbbíthatja, hogy a következő adatokat adja meg:

- Leválasztott érzékelők

- Távoli biztonsági mentés hibái

Ezek az adatok akkor lesznek elküldve, amikor a rendszer értesítéseihez létrehoz egy továbbítási szabályt.

> [!NOTE]
> A rendszergazdák rendszerértesítéseket küldhetnek.

Értesítések küldése:

1. Jelentkezzen be a helyszíni felügyeleti konzolba.
1. Válassza az oldal menü **továbbítás** elemét.
1. Hozzon létre egy továbbítási szabályt.
1. Válassza a **jelentési Rendszerértesítések** lehetőséget.

További információ a továbbítási szabályokról: [riasztási információk továbbítása](how-to-forward-alert-information-to-partners.md).

## <a name="adjust-system-properties"></a>A Rendszertulajdonságok módosítása

A Rendszertulajdonságok az érzékelő különböző műveleteit és beállításait vezérlik. Előfordulhat, hogy a Szerkesztés vagy a módosítás miatt az érzékelő konzol működése megsérült.

A beállítások módosítása előtt forduljon a [Microsoft ügyfélszolgálatahoz](https://support.microsoft.com/) .

A Rendszertulajdonságok elérése:

1. Jelentkezzen be a helyszíni felügyeleti konzolba vagy az érzékelőbe.

2. Válassza a **Rendszerbeállítások** lehetőséget.

3. Válassza ki a **rendszer tulajdonságai** elemet az **általános** szakaszban.

## <a name="see-also"></a>Lásd még

[Veszélyforrások elleni kutatás és csomagok](how-to-work-with-threat-intelligence-packages.md)

[Érzékelők kezelése a felügyeleti konzolról](how-to-manage-sensors-from-the-on-premises-management-console.md)
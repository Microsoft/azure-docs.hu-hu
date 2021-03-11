---
title: A helyszíni felügyeleti konzol kezelése
description: Ismerje meg a helyszíni felügyeleti konzol beállításait, például a biztonsági mentést és visszaállítást, az állomásnév definiálását és a proxy az érzékelőkhöz való beállítását.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: f3c9f8f78f17153c3d2eb7b014cf616253b3c0c9
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618253"
---
# <a name="manage-the-on-premises-management-console"></a>A helyszíni felügyeleti konzol kezelése

Ez a cikk a helyszíni felügyeleti konzol lehetőségeit, például a biztonsági mentést és visszaállítást, a számítógép-eszköz aktiválási fájljának letöltését, a tanúsítványok frissítését és az érzékelőkhöz tartozó proxy beállítását ismerteti.

A helyszíni felügyeleti konzolt a Azure Portal.

## <a name="upload-an-activation-file"></a>Aktiválási fájl feltöltése

Amikor először jelentkezik be, a rendszer letölt egy aktiválási fájlt a helyszíni felügyeleti konzolhoz. Ez a fájl tartalmazza a bevezetési folyamat során meghatározott összesített véglegesített eszközöket. A lista több előfizetéshez társított érzékelőket is tartalmaz.

A kezdeti aktiválás után a figyelt eszközök száma túllépheti a bevezetéskor definiált véglegesített eszközök számát. Ez az esemény például akkor fordulhat elő, ha több érzékelőt csatlakozik a felügyeleti konzolhoz. Ha a figyelt eszközök száma és a véglegesített eszközök száma között eltérés tapasztalható, a felügyeleti konzolon megjelenik egy figyelmeztetés. Ha ez az esemény történik, fel kell töltenie egy új aktiválási fájlt.

Aktiválási fájl feltöltése:

1. Nyissa meg az Azure Defender for IoT **díjszabási** oldalát.
1. Válassza az **aktiválási fájl letöltése a felügyeleti konzolhoz** lapot. Az aktiválási fájl letöltése megtörténik.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Töltse le az aktiválási fájlt.":::

1. Válassza ki a **Rendszerbeállítások** elemet a felügyeleti konzolon.
1. Válassza az **aktiválás** lehetőséget.
1. Válassza a **fájl kiválasztása** elemet, és válassza ki a mentett fájlt.

## <a name="manage-certificates"></a>Tanúsítványok kezelése

A helyszíni felügyeleti konzol telepítése után létrejön egy helyi önaláírt tanúsítvány, amely a felügyeleti konzol webalkalmazásának elérésére szolgál. Amikor a rendszergazda felhasználó első alkalommal jelentkezik be a felügyeleti konzolra, a rendszer megkéri, hogy adjon meg egy SSL/TLS-tanúsítványt. Az első telepítéssel kapcsolatos további információkért lásd: [a helyszíni felügyeleti konzol aktiválása és beállítása](how-to-activate-and-set-up-your-on-premises-management-console.md).

A következő szakaszokban információkat talál a tanúsítványok frissítéséről, a tanúsítvány CLI-parancsainak használatáról, valamint a támogatott tanúsítványokról és tanúsítvány-paraméterekről.

### <a name="about-certificates"></a>Információ a tanúsítványokról

Az Azure Defender for IoT SSL-és TLS-tanúsítványokat használ a következőhöz:

- A HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítvány feltöltésével teljesítheti a szervezet által kért bizonyos tanúsítvány-és titkosítási követelményeket.

- A felügyeleti konzol és a csatlakoztatott érzékelők, valamint a felügyeleti konzol és a magas rendelkezésre állású felügyeleti konzol közötti ellenőrzés engedélyezése. Az érvényesítés kiértékelése a visszavont tanúsítványok listáján és a tanúsítvány lejárati dátumán történik. *Ha az ellenőrzés sikertelen, a felügyeleti konzol és az érzékelő közötti kommunikáció leáll, és egy érvényesítési hiba jelenik meg a konzolon*. Ez a beállítás alapértelmezés szerint engedélyezve van a telepítés után.

A harmadik féltől származó továbbítási szabályok nincsenek érvényesítve. Ilyenek például a SYSLOG, a splunk vagy a ServiceNow számára továbbított riasztási információk. és kommunikáció a Active Directoryával.

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

### <a name="update-certificates"></a>Tanúsítványok frissítése

A helyszíni felügyeleti konzol rendszergazda felhasználói frissíthetik a tanúsítványokat.

Tanúsítvány frissítése:  

1. Válassza a **Rendszerbeállítások** lehetőséget.

1. Válassza az **SSL/TLS-tanúsítványok** lehetőséget.
1. Törölje vagy szerkessze a tanúsítványt, és adjon hozzá egy újat.
   
   - Adja meg a tanúsítvány nevét.
   
   - Töltse fel a CRT-fájlt és a kulcsot tartalmazó fájlt, és adjon meg egy jelszót.
   - Szükség esetén töltse fel a PEM-fájlt.

Az érvényesítési beállítás módosítása:

1. A **tanúsítvány-ellenőrzés engedélyezése** váltógomb bekapcsolása vagy kikapcsolása.

1. Kattintson a **Mentés** gombra.

Ha a beállítás engedélyezve van, és az érvényesítés sikertelen, a felügyeleti konzol és az érzékelő közötti kommunikáció leállt, és egy érvényesítési hiba jelenik meg a konzolon.

### <a name="certificate-support"></a>Tanúsítvány-támogatás

A következő tanúsítványok támogatottak:

- Magán-és nagyvállalati kulcsokra épülő infrastruktúra (privát PKI)
 
- Nyilvános kulcsokra épülő infrastruktúra (nyilvános PKI) 

- Helyileg generált a készüléken (helyileg önaláírt) 

  > [!IMPORTANT]
  > Nem javasoljuk önaláírt tanúsítványok használatát. Az ilyen típusú kapcsolatok nem biztonságosak, és csak tesztelési környezetekben használhatók. Mivel a tanúsítvány tulajdonosa nem érvényesíthető, és a rendszer biztonsága nem tartható fenn, az önaláírt tanúsítványok soha nem használhatók éles hálózatokhoz.

### <a name="supported-ssl-certificates"></a>Támogatott SSL-tanúsítványok 

A következő paraméterek támogatottak: 

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
- Tárgy (OU) szervezeti egység = definiálva; például: contoso Labs
- Tárgy (O) rvezet = meghatározva; például: contoso Inc

**Kulcsfájl**

- A CSR létrehozásakor generált kulcsfájl

- RSA 2048 BITS (minimum) vagy 4096 bit

 > [!Note]
 > A 4096bits kulcs hosszának használata:
 > - Az SSL-kézfogás az egyes kapcsolatok elején lassabb lesz.  
 > - A kézfogások során a CPU-használat növekedése növekszik. 

**Tanúsítványlánc**

- A HITELESÍTÉSSZOLGÁLTATÓ által megadott köztes tanúsítványfájl (ha van ilyen).

- A kiszolgáló tanúsítványát kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítványnak először a fájlban kell lennie, amelyet a többinek a gyökérig kell megadnia. 
- A lánc tartalmazhat táska-attribútumokat.

**Jelszót**

- Egy kulcs támogatott.

- A tanúsítvány importálásakor be kell állítani.

A más paraméterekkel rendelkező tanúsítványok is működhetnek, de a Microsoft nem támogatja őket.

#### <a name="encryption-key-artifacts"></a>Titkosítási kulcs összetevői

**. PEM: tanúsítvány-tároló fájl**

A Privacy Enhanced Mail (PEM) fájlok az e-mailek védelméhez használt általános fájltípusok voltak. Napjainkban a PEM-fájlok tanúsítványokkal vannak ellátva, és x509 ASN1 kulcsokat használnak.  

A tároló-fájl a 1421 – 1424, a csak nyilvános tanúsítványt tartalmazó tároló formátuma alapján van meghatározva. Például az Apache telepíti, a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt, a fájlokat, az SSL-t vagy a TANÚSÍTVÁNYokat. Ez magában foglalhatja a teljes tanúsítványláncot, beleértve a nyilvános kulcsokat, a titkos kulcsokat és a főtanúsítványokat is.  

Emellett a CSR-t PKCS10 formátumban is kódolhatja, amely a PEM-ba fordítható le.

**. CERT. cer. CRT: tanúsítvány-tároló fájlja**

Egy `.pem` `.der` másik kiterjesztéssel rendelkező vagy formázott fájl. A Windows Intéző tanúsítványként ismeri fel a fájlt. A `.pem`   Windows Intéző nem ismeri fel a fájlt.

**. Key: titkos kulcsfájl**

A kulcsfájl formátuma azonos a PEM-fájllal, de más kiterjesztéssel rendelkezik. 

#### <a name="additional-commonly-available-key-artifacts"></a>További általánosan elérhető főbb összetevők

**. CSR – tanúsítvány-aláírási kérelem**.  

Ez a fájl a hitelesítésszolgáltatók számára történő küldéshez használatos. A tényleges formátum a PKCS10, amely az RFC 2986-ben van meghatározva, és tartalmazhat néhányat vagy a kért tanúsítvány legfontosabb részleteit. Például a tárgy, a szervezet és az állapot. Ez a HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítvány nyilvános kulcsa, és a rendszer visszaküldi a tanúsítványt.  

A visszaadott tanúsítvány a nyilvános tanúsítvány, amely magában foglalja a nyilvános kulcsot, de nem a titkos kulcsot. 

**. PKCS12/pfx-profil. pfx. P12 – jelszó tároló**. 

Az RSA által eredetileg az Public-Key titkosítási szabványokban (PKCS) definiált, a 12 változatot eredetileg a Microsoft fejlesztette ki, később pedig RFC 7292-ként lett elküldve.  

A tároló formátumához a nyilvános és a privát tanúsítvány párokat egyaránt tartalmazó jelszó szükséges. `.pem`   A fájlokkal ellentétben ez a tároló teljesen titkosítva van.  

Az OpenSSL használatával a fájl a `.pem`   nyilvános és titkos kulcsokat tartalmazó fájlba is bekapcsolható: `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**. der – bináris kódolású PEM**.

Az ASN. 1 szintaxis bináris fájlban való kódolásának módja egy `.pem`   fájlon keresztül történik, amely csak egy Base64 kódolású `.der` fájl. 

Az OpenSSL konvertálhatja ezeket a fájlokat a következőre `.pem` :  `openssl x509 -inform der -in to-convert.der -out converted.pem` .  

A Windows a fájlokat tanúsítványfájlként fogja felismerni. Alapértelmezés szerint a Windows a tanúsítványokat `.der` más kiterjesztésű formázott fájlként fogja exportálni.

**. CRL – visszavont tanúsítványok listája**.  

A hitelesítésszolgáltatók ezeket a tanúsítványokat a lejárat előtt előállítják a tanúsítványok engedélyezéséhez. 

#### <a name="cli-commands"></a>Parancssori felületi parancsok

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

## <a name="define-backup-and-restore-settings"></a>Biztonsági mentési és visszaállítási beállítások megadása

A helyszíni felügyeleti konzol rendszerének biztonsági mentése automatikusan, naponta történik. Az adattárolást egy másik lemezen menti a rendszer. Az alapértelmezett hely a `/var/cyberx/backups` . 

Ezt a fájlt automatikusan átviheti a belső hálózatra. 

> [!NOTE]
> A biztonsági mentési és visszaállítási eljárást csak ugyanazon a verzión lehet elvégezni. 

A helyszíni felügyeleti konzol számítógépének biztonsági mentése: 

- Jelentkezzen be egy rendszergazdai fiókba, és írja be a következőt: `sudo cyberx-management-backup -full` .

A legújabb biztonságimásolat-fájl visszaállítása:

- Jelentkezzen be egy rendszergazdai fiókba, és írja be a következőt: `$ sudo cyberx-management-system-restore` .

A biztonsági másolat mentése külső SMB-kiszolgálóra:

1. Hozzon létre egy megosztott mappát a külső SMB-kiszolgálón.  

   Szerezze be az SMB-kiszolgáló eléréséhez szükséges mappa elérési útját, felhasználónevét és jelszavát. 

2. A IoT-beli Defender esetében készítse el a biztonsági másolatok könyvtárát:

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. Fstab-szerkesztő:  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. Az SMB-kiszolgáló megosztásához szükséges hitelesítő adatok szerkesztése vagy létrehozása: 

   - `sudo nano /etc/samba/user` 

5. Adja hozzá a következőket: 

   - `username=<user name>`

   - `password=<password>` 

6. A könyvtár csatlakoztatása: 

   - `sudo mount -a` 

7. Állítson be egy biztonsági mentési könyvtárat a Defender IoT helyszíni felügyeleti konzoljának megosztott mappájába:  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>Az állomásnév szerkesztése

A felügyeleti konzolon a szervezeti DNS-kiszolgálón konfigurált állomásnév szerkesztése:

1. A felügyeleti konzol bal oldali ablaktábláján válassza a **Rendszerbeállítások** elemet.  

2. A konzol hálózatkezelés szakaszában válassza a **hálózat** lehetőséget. 

3. Adja meg a szervezeti DNS-kiszolgálón konfigurált állomásnév nevét. 

4. Kattintson a **Mentés** gombra.

## <a name="define-vlan-names"></a>VLAN-nevek definiálása

A VLAN-nevek nincsenek szinkronizálva az érzékelő és a felügyeleti konzol között. Meg kell határoznia az összetevők azonos nevét.

A hálózatkezelés területen válassza a **VLAN** lehetőséget, és adja hozzá a neveket a felderített VLAN-azonosítóhoz. Kattintson a **Mentés** gombra.

## <a name="define-a-proxy-to-sensors"></a>Proxy definiálása érzékelőkhöz

Fokozza a rendszerbiztonságot azáltal, hogy megakadályozza a felhasználói bejelentkezést közvetlenül az érzékelőn. Ehelyett használja a proxy bújtatást, hogy a felhasználók egyetlen tűzfalszabály használatával férhessenek hozzá az érzékelőhöz a helyszíni felügyeleti konzolról. Ez a fejlesztés leszűkíti az érzékelőn túli hálózati környezethez való jogosulatlan hozzáférés lehetőségét.

Használjon proxyt olyan környezetekben, ahol nincs közvetlen kapcsolat az érzékelőkkel.

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="Felhasználói.":::

Az alábbi eljárás egy érzékelőt csatlakoztat a helyszíni felügyeleti konzolhoz, és engedélyezi a bújtatást a konzolon:

1. Jelentkezzen be a helyszíni felügyeleti konzol készülék CLI-be rendszergazdai hitelesítő adatokkal.

2. Írja be `sudo cyberx-management-tunnel-enable` az **ENTER billentyűt**, majd válassza a.

4. Írja be `--port 10000` az **ENTER billentyűt**, majd válassza a.

## <a name="adjust-system-properties"></a>A Rendszertulajdonságok módosítása

A Rendszertulajdonságok a kezelési konzol különböző műveleteit és beállításait vezérlik. Előfordulhat, hogy a szerkesztési vagy módosítási művelet kárt okozhat a felügyeleti konzol működésében. A beállítások módosítása előtt forduljon a [Microsoft ügyfélszolgálatahoz](https://support.microsoft.com) .

A Rendszertulajdonságok elérése: 

1. Jelentkezzen be a helyszíni felügyeleti konzolba vagy az érzékelőbe.

2. Válassza a **Rendszerbeállítások** lehetőséget.

3. Válassza ki a **rendszer tulajdonságai** elemet az **általános** szakaszban.

## <a name="change-the-name-of-the-on-premises-management-console"></a>A helyszíni felügyeleti konzol nevének módosítása

Módosíthatja a helyszíni felügyeleti konzol nevét. Az új nevek megjelennek a konzol webböngészőjében, a különböző konzolok ablakában és a hibaelhárítási naplókban. Az alapértelmezett név a **felügyeleti konzol**.

A név módosítása:

1. A bal oldali ablaktábla alján válassza ki az aktuális nevet.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="Képernyőkép a helyszíni felügyeleti konzol verziószámáról.":::

2. A **felügyeleti konzol konfigurációjának szerkesztése** párbeszédpanelen adja meg az új nevet. A név nem lehet hosszabb 25 karakternél.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="Képernyőkép a Defender IoT platform konfigurációjának szerkesztéséről.":::

3. Kattintson a **Mentés** gombra. Az új név lesz alkalmazva.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="A konzol módosított nevét bemutató képernyőkép.":::

## <a name="password-recovery"></a>Jelszó-helyreállítás

A helyszíni felügyeleti konzol jelszavas helyreállítása ahhoz az előfizetéshez van kötve, amelyhez az eszköz csatolva van. A jelszó nem állítható helyre, ha nem tudja, hogy az eszköz melyik előfizetéshez van csatolva.

A jelszó alaphelyzetbe állítása:

1. Nyissa meg a helyszíni felügyeleti konzol bejelentkezési lapját.
1. Válassza a **jelszó-helyreállítás** lehetőséget.
1. Másolja ki az egyedi azonosítót.
1. Nyissa meg a Defender for IoT **Sites and sensors** lapot, és válassza a **saját jelszó helyreállítása** lapot.
1. Adja meg az egyedi azonosítót, és kattintson a **helyreállítás** elemre. Az aktiválási fájl letöltése megtörténik.
1. Lépjen a **jelszó-helyreállítás** lapra, és töltse fel az aktiválási fájlt.
1. Kattintson a **Tovább** gombra.
 
   Ekkor megadta a felhasználónevét és egy új, rendszer által létrehozott jelszót.

> [!NOTE]
> Az érzékelő ahhoz az előfizetéshez van csatolva, amelyhez eredetileg kapcsolódott. A jelszót csak azzal az előfizetéssel állíthatja helyre, amelyhez hozzá van csatolva.

## <a name="update-the-software-version"></a>A szoftver verziójának frissítése

Az alábbi eljárás ismerteti, hogyan frissítheti a helyszíni felügyeleti konzol szoftverének verzióját. A frissítési folyamat körülbelül 30 percet vesz igénybe.

1. Nyissa meg az [Azure Portal](https://portal.azure.com/).

1. Nyissa meg a Defender for IoT.

1. Nyissa meg a **frissítések** lapot.

1. Válasszon egy verziót a helyszíni felügyeleti konzol szakaszban.

1. Válassza a **Letöltés** lehetőséget, és mentse a fájlt.

1. Jelentkezzen be a helyszíni felügyeleti konzolba, és válassza a **System Settings (rendszerbeállítások** ) lehetőséget az oldal menüből.

1. A **verzió frissítése** panelen válassza a **frissítés** lehetőséget.

1. Válassza ki azt a fájlt, amelyet a Defender for IoT **Updates** lapon töltött le.

## <a name="mail-server-settings"></a>Levelezési kiszolgáló beállításai

Az SMTP levelezési kiszolgáló beállításainak megadása a helyszíni felügyeleti konzolhoz.

A következő megadása:

1. Jelentkezzen be a parancssori felületre a rendszergazdai hitelesítő adatokkal rendelkező helyszíni felügyelethez.
1. Gépelje be: ```nano /var/cyberx/properties/remote-interfaces.properties```.
1. Nyomja le az Enter billentyűt. A következő promptok jelennek meg.
```mail.smtp_server= ```
```mail.port=25 ```
```mail.sender=```
1. Adja meg az SMTP-kiszolgáló nevét és a küldőt, majd kattintson az ENTER gombra.

## <a name="see-also"></a>Lásd még

[Érzékelők kezelése a felügyeleti konzolról](how-to-manage-sensors-from-the-on-premises-management-console.md)

[Különálló érzékelők kezelése](how-to-manage-individual-sensors.md)

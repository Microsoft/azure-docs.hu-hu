---
title: Tanúsítványok használata Azure Stack Edge Pro GPU-val | Microsoft Docs
description: Ismerteti a tanúsítványok használatát Azure Stack Edge Pro GPU-eszközzel, beleértve a használatának okát és a tanúsítványok az eszközön való feltöltésének módját.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 4193e58c28b481297df38bca8f18d2ea766ce886
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443131"
---
# <a name="use-certificates-with-azure-stack-edge-pro-gpu-device"></a>Tanúsítványok használata Azure Stack Edge Pro GPU-eszközzel

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk az Azure Stack Edge Pro-eszközre telepíthető tanúsítványok típusait ismerteti. A cikk az egyes tanúsítvány-típusok részleteit is tartalmazza, valamint a lejárati dátum telepítésére és azonosítására szolgáló eljárást. 

## <a name="about-certificates"></a>Információ a tanúsítványokról

A tanúsítvány egy **nyilvános kulcs** és egy olyan entitás (például tartománynév) közötti kapcsolatot biztosít, amely egy megbízható harmadik fél ( **például egy hitelesítésszolgáltató**) számára van **aláírva** (ellenőrizve).  A tanúsítvány kényelmes módszert biztosít a megbízható nyilvános titkosítási kulcsok terjesztésére. A tanúsítványok segítségével biztosítható, hogy a kommunikáció megbízható legyen, és titkosított adatokat küldjön a megfelelő kiszolgálóra. 

Ha a Azure Stack Edge Pro-eszköz eredetileg be van állítva, az önaláírt tanúsítványok automatikusan létrejönnek. Igény szerint saját tanúsítványokat is használhat. Ha saját tanúsítványokat szeretne létrehozni, kövesse az alábbi irányelveket.

## <a name="types-of-certificates"></a>A tanúsítványok típusai

Az Azure Stack Edge Pro-eszközön használt különböző típusú tanúsítványok a következők: 
- Aláíró tanúsítványok
    - Legfelső szintű hitelesítésszolgáltató
    - Haladó

- Végponti tanúsítványok
    - Csomópont-tanúsítvány
    - Helyi felhasználói felületi tanúsítványok
    - Tanúsítványok Azure Resource Manager
    - BLOB Storage-tanúsítványok
    - IoT-eszközök tanúsítványainak
    <!--- WiFi certificates
    - VPN certificates-->

- Titkosítási tanúsítványok
    - A munkamenet-tanúsítványok támogatása

A következő szakaszokban részletesen ismertetjük ezeket a tanúsítványokat.

## <a name="signing-chain-certificates"></a>Aláíró lánc tanúsítványainak

Ezek azok a hatóságok tanúsítványai, amelyek aláírják a tanúsítványokat vagy az aláíró hitelesítésszolgáltatókat. 

### <a name="types"></a>Típusok

Ezek a tanúsítványok lehetnek főtanúsítványok vagy közbenső tanúsítványok. A főtanúsítványok mindig önaláírtak (vagy önaláírtak). A köztes tanúsítványok nem önaláírtak, és az aláíró hatóság aláírja őket.

### <a name="caveats"></a>Figyelmeztetések

- A főtanúsítványoknak regisztrálniuk kell a tanúsítványlánc tanúsítványait.
- A főtanúsítványok a következő formátumban tölthetők fel az eszközre: 
    - **Der** – ezek fájlkiterjesztésként érhetők el `.cer` .
    - **Base-64 kódolású** – ezek `.cer` fájlkiterjesztésként érhetők el.
    - **P7b** – ez a formátum csak olyan aláírási láncú tanúsítványokhoz használható, amelyek tartalmazzák a gyökér és a köztes tanúsítványokat.
- Az aláíró lánc tanúsítványait a rendszer mindig feltölti az egyéb tanúsítványok feltöltése előtt.


## <a name="node-certificates"></a>Csomóponti tanúsítványok

<!--Your Azure Stack Edge Pro device could be a 1-node device or a 4-node device.--> Az eszköz összes csomópontja folyamatosan kommunikál egymással, ezért megbízhatósági kapcsolatban kell lennie. A csomópont-tanúsítványok lehetőséget biztosítanak a megbízhatóság létrehozására. A csomópont-tanúsítványok akkor is lejátszásra kerülnek, ha egy távoli PowerShell-munkamenettel csatlakozik a https-kapcsolaton keresztül az eszköz csomóponthoz.

### <a name="caveats"></a>Figyelmeztetések

- A csomópont tanúsítványát olyan formátumban kell megadni, `.pfx` amelyet exportálni lehet egy titkos kulccsal. 
- Létrehozhatja és feltöltheti az 1 helyettesítő vagy 4 különálló csomópont-tanúsítványokat. 
- Ha a DNS-tartomány megváltozik, de az eszköz neve nem változik, a csomópont-tanúsítványt módosítani kell. Ha saját csomópont-tanúsítványt hoz, akkor az eszköz sorozatszáma nem módosítható, csak a tartománynevet lehet megváltoztatni.
- A következő táblázat segítséget nyújt a csomópont-tanúsítványok létrehozásakor.
   
    |Típus |Tulajdonos neve (SN)  |Tulajdonos alternatív neve (SAN)  |Tulajdonos neve – példa |
    |---------|---------|---------|---------|
    |Csomópont|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>Végponti tanúsítványok

Az eszköz által közzétett bármely végpont esetében egy tanúsítványra van szükség a megbízható kommunikációhoz. A végponti tanúsítványok magukban foglalják a Azure Resource Manager és a blob Storage REST API-kon keresztüli eléréséhez szükségesket. 

Ha saját aláírt tanúsítványt használ, a tanúsítvány megfelelő aláíró láncára is szüksége lesz. Az aláíró lánchoz, Azure Resource Managerhoz és az eszközön található blob-tanúsítványokhoz szüksége lesz a megfelelő tanúsítványokra az ügyfélszámítógépen is, hogy hitelesítse és kommunikáljon az eszközzel.

### <a name="caveats"></a>Figyelmeztetések

- A végponti tanúsítványokat `.pfx` titkos kulccsal kell formázni. Az aláírási láncnak DER formátumúnak (fájlkiterjesztés) kell lennie `.cer` . 
- Ha saját végponti tanúsítványokat használ, ezek lehetnek önálló tanúsítványok vagy többtartományos tanúsítványok. 
- Ha aláíró láncba kerül, az aláíró lánc tanúsítványát fel kell tölteni a végponti tanúsítvány feltöltése előtt.
- Ezeket a tanúsítványokat meg kell változtatni, ha az eszköz neve vagy a DNS-tartománynév megváltozik.
- Helyettesítő karakteres végponti tanúsítvány is használható.
- A végponti tanúsítványok tulajdonságai hasonlóak egy tipikus SSL-tanúsítványhoz. 
- Végponti tanúsítvány létrehozásakor használja az alábbi táblázatot:

    |Típus |Tulajdonos neve (SN)  |Tulajdonos alternatív neve (SAN)  |Tulajdonos neve – példa |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Több-SAN önálló tanúsítvány mindkét végponthoz|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>Helyi felhasználói felületi tanúsítványok

Az eszköz helyi webes FELÜLETét böngészőn keresztül érheti el. A kommunikáció biztonsága érdekében feltöltheti saját tanúsítványát. 

### <a name="caveats"></a>Figyelmeztetések

- A helyi felhasználói felületi tanúsítvány egy olyan formátumban is fel van töltve, amely `.pfx` exportálható titkos kulccsal.
- A helyi felhasználói felület tanúsítványának feltöltése után újra kell indítania a böngészőt, és törölnie kell a gyorsítótárat. Tekintse meg a böngésző konkrét utasításait.

    |Típus |Tulajdonos neve (SN)  |Tulajdonos alternatív neve (SAN)  |Tulajdonos neve – példa |
    |---------|---------|---------|---------|
    |Helyi felhasználói felület| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>IoT Edge eszközök tanúsítványainak

Az Azure Stack Edge Pro-eszköz egy IoT-eszköz is, amely egy IoT Edge eszközhöz csatlakoztatott számítási kapacitást is engedélyez. A IoT Edge-eszköz és a hozzájuk kapcsolódó alsóbb rétegbeli eszközök közötti biztonságos kommunikációhoz IoT Edge tanúsítványokat is fel lehet tölteni. 

Az eszköz önaláírt tanúsítványokkal rendelkezik, amelyek akkor használhatók, ha csak a számítási forgatókönyvet szeretné használni az eszközzel. Ha az Azure Stack Edge Pro-eszköz csatlakoztatva van az alárendelt eszközökhöz, akkor saját tanúsítványokat kell használnia.

A megbízhatósági kapcsolat engedélyezéséhez három IoT Edge tanúsítványt kell telepítenie:

- **Legfelső szintű hitelesítésszolgáltató vagy a tulajdonos hitelesítésszolgáltatója**
- **Eszköz hitelesítésszolgáltatója** 
- **Eszköz kulcsának tanúsítványa**

### <a name="caveats"></a>Figyelmeztetések

- A IoT Edge tanúsítványokat a rendszer `.pem` formátumban feltölti. 


További információ a IoT Edge tanúsítványokról: [Azure IoT Edge tanúsítvány részletei](../iot-edge/iot-edge-certs.md#iot-edge-certificates).

## <a name="support-session-certificates"></a>A munkamenet-tanúsítványok támogatása

Ha az Azure Stack Edge Pro-eszköz bármilyen problémát tapasztal, akkor a problémák elhárítása érdekében egy távoli PowerShell-támogatási munkamenet is megnyitható az eszközön. Ha biztonságos, titkosított kommunikációt szeretne engedélyezni ezen a támogatási munkameneten keresztül, feltöltheti a tanúsítványt.

### <a name="caveats"></a>Figyelmeztetések

- Győződjön meg arról, hogy a megfelelő `.pfx` , titkos kulccsal rendelkező tanúsítvány telepítve van az ügyfélszámítógépen a visszafejtés eszköz használatával.
- Ellenőrizze, hogy a tanúsítvány **kulcshasználat** mezője nem tanúsítvány- **aláírás**-e. Ennek ellenőrzéséhez kattintson a jobb gombbal a tanúsítványra, válassza a **Megnyitás** lehetőséget, majd a **részletek** lapon keresse meg a **kulcshasználat** elemet. 


### <a name="caveats"></a>Figyelmeztetések

- A támogatási munkamenet tanúsítványát DER formátummal kell megadni `.cer` kiterjesztéssel.


<!--## VPN certificates

If VPN is configured on your Azure Stack Edge Pro device, then you will also need a certificate for any communication that occurs over the VPN channel. You can bring your own VPN certificate to ensure the communication is trusted.

### Caveats

- The VPN certificate must be uploaded as a pfx format with a  private key.
- The VPN certificate is not dependant on the device name, device serial number, or device configuration. It only requires the external FQDN.
- Make sure that the client OID is set.-->

<!--## WiFi certificates

If your device is configured to operate on a wireless network, then you will also need a WiFi certificate for any communication that occurs over the wireless network. 

### Caveats

- The WiFi certificate must be uploaded as a pfx format with a private key.
- Make sure that the client OID is set.-->


## <a name="create-certificates-optional"></a>Tanúsítványok létrehozása (nem kötelező)

A következő szakasz az aláíró lánc és a végponti tanúsítványok létrehozási eljárását ismerteti.

### <a name="certificate-workflow"></a>Tanúsítvány munkafolyamata

A környezetben működő eszközök tanúsítványainak létrehozásához meg kell adni egy meghatározott módszert. Használhatja a rendszergazda által biztosított tanúsítványokat. 

**Csak fejlesztési vagy tesztelési célokra a Windows PowerShell használatával is létrehozhat tanúsítványokat a helyi rendszeren.** Az ügyfél tanúsítványainak létrehozásakor kövesse az alábbi irányelveket:

1. A következő típusú tanúsítványokat hozhatja létre:

    - Hozzon létre egyetlen tanúsítványt, amely egyetlen teljesen minősített tartománynév (FQDN) használatával használható. Például: *mydomain.com*.
    - Hozzon létre helyettesítő tanúsítványt a fő tartománynév és több altartomány biztonságossá tételéhez is. Például: **. mydomain.com*.
    - Hozzon létre egy tulajdonos alternatív neve (SAN) tanúsítványát, amely egyetlen tanúsítványban több tartománynevet is magában foglal. 

2. Ha saját tanúsítványt hoz, szüksége lesz egy főtanúsítványra az aláírási lánchoz. Lásd: az [aláírási lánc tanúsítványainak létrehozásának](#create-signing-chain-certificate)lépései.

3. Ezután hozza létre a végponti tanúsítványokat a készülék, a blob és a Azure Resource Manager helyi felhasználói felületéhez. Létrehozhat 3 különálló tanúsítványt a készülékhez, a blobhoz és a Azure Resource Managerhoz, vagy létrehozhat egy tanúsítványt az összes 3 végponthoz. A részletes lépésekért lásd: [aláírási és végponti tanúsítványok létrehozása](#create-signed-endpoint-certificates).

4. Függetlenül attól, hogy 3 különálló tanúsítványt vagy egy tanúsítványt hoz létre, adja meg a tulajdonos nevét (SN) és a tulajdonos alternatív nevét (SAN) az egyes tanúsítványok típusaihoz megadott útmutatás szerint. 

### <a name="create-signing-chain-certificate"></a>Aláíró lánc tanúsítványának létrehozása

Hozza létre ezeket a tanúsítványokat rendszergazdai módban futó Windows PowerShell használatával. **Az ily módon létrehozott tanúsítványokat csak fejlesztési vagy tesztelési célokra lehet használni.**

Az aláíró lánc tanúsítványát csak egyszer kell létrehozni. A többi végponti tanúsítvány erre a tanúsítványra vonatkozik az aláíráshoz.
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>Aláírt végponti tanúsítványok létrehozása

Hozza létre ezeket a tanúsítványokat rendszergazdai módban futó Windows PowerShell használatával.

Ezekben a példákban a végponti tanúsítványokat egy eszközhöz hozza létre a következővel:
    - Eszköz neve: `DBE-HWDC1T2`
    - DNS-tartomány: `microsoftdatabox.com`

A helyére írja be az eszköz nevét és DNS-tartományát az eszközhöz tartozó tanúsítványok létrehozásához.
 
**BLOB-végpont tanúsítványa**

Hozzon létre egy tanúsítványt a blob-végponthoz a személyes tárolóban.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Azure Resource Manager Endpoint Certificate**

Hozzon létre egy tanúsítványt a Azure Resource Manager-végpontok számára a személyes tárolóban.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Eszköz helyi webes felhasználói felületének tanúsítványa**

Hozzon létre egy tanúsítványt az eszköz helyi webes felhasználói felületéhez a személyes tárolóban.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Egyetlen több-SAN tanúsítvány az összes végponthoz**

Hozzon létre egyetlen tanúsítványt a személyes tárolóban lévő összes végponthoz.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

A tanúsítványok létrehozása után a következő lépés a tanúsítványok feltöltése a Azure Stack Edge Pro-eszközön


## <a name="upload-certificates"></a>Tanúsítványok feltöltése 

Az eszközhöz létrehozott tanúsítványok alapértelmezés szerint az ügyfél **személyes tárolójában** találhatók. Ezeket a tanúsítványokat a megfelelő formátumú fájlokra kell exportálni az ügyfélen, amelyeket aztán fel lehet tölteni az eszközre.

1. A főtanúsítványt DER formátumban kell exportálni `.cer` kiterjesztéssel. A részletes lépésekért lásd: [tanúsítványok exportálása der formátumként](#export-certificates-as-der-format).
2. A végponti tanúsítványokat *. pfx* -fájlként kell exportálni titkos kulcsokkal. A részletes lépésekért lásd: [tanúsítványok exportálása *. pfx* -fájlként, titkos kulcsokkal](#export-certificates-as-pfx-format-with-private-key). 
3. Ezután a rendszer feltölti a gyökér-és végpont-tanúsítványokat az eszközre a helyi webes felhasználói felület tanúsítványok lapján található **+ tanúsítvány hozzáadása** lehetőség használatával. 

    1. Először töltse fel a főtanúsítványokat. A helyi webes KEZELŐFELÜLETen válassza a **tanúsítványok > + tanúsítvány hozzáadása** lehetőséget.

        ![1. aláíró tanúsítványlánc hozzáadása](media/azure-stack-edge-series-manage-certificates/add-cert-1.png)

    2. Ezután töltse fel a végponti tanúsítványokat. 

        ![2. aláíró tanúsítványlánc hozzáadása](media/azure-stack-edge-series-manage-certificates/add-cert-2.png)

        Válassza ki a tanúsítványfájlt *. pfx* formátumban, majd adja meg a tanúsítvány exportálásakor megadott jelszót. Az Azure Resource Manager-tanúsítvány alkalmazása több percet is igénybe vehet.

        Ha az aláíró lánc nem frissül először, és megpróbálja feltölteni a végponti tanúsítványokat, hibaüzenet jelenik meg.

        ![Tanúsítvány alkalmazása hiba](media/azure-stack-edge-series-manage-certificates/apply-cert-error-1.png)

        Lépjen vissza, és töltse fel az aláíró lánc tanúsítványát, majd töltse fel és alkalmazza a végponti tanúsítványokat.

> [!IMPORTANT]
> Ha az eszköz neve vagy a DNS-tartomány megváltozik, új tanúsítványokat kell létrehozni. Az ügyféltanúsítványok és az eszközök tanúsítványait ezután frissíteni kell az új eszköznév és a DNS-tartománnyal. 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>Tanúsítványok importálása az eszközhöz hozzáférő ügyfélen

Az eszközre létrehozott és feltöltött tanúsítványokat importálni kell a Windows-ügyfélre (az eszköz eléréséhez) a megfelelő tanúsítványtárolóba.

1. A DER-ként exportált főtanúsítványt most importálni kell a **megbízható legfelső szintű** hitelesítésszolgáltatókba az ügyfél-rendszeren. A részletes lépésekért lásd: [tanúsítványok importálása a megbízható legfelső szintű hitelesítésszolgáltatók tárolójába](#import-certificates-as-der-format).

2. A-ként exportált végpont-tanúsítványokat a `.pfx` der kiterjesztéssel kell exportálni `.cer` . Ezt `.cer` követően a rendszer a **személyes tanúsítványtárolóba** importálja. A részletes lépésekért lásd: [tanúsítványok importálása a személyes tanúsítványtárolóba](#import-certificates-as-der-format).

### <a name="import-certificates-as-der-format"></a>Tanúsítványok importálása DER formátumban

A tanúsítványok Windows-ügyfélre történő importálásához hajtsa végre a következő lépéseket:

1. Kattintson a jobb gombbal a fájlra, majd válassza a **tanúsítvány telepítése** lehetőséget. Ez a művelet elindítja a tanúsítvány importálása varázslót.

    ![1. tanúsítvány importálása](media/azure-stack-edge-series-manage-certificates/import-cert-1.png)

2. Az **áruház helye** területen válassza a **helyi számítógép** lehetőséget, majd kattintson a **tovább** gombra.

    ![2. tanúsítvány importálása](media/azure-stack-edge-series-manage-certificates/import-cert-2.png)

3. Jelölje be **az összes tanúsítvány tárolása a következő tárolóban** jelölőnégyzetet, majd válassza a **Tallózás** lehetőséget. 

    - A személyes tárolóba való importáláshoz navigáljon a távoli gazdagép személyes tárolójába, majd válassza a **tovább** lehetőséget.

        ![4. tanúsítvány importálása](media/azure-stack-edge-series-manage-certificates/import-cert-4.png)


    - A megbízható tárolóba való importáláshoz navigáljon a megbízható legfelső szintű hitelesítésszolgáltatóhoz, majd válassza a **tovább** lehetőséget.

        ![3. tanúsítvány importálása](media/azure-stack-edge-series-manage-certificates/import-cert-3.png)

 
4. Válassza a **Befejezés** gombot. Megjelenik egy üzenet, amely azt eredményezi, hogy az importálás sikeres volt.

### <a name="export-certificates-as-pfx-format-with-private-key"></a>Tanúsítványok exportálása. pfx formátumban, titkos kulccsal

A következő lépésekkel exportálhat egy titkos kulccsal rendelkező SSL-tanúsítványt egy Windows rendszerű gépen. 

> [!IMPORTANT]
> Hajtsa végre ezeket a lépéseket ugyanarra a gépre, amelyet a tanúsítvány létrehozásához használt. 

1. Futtassa a *certlm. msc fájlt* a helyi számítógép tanúsítványtárolójának elindításához.

1. Kattintson duplán a **személyes** mappára, majd a **tanúsítványok** elemre.

    ![1. tanúsítvány exportálása](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-1.png)
 
2. Kattintson a jobb gombbal arra a tanúsítványra, amelyről biztonsági másolatot szeretne készíteni, és válassza az **összes feladat > exportálás..** . lehetőséget.

    ![2. tanúsítvány exportálása](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-2.png)

3. A tanúsítvány egy. pfx-fájlba történő biztonsági mentéséhez kövesse a tanúsítvány exportálása varázslót.

    ![3. tanúsítvány exportálása](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-3.png)

4. Válassza **az Igen lehetőséget, majd exportálja a titkos kulcsot**.

    ![4. tanúsítvány exportálása](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-4.png)

5. Ha lehetséges, válassza az **összes tanúsítvány belefoglalása a tanúsítvány elérési útjában** lehetőséget, **exportálja az összes kiterjesztett tulajdonságot** , és **engedélyezze a tanúsítvány** 

    > [!IMPORTANT]
    > Ha az Exportálás sikeres, ne jelölje be a **titkos kulcs törlése beállítást**.

    ![5. tanúsítvány exportálása](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-5.png)

6. Adjon meg egy jelszót, amelyet meg fog emlékezni. Erősítse meg a jelszót. A jelszó védi a titkos kulcsot.

    ![6. tanúsítvány exportálása](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-6.png)

7. Válassza a fájl mentése egy meghatározott helyen lehetőséget.

    ![7. tanúsítvány exportálása](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-7.png)
  
8. Válassza a **Befejezés** gombot.

    ![8. tanúsítvány exportálása](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-8.png)

9. Az Exportálás sikerességét jelző üzenet jelenik meg. Válassza az **OK** lehetőséget.

    ![9. tanúsítvány exportálása](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-9.png)

A. pfx-fájl biztonsági mentése mostantól a kiválasztott helyre lett mentve, és készen áll arra, hogy áthelyezi vagy tárolja a biztonságos megőrzését.


### <a name="export-certificates-as-der-format"></a>Tanúsítványok exportálása DER-formátumként

1. Futtassa a *certlm. msc fájlt* a helyi számítógép tanúsítványtárolójának elindításához.

1. A személyes tanúsítványtárolóban válassza ki a főtanúsítványt. Kattintson a jobb gombbal, és válassza **az összes feladat > exportálás..** . lehetőséget.

    ![Exportálja a DER 1 tanúsítványt](media/azure-stack-edge-series-manage-certificates/export-cert-cer-1.png)

2. Megnyílik a tanúsítvány varázsló. Válassza ki a formátumot **der kódolású bináris X. 509 (. cer)** formátumban. Kattintson a **Tovább** gombra.

    ![A DER 2 tanúsítvány exportálása](media/azure-stack-edge-series-manage-certificates/export-cert-cer-2.png)

3. Tallózással keresse meg és válassza ki azt a helyet, ahová exportálni kívánja a. cer formátumú fájlt.

    ![Exportálási tanúsítvány DER 3](media/azure-stack-edge-series-manage-certificates/export-cert-cer-3.png)

4. Válassza a **Befejezés** gombot.

    ![Exportálási tanúsítvány DER 4](media/azure-stack-edge-series-manage-certificates/export-cert-cer-4.png)


## <a name="supported-certificate-algorithms"></a>Támogatott tanúsítvány-algoritmusok

 Az Azure Stack Edge Pro-eszközön csak a Rivest – a-Adleman (RSA) tanúsítványok támogatottak. Ha a rendszer elliptikus görbe digitális aláírási algoritmust (ECDSA) használ, az eszköz viselkedése meghatározatlan.

 Az RSA nyilvános kulcsot tartalmazó tanúsítványokat RSA-tanúsítványoknak nevezzük. Az elliptikus görbe kriptográfiai (ECC) nyilvános kulcsát tartalmazó tanúsítványokat ECDSA (elliptikus görbe digitális aláírási algoritmus) tanúsítványoknak nevezzük. 


## <a name="view-certificate-expiry"></a>Tanúsítvány lejáratának megtekintése

Ha saját tanúsítványokat használ, a tanúsítványok általában 1 év vagy 6 hónap múlva lejárnak. A tanúsítvány lejárati dátumának megtekintéséhez nyissa meg a **tanúsítványok** lapot az eszköz helyi webes felületén. Ha kiválaszt egy adott tanúsítványt, megtekintheti a tanúsítvány lejárati dátumát.

<!--## Rotate certificates

Rotation of certificates is not implemented in this release. You are also not notified of the pending expiration date on your certificate. 

View the certificate expiration date on the **Certificates** page in the local web UI of your device. Once the certificate expiration is approaching, create and upload new certificates as per the detailed instructions in [Create and upload certificates]().-->

## <a name="next-steps"></a>Következő lépések

[Az Azure Stack Edge Pro-eszköz üzembe helyezése](azure-stack-edge-gpu-deploy-prep.md)
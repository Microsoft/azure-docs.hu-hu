---
title: Felosztás – biztonsági konfiguráció egyesítése
description: Állítsa be a x409 tanúsítványokat a titkosításhoz a Split/Merge szolgáltatással a rugalmas skálázáshoz.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 235efc550fd47d4244a5bf081c75d5e824a8e4b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793432"
---
# <a name="split-merge-security-configuration"></a>Felosztás – biztonsági konfiguráció egyesítése
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A felosztás/egyesítés szolgáltatás használatához megfelelően kell konfigurálnia a biztonságot. A szolgáltatás a Azure SQL Database rugalmas skálázási funkciójának része. További információ: [rugalmas skálázási és egyesítési szolgáltatás oktatóanyaga](elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Tanúsítványok konfigurálása

A tanúsítványok két módon vannak konfigurálva. 

1. [A TLS/SSL-tanúsítvány konfigurálása](#to-configure-the-tlsssl-certificate)
2. [Ügyféltanúsítványok konfigurálása](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Tanúsítványok beszerzése

A tanúsítványok a nyilvános hitelesítésszolgáltatóktól (CA) vagy a [Windows Certificate Service szolgáltatásból](/windows/win32/seccrypto/certificate-services)szerezhetők be. A tanúsítványok beszerzésének előnyben részesített módszerei.

Ha ezek a beállítások nem érhetők el, **önaláírt tanúsítványokat** is létrehozhat.

## <a name="tools-to-generate-certificates"></a>Tanúsítványok létrehozásához szükséges eszközök

* [makecert.exe](/previous-versions/dotnet/netframework-4.0/bfsktky3(v=vs.100))
* [pvk2pfx.exe](/windows-hardware/drivers/devtest/pvk2pfx)

### <a name="to-run-the-tools"></a>Az eszközök futtatása

* A Visual studiók fejlesztői parancssorában tekintse meg a [Visual Studio parancssorát](/dotnet/framework/tools/developer-command-prompt-for-vs) . 
  
    Ha telepítve van, lépjen a következőre:
  
    ```console
    %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
    ```

* A WDK beszerzése a [Windows 8,1 rendszerből: letölthető készletek és eszközök](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-tlsssl-certificate"></a>A TLS/SSL-tanúsítvány konfigurálása

A kommunikáció titkosításához és a kiszolgáló hitelesítéséhez TLS/SSL-tanúsítvány szükséges. Válassza ki az alábbi három forgatókönyv közül leginkább megfelelőt, és hajtsa végre az összes lépését:

### <a name="create-a-new-self-signed-certificate"></a>Új önaláírt tanúsítvány létrehozása

1. [Self-Signed tanúsítvány létrehozása](#create-a-self-signed-certificate)
2. [PFX-fájl létrehozása Self-Signed TLS/SSL-tanúsítványhoz](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [TLS/SSL-tanúsítvány feltöltése a Cloud Service-be](#upload-tlsssl-certificate-to-cloud-service)
4. [TLS/SSL-tanúsítvány frissítése a szolgáltatás konfigurációs fájljában](#update-tlsssl-certificate-in-service-configuration-file)
5. [TLS/SSL-hitelesítésszolgáltató importálása](#import-tlsssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Meglévő tanúsítvány használata a tanúsítványtárolóból
1. [TLS/SSL-tanúsítvány exportálása a tanúsítványtárolóból](#export-tlsssl-certificate-from-certificate-store)
2. [TLS/SSL-tanúsítvány feltöltése a Cloud Service-be](#upload-tlsssl-certificate-to-cloud-service)
3. [TLS/SSL-tanúsítvány frissítése a szolgáltatás konfigurációs fájljában](#update-tlsssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Meglévő tanúsítvány használata PFX-fájlban
1. [TLS/SSL-tanúsítvány feltöltése a Cloud Service-be](#upload-tlsssl-certificate-to-cloud-service)
2. [TLS/SSL-tanúsítvány frissítése a szolgáltatás konfigurációs fájljában](#update-tlsssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Ügyféltanúsítványok konfigurálása
Az ügyféltanúsítványok a szolgáltatáshoz intézett kérések hitelesítéséhez szükségesek. Válassza ki az alábbi három forgatókönyv közül leginkább megfelelőt, és hajtsa végre az összes lépését:

### <a name="turn-off-client-certificates"></a>Ügyféltanúsítványok kikapcsolása
1. [Ügyfél Certificate-Based hitelesítésének kikapcsolása](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Új önaláírt Ügyféltanúsítványok kibocsátása
1. [Self-Signed hitelesítésszolgáltató létrehozása](#create-a-self-signed-certification-authority)
2. [HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány feltöltése a Cloud Service-be](#upload-ca-certificate-to-cloud-service)
3. [HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány frissítése a szolgáltatás konfigurációs fájljában](#update-ca-certificate-in-service-configuration-file)
4. [Ügyféltanúsítványok kiállítása](#issue-client-certificates)
5. [PFX-fájlok létrehozása az Ügyféltanúsítványok számára](#create-pfx-files-for-client-certificates)
6. [Ügyféltanúsítvány importálása](#import-client-certificate)
7. [Ügyféltanúsítvány ujjlenyomatai megfelelnek másolása](#copy-client-certificate-thumbprints)
8. [Az engedélyezett ügyfelek konfigurálása a szolgáltatás konfigurációs fájljában](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Meglévő ügyféltanúsítványok használata
1. [HITELESÍTÉSSZOLGÁLTATÓ nyilvános kulcsának keresése](#find-ca-public-key)
2. [HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány feltöltése a Cloud Service-be](#upload-ca-certificate-to-cloud-service)
3. [HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány frissítése a szolgáltatás konfigurációs fájljában](#update-ca-certificate-in-service-configuration-file)
4. [Ügyféltanúsítvány ujjlenyomatai megfelelnek másolása](#copy-client-certificate-thumbprints)
5. [Az engedélyezett ügyfelek konfigurálása a szolgáltatás konfigurációs fájljában](#configure-allowed-clients-in-the-service-configuration-file)
6. [Ügyféltanúsítvány-visszavonási vizsgálat konfigurálása](#configure-client-certificate-revocation-check)

## <a name="allowed-ip-addresses"></a>Engedélyezett IP-címek
A szolgáltatási végpontokhoz való hozzáférés az IP-címek meghatározott tartományára korlátozható.

## <a name="to-configure-encryption-for-the-store"></a>A tároló titkosításának konfigurálása
A metaadat-tárolóban tárolt hitelesítő adatok titkosításához tanúsítvány szükséges. Válassza ki az alábbi három forgatókönyv közül leginkább megfelelőt, és hajtsa végre az összes lépését:

### <a name="use-a-new-self-signed-certificate"></a>Új önaláírt tanúsítvány használata
1. [Self-Signed tanúsítvány létrehozása](#create-a-self-signed-certificate)
2. [PFX-fájl létrehozása Self-Signed titkosítási tanúsítványhoz](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Titkosítási tanúsítvány feltöltése a Cloud Service-be](#upload-encryption-certificate-to-cloud-service)
4. [Titkosítási tanúsítvány frissítése a szolgáltatás konfigurációs fájljában](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Meglévő tanúsítvány használata a tanúsítványtárolóból
1. [Titkosítási tanúsítvány exportálása a tanúsítványtárolóból](#export-encryption-certificate-from-certificate-store)
2. [Titkosítási tanúsítvány feltöltése a Cloud Service-be](#upload-encryption-certificate-to-cloud-service)
3. [Titkosítási tanúsítvány frissítése a szolgáltatás konfigurációs fájljában](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Meglévő tanúsítvány használata PFX-fájlban
1. [Titkosítási tanúsítvány feltöltése a Cloud Service-be](#upload-encryption-certificate-to-cloud-service)
2. [Titkosítási tanúsítvány frissítése a szolgáltatás konfigurációs fájljában](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>Az alapértelmezett konfiguráció
Az alapértelmezett konfiguráció megtagadja a HTTP-végponthoz való hozzáférést. Ez az ajánlott beállítás, mivel a végpontoknak küldött kérések bizalmas adatokat, például adatbázis-hitelesítő adatokat is tartalmazhatnak.
Az alapértelmezett konfiguráció lehetővé teszi a HTTPS-végponthoz való hozzáférést. Ez a beállítás továbbra is korlátozható.

### <a name="changing-the-configuration"></a>A konfiguráció módosítása
A és a végpontra vonatkozó hozzáférés-vezérlési szabályok csoportja a **\<EndpointAcls>** **szolgáltatás konfigurációs fájljának** szakaszában van konfigurálva.

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

A hozzáférés-vezérlési csoport szabályai \<AccessControl name=""> a szolgáltatás konfigurációs fájljának egy szakaszában vannak konfigurálva. 

A formátumot a Network Access Control lists dokumentációja ismerteti.
Ha például csak a 100.100.0.0 tartományhoz tartozó IP-címeket szeretné engedélyezni a HTTPS-végpont eléréséhez, a szabályok a következőképpen 100.100.255.255:

```xml
<AccessControl name="Retricted">
    <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
    <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
</AccessControl>
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />
</EndpointAcls>
```

## <a name="denial-of-service-prevention"></a>Szolgáltatásmegtagadást megakadályozó szolgáltatás
Két különböző mechanizmus támogatott a szolgáltatásmegtagadási támadások észlelésére és megelőzésére:

* Az egyidejű kérelmek számának korlátozása távoli gazdagépen (alapértelmezés szerint kikapcsolva)
* A távoli gazdagépek hozzáférési sebességének korlátozása (alapértelmezés szerint)

Ezek a dinamikus IP-biztonság IIS-ben leírt szolgáltatásain alapulnak. Ha módosítja ezt a konfigurációt, a következő tényezőkre van figyelemmel:

* A proxyk és hálózati címfordítási eszközök viselkedése a távoli gazdagép adatain keresztül
* A webes szerepkörben lévő erőforrásokra irányuló kérések (például parancsfájlok, képek stb.) megtekinthetők.

## <a name="restricting-number-of-concurrent-accesses"></a>Párhuzamos hozzáférések számának korlátozása
A viselkedést konfiguráló beállítások a következők:

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

A védelem engedélyezéséhez módosítsa a DynamicIpRestrictionDenyByConcurrentRequests igaz értékre.

## <a name="restricting-rate-of-access"></a>Hozzáférési arány korlátozása
A viselkedést konfiguráló beállítások a következők:

```xml
<Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
<Setting name="DynamicIpRestrictionMaxRequests" value="100" />
<Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />
```

## <a name="configuring-the-response-to-a-denied-request"></a>Egy elutasított kérelemre adott válasz konfigurálása
A következő beállítás egy elutasított kérelemre vonatkozó választ konfigurál:

```xml
<Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
```

További támogatott értékekért tekintse meg a dinamikus IP-biztonságra vonatkozó dokumentációt az IIS-ben.

## <a name="operations-for-configuring-service-certificates"></a>A szolgáltatási tanúsítványok konfigurálásának műveletei
Ez a témakör csak referenciául szolgál. Kövesse a következő témakörben ismertetett konfigurációs lépéseket:

* A TLS/SSL-tanúsítvány konfigurálása
* Ügyféltanúsítványok konfigurálása

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása
Végrehajtása

```console
makecert ^
  -n "CN=myservice.cloudapp.net" ^
  -e MM/DD/YYYY ^
  -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
  -a sha256 -len 2048 ^
  -sv MySSL.pvk MySSL.cer
```

A testreszabáshoz:

* -n a szolgáltatás URL-címével. A helyettesítő karakterek ("CN = *. cloudapp. net") és az alternatív nevek ("CN = myservice1. cloudapp. net, CN = myservice2. cloudapp. net") támogatottak.
* -e a tanúsítvány lejárati dátumával hozzon létre egy erős jelszót, és ha a rendszer kéri, válassza ki.

## <a name="create-pfx-file-for-self-signed-tlsssl-certificate"></a>PFX-fájl létrehozása önaláírt TLS/SSL-tanúsítványhoz
Végrehajtása

```console
pvk2pfx -pvk MySSL.pvk -spc MySSL.cer
```

Adja meg a jelszót, majd exportálja a tanúsítványt a következő beállításokkal:

* Igen, exportálja a titkos kulcsot
* Az összes kiterjesztett tulajdonság exportálása

## <a name="export-tlsssl-certificate-from-certificate-store"></a>TLS/SSL-tanúsítvány exportálása a tanúsítványtárolóból
* Tanúsítvány keresése
* Kattintson a műveletek-> az összes feladat – > exportálás... lehetőségre.
* Tanúsítvány exportálása a-ba. PFX-fájl a következő beállításokkal:
  * Igen, exportálja a titkos kulcsot
  * Az összes tanúsítvány belefoglalása a tanúsítvány elérési útjába, ha lehetséges * az összes kiterjesztett tulajdonság exportálása

## <a name="upload-tlsssl-certificate-to-cloud-service"></a>TLS/SSL-tanúsítvány feltöltése a Cloud Service-be
Meglévő vagy előállított tanúsítvány feltöltése. PFX-fájl a TLS-kulcspár használatával:

* Adja meg a titkos kulcs adatainak védelmét biztosító jelszót

## <a name="update-tlsssl-certificate-in-service-configuration-file"></a>TLS/SSL-tanúsítvány frissítése a szolgáltatás konfigurációs fájljában
Frissítse a szolgáltatás konfigurációs fájljában a következő beállítás ujjlenyomat-értékét a Cloud Service-be feltöltött tanúsítvány ujjlenyomatával:

```console
<Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="import-tlsssl-certification-authority"></a>TLS/SSL-hitelesítésszolgáltató importálása
Kövesse az alábbi lépéseket a szolgáltatással kommunikáló összes fiók/gép esetén:

* Kattintson duplán a elemre. CER-fájl a Windows Intézőben
* A tanúsítvány párbeszédpanelen kattintson a tanúsítvány telepítése... lehetőségre.
* Tanúsítvány importálása a megbízható legfelső szintű hitelesítésszolgáltatók tárolójába

## <a name="turn-off-client-certificate-based-authentication"></a>Ügyféltanúsítvány-alapú hitelesítés kikapcsolása
Csak az ügyféltanúsítvány-alapú hitelesítés támogatott, és a letiltása lehetővé teszi a szolgáltatás-végpontokhoz való nyilvános hozzáférést, kivéve, ha más mechanizmusok vannak érvényben (például Microsoft Azure Virtual Network).

Módosítsa a beállításokat hamis értékre a szolgáltatás konfigurációs fájljában a funkció kikapcsolásához:

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

Ezután másolja ki ugyanazt az ujjlenyomatot, mint a TLS/SSL-tanúsítvány a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány beállításában:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>Önaláírt hitelesítésszolgáltató létrehozása
A következő lépések végrehajtásával hozzon létre egy önaláírt tanúsítványt, amely hitelesítésszolgáltatóként működik:

```console
makecert ^
-n "CN=MyCA" ^
-e MM/DD/YYYY ^
 -r -cy authority -h 1 ^
 -a sha256 -len 2048 ^
  -sr localmachine -ss my ^
  MyCA.cer
```

A testreszabáshoz

* -e a minősítés lejárati dátumával

## <a name="find-ca-public-key"></a>HITELESÍTÉSSZOLGÁLTATÓ nyilvános kulcsának keresése
Az összes ügyféltanúsítványt ki kell állítani a szolgáltatás által megbízhatónak tartott hitelesítésszolgáltató. Keresse meg a hitelesítésszolgáltató nyilvános kulcsát, amely kiadta a hitelesítéshez használni kívánt ügyféltanúsítványt a felhőalapú szolgáltatásba való feltöltéshez.

Ha a nyilvános kulccsal rendelkező fájl nem érhető el, exportálja a tanúsítványtárolóból:

* Tanúsítvány keresése
  * Azonos hitelesítésszolgáltató által kiállított ügyféltanúsítvány keresése
* Kattintson duplán a tanúsítványra.
* A tanúsítvány párbeszédpanelen válassza a tanúsítványlánc fület.
* Kattintson duplán a CA bejegyzésre az elérési útban.
* Megjegyzések készítése a tanúsítvány tulajdonságairól.
* A **tanúsítvány** párbeszédpanel bezárásához.
* Tanúsítvány keresése
  * Keresse meg a fent említett HITELESÍTÉSSZOLGÁLTATÓT.
* Kattintson a műveletek-> az összes feladat – > exportálás... lehetőségre.
* Tanúsítvány exportálása a-ba. CER a következő beállításokkal:
  * **Nem, ne exportálja a titkos kulcsot**
  * Ha lehetséges, az összes tanúsítvány belefoglalása a tanúsítvány elérési útjába.
  * Az összes kiterjesztett tulajdonság exportálása.

## <a name="upload-ca-certificate-to-cloud-service"></a>HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány feltöltése a Cloud Service-be
Meglévő vagy előállított tanúsítvány feltöltése. A HITELESÍTÉSSZOLGÁLTATÓ nyilvános kulcsát tartalmazó CER-fájl.

## <a name="update-ca-certificate-in-service-configuration-file"></a>HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány frissítése a szolgáltatás konfigurációs fájljában
Frissítse a szolgáltatás konfigurációs fájljában a következő beállítás ujjlenyomat-értékét a Cloud Service-be feltöltött tanúsítvány ujjlenyomatával:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

Módosítsa a következő beállítás értékét ugyanazzal az ujjlenyomattal:

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>Ügyféltanúsítványok kiállítása
A szolgáltatás elérésére jogosult minden egyes személynek rendelkeznie kell egy, a kizárólagos használatra kiállított ügyféltanúsítványt, és a titkos kulcsának megvédéséhez ki kell választania a saját erős jelszavát. 

A következő lépéseket ugyanabban a gépen kell végrehajtani, ahol az önaláírt HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány létrejött és tárolva lett:

```console
makecert ^
  -n "CN=My ID" ^
  -e MM/DD/YYYY ^
  -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
  -a sha256 -len 2048 ^
  -in "MyCA" -ir localmachine -is my ^
  -sv MyID.pvk MyID.cer
```

Testreszabása

* -n AZONOSÍTÓval ahhoz az ügyfélhez, amely a tanúsítvánnyal lesz hitelesítve
* -e a tanúsítvány lejárati dátumával
* MyID. PVK és MyID. cer – egyedi fájlnevek ehhez az ügyféltanúsítvány-hoz

Ez a parancs felszólítja a létrehozandó jelszó megadására, majd egyszer használatba veszi azt. Használjon erős jelszót.

## <a name="create-pfx-files-for-client-certificates"></a>PFX-fájlok létrehozása az Ügyféltanúsítványok számára
Minden létrehozott ügyféltanúsítvány esetében hajtsa végre a következőt:

```console
pvk2pfx -pvk MyID.pvk -spc MyID.cer
```

Testreszabása

```console
MyID.pvk and MyID.cer with the filename for the client certificate
```

Adja meg a jelszót, majd exportálja a tanúsítványt a következő beállításokkal:

* Igen, exportálja a titkos kulcsot
* Az összes kiterjesztett tulajdonság exportálása
* A tanúsítványt kiállító személynek ki kell választania az exportálási jelszót

## <a name="import-client-certificate"></a>Ügyféltanúsítvány importálása
Minden olyan személynek, akivel egy ügyféltanúsítványt kiállítottak, importálnia kell a kulcspárt a szolgáltatással folytatott kommunikációhoz használni kívánt számítógépeken:

* Kattintson duplán a elemre. PFX-fájl a Windows Intézőben
* Importálja a tanúsítványt a személyes tárolóba legalább ezt a lehetőséget választva:
  * Az összes kijelölt kibővített tulajdonság belefoglalása

## <a name="copy-client-certificate-thumbprints"></a>Ügyféltanúsítvány ujjlenyomatai megfelelnek másolása
A tanúsítvány ujjlenyomatának beszerzéséhez minden olyan személynek, akivel egy ügyféltanúsítványt bocsátottak ki, az alábbi lépéseket kell követnie a szolgáltatás konfigurációs fájljához:

* certmgr.exe futtatása
* Személyes lap kiválasztása
* Kattintson duplán a hitelesítéshez használandó ügyféltanúsítvány használatára
* A megnyíló tanúsítvány párbeszédpanelen válassza a részletek lapot.
* Győződjön meg arról, hogy az összes megjelenítése
* Jelölje ki az ujjlenyomat nevű mezőt a listában.
* Az ujjlenyomat értékének másolása
  * Nem látható Unicode-karakterek törlése az első számjegy előtt
  * Összes szóköz törlése

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Az engedélyezett ügyfelek konfigurálása a szolgáltatás konfigurációs fájljában
Frissítse a szolgáltatás konfigurációs fájljában a következő beállítás értékét a szolgáltatáshoz való hozzáférést engedélyező ujjlenyomatai megfelelnek vesszővel tagolt listájának használatával:

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>Ügyféltanúsítvány-visszavonási vizsgálat konfigurálása
Az alapértelmezett beállítás nem az ügyféltanúsítvány visszavonási állapotának hitelesítésszolgáltatóját jelöli. Az ellenőrzések bekapcsolásához, ha az ügyféltanúsítványt kiállító hitelesítésszolgáltató támogatja az ilyen ellenőrzéseket, módosítsa a következő beállítást a X509RevocationMode Enumerálásban definiált értékek egyikével:

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>PFX-fájl létrehozása önaláírt titkosítási tanúsítványokhoz
Titkosítási tanúsítvány esetén hajtsa végre a következőt:

```console
pvk2pfx -pvk MyID.pvk -spc MyID.cer
```

Testreszabása

```console
MyID.pvk and MyID.cer with the filename for the encryption certificate
```

Adja meg a jelszót, majd exportálja a tanúsítványt a következő beállításokkal:

* Igen, exportálja a titkos kulcsot
* Az összes kiterjesztett tulajdonság exportálása
* A tanúsítvány felhőalapú szolgáltatásba való feltöltésekor szüksége lesz a jelszóra.

## <a name="export-encryption-certificate-from-certificate-store"></a>Titkosítási tanúsítvány exportálása a tanúsítványtárolóból
* Tanúsítvány keresése
* Kattintson a műveletek-> az összes feladat – > exportálás... lehetőségre.
* Tanúsítvány exportálása a-ba. PFX-fájl a következő beállításokkal: 
  * Igen, exportálja a titkos kulcsot
  * Az összes tanúsítvány belefoglalása a tanúsítvány elérési útjába, ha lehetséges 
* Az összes kiterjesztett tulajdonság exportálása

## <a name="upload-encryption-certificate-to-cloud-service"></a>Titkosítási tanúsítvány feltöltése a Cloud Service-be
Meglévő vagy előállított tanúsítvány feltöltése. PFX-fájl a titkosítási kulcspár:

* Adja meg a titkos kulcs adatainak védelmét biztosító jelszót

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Titkosítási tanúsítvány frissítése a szolgáltatás konfigurációs fájljában
Frissítse a szolgáltatás konfigurációs fájljának következő beállításainak ujjlenyomat-értékét a Cloud Service-be feltöltött tanúsítvány ujjlenyomatával:

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>Általános tanúsítvány-műveletek
* A TLS/SSL-tanúsítvány konfigurálása
* Ügyféltanúsítványok konfigurálása

## <a name="find-certificate"></a>Tanúsítvány keresése
Kövesse az alábbi lépéseket:

1. mmc.exe futtatása.
2. Fájl – > beépülő modul hozzáadása/eltávolítása...
3. Válassza a **tanúsítványok** lehetőséget.
4. Kattintson a **Hozzáadás** parancsra.
5. Válassza ki a tanúsítványtároló helyét.
6. Kattintson a **Finish** (Befejezés) gombra.
7. Kattintson az **OK** gombra.
8. Bontsa ki a **tanúsítványok** csomópontot.
9. Bontsa ki a tanúsítványtároló csomópontot.
10. Bontsa ki a tanúsítvány gyermek csomópontját.
11. Válasszon ki egy tanúsítványt a listában.

## <a name="export-certificate"></a>Tanúsítvány exportálása
A **Tanúsítvány exportálása varázslóban**:

1. Kattintson a **Tovább** gombra.
2. Válassza az **Igen** lehetőséget, majd **exportálja a titkos kulcsot**.
3. Kattintson a **Tovább** gombra.
4. Válassza ki a kívánt kimeneti fájlformátumot.
5. Keresse meg a kívánt beállításokat.
6. Győződjön meg róla, hogy a **jelszó**.
7. Adjon meg egy erős jelszót, és erősítse meg.
8. Kattintson a **Tovább** gombra.
9. Írja be vagy tallózással keresse meg azt a fájlnevet, amelyben tárolni kívánja a tanúsítványt (a-t használja. PFX-bővítmény).
10. Kattintson a **Tovább** gombra.
11. Kattintson a **Finish** (Befejezés) gombra.
12. Kattintson az **OK** gombra.

## <a name="import-certificate"></a>Tanúsítvány importálása
A tanúsítvány importálása varázslóban:

1. Válassza ki az áruház helyét.
   
   * Válassza az **aktuális felhasználó** lehetőséget, ha csak az aktuális felhasználó alatt futó folyamatok férnek hozzá a szolgáltatáshoz
   * Ha a számítógép más folyamatai is hozzáférnek a szolgáltatáshoz, válassza a **helyi számítógép** lehetőséget.
2. Kattintson a **Tovább** gombra.
3. Ha fájlból importál, erősítse meg a fájl elérési útját.
4. Ha a-t importálja. PFX-fájl:
   1. Adja meg a titkos kulcs védelmét biztosító jelszót
   2. Importálási beállítások kiválasztása
5. Válassza a "hely" tanúsítványokat a következő tárolóban
6. Kattintson a **Browse** (Tallózás) gombra.
7. Válassza ki a kívánt tárolót.
8. Kattintson a **Finish** (Befejezés) gombra.
   
   * Ha a megbízható legfelső szintű hitelesítésszolgáltató tárolót választotta, kattintson az **Igen** gombra.
9. Kattintson **az OK** gombra az összes párbeszédablak-ablakban.

## <a name="upload-certificate"></a>Tanúsítvány feltöltése
A [Azure Portal](https://portal.azure.com/)

1. Válassza a **Cloud Services** lehetőséget.
2. Válassza ki a Cloud Service-t.
3. A felső menüben kattintson a **tanúsítványok** elemre.
4. Az alsó sávban kattintson a **feltöltés** elemre.
5. Válassza ki a tanúsítványfájl.
6. Ha a. PFX-fájl, adja meg a titkos kulcs jelszavát.
7. Ha elkészült, másolja a tanúsítvány ujjlenyomatát a lista új bejegyzéséről.

## <a name="other-security-considerations"></a>Egyéb biztonsági megfontolások
A jelen dokumentumban ismertetett TLS-beállítások titkosítják a szolgáltatás és az ügyfelek közötti kommunikációt a HTTPS-végpont használatakor. Ez azért fontos, mert az adatbázis-hozzáféréshez szükséges hitelesítő adatok és az esetlegesen más bizalmas információk is szerepelnek a kommunikációban. Vegye figyelembe azonban, hogy a szolgáltatás belső állapotba kerül, beleértve a hitelesítő adatokat is, az adatbázisában található belső táblákban Azure SQL Database, amelyeket a Microsoft Azure-előfizetésében a metaadatok tárolásához adott meg. Ez az adatbázis a szolgáltatás konfigurációs fájljának következő beállításának részeként lett meghatározva (. CSCFG-fájl): 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

Az adatbázisban tárolt hitelesítő adatok titkosítva vannak. Az ajánlott eljárás szerint azonban győződjön meg arról, hogy a szolgáltatás központi telepítésének webes és feldolgozói szerepkörei naprakészek és biztonságosak, mivel mindkettő hozzáfér a metaadat-adatbázishoz, valamint a tárolt hitelesítő adatok titkosításához és visszafejtéséhez használt tanúsítványhoz. 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]
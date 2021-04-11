---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: faab8aa124ca2f290938cb6cff0a2f4d072caffd
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106072881"
---
## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Önaláírt főtanúsítvány létrehozása

Önaláírt főtanúsítvány létrehozásához használja a New-SelfSignedCertificate parancsmagot. További információ a paraméterekről: [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate).

1. Windows 10 vagy Windows Server 2016 operációs rendszert futtató számítógépről nyisson meg egy emelt szintű jogosultságokkal rendelkező Windows PowerShell-konzolt. Ezek a példák nem működnek a "kipróbálás" Azure Cloud Shell. Ezeket a példákat helyileg kell futtatnia.
1. A következő példa használatával hozza létre az önaláírt főtanúsítványt. A következő példa létrehoz egy "P2SRootCert" nevű önaláírt főtanúsítványt, amely automatikusan települ a "Certificates-current User\Personal\Certificates" tulajdonságban. A tanúsítványt a *certmgr. msc* megnyitásával vagy *felhasználói tanúsítványok kezelésével* tekintheti meg.

   Jelentkezzen be a `Connect-AzAccount` parancsmag használatával. Ezután futtassa a következő példát a szükséges módosításokkal.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

1. Hagyja nyitva a PowerShell-konzolt, és folytassa a következő lépésekkel az Ügyféltanúsítványok létrehozásához.

## <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Ügyféltanúsítvány létrehozása

Minden, a virtuális hálózathoz pont–hely kapcsolattal csatlakozó ügyfélszámítógépnek rendelkeznie kell telepített ügyféltanúsítvánnyal. Létrehoz egy ügyféltanúsítványt az önaláírt főtanúsítványból, majd exportálja és telepíti az ügyféltanúsítványt. Ha az ügyféltanúsítvány nincs telepítve, a hitelesítés sikertelen lesz. 

Az alábbi lépések végigvezetik az ügyféltanúsítvány önaláírt főtanúsítványból történő létrehozásán. Több ügyféltanúsítványt is létrehozhat ugyanahhoz a főtanúsítványhoz. Ha az alábbi lépésekkel állít elő ügyféltanúsítványt, az ügyféltanúsítvány automatikusan települ arra a számítógépre, amelyet a tanúsítvány létrehozásához használt. Ha egy másik ügyfélszámítógépen szeretné telepíteni az ügyféltanúsítványt, exportálhatja a tanúsítványt.

A példák a New-SelfSignedCertificate parancsmagot használják olyan ügyféltanúsítvány létrehozásához, amely egy évig lejár. További paraméter-információk, például az ügyféltanúsítvány eltérő lejárati értékének beállítása: [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate).

### <a name="example-1---powershell-console-session-still-open"></a>1. példa – a PowerShell-konzol munkamenete még nyitva van

Akkor használja ezt a példát, ha nem zárta be a PowerShell-konzolt az önaláírt főtanúsítvány létrehozása után. Ez a példa az előző szakaszból folytatódik, és a deklarált "$cert" változót használja. Ha bezárta a PowerShell-konzolt az önaláírt főtanúsítvány létrehozása után, vagy további ügyféltanúsítványt hoz létre egy új PowerShell-konzol munkamenetben, használja a [2. példában](#ex2)szereplő lépéseket.

Módosítsa és futtassa a példát az ügyféltanúsítvány létrehozásához. Ha a következő példát a módosítás nélkül futtatja, az eredmény egy "P2SChildCert" nevű ügyféltanúsítvány.  Ha a gyermek tanúsítvány nevet szeretné használni, módosítsa a CN-értéket. A példa futtatásakor ne módosítsa a TextExtension. A létrehozott ügyféltanúsítvány automatikusan települ a számítógép "tanúsítványok – aktuális User\Personal\Certificates" mappájába.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="example-2---new-powershell-console-session"></a><a name="ex2"></a>2. példa – új PowerShell-konzol munkamenete

Ha további ügyféltanúsítványt hoz létre, vagy nem ugyanazt a PowerShell-munkamenetet használja, mint amelyet az önaláírt főtanúsítvány létrehozásához használt, kövesse az alábbi lépéseket:

1. Azonosítsa a számítógépen telepített önaláírt főtanúsítványt. Ez a parancsmag a számítógépre telepített tanúsítványok listáját adja vissza.

   ```powershell
   Get-ChildItem -Path "Cert:\CurrentUser\My"
   ```

1. Keresse meg a tulajdonos nevét a visszaadott listából, majd másolja a mellette található ujjlenyomatot egy szövegfájlba. A következő példában két tanúsítvány van. A CN Name annak az önaláírt főtanúsítványnak a neve, amelyből alárendelt tanúsítványt szeretne előállítani. Ebben az esetben a "P2SRootCert".

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```

1. Deklaráljon egy változót a főtanúsítványhoz az előző lépés ujjlenyomatának használatával. Cserélje le az UJJLENYOMATot annak a főtanúsítványnak az ujjlenyomatával, amelyből alárendelt tanúsítványt kíván előállítani.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Például az előző lépésben a P2SRootCert ujjlenyomatának használatával a változó a következőképpen néz ki:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```

1. Módosítsa és futtassa a példát az ügyféltanúsítvány létrehozásához. Ha a következő példát a módosítás nélkül futtatja, az eredmény egy "P2SChildCert" nevű ügyféltanúsítvány. Ha a gyermek tanúsítvány nevet szeretné használni, módosítsa a CN-értéket. A példa futtatásakor ne módosítsa a TextExtension. A létrehozott ügyféltanúsítvány automatikusan települ a számítógép "tanúsítványok – aktuális User\Personal\Certificates" mappájába.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="export-the-root-certificate-public-key-cer"></a><a name="cer"></a>A főtanúsítvány nyilvános kulcsának (. cer) exportálása

[!INCLUDE [Export public key](vpn-gateway-certificates-export-public-key-include.md)]

### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Az önaláírt főtanúsítvány és a titkos kulcs exportálása a tároláshoz (nem kötelező)

Előfordulhat, hogy exportálni szeretné az önaláírt főtanúsítványt, és biztonságosan tárolja a biztonsági mentést. Ha szükséges, később telepítheti egy másik számítógépre, és több ügyféltanúsítványt is létrehozhat. Az önaláírt főtanúsítvány. pfx néven való exportálásához jelölje ki a főtanúsítványt, és használja az [ügyféltanúsítvány exportálása](#clientexport)című témakörben leírt lépéseket.

## <a name="export-the-client-certificate"></a><a name="clientexport"></a>Az ügyféltanúsítvány exportálása

[!INCLUDE [Export client certificate](vpn-gateway-certificates-export-client-cert-include.md)]
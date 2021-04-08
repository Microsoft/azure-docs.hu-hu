---
title: 'Azure VPN Gateway: & exportálási tanúsítványok előállítása a P2S: MakeCert'
description: Hozzon létre egy önaláírt főtanúsítványt, exportálja a nyilvános kulcsot, és hozzon létre ügyféltanúsítványt a MakeCert használatával.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 55e22ebec5853d6b4f10b53be8e24f4dbebe4e1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659777"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Tanúsítványok létrehozása és exportálása pont – hely kapcsolatokhoz a MakeCert használatával

A pont – hely kapcsolatok tanúsítványokat használnak a hitelesítéshez. Ez a cikk bemutatja, hogyan hozhat létre önaláírt főtanúsítványokat, és hogyan hozhat létre ügyféltanúsítványt az MakeCert használatával. Ha más tanúsítványokra vonatkozó utasításokat keres, lásd: [tanúsítványok – PowerShell](vpn-gateway-certificates-point-to-site.md) vagy [tanúsítványok – Linux](vpn-gateway-certificates-point-to-site-linux.md).

Habár azt javasoljuk, hogy a [Windows 10 PowerShell-lépések](vpn-gateway-certificates-point-to-site.md) használatával hozza létre a tanúsítványokat, a MakeCert utasításait opcionális módszerként adja meg. A bármelyik módszerrel létrehozott tanúsítványok a [támogatott ügyfél operációs rendszerekre](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)telepíthetők. A MakeCert azonban a következő korlátozásokkal rendelkezik:

* A MakeCert elavult. Ez azt jelenti, hogy ez az eszköz bármikor eltávolítható. Az MakeCert használatával már létrehozott tanúsítványok nem lesznek hatással, ha a MakeCert már nem érhető el. A MakeCert csak tanúsítványok létrehozásához használatos, nem pedig érvényesítő mechanizmusként.

## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Önaláírt főtanúsítvány létrehozása

A következő lépések bemutatják, hogyan hozhat létre önaláírt tanúsítványt a MakeCert használatával. Ezek a lépések nem üzemelő példány-modellre vonatkoznak. Ezek a Resource Manager és a klasszikus esetében egyaránt érvényesek.

1. Töltse le és telepítse a [MakeCert](/windows/win32/seccrypto/makecert).
2. A telepítés után általában a "C:\Program Files (x86) \Windows Kits\10\bin" elérési úton található makecert.exe segédprogramot találhatja meg \<arch> . Bár lehetséges, hogy egy másik helyre telepítették. Nyisson meg egy parancssort rendszergazdaként, és navigáljon a MakeCert segédprogram helyére. A következő példát használhatja a megfelelő helyhez való igazításhoz:

   ```cmd
   cd C:\Program Files (x86)\Windows Kits\10\bin\x64
   ```
3. Hozzon létre és telepítsen egy tanúsítványt a számítógép személyes tanúsítványtárolójában. A következő példa létrehoz egy megfelelő *. cer* fájlt, amelyet az Azure-ba tölt fel a P2S konfigurálásakor. Cserélje le a "P2SRootCert" és a "P2SRootCert. cer" nevet a tanúsítványhoz használni kívánt névre. A tanúsítvány a "tanúsítványok – aktuális User\Personal\Certificates" mappában található.

   ```cmd
   makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
   ```

## <a name="export-the-public-key-cer"></a><a name="cer"></a>A nyilvános kulcs (. cer) exportálása

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

Az exportált. cer fájlt fel kell tölteni az Azure-ba. Útmutatásért lásd: [pont – hely kapcsolat konfigurálása](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). További megbízható legfelső szintű tanúsítvány hozzáadásához tekintse meg a cikk [ezen szakaszát](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) .

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Az önaláírt tanúsítvány és a titkos kulcs exportálása a tároláshoz (nem kötelező)

Előfordulhat, hogy exportálni szeretné az önaláírt főtanúsítványt, és biztonságosan tárolja. Ha szükséges, később telepítheti egy másik számítógépre, és több ügyféltanúsítványt is létrehozhat, vagy exportálhat egy másik. cer fájlt. Az önaláírt főtanúsítvány. pfx néven való exportálásához jelölje ki a főtanúsítványt, és használja az [ügyféltanúsítvány exportálása](#clientexport)című témakörben leírt lépéseket.

## <a name="create-and-install-client-certificates"></a>Ügyféltanúsítványok létrehozása és telepítése

Az önaláírt tanúsítványt nem telepíti közvetlenül az ügyfélszámítógépen. Az önaláírt tanúsítványból kell létrehoznia egy ügyféltanúsítványt. Ezután exportálja és telepíti az ügyféltanúsítványt az ügyfélszámítógépre. A következő lépések nem üzemelő példány – modell specifikusak. Ezek a Resource Manager és a klasszikus esetében egyaránt érvényesek.

### <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Ügyféltanúsítvány létrehozása

Minden, a virtuális hálózathoz pont–hely kapcsolattal csatlakozó ügyfélszámítógépnek rendelkeznie kell telepített ügyféltanúsítvánnyal. Létrehoz egy ügyféltanúsítványt az önaláírt főtanúsítványból, majd exportálja és telepíti az ügyféltanúsítványt. Ha az ügyféltanúsítvány nincs telepítve, a hitelesítés sikertelen lesz. 

Az alábbi lépések végigvezetik az ügyféltanúsítvány önaláírt főtanúsítványból történő létrehozásán. Több ügyféltanúsítványt is létrehozhat ugyanahhoz a főtanúsítványhoz. Ha az alábbi lépésekkel állít elő ügyféltanúsítványt, az ügyféltanúsítvány automatikusan települ arra a számítógépre, amelyet a tanúsítvány létrehozásához használt. Ha egy másik ügyfélszámítógépen szeretné telepíteni az ügyféltanúsítványt, exportálhatja a tanúsítványt.
 
1. Az önaláírt tanúsítvány létrehozásához használt számítógépen nyisson meg egy parancssort rendszergazdaként.
2. Módosítsa és futtassa a mintát egy ügyféltanúsítvány létrehozásához.
   * Módosítsa a *"P2SRootCert"* elemet annak az önaláírt gyökérnek a nevére, amelyről az ügyféltanúsítványt létrehozza. Győződjön meg arról, hogy a főtanúsítvány nevét használja, azaz a "CN =" értéket, amelyet az önaláírt gyökér létrehozásakor adott meg.
   * Módosítsa a *P2SChildCert* arra a névre, amelyhez ügyféltanúsítványt szeretne készíteni.

   Ha a következő példát a módosítás nélkül futtatja, az eredmény egy P2SChildcert nevű ügyféltanúsítvány, amelyet a rendszer a főtanúsítvány P2SRootCert generált a személyes tanúsítványtárolóban.

   ```cmd
   makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
   ```

### <a name="export-a-client-certificate"></a><a name="clientexport"></a>Ügyféltanúsítvány exportálása

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install-an-exported-client-certificate"></a><a name="install"></a>Exportált ügyféltanúsítvány telepítése

Az ügyféltanúsítvány telepítéséhez tekintse meg az [ügyféltanúsítvány telepítése](point-to-site-how-to-vpn-client-install-azure-cert.md)című témakört.

## <a name="next-steps"></a>Következő lépések

Folytassa a pont – hely konfigurációval. 

* A **Resource Manager** -alapú üzemi modell lépéseivel kapcsolatban lásd: [P2S konfigurálása natív Azure tanúsítványalapú hitelesítés használatával](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* A **klasszikus** üzemi modell lépéseivel kapcsolatban lásd: [pont – hely VPN-kapcsolat konfigurálása VNet (klasszikus)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

A pont–hely hibaelhárítási információiért tekintse át az [Azure pont–hely kapcsolatok hibaelhárításával](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) foglalkozó cikket.
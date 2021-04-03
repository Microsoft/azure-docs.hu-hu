---
title: 'Számítógép összekötése virtuális hálózattal P2S használatával: tanúsítványalapú hitelesítés: Azure Portal klasszikus'
titleSuffix: Azure VPN Gateway
description: Hozzon létre egy klasszikus pont – hely VPN Gateway kapcsolatokat a Azure Portal használatával.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 42b0945de55775f55f20cefdeb547cb5d6492c06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94657074"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Pont – hely kapcsolat konfigurálása tanúsítványalapú hitelesítés használatával (klasszikus)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Ez a cikk bemutatja, hogyan hozhat létre pont – hely kapcsolattal rendelkező VNet. Ezt a VNet a klasszikus üzemi modellel hozza létre a Azure Portal használatával. A konfiguráció önaláírt vagy hitelesítésszolgáltató által kibocsátott tanúsítványokat használ a kapcsolódó ügyfelek hitelesítéséhez. Ezt a konfigurációt egy másik központi telepítési eszközzel vagy modellel is létrehozhatja az alábbi cikkekben ismertetett beállítások használatával:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

A pont – hely (P2S) VPN-átjáró használatával biztonságos kapcsolat hozható létre a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont – hely VPN-kapcsolat akkor hasznos, ha távoli helyről szeretne csatlakozni a VNet. Ha csak néhány ügyfelet szeretne egy VNet csatlakozni, a P2S VPN hasznos megoldás, ha helyek közötti VPN-t használ. A pont–hely VPN-kapcsolat létesítéséhez a kapcsolatot az ügyfélszámítógépről kell elindítani.

> [!IMPORTANT]
> A klasszikus üzemi modell kizárólag a Windows VPN-ügyfeleket támogatja, és a Secure Socket Tunneling Protocol (SSTP) SSL-alapú VPN-protokollt használja. A nem Windows rendszerű VPN-ügyfelek támogatásához létre kell hoznia a VNet a Resource Manager-alapú üzemi modellel. A Resource Manager-alapú üzemi modell az SSTP mellett az IKEv2 VPN protokollt is támogatja. További információk: [A pont–hely kapcsolatok](point-to-site-about.md).
>
>

![Pont–hely diagram](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="settings-and-requirements"></a>Beállítások és követelmények

### <a name="requirements"></a>Követelmények

A pont – hely tanúsítvány-hitelesítési kapcsolatokhoz a következő elemek szükségesek. Ebben a cikkben a létrehozásához szükséges lépéseket talál.

* Egy dinamikus VPN-átjáró.
* A nyilvános kulcs (.cer fájl) egy főtanúsítványhoz, amely az Azure-ba van feltöltve. Ez a kulcs megbízható tanúsítványnak minősül, és a hitelesítéshez használatos.
* A főtanúsítványból létrejön egy ügyféltanúsítvány, majd települ az egyes csatlakozó ügyfélszámítógépekre. A rendszer ezt a tanúsítványt használja ügyfélhitelesítéshez.
* Minden csatlakozó ügyfélszámítógépen létre kell hozni és telepíteni kell egy VPN-ügyfélkonfigurációs csomagot. Az ügyfél-konfigurációs csomag konfigurálja az operációs rendszeren már meglévő natív VPN-ügyfelet a VNet való csatlakozáshoz szükséges adatokkal.

A pont – hely kapcsolatok nem igényelnek VPN-eszközt vagy helyszíni nyilvános IP-címet. A VPN-kapcsolat kiépítése SSTP (Secure Socket Tunneling Protocol) használatával történik. A kiszolgálói oldalon az SSTP 1.0, 1.1 és 1.2 verziója támogatott. Az ügyfél dönti el, hogy melyik verziót használja. Windows 8.1 és újabb kiadások esetén az SSTP alapértelmezés szerint az 1.2 verziót használja.

További információ: tudnivalók a [pont – hely kapcsolatokról](point-to-site-about.md) és a [GYIK](#faq)-ról.

### <a name="example-settings"></a>Példabeállítások

A következő értékek használatával hozzon létre egy tesztkörnyezetben, vagy tekintse át ezeket az értékeket a cikkben szereplő példák jobb megismeréséhez:

* **Erőforráscsoport:** TestRG
* **Virtuális hálózat neve:** VNet1
* **Címterület:** 192.168.0.0/16 <br>Ebben a példában csak egy címteret használunk. Azonban a virtuális hálózatához több címteret is használhat.
* **Alhálózat neve:** FrontEnd
* **Alhálózati címtartomány:** 192.168.1.0/24
* **Átjáróalhálózat:** 10.11.255.0/27
* **Régió:** (USA) USA keleti régiója
* **Ügyfél címterület:** 172.16.201.0/24 <br> Azok a VPN-ügyfelek, amelyek ezzel a pont – hely kapcsolattal csatlakoznak a VNet, a megadott készletből kapnak IP-címet.
* **Kapcsolat típusa**: válassza **a pont – hely** lehetőséget.
* **GatewaySubnet címtartomány (CIDR-blokk):** 192.168.200.0/24

Mielőtt elkezdené, ellenőrizze, hogy rendelkezik-e Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Virtuális hálózat létrehozása

Ha már rendelkezik egy virtuális hálózattal, győződjön meg arról, hogy a beállításai kompatibilisek a VPN-átjáró kialakításával. Különösen ügyeljen az esetleges olyan alhálózatokra, amelyek átfedésbe kerülhetnek más hálózatokkal.

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="create-a-vpn-gateway"></a><a name="gateway"></a>VPN-átjáró létrehozása

1. Navigáljon a létrehozott VNet.
1. A VNet lap beállítások területén válassza az **átjáró** elemet. Az **átjáró** oldalon megtekintheti a virtuális hálózat átjáróját. Ez a virtuális hálózat még nem rendelkezik átjáróval. Kattintson a megjegyzésre, amelyre **kattintva hozzáadhat egy kapcsolatokat és egy átjárót**.
1. A **VPN-kapcsolat és-átjáró konfigurálása** lapon válassza ki a következő beállításokat:

   * Kapcsolat típusa: pont–hely
   * Ügyfél címterület: adja meg azt az IP-címtartományt, amelyből a VPN-ügyfelek a csatlakozáskor IP-címet kapnak. Olyan magánhálózati IP-címtartományt használjon, amely nincs átfedésben azzal a helyszíni hellyel, amelyről csatlakozik, vagy azzal a VNet, amelyhez csatlakozik.
1. Hagyja üresen a **ne konfigurálja az átjárót** jelölőnégyzetet, ha nincs kiválasztva. Létre fogunk hozni egy átjárót.
1. A lap alján válassza a **Tovább: átjáró >** elemet.
1. Az **átjáró** lapon válassza ki a következő értékeket:

   * **Méret:** A méret a virtuális hálózati átjáró átjárójának SKU-jának értéke. A Azure Portal az alapértelmezett SKU **alapértelmezett érték**. További információ az átjárók SKU-ról: [Tudnivalók a VPN Gateway beállításairól](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   * **Útválasztási típus:** Pont – hely konfiguráció esetén a **dinamikus** lehetőséget kell választania. A statikus útválasztás nem fog működni.
   * **Átjáró-alhálózat:** Ez a mező már be van töltve. A név nem módosítható. Ha a nevet a PowerShell vagy bármely más eszköz használatával próbálja módosítani, az átjáró nem fog megfelelően működni.
   * **Címtartomány (CIDR-blokk):** Habár egy átjáró-alhálózatot kisebb mint/29-ként lehet létrehozni, javasoljuk, hogy hozzon létre egy nagyobb alhálózatot, amely több címet is tartalmaz, ha legalább/28 vagy/27 elemet választ. Ez lehetővé teszi, hogy a jövőben elegendő címet biztosítson a lehetséges további konfigurációkhoz. Amikor átjáró-alhálózatokkal dolgozik, kerülje a hálózati biztonsági csoportok (NSG) társítását az átjáró-alhálózathoz. Ha hálózati biztonsági csoportot társít ehhez az alhálózathoz, előfordulhat, hogy a VPN-átjáró nem a várt módon fog működni.
1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a beállítások érvényesítéséhez.
1. Az ellenőrzés után válassza a **Létrehozás** lehetőséget. A VPN-átjáró akár 45 percet is igénybe vehet, attól függően, hogy melyik átjárói SKU-t választotta.

## <a name="create-certificates"></a><a name="generatecerts"></a>Tanúsítványok létrehozása

Az Azure tanúsítványokat használ a VPN-ügyfelek hitelesítéséhez pont – hely VPN-kapcsolatokhoz. A főtanúsítvány nyilvánoskulcs-adatait feltölti az Azure-ba. A nyilvános kulcsot Ezután *megbízhatónak* tekinti a rendszer. Az ügyféltanúsítványt a megbízható főtanúsítványból kell létrehozni, majd telepíteni kell az összes ügyfélszámítógépen a Certificates-Current User\Personal\Certificates-tanúsítványtárolóban. A tanúsítvány az ügyfél hitelesítésére szolgál a VNet való csatlakozáskor. 

Ha önaláírt tanúsítványokat használ, azokat meghatározott paraméterek használatával kell létrehoznia. Létrehozhat egy önaláírt tanúsítványt a [PowerShell és a Windows 10](vpn-gateway-certificates-point-to-site.md), vagy a [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)utasítások használatával. Fontos, hogy kövesse ezeket az utasításokat, amikor önaláírt főtanúsítványokat használ, és az önaláírt főtanúsítványból állít elő ügyféltanúsítványt. Ellenkező esetben a létrehozott tanúsítványok nem lesznek kompatibilisek a P2S-kapcsolatokkal, és hibaüzenetet kap.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>A főtanúsítványhoz tartozó nyilvános kulcs (. cer) megvásárlása

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Ügyféltanúsítvány létrehozása

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>A főtanúsítvány .cer fájljának feltöltése

Az átjáró létrehozása után töltse fel a megbízható főtanúsítványhoz tartozó. cer fájlt (amely a nyilvános kulcs adatait tartalmazza) az Azure-kiszolgálóra. Ne töltse fel a főtanúsítvány titkos kulcsát. A tanúsítvány feltöltése után az Azure arra használja, hogy hitelesítse a megbízható főtanúsítványból generált ügyféltanúsítványt telepítő ügyfeleket. Később további megbízható főtanúsítvány-fájlokat (akár 20) is feltölthet, ha szükséges.

1. Navigáljon a létrehozott virtuális hálózathoz.
1. A **Beállítások** területen válassza a **pont – hely kapcsolat** lehetőséget.
1. Válassza a **tanúsítvány kezelése** lehetőséget.
1. Válassza a **Feltöltés** lehetőséget.
1. A **tanúsítvány feltöltése** panelen válassza a mappa ikont, és navigáljon a feltölteni kívánt tanúsítványhoz.
1. Válassza a **Feltöltés** lehetőséget.
1. A tanúsítvány sikeres feltöltése után megtekintheti azt a tanúsítvány kezelése oldalon. Előfordulhat, hogy a **frissítés** elemre kell kattintania az imént feltöltött tanúsítvány megtekintéséhez.

## <a name="configure-the-client"></a>Az ügyfél konfigurálása

Ha pont – hely típusú VPN-kapcsolattal szeretne kapcsolódni egy VNet, minden ügyfélnek telepítenie kell egy csomagot a natív Windows VPN-ügyfél konfigurálásához. A konfigurációs csomag konfigurálja a natív Windows VPN-ügyfelet a virtuális hálózathoz való csatlakozáshoz szükséges beállításokkal.

Használhatja a VPN-ügyfél azonos konfigurációs csomagját minden ügyfélszámítógépen, feltéve, hogy a verzió megfelel az ügyfél architektúrájának. A támogatott ügyféloldali operációs rendszerek listáját lásd: [Tudnivalók a pont – hely kapcsolatokról](point-to-site-about.md) és a [GYIK](#faq)-ról.

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>VPN-ügyfél konfigurációs csomagjának létrehozása és telepítése

1. Navigáljon a VNet **pont – hely kapcsolatok** beállításaihoz.
1. A lap tetején válassza ki azt a letöltési csomagot, amely megfelel az ügyfél operációs rendszerének, ahol telepítve lesz:

   * 64 bites ügyfelek esetén válassza a **VPN-ügyfél (64 bites)** lehetőséget.
   * 32 bites ügyfelek esetén válassza a **VPN-ügyfél (32 bites)** lehetőséget.

1. Az Azure létrehoz egy csomagot az ügyfél által igényelt beállításokkal. Minden alkalommal, amikor módosítja a VNet vagy az átjárót, le kell töltenie egy új ügyfél-konfigurációs csomagot, és telepítenie kell őket az ügyfélszámítógépekre.
1. A csomag generálása után válassza a **Letöltés** lehetőséget.
1. Telepítse az ügyfél-konfigurációs csomagot az ügyfélszámítógépen. Ha a telepítésekor egy SmartScreen felugró ablak jelenik meg, amely azt jelzi, hogy a Windows védett a SZÁMÍTÓGÉPén, válassza a **További információ**, majd a **Futtatás egyébként** lehetőséget. A csomagot mentheti is, így más ügyfélszámítógépekre is telepítheti.

### <a name="install-a-client-certificate"></a>Ügyféltanúsítvány telepítése

Ebben a gyakorlatban az ügyféltanúsítvány létrehozásakor a rendszer automatikusan telepíti a számítógépre. Ahhoz, hogy P2S-kapcsolatokat hozzon létre egy másik ügyfélszámítógépről, mint amelyet az Ügyféltanúsítványok létrehozásához használ, telepítenie kell a generált ügyféltanúsítványt a számítógépen.

Ügyféltanúsítvány telepítésekor szükség lesz az ügyféltanúsítvány exportálásakor létrehozott jelszóra. A tanúsítványt általában úgy is telepítheti, ha duplán kattint rá. További információkért lásd az [exportált ügyféltanúsítványok telepítését](vpn-gateway-certificates-point-to-site.md#install) ismertető cikket.

## <a name="connect-to-your-vnet"></a>Csatlakozás a virtuális hálózathoz

>[!NOTE]
>Rendszergazdai jogosultsággal kell rendelkeznie azon az ügyfélszámítógépen, ahonnan csatlakozik.
>

1. Az ügyfélszámítógépen nyissa meg a VPN-beállításokat.
1. Válassza ki a létrehozott VPN-t. Ha a példa beállításait használta, a rendszer a **TestRG VNet1** címkével látja el a kapcsolatokat.
1. Válassza a **Kapcsolódás** lehetőséget.
1. A Windows Azure Virtual Network mezőben válassza a **kapcsolat** lehetőséget. Ha megjelenik egy előugró üzenet a tanúsítványról, kattintson a **tovább** gombra emelt szintű jogosultságok használata és **Igen** lehetőségre a konfigurációs módosítások elfogadásához.
1. Ha a kapcsolat sikeres, egy **csatlakoztatott** értesítés jelenik meg.

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>A VPN-kapcsolat ellenőrzése

1. Ellenőrizze, hogy a VPN-kapcsolat aktív-e. Nyisson meg egy rendszergazda jogú parancssort az ügyfélszámítógépen, és futtassa az **ipconfig/all** parancsot.
1. Tekintse meg az eredményeket. A kapott IP-cím a virtuális hálózat létrehozásakor megadott pont–hely kapcsolati címtartományba tartozó valamelyik cím. Az eredmények a következő példához hasonlóak:

   ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a>Csatlakozás virtuális géphez

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="to-add-or-remove-trusted-root-certificates"></a>Megbízható főtanúsítványok hozzáadása vagy eltávolítása

A megbízható főtanúsítványokat felveheti vagy el is távolíthatja az Azure-ban. Ha eltávolít egy főtanúsítványt, akkor az adott gyökérből létrehozott tanúsítvánnyal rendelkező ügyfelek már nem hitelesíthetők és nem csatlakozhatnak. Ahhoz, hogy az ügyfelek hitelesíteni és újra csatlakozzanak, telepítenie kell egy új ügyféltanúsítványt, amely az Azure által megbízhatónak tartott főtanúsítványból lett létrehozva.

### <a name="add-a-trusted-root-certificate"></a>Megbízható főtanúsítvány hozzáadása

Akár 20 megbízható főtanúsítvány. cer fájlt is hozzáadhat az Azure-hoz az első megbízható legfelső szintű tanúsítvány hozzáadásához használt folyamat használatával.

### <a name="remove-a-trusted-root-certificate"></a>Megbízható főtanúsítvány eltávolítása

1. A VNet lapjának **pont – hely kapcsolat** szakaszában válassza a **tanúsítvány kezelése** lehetőséget.
1. Válassza ki az eltávolítani kívánt tanúsítvány melletti három pontot, majd válassza a **Törlés** lehetőséget.

## <a name="to-revoke-a-client-certificate"></a>Ügyféltanúsítvány visszavonása

Ha szükséges, visszavonhatja az ügyféltanúsítványt. A visszavont tanúsítványok listájával az egyes ügyféltanúsítványok alapján, szelektíven tagadhatja meg a pont–hely kapcsolódás lehetőségét. Ez a módszer különbözik a megbízható főtanúsítványok eltávolításának. Ha töröl egy .cer formátumú megbízható főtanúsítványt az Azure-ból, azzal megvonja a hozzáférést minden olyan ügyféltanúsítványtól, amelyet a visszavont főtanúsítvánnyal hoztak létre/írtak alá. A főtanúsítvány helyett az ügyféltanúsítvány visszavonásával a főtanúsítványból létrehozott többi tanúsítvány továbbra is használható a pont–hely kapcsolat hitelesítésére.

A szokásos gyakorlat az, hogy a főtanúsítvánnyal kezelik a hozzáférést a munkacsoport vagy a szervezet szintjén, az egyes felhasználókra vonatkozó részletesebb szabályozást pedig visszavont ügyféltanúsítványokkal oldják meg.

Az ügyféltanúsítványok visszavonásához vegye fel az ujjlenyomatot a visszavont tanúsítványok listájára.

1. Kérje le az ügyféltanúsítvány ujjlenyomatát. További információ [: How to: lekérdezheti a tanúsítvány ujjlenyomatát](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).
1. Másolja az adatokat egy szövegszerkesztőbe, és távolítsa el a szóközöket úgy, hogy az folytonos sztring legyen.
1. Navigáljon a **pont – hely VPN-kapcsolathoz**, majd válassza a **tanúsítvány kezelése** lehetőséget.
1. Válassza a **visszavont tanúsítványok listája** elemet a **visszavont tanúsítványok listája** lap megnyitásához.
1. Az **ujjlenyomatban** illessze be a tanúsítvány ujjlenyomatát egyetlen folytonos szövegként, szóközök nélkül.
1. Válassza a **+ Hozzáadás a listához** lehetőséget, hogy hozzáadja az ujjlenyomatot a visszavont tanúsítványok listájához (CRL).

A frissítés befejezését követően a tanúsítvány már nem használható csatlakozáshoz. Azok az ügyfelek, akik ezzel a tanúsítvánnyal próbálnak csatlakozni, egy üzenetet kapnak, amely azt jelzi, hogy a tanúsítvány már nem érvényes.

## <a name="faq"></a><a name="faq"></a>GYIK

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Következő lépések

* A kapcsolódás befejezése után virtuális gépeket adhat hozzá a virtuális hálózatokhoz. További információkért lásd: [Virtuális gépek](../index.yml).

* A hálózatkezelési és a Linux-alapú virtuális gépekkel kapcsolatos további információkért lásd: az [Azure és a Linux](../virtual-machines/network-overview.md)rendszerű virtuálisgép-hálózatok áttekintése.

* A pont–hely hibaelhárítási információiért tekintse át az [Azure pont–hely kapcsolatok hibaelhárításával](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) foglalkozó cikket.
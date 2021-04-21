---
title: Pont–hely (S2S) VPN konfigurálása az Azure Files | Microsoft Docs
description: A pont–hely (S2S) VPN konfigurálása az Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1a08ca4142876a5a92adbe8b1c3fce9ec7953019
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778012"
---
# <a name="configure-a-site-to-site-vpn-for-use-with-azure-files"></a>Konfigurál egy hely–hely VPN-t a Azure Files
Az Azure-fájlmegosztásokat SMB-kapcsolaton keresztül csatlakoztathatja a helyszíni hálózatról egy S2S VPN-kapcsolat segítségével a 445-ös port megnyitása nélkül. A vpn-t az [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)használatával állíthatja be, amely egy VPN-szolgáltatásokat kínáló Azure-erőforrás, és egy erőforráscsoportban van üzembe állítva a tárfiókokkal vagy más Azure-erőforrásokkal együtt.

![Egy Azure-fájlmegosztást egy helyszíni helyhez egy virtuális hálózattal összekötő Azure VPN-átjáró topológiáját bemutató topológiadiagram S2S VPN](media/storage-files-configure-s2s-vpn/s2s-topology.png)

Határozottan javasoljuk, hogy olvassa el [Azure Files áttekintését,](storage-files-networking-overview.md) mielőtt tovább folytatná a cikkel, hogy átfogó képet kapjon az egyes Azure Files.

A cikk részletesen beállítja a pont–hely VPN-t az Azure-fájlmegosztások közvetlenül a helyszínen való csatlakoztatására. Ha a virtuális hálózat szinkronizálási forgalmát Azure File Sync egy hely–hely VPN-en keresztül, tekintse meg a [proxy- és Azure File Sync-beállítások konfigurálását.](../file-sync/file-sync-firewall-and-proxy.md)

## <a name="prerequisites"></a>Előfeltételek
- Egy Azure-fájlmegosztás, amely a helyszínen csatlakoztatható. Az Azure-fájlmegosztások a tárfiókokba vannak telepítve, amelyek olyan megosztott tárolókészletet képviselő felügyeleti szerkezetek, amelyekben több fájlmegosztást, valamint egyéb tárolási erőforrásokat, például blobtárolókat vagy üzenetsorokat helyezhet üzembe. További információ az Azure-fájlmegosztások és -tárfiókok üzembe helyezéséről: [Azure-fájlmegosztás létrehozása.](storage-how-to-create-file-share.md)

- A helyszíni csatlakoztatáshoz csatlakoztatni kívánt Azure-fájlmegosztást tartalmazó tárfiók privát végpontja. További információ a privát végpontok létrehozásáról: [Configuring Azure Files network endpoints](storage-files-networking-endpoints.md?tabs=azure-portal)( Hálózati végpontok konfigurálása). 

- A helyszíni adatközpontban található olyan hálózati berendezés vagy kiszolgáló, amely kompatibilis az Azure VPN Gateway. Azure Files a kiválasztott helyszíni hálózati berendezéstől független, de az Azure VPN Gateway a tesztelt eszközök [listáját tartja fenn.](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) A különböző hálózati berendezések különböző funkciókat, teljesítményjellemzőt és felügyeleti funkciókat kínálnak, ezért a hálózati berendezések kiválasztásakor vegye figyelembe ezeket.

    Ha nem rendelkezik meglévő hálózati készülékkel, a Windows Server beépített kiszolgálói szerepkört, útválasztást és távelérést (RRAS) tartalmaz, amely helyszíni hálózati berendezésként használható. Az útválasztás és távelérés Windows Serveren való konfigurálásról további információért lásd: [RAS-átjáró.](/windows-server/remote/remote-access/ras-gateway/ras-gateway)

## <a name="add-storage-account-to-vnet"></a>Tárfiók hozzáadása virtuális hálózathoz
A Azure Portal keresse meg a helyszíni csatlakoztatáshoz csatlakoztatni kívánt Azure-fájlmegosztást tartalmazó tárfiókot. A tárfiók tartalomjegyzékében válassza a **Tűzfalak és virtuális hálózatok bejegyzést.** Hacsak nem adott hozzá virtuális hálózatot a tárfiókhoz annak létrehozásakor,  az eredményül kapott panelen a Hozzáférés engedélyezése választógombnak a Minden hálózat elemnek **kell** kijelölve lennie.

Ha hozzá szeretné adni a tárfiókot a kívánt virtuális hálózathoz, válassza a **Kijelölt hálózatok lehetőséget.** A Virtuális **hálózatok állapota** alatt kattintson a **+ Meglévő** virtuális hálózat hozzáadása vagy **a +Új** virtuális hálózat hozzáadása elemre a kívánt állapottól függően. Egy új virtuális hálózat létrehozása egy új Azure-erőforrás létrehozását eredményezi. Az új vagy meglévő VNet-erőforrásnak nem kell ugyanabban az erőforráscsoportban vagy előfizetésben lennie, mint a tárfióknak, azonban ugyanabban a régióban kell lennie, mint a tárfióknak, valamint annak az erőforráscsoportnak és előfizetésnek, amelybe a virtuális hálózatát telepíti, meg kell egyeznie a virtuális hálózat üzembe helyezésével VPN Gateway. 

![Képernyőkép a Azure Portal, amely lehetőséget biztosít egy meglévő vagy új virtuális hálózat hozzáadására a tárfiókhoz](media/storage-files-configure-s2s-vpn/add-vnet-1.png)

Ha meglévő virtuális hálózatot ad hozzá, a rendszer megkéri, hogy válassza ki annak a virtuális hálózatnak egy vagy több alhálózatát, amelyhez hozzá kell adni a tárfiókot. Ha új virtuális hálózatot választ, a virtuális hálózat létrehozásának részeként létre fog hozni egy alhálózatot, és később további szolgáltatásokat adhat hozzá a virtuális hálózat eredményül kapott Azure-erőforrásán keresztül.

Ha még nem adott hozzá tárfiókot az előfizetéséhez, a Microsoft.Storage szolgáltatásvégpontot hozzá kell adni a virtuális hálózathoz. Ez némi időt vehet igénybe, és amíg a művelet be nem fejeződik, nem fog tudni hozzáférni a tárfiókon belüli Azure-fájlmegosztáshoz, beleértve a VPN-kapcsolatot is. 

## <a name="deploy-an-azure-vpn-gateway"></a>Azure-beli virtuális VPN Gateway
A virtuális gép tartalomjegyzékében válassza az Új erőforrás Azure Portal, és keressen **rá a** Virtuális hálózati *átjáró kifejezésre.* A virtuális hálózati átjárónak ugyanabban az előfizetésben, Azure-régióban és erőforráscsoportban kell lennie, mint az előző lépésben üzembe helyezett virtuális hálózatnak (vegye figyelembe, hogy a virtuális hálózat kiválasztásakor a rendszer automatikusan kiválasztja az erőforráscsoportot). 

Azure-beli virtuális gép üzembe helyezése VPN Gateway a következő mezőket:

- **Név:** A fürt Azure-erőforrásának VPN Gateway. Ez a név bármilyen olyan név lehet, amely hasznos lehet a felügyelethez.
- **Régió:** Az a régió, amelyben a VPN Gateway üzembe lesz stb.
- **Átjáró típusa:** A hely–hely TÍPUSÚ VPN üzembe helyezéséhez a VPN lehetőséget kell **választania.**
- **VPN típusa:** A *VPN-eszköztől*  függően választhat útvonalalapú * vagy szabályzatalapú lehetőséget. Az útvonalalapú VPN-ek támogatják az IKEv2-t, míg a szabályzatalapú VPN-ek csak az IKEv1-et támogatják. A VPN-átjárók két típusának további információért lásd: Tudnivalók a szabályzatalapú és az útvonalalapú [VPN-átjárókról](../../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md#about)
- **Termékváltozat:** A termékváltozat szabályozza az engedélyezett hely–hely alagutak számát és a VPN kívánt teljesítményét. A megfelelő termékváltozat kiválasztásához tekintse meg az átjáró [termékváltozatának listáját.](../../vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) A termékváltozat VPN Gateway később módosítható, ha szükséges.
- **Virtuális hálózat:** Az előző lépésben létrehozott virtuális hálózat.
- **Nyilvános IP-cím:** A VPN Gateway ip-címe, amely elérhető lesz az interneten. Valószínűleg létre kell hoznia egy új IP-címet, de szükség esetén használhat meglévő nem használt IP-címet is. Ha az Új létrehozása lehetőséget **választja,** az Azure-erőforrás ugyanabban az erőforráscsoportban jön létre, mint az VPN Gateway, a Nyilvános  **IP-cím** neve pedig az újonnan létrehozott IP-cím neve lesz. Ha a Meglévő **használata lehetőséget választja,** ki kell választania a meglévő nem használt IP-címet.
- **Aktív-aktív mód engedélyezése:** Csak akkor válassza az **Engedélyezve** lehetőséget, ha aktív-aktív átjárókonfigurációt hoz létre, egyéb esetben hagyja bejelölve **a Letiltva** beállítást. További információ az aktív-aktív módról: Magas rendelkezésre álló létesítmények közötti és virtuális hálózatok közötti [kapcsolat.](../../vpn-gateway/vpn-gateway-highlyavailable.md)
- **BGP ASN** konfigurálása: Csak akkor válassza az **Engedélyezve lehetőséget,** ha a konfiguráció kifejezetten ezt a beállítást igényli. További információ erről a beállításról: A BGP és az [Azure VPN Gateway.](../../vpn-gateway/vpn-gateway-bgp-overview.md)

Válassza **az Áttekintés + létrehozás** lehetőséget a VPN Gateway. Egy VPN Gateway létrehozása és üzembe helyezése akár 45 percet is igénybe vehet.

### <a name="create-a-local-network-gateway-for-your-on-premises-gateway"></a>Helyi hálózati átjáró létrehozása a helyszíni átjáróhoz 
A helyi hálózati átjárók olyan Azure-erőforrások, amelyek a helyszíni hálózati berendezést képviselik. A tartalomjegyzékben válassza az Új erőforrás Azure Portal, és keressen **rá a** helyi hálózati *átjáró kifejezésre.* A helyi hálózati átjáró egy Olyan Azure-erőforrás, amely a tárfiókkal, a virtuális hálózattal és az VPN Gateway-sel együtt lesz üzembe helyezni, de nem kell ugyanabban az erőforráscsoportban vagy előfizetésben lennie, mint a tárfiók. 

A helyi hálózati átjáró erőforrásának üzembe helyezéséhez a következő mezőket kell feltöltenie:

- **Név:** A helyi hálózati átjáró Azure-erőforrásának neve. Ez a név lehet bármilyen név, amely hasznos lehet a felügyelethez.
- **IP-cím:** A helyi átjáró helyszíni nyilvános IP-címe.
- **Címtér:** A helyi hálózati átjáró által képviselt hálózat címtartományai. Több címtartományt is hozzáadhat, de ügyeljen arra, hogy az itt megadott tartományok ne legyenek átfedésben más olyan hálózatok tartományaival, amelyekhez csatlakozni szeretne. 
- **BGP-beállítások konfigurálása:** Csak akkor konfigurálja a BGP-beállításokat, ha a konfiguráció ezt a beállítást igényli. További információ erről a beállításról: [Tudnivalók a BGP-ről az Azure VPN Gateway.](../../vpn-gateway/vpn-gateway-bgp-overview.md)
- **Előfizetés:** A kívánt előfizetés. Ennek nem kell megegyezni a VPN Gateway vagy a tárfiókhoz használt előfizetéssel.
- **Erőforráscsoport:** A kívánt erőforráscsoport. Ennek nem kell megegyezni a tárfiókhoz vagy a VPN Gateway használt erőforráscsoporttal.
- **Hely:** Az az Azure-régió, ahol a helyi hálózati átjáró erőforrását létre kell hoznunk. Ennek meg kell egyeznie a tárfiókhoz VPN Gateway régióval.

Válassza **a Létrehozás** lehetőséget a helyi hálózati átjáró erőforrásának létrehozásához.  

## <a name="configure-on-premises-network-appliance"></a>Helyszíni hálózati berendezés konfigurálása
A helyszíni hálózati berendezés konfigurálásának konkrét lépései a szervezet által kiválasztott hálózati berendezéstől függnek. A szervezet által választott eszköztől [](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) függően előfordulhat, hogy a tesztelt eszközök listájában szerepel egy hivatkozás, amely az eszköz gyártójának az Azure-beli virtuális VPN Gateway.

## <a name="create-the-site-to-site-connection"></a>A hely–hely kapcsolat létrehozása
Az átjáró üzembe helyezésének befejezéséhez S2S VPN kapcsolatot kell létrehoznia a helyszíni hálózati berendezés (amelyet a helyi hálózati átjáró erőforrása képvisel) és a VPN Gateway. Ehhez lépjen a VPN Gateway létrehozott alkalmazáshoz. Az alkalmazás tartalomjegyzékében válassza a Kapcsolatok **VPN Gateway,** majd kattintson a **Hozzáadás gombra.** Az eredményül kapott **Kapcsolat hozzáadása panelen** a következő mezőkre van szükség:

- **Név:** A kapcsolat neve. Egy VPN Gateway több kapcsolatot is képes létesíteni, ezért válasszon olyan nevet a felügyelet számára, amely megkülönbözteti ezt az adott kapcsolatot.
- **Kapcsolat típusa:** Mivel ez egy S2S-kapcsolat, válassza a **Hely–hely (IPSec)** lehetőséget a legördülő listából.
- **Virtuális hálózati átjáró:** Ez a mező automatikusan ki van jelölve a VPN Gateway amikor kapcsolatot hoz létre, és nem módosítható.
- **Helyi hálózati átjáró:** Ez az a helyi hálózati átjáró, amely a VPN Gateway. Az eredményül kapott kijelölési panelen a fent létrehozott helyi hálózati átjáró nevének kell lennie.
- **Megosztott kulcs (PSK):** Betűk és számok egyveve, amely a kapcsolat titkosításának létrehozására használatos. Ugyanazt a megosztott kulcsot kell használni a virtuális hálózatban és a helyi hálózati átjárókban is. Ha az átjáróeszköz nem biztosít egyet, itt is felhasználhatja, és meg is használhatja az eszköznek.

A kapcsolat létrehozásához válassza az **OK** lehetőséget. A Kapcsolat lapon ellenőrizheti, hogy a kapcsolat sikeresen **megtörtént-e.**

## <a name="mount-azure-file-share"></a>Azure-fájlmegosztás csatlakoztatása 
A konfiguráció utolsó lépése annak S2S VPN, hogy működik-e a Azure Files. Ezt az Azure-fájlmegosztásnak a kívánt operációs rendszerrel való helyszíni csatlakoztatásával használhatja. Az operációs rendszer által való csatlakoztatásra vonatkozó utasításokat itt láthatja:

- [Windows](storage-how-to-use-files-windows.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Linux](storage-how-to-use-files-linux.md)

## <a name="see-also"></a>Lásd még
- [Azure Files hálózatok áttekintése](storage-files-networking-overview.md)
- [Pont–hely (P2S) VPN konfigurálása Windows rendszeren az Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Pont–hely (P2S) VPN konfigurálása Linux rendszeren az Azure Files](storage-files-configure-p2s-vpn-linux.md)
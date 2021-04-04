---
title: Helyek közötti (S2S) VPN konfigurálása Azure Fileshoz való használatra | Microsoft Docs
description: Helyek közötti (S2S) VPN konfigurálása Azure Fileshoz való használatra
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0fa3fb8040fd79d68f9260ab520d3b6823ab363d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629291"
---
# <a name="configure-a-site-to-site-vpn-for-use-with-azure-files"></a>Helyek közötti VPN konfigurálása Azure Fileshoz való használatra
A helyek közötti (S2S) VPN-kapcsolat használatával csatlakoztathatja az Azure-fájlmegosztást az SMB-hez a helyszíni hálózatról a 445-es port megnyitása nélkül. Létrehozhat egy helyek közötti VPN-t az [azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)használatával, amely egy VPN-szolgáltatásokat kínáló Azure-erőforrás, amelyet a Storage-fiókok vagy más Azure-erőforrások mellett egy erőforráscsoporthoz helyeznek üzembe.

![Egy topológiai diagram, amely egy Azure-fájlmegosztás és egy helyszíni hely között egy S2S VPN használatával összekapcsoló Azure VPN Gateway topológiáját mutatja be](media/storage-files-configure-s2s-vpn/s2s-topology.png)

Javasoljuk, hogy olvassa el [Azure Files hálózatkezelési áttekintést](storage-files-networking-overview.md) , mielőtt folytatná ezt a cikket a Azure Files elérhető hálózati beállítások teljes körű megvitatására.

A cikk részletesen ismerteti a helyek közötti VPN konfigurálásának lépéseit az Azure-fájlmegosztás közvetlen helyszíni csatlakoztatásához. Ha helyek közötti VPN-kapcsolaton keresztül szeretné átirányítani a Azure File Sync szinkronizálási forgalmát, tekintse meg a [Azure file Sync proxy-és tűzfalbeállítások konfigurálása](storage-sync-files-firewall-and-proxy.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek
- Egy Azure-fájlmegosztás, amelyet a helyszínen kíván csatlakoztatni. Az Azure-fájlmegosztás üzembe helyezése a Storage-fiókokban történik, amelyek olyan felügyeleti szerkezetek, amelyek olyan megosztott tárolót képviselnek, amelyben több fájlmegosztás, valamint más tárolási erőforrások, például blob-tárolók vagy várólisták helyezhetők üzembe. Az Azure-fájlmegosztás és a Storage-fiókok Azure- [fájlmegosztás létrehozása](storage-how-to-create-file-share.md)című részében olvashat bővebben.

- Az Azure-fájlmegosztás a helyszínen csatlakoztatni kívánt részét tartalmazó Storage-fiókhoz tartozó magánhálózati végpont. További információ a privát végpontok létrehozásáról: [Azure Files hálózati végpontok konfigurálása](storage-files-networking-endpoints.md?tabs=azure-portal). 

- Olyan hálózati berendezés vagy kiszolgáló a helyszíni adatközpontban, amely kompatibilis az Azure VPN Gatewayával. Azure Files a kiválasztott helyszíni hálózati berendezés agnosztikusja, de az Azure VPN Gateway megtartja a [tesztelt eszközök listáját](../../vpn-gateway/vpn-gateway-about-vpn-devices.md). A különböző hálózati készülékek különböző funkciókat, teljesítménnyel kapcsolatos tulajdonságokat és felügyeleti funkciókat kínálnak, ezért ezeket a hálózati berendezések kiválasztásakor érdemes megfontolni.

    Ha nem rendelkezik meglévő hálózati berendezéssel, a Windows Server beépített kiszolgálói szerepkört, útválasztást és távelérést (RRAS) tartalmaz, amely a helyszíni hálózati berendezésként is használható. További információ az Útválasztás és távelérés konfigurálásáról a Windows Serveren: [RAS-átjáró](/windows-server/remote/remote-access/ras-gateway/ras-gateway).

## <a name="add-storage-account-to-vnet"></a>Storage-fiók hozzáadása a VNet-hez
A Azure Portal Navigáljon arra a Storage-fiókra, amely tartalmazza azt az Azure-fájlmegosztást, amelyet a helyszínen kíván csatlakoztatni. A Storage-fiók tartalomjegyzékében válassza ki a **tűzfalak és a virtuális hálózatok** bejegyzést. Hacsak nem adott meg virtuális hálózatot a Storage-fiókjához a létrehozásakor, az eredményül kapott ablaktáblán az **összes kijelölt hálózat** esetében engedélyezni kell a **hozzáférés engedélyezése** a választógombot.

Ha hozzá szeretné adni a Storage-fiókot a kívánt virtuális hálózathoz, válassza a **kiválasztott hálózatok** elemet. A **virtuális hálózatok** Alfejléc alatt kattintson a **meglévő virtuális hálózat hozzáadása** vagy az **új virtuális hálózat** hozzáadása lehetőségre a kívánt állapottól függően. Új virtuális hálózat létrehozása új Azure-erőforrás létrehozását eredményezi majd. Az új vagy meglévő VNet erőforrásnak nem kell ugyanabban az erőforráscsoportban vagy előfizetésben lennie, mint a Storage-fióknak, azonban ugyanabban a régióban kell lennie, mint a Storage-fióknak, valamint azt az erőforráscsoportot és előfizetést, amelyet a VNet üzembe helyezéséhez telepítenie VPN Gateway kell. 

![Képernyőfelvétel a Azure Portalről, amely lehetővé teszi egy meglévő vagy új virtuális hálózat hozzáadását a Storage-fiókhoz](media/storage-files-configure-s2s-vpn/add-vnet-1.png)

Ha meglévő virtuális hálózatot ad hozzá, a rendszer arra kéri, hogy válasszon ki egy vagy több alhálózatot a virtuális hálózatból, amelyhez a Storage-fiókot hozzá kell adni. Ha új virtuális hálózatot választ, a virtuális hálózat létrehozásának részeként létre kell hoznia egy alhálózatot, amelyet később is hozzáadhat a virtuális hálózathoz tartozó Azure-erőforráshoz.

Ha még nem adott hozzá Storage-fiókot az előfizetéséhez, a Microsoft. Storage szolgáltatás végpontját hozzá kell adni a virtuális hálózathoz. Ez eltarthat egy ideig, amíg ez a művelet nem fejeződött be, nem fogja tudni elérni az Azure-fájlmegosztást a Storage-fiókon belül, beleértve a VPN-kapcsolaton keresztül is. 

## <a name="deploy-an-azure-vpn-gateway"></a>Azure-VPN Gateway üzembe helyezése
A Azure Portal tartalomjegyzékében válassza az **új erőforrás létrehozása** és a *virtuális hálózati átjáró* keresése lehetőséget. A virtuális hálózati átjárónak ugyanahhoz az előfizetéshez, Azure-régióhoz és erőforráscsoporthoz kell tartoznia, mint az előző lépésben üzembe helyezett virtuális hálózatnak (vegye figyelembe, hogy az erőforráscsoport automatikusan ki van választva a virtuális hálózat kiválasztásakor). 

Az Azure-VPN Gateway üzembe helyezéséhez fel kell töltenie a következő mezőket:

- **Name (név**): az VPN Gateway Azure-erőforrásának neve. Ez a név lehet a felügyelethez hasznos név.
- **Régió**: az a régió, ahová a VPN Gateway telepíteni fogja.
- **Átjáró típusa**: helyek közötti VPN üzembe helyezéséhez ki kell választania a **VPN-** t.
- **VPN-típus**: a VPN-eszköztől függően az *útvonalon alapuló** vagy a **házirend alapján** is választhat. Az útválasztó-alapú VPN-EK támogatják a IKEv2, míg a házirend alapú VPN-ek csak a IKEv1 támogatják. Ha többet szeretne megtudni a VPN-átjárók két típusáról, tekintse meg a [házirend-alapú és az Útválasztás-alapú VPN-átjárók](../../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md#about) című témakört.
- **SKU**: az SKU szabályozza az engedélyezett helyek közötti alagutak számát és a VPN kívánt teljesítményét. A megfelelő SKU kiválasztásához használja a használati esetet, és nézze meg az [ÁTJÁRÓ SKU](../../vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) -listáját. A VPN Gateway SKU-jának később is módosítható, ha szükséges.
- **Virtual Network (virtuális hálózat**): az előző lépésben létrehozott virtuális hálózat.
- **Nyilvános IP-cím**: az interneten elérhetővé tett VPN Gateway IP-címe. Valószínűleg létre kell hoznia egy új IP-címet, azonban szükség esetén használhat egy meglévő, nem használt IP-címet is. Ha az **új létrehozása** lehetőséget választja, akkor a rendszer létrehoz egy új IP-címet az Azure-beli erőforrásban, amelyben a VPN Gateway és a  **nyilvános IP-cím neve** lesz az újonnan létrehozott IP-cím neve. Ha a **meglévő használata** lehetőséget választja, ki kell választania a meglévő nem használt IP-címet.
- **Aktív-aktív üzemmód engedélyezése**: csak akkor válassza az **engedélyezve** lehetőséget, ha aktív-aktív átjáró-konfigurációt hoz létre, ellenkező esetben hagyja **Letiltva** a kijelölést. Ha többet szeretne megtudni az aktív-aktív üzemmódról, tekintse meg a [magasan elérhető, létesítmények közötti és VNet – VNet kapcsolatot](../../vpn-gateway/vpn-gateway-highlyavailable.md).
- A **BGP ASN konfigurálása**: csak akkor válassza az **engedélyezve** lehetőséget, ha a konfigurációhoz kifejezetten szükség van erre a beállításra. Ha többet szeretne megtudni erről a beállításról, tekintse meg a [BGP és az Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md)című témakört.

Válassza a **felülvizsgálat + létrehozás** lehetőséget a VPN Gateway létrehozásához. Egy VPN Gateway akár 45 percet is igénybe vehet, hogy teljesen létre lehessen hozni és telepíteni lehessen.

### <a name="create-a-local-network-gateway-for-your-on-premises-gateway"></a>Helyi hálózati átjáró létrehozása a helyszíni átjáróhoz 
A helyi hálózati átjáró egy Azure-erőforrás, amely a helyszíni hálózati készüléket képviseli. A Azure Portal tartalomjegyzékében válassza az **új erőforrás létrehozása** és a *helyi hálózati átjáró* keresése lehetőséget. A helyi hálózati átjáró egy Azure-erőforrás, amelyet a rendszer a Storage-fiók, a virtuális hálózat és a VPN Gateway mellett helyez üzembe, de nem kell a Storage-fiókkal megegyező erőforráscsoporthoz vagy előfizetésben lennie. 

A helyi hálózati átjáró erőforrásának üzembe helyezéséhez fel kell töltenie a következő mezőket:

- **Name (név**): a helyi hálózati átjáróhoz tartozó Azure-Erőforrás neve. Ez a név lehet a felügyelethez hasznos név.
- **IP-cím**: a helyi ÁTJÁRÓ nyilvános IP-címe a helyszínen.
- **Címterület**: a helyi hálózati átjáró által reprezentált hálózati címtartomány. Több címtartományt is hozzáadhat, de ügyeljen arra, hogy az itt megadott tartományok ne legyenek átfedésben más hálózatok tartományával, amelyhez csatlakozni szeretne. 
- **BGP-beállítások konfigurálása**: csak a BGP-beállítások konfigurálása, ha a konfigurációhoz ezt a beállítást kell megadni. Ha többet szeretne megtudni erről a beállításról, tekintse meg a [BGP és az Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md)című témakört.
- **Előfizetés**: a kívánt előfizetés. Ennek nem kell megegyeznie a VPN Gateway vagy a Storage-fiókhoz használt előfizetéssel.
- **Erőforráscsoport**: a kívánt erőforráscsoport. Ennek nem kell megegyeznie az VPN Gateway vagy a Storage-fiókhoz használt erőforráscsoporthoz.
- **Hely**: az az Azure-régió, ahol a helyi hálózati átjáró erőforrását létre kell hozni. Ennek egyeznie kell a VPN Gateway és a Storage-fiók kiválasztott régiójával.

Válassza a **Létrehozás** lehetőséget a helyi hálózati átjáró erőforrás létrehozásához.  

## <a name="configure-on-premises-network-appliance"></a>Helyszíni hálózati berendezés konfigurálása
A helyszíni hálózati berendezés konfigurálásának konkrét lépései a szervezet által kiválasztott hálózati berendezéstől függenek. A szervezet által kiválasztott eszköztől függően előfordulhat, hogy a [tesztelt eszközök listáján](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) az eszköz gyártójának az Azure VPN Gateway-vel való konfigurálására vonatkozó utasítások találhatók.

## <a name="create-the-site-to-site-connection"></a>Helyek közötti kapcsolat létrehozása
Egy S2S VPN központi telepítésének befejezéséhez létre kell hoznia egy kapcsolatot a helyszíni hálózati berendezés (amelyet a helyi hálózati átjáró erőforrása) és a VPN Gateway között. Ehhez navigáljon a fent létrehozott VPN Gateway. A VPN Gateway tartalomjegyzékében válassza a **kapcsolatok** lehetőséget, majd kattintson a **Hozzáadás** gombra. Az eredményül kapott **kapcsolat hozzáadása** panelen a következő mezők szükségesek:

- **Name (név**): a kapcsolatok neve. Egy VPN Gateway több kapcsolatot is tárolhat, ezért a felügyelethez hasznos nevet adjon meg, amely megkülönbözteti az adott kapcsolatot.
- **Kapcsolat típusa**: mivel ez egy S2S-kapcsolat, válassza a **helyek közötti (IPSec)** lehetőséget a legördülő listában.
- **Virtuális hálózati átjáró**: Ez a mező automatikusan ki van választva arra a VPN Gatewayre, amelyhez a kapcsolódást végzi, és amely nem módosítható.
- **Helyi hálózati átjáró**: ez az a helyi hálózati átjáró, amelyhez csatlakozni szeretne a VPN Gatewayhoz. Az eredményül kapott kiválasztási ablaktáblának rendelkeznie kell a fent létrehozott helyi hálózati átjáró nevével.
- **Megosztott kulcs (PSK)**: a kapcsolat titkosításának létrehozásához használt betűk és számok keveréke. Ugyanazt a megosztott kulcsot kell használni a virtuális hálózaton és a helyi hálózati átjárókban is. Ha az átjáró-eszköz nem rendelkezik ilyennel, itt létrehozhat egyet, és megadhatja az eszközének.

A kapcsolat létrehozásához válassza az **OK** lehetőséget. A **kapcsolatok** lapon ellenőrizheti, hogy a kapcsolat sikeresen létrejött-e.

## <a name="mount-azure-file-share"></a>Azure-fájlmegosztás csatlakoztatása 
A S2S VPN konfigurálásának utolsó lépése annak ellenőrzése, hogy Azure Files működik-e. Ezt úgy teheti meg, hogy az Azure-fájlmegosztást a helyszínen csatlakoztatja a kívánt operációs rendszerhez. Tekintse meg az operációs rendszerhez való csatlakoztatásra vonatkozó utasításokat itt:

- [Windows](storage-how-to-use-files-windows.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Linux](storage-how-to-use-files-linux.md)

## <a name="see-also"></a>Lásd még
- [Azure Files hálózatkezelés – áttekintés](storage-files-networking-overview.md)
- [Pont – hely (P2S) VPN konfigurálása Windows rendszeren a Azure Files-mel való használatra](storage-files-configure-p2s-vpn-windows.md)
- [Pont – hely (P2S) VPN konfigurálása Linux rendszeren a Azure Files-vel való használatra](storage-files-configure-p2s-vpn-linux.md)
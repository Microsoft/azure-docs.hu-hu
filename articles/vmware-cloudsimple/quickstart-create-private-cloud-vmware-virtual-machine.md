---
title: Rövid útmutató – Azure VMware virtuális gép létrehozása privát felhőben – Azure VMware-megoldás CloudSimple
description: Ismerje meg, hogyan hozhat létre VMware-alapú virtuális gépet a CloudSimple privát felhőben. Nyissa meg a CloudSimple portált a Azure Portal.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c741f01ccc3e34ec991aa77fc850fd9f60c4d8aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899286"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>VMware virtuális gépek létrehozása a saját felhőben

Ahhoz, hogy virtuális gépeket hozzon létre a saját felhőben, először nyissa meg a CloudSimple-portált a Azure Portal.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="access-the-cloudsimple-portal"></a>Hozzáférés a CloudSimple portáljához

1. Válassza az **Összes szolgáltatás** elemet.
2. Keressen rá a **CloudSimple Services** kifejezésre.
3. Válassza ki azt a CloudSimple-szolgáltatást, amelyen létre szeretné hozni a saját Felhőjét.
4. Az **Áttekintés** lapon kattintson **az Ugrás a CloudSimple portálra** elemre, és nyissa meg a CloudSimple-portál új böngésző lapját.  Ha a rendszer kéri, jelentkezzen be az Azure bejelentkezési hitelesítő adataival.  

    ![A CloudSimple-portál elindítása](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Webes vCenter elindítása – felhasználói felület

Most már elindíthatja a vCenter-t a virtuális gépek és házirendek beállításához.

A vCenter eléréséhez Kezdje a CloudSimple portálról. A Kezdőlap **Általános feladatok** területén kattintson az **vSphere-ügyfél indítása** lehetőségre.  Válassza ki a privát felhőt, majd kattintson a **vSphere-ügyfél indítása** lehetőségre a privát felhőben.

   ![VSphere-ügyfél elindítása](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>ISO-vagy vSphere-sablon feltöltése

  > [!WARNING]
  > Az ISO-feltöltéshez használja a vSphere HTML5-ügyfelet.  A Flash-ügyfél használata hibát okozhat.

1. Szerezze be a vCenter feltölteni kívánt ISO-vagy vSphere-sablont, és hozzon létre egy virtuális gépet, és legyen elérhető a helyi rendszeren.
2. A vCenter-ben kattintson a **lemez** ikonra, és válassza a **vsanDatastore** lehetőséget. Kattintson a **fájlok** , majd az **új mappa** elemre.
    ![vCenter ISO](media/vciso00.png)

3. Hozzon létre egy "ISO-és sablonok" nevű mappát.

4. Navigáljon az ISOs mappába az ISO-fájlok és a sablonok területen, majd kattintson a **fájlok feltöltése** elemre. Az ISO feltöltéséhez kövesse a képernyőn megjelenő utasításokat.

## <a name="create-a-virtual-machine-in-vcenter"></a>Virtuális gép létrehozása a vCenter-ben

1. A vCenter-ben kattintson a **gazdagépek és fürtök** ikonra.

2. Kattintson a jobb gombbal a **munkaterhelés** elemre, és válassza az **új virtuális gép** lehetőséget.
    ![Képernyőkép, amely kiemeli az új virtuális gép menüpontot.](media/vcvm01.png)

3. Válassza az **új virtuális gép létrehozása** lehetőséget, majd kattintson a **tovább** gombra.
    ![Képernyőkép, amely kiemeli az új virtuális gép létrehozása lehetőséget.](media/vcvm02.png)

4. Nevezze el a gépet, válassza ki a számítási **feladatok virtuális** gépe helyét, majd kattintson a **tovább** gombra.
    ![Képernyőfelvétel: a számítási feladatok virtuális gépei lehetőség.](media/vcvm03.png)

5. Válassza ki a **munkaterhelés** számítási erőforrását, és kattintson a **tovább** gombra.
    ![Képernyőkép, amely kiemeli a számítási feladatok számítási erőforrását.](media/vcvm04.png)

6. Válassza a **vsanDatastore** lehetőséget, majd kattintson a **tovább** gombra.
    ![Képernyőkép, amely kiemeli a vsanDatastore beállítást.](media/vcvm05.png)

7. Tartsa meg az alapértelmezett ESXi 6,5 kompatibilitási beállítást, majd kattintson a **tovább** gombra.
    ![A kiválasztott ESXi 6,5 kompatibilitási beállítást bemutató képernyőkép.](media/vcvm06.png)

8. Válassza ki az ISO-t a létrehozandó virtuális géphez tartozó vendég operációs rendszernek, és kattintson a **tovább** gombra.
    ![Képernyőkép, amely bemutatja, hogyan választhatja ki a virtuális gép ISO-guese operációs rendszerét.](media/vcvm07.png)

9. Válassza a merevlemez és hálózati beállítások lehetőséget. Új CD/DVD-meghajtó esetén válassza az **ADATTÁR ISO-fájlja** lehetőséget.  Ha engedélyezni szeretné a nyilvános IP-címről érkező forgalmat erre a virtuális gépre, válassza a hálózatot **VM-1-** ként.
    ![Képernyőkép, amely kiemeli az adattár ISO-fájljának kiválasztását.](media/vcvm08.png)

10. Megnyílik egy kiválasztási ablak. Válassza ki az ISO-fájlok és Sablonok mappába korábban feltöltött fájlt, majd kattintson **az OK** gombra.
    ![Új virtuális gép](media/vcvm10.png)

11. Tekintse át a beállításokat, majd kattintson az **OK** gombra a virtuális gép létrehozásához.
    ![A beállítások áttekintését bemutató képernyőkép.](media/vcvm11.png)

A virtuális gép most már hozzá van adva a munkaterhelés számítási erőforrásaihoz, és készen áll a használatra. 
![Képernyőkép, amely megjeleníti a számítási feladathoz tartozó számítási erőforrásokhoz hozzáadott virtuális gépet.](media/vcvm12.png)

Az alapszintű telepítés már befejeződött. A saját Felhőjét ugyanúgy használhatja, mint a helyszíni virtuálisgép-infrastruktúrát.

A következő szakaszban a DNS-és DHCP-kiszolgálók saját Felhőbeli számítási feladatokhoz való beállításával és az alapértelmezett hálózati konfiguráció módosításával kapcsolatos opcionális információk szerepelnek.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Felhasználók és identitási források hozzáadása a vCenter (nem kötelező)

A CloudSimple egy alapértelmezett vCenter-felhasználói fiókot rendel a felhasználónévhez `cloudowner@cloudsimple.local` . A kezdéshez nincs szükség további fiók beállítására.  A CloudSimple szabályosan rendeli hozzá a rendszergazdákat a normál működéshez szükséges jogosultságok végrehajtásához.  Állítsa be a helyszíni Active Directoryt vagy az Azure AD-t egy [további személyazonossági forrásként](set-vcenter-identity.md) a privát felhőben.

## <a name="create-a-dns-and-dhcp-server-optional"></a>DNS-és DHCP-kiszolgáló létrehozása (nem kötelező)

A privát felhőalapú környezetekben futó alkalmazások és munkaterhelések névfeloldást és DHCP-szolgáltatásokat igényelnek a keresési és IP-címek hozzárendeléséhez. Ezeknek a szolgáltatásoknak a biztosításához megfelelő DHCP-és DNS-infrastruktúra szükséges. A vCenter virtuális gépet úgy is konfigurálhatja, hogy ezeket a szolgáltatásokat a saját felhőalapú környezetében adja meg.

Előfeltételek

* Elosztott porttartomány konfigurált VLAN-val

* A beállítás átirányítása helyszíni vagy internetalapú DNS-kiszolgálókra

* Virtuálisgép-sablon vagy ISO virtuális gép létrehozásához

A következő hivatkozások útmutatást nyújtanak a DHCP-és DNS-kiszolgálók Linux és Windows rendszeren történő beállításához.

#### <a name="linux-based-dns-server-setup"></a>Linux-alapú DNS-kiszolgáló beállítása

A Linux különböző csomagokat kínál a DNS-kiszolgálók beállításához.  Az alábbi hivatkozás egy nyílt forráskódú kötési DNS-kiszolgáló beállítására vonatkozó útmutatást tartalmaz.

[Példa a telepítőre](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Windows-alapú telepítés

Ezek a Microsoft-témakörök leírják, hogyan állíthat be egy Windows-kiszolgálót DNS-kiszolgálóként és DHCP-kiszolgálóként.

[Windows Server DNS-kiszolgálóként](/windows-server/networking/dns/dns-top)

[Windows Server DHCP-kiszolgálóként](/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Hálózati konfiguráció testreszabása (nem kötelező)

A CloudSimple-portál hálózati lapjai lehetővé teszik a tűzfalak és a virtuális gépek nyilvános IP-címei konfigurációjának megadását.

### <a name="allocate-public-ips"></a>Nyilvános IP-címek lefoglalása

1. Navigáljon a CloudSimple **-portál hálózati > nyilvános IP** -címére.
2. Kattintson a **nyilvános IP-cím lefoglalása** elemre.
3. Adja meg az IP-cím bejegyzését azonosító nevet.
4. Tartsa meg az alapértelmezett helyet.
5. Ha szükséges, használja a csúszkát az Üresjárati időkorlát módosításához.
6. Adja meg azt a helyi IP-címet, amelyhez nyilvános IP-címet szeretne hozzárendelni.
7. Szükség esetén adjon meg egy társított DNS-nevet.
8. Kattintson a **Kész** gombra.

    ![Nyilvános IP-cím](media/quick-create-pc-public-ip.png)

A nyilvános IP-cím lefoglalásának feladata megkezdődik. A feladat állapotát a **tevékenység > feladatok** oldalon tekintheti meg. A foglalás befejezésekor az új bejegyzés megjelenik a nyilvános IP-címek lapon.

Azt a virtuális gépet, amelyhez meg kell feleltetni ezt az IP-címet, konfigurálni kell a fent megadott helyi címmel. Az IP-cím konfigurálására szolgáló eljárás a virtuális gép operációs rendszerére vonatkozik. A megfelelő eljáráshoz olvassa el a virtuális gép operációs rendszerének dokumentációját.

#### <a name="example"></a>Példa

Íme például az Ubuntu 16,04 részletei.

Adja hozzá a statikus metódust az inet-címek családjának konfigurációjához a/etc/network/interfaces. fájlban. Módosítsa a címeket, a hálózati maszkot és az átjáró értékeit. Ebben a példában a ETH0 felületet, a belső IP-192.168.24.10, az átjáró 192.168.24.1 és a hálózati maszk 255.255.255.0 használjuk. A környezetében az üdvözlő e-mailben a rendelkezésre álló alhálózati információk szerepelnek.

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
Manuálisan tiltsa le az illesztőfelületet.

```
sudo ifdown eth0
```
Manuálisan engedélyezze újra a felületet.

```
sudo ifup eth0
```

Alapértelmezés szerint a rendszer **megtagadja** az internetről érkező összes bejövő forgalmat. Ha bármilyen más portot szeretne megnyitni, hozzon létre egy [Tűzfalszabály-táblázatot](firewall.md).

Miután a belső IP-címet statikus IP-címként konfigurálta, ellenőrizze, hogy elérhető-e az Internet a virtuális gépről.

```
ping 8.8.8.8
```
Ellenőrizze azt is, hogy a virtuális gépet a nyilvános IP-cím használatával is elérheti-e az internetről.

Győződjön meg arról, hogy a virtuális gépen lévő összes iptables-szabály nem blokkolja a bejövő 80-as portot.
        
```
netstat -an | grep 80
```

Indítsa el a 80-es portot figyelő http-kiszolgálót.
       
```
python2.7 -m SimpleHTTPServer 80
```

vagy

```
python3 -m http.server 80
```
Nyisson meg egy böngészőt az asztalon, és mutasson a 80-es portra a nyilvános IP-cím megadásához a virtuális gépen található fájlok tallózásához.

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Alapértelmezett CloudSimple tűzfalszabályok a nyilvános IP-címekhez

* VPN-forgalom: a VPN és az összes munkaterhelés-hálózat és felügyeleti hálózat közötti összes forgalom engedélyezett.
* Privát felhő belső forgalma: a (z) és a (z) és a (z) közötti összes kelet-nyugati forgalom engedélyezett.
* Internetes forgalom:
  * Az internetről érkező összes bejövő forgalom megtagadva a munkaterhelés-hálózatok és a felügyeleti hálózat számára.
  * Az internetre irányuló összes kimenő forgalom a munkaterhelés-hálózatokból vagy a felügyeleti hálózatból engedélyezett.

A tűzfalszabályok funkció használatával módosíthatja a forgalom védelmét is. További információ: [Tűzfalszabályok és szabályok beállítása](firewall.md).

## <a name="install-solutions-optional"></a>Megoldások telepítése (nem kötelező)

A saját felhőalapú vCenter-környezete teljes körű kihasználása érdekében telepíthet megoldásokat a saját CloudSimple-felhőbe. Beállíthatja a biztonsági mentést, a vész-helyreállítást, a replikálást és a virtuális gépeket védő egyéb funkciókat. Ilyenek például a VMware Site Recovery Manager (VMware SRM) és a Veeam Backup & replikációja.

Megoldás telepítéséhez további jogosultságokat kell megadnia korlátozott időtartamra. Lásd: a [jogosultságok kiterjesztésének](escalate-private-cloud-privileges.md)megtalálása.

## <a name="next-steps"></a>Következő lépések

* [VMware rendszerű virtuális gépek felhasználása az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* [Kapcsolódás helyszíni hálózathoz az Azure ExpressRoute](on-premises-connection.md)
* [VPN-átjárók beállítása a CloudSimple hálózaton](vpn-gateway.md)

---
title: A VPN átviteli sebességének ellenőrzése Microsoft Azure Virtual Network
description: Ez a cikk segítséget nyújt a helyszíni erőforrások hálózati teljesítményének ellenőrzéséhez egy Azure-beli virtuális gépre.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: 2d5b51e8cfbfcb5f771e9da524231f8ddfc40a9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660933"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>VPN teljesítményének érvényesítése virtuális hálózaton

A VPN Gateway-kapcsolat lehetővé teszi, hogy biztonságos, létesítmények közötti kapcsolatot hozzon létre az Azure-ban és a helyszíni informatikai infrastruktúrán belüli Virtual Network között.

Ez a cikk bemutatja, hogyan érvényesítheti a helyszíni erőforrások hálózati átviteli sebességét egy Azure-beli virtuális gépre (VM).

> [!NOTE]
> Ez a cikk a gyakori problémák diagnosztizálásához és megoldásához nyújt segítséget. Ha a következő információk használatával nem tudja megoldani a problémát, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="overview"></a>Áttekintés

A VPN Gateway-kapcsolat a következő összetevőket foglalja magában:

* Helyszíni VPN-eszköz (a [hitelesített VPN-eszközök](vpn-gateway-about-vpn-devices.md#devicetable)listájának megtekintése)
* Nyilvános Internet
* Azure VPN Gateway
* Azure VM

A következő ábra egy helyszíni hálózat logikai kapcsolatát mutatja be VPN-en keresztül egy Azure-beli virtuális hálózathoz.

![Az ügyfél hálózatának logikai kapcsolata az MSFT-hálózattal VPN használatával](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>A várható bejövő/kimenő forgalom kiszámításának kiszámítása

1. Határozza meg az alkalmazás alapteljesítményre vonatkozó követelményeit.
1. Határozza meg az Azure VPN Gateway átviteli sebességének korlátait. További segítségért tekintse meg a [VPN Gateway névjegyének](vpn-gateway-about-vpngateways.md#gwsku)"Gateway SKUs" című szakaszát.
1. Határozza meg az [Azure virtuális gépek átviteli sebességére vonatkozó útmutatót](../virtual-machines/sizes.md) a virtuális gép méretéhez.
1. Határozza meg az INTERNETSZOLGÁLTATÓ sávszélességét.
1. A várt átviteli sebesség kiszámításához a virtuális gép, a VPN Gateway vagy az ISP legkevesebb sávszélességét kell használnia. amelyet a megabit/másodperc (/) mérése nyolc (8) értékkel elosztva.

Ha a számított átviteli sebesség nem felel meg az alkalmazás alapkövetelményének, akkor a szűk keresztmetszetként azonosított erőforrás sávszélességét meg kell emelni. Azure-VPN Gateway átméretezéséhez tekintse meg az [ÁTJÁRÓ SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)-jának módosítása című témakört. A virtuális gépek átméretezésével kapcsolatban lásd: virtuális gép [átméretezése](../virtual-machines/windows/resize-vm.md). Ha nem tapasztalja a várt internetes sávszélességet, akkor az INTERNETSZOLGÁLTATÓval is kapcsolatba léphet.

> [!NOTE]
> A VPN Gateway átviteli sebesség az összes Site-to-Site\VNET-to-VNET vagy pont – hely kapcsolat összesítése.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Hálózati átviteli sebesség ellenőrzése a teljesítmény-eszközök használatával

Ezt az ellenőrzést a nem csúcsidőben töltött órákban kell végrehajtani, mivel a VPN-alagút sávszélesség-telítettsége a tesztelés során nem ad pontos eredményeket.

A teszthez használt eszköz a iPerf, amely Windows és Linux rendszeren is működik, és az ügyfél-és a kiszolgálói módok is használhatók. A Windows rendszerű virtuális gépek 3Gbps korlátozódik.

Ez az eszköz nem végez írási/olvasási műveleteket a lemezre. Kizárólag a saját maga által létrehozott TCP-forgalmat hozza létre az egyik végpontról a másikra. Az ügyfél és a kiszolgáló csomópontjai között rendelkezésre álló sávszélességet megcélzó kísérletezés alapján generál statisztikát. Két csomópont közötti tesztelés során az egyik csomópont kiszolgálóként működik, és a másik csomópont ügyfélként működik. A teszt befejezése után javasolt visszafordítani a csomópontok szerepköreit a feltöltési és letöltési sebesség tesztelésére mindkét csomóponton.

### <a name="download-iperf"></a>IPerf letöltése

Töltse le a [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Részletekért lásd a [iPerf dokumentációját](https://iperf.fr/iperf-doc.php).

 > [!NOTE]
 > A jelen cikkben tárgyalt harmadik féltől származó termékeket a Microsofttól független vállalatok gyártják. A Microsoft nem vállal szavatosságot vagy egyéb garanciát ezen termékek teljesítményéről vagy megbízhatóságáról.

### <a name="run-iperf-iperf3exe"></a>IPerf futtatása (iperf3.exe)

1. Engedélyezze a forgalmat (nyilvános IP-cím tesztelése az Azure virtuális gépen) NSG/ACL-szabályt.

1. Mindkét csomóponton engedélyezze a tűzfal kivételét a 5001-es porton.

   **Windows:** Futtassa a következő parancsot rendszergazdaként:

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   Ha a teszt befejezésekor el szeretné távolítani a szabályt, futtassa a következő parancsot:

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Azure Linux:** Az Azure Linux-lemezképek megengedő tűzfallal rendelkeznek. Ha van egy olyan alkalmazás, amely figyeli a portot, a forgalom a-on keresztül engedélyezhető. A védett egyéni rendszerképeknek explicit módon kell megnyitnia a portokat. Az általános Linux operációsrendszer-rétegbeli tűzfalak a következők:, `iptables` `ufw` vagy `firewalld` .

1. A kiszolgáló csomóponton váltson arra a könyvtárra, ahol a iperf3.exe ki van csomagolva. Ezután futtassa a iPerf kiszolgálói módban, és állítsa be a 5001-as porton az alábbi parancsokkal:

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > A 5001-es port testreszabható a környezete bizonyos tűzfal-korlátozásai miatt.

1. Az ügyfél csomópontján váltson arra a könyvtárra, ahol a Iperf eszközt kibontotta, majd futtassa a következő parancsot:

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   Az ügyfél az 5001-as porton 30 másodpercig irányítja át a forgalmat a kiszolgálóra. A "-P" jelző jelzi, hogy 32 egyidejű kapcsolatot biztosítanak a kiszolgáló-csomóponttal.

   A következő képernyőn a példa kimenete látható:

   ![Kimenet](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. VÁLASZTHATÓ A tesztelési eredmények megőrzéséhez futtassa a következő parancsot:

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. Az előző lépések elvégzése után hajtsa végre ugyanezen lépéseket a fordított szerepkörökkel, hogy a kiszolgáló-csomópont ekkor az ügyfél csomópontja legyen, és fordítva.

> [!Note]
> A Iperf nem az egyetlen eszköz. A [NTTTCP egy alternatív megoldás a teszteléshez](../virtual-network/virtual-network-bandwidth-testing.md).

## <a name="test-vms-running-windows"></a>Windows rendszerű virtuális gépek tesztelése

### <a name="load-latteexe-onto-the-vms"></a>Latte.exe betöltése a virtuális gépekre

[Latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b) legújabb verziójának letöltése

Fontolja meg a Latte.exe külön mappába helyezését, például: `c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>Latte.exe engedélyezése a Windows tűzfalon keresztül

A fogadón hozzon létre egy engedélyezési szabályt a Windows tűzfalon, hogy engedélyezze a Latte.exe forgalmat. A legegyszerűbben úgy engedélyezheti a teljes Latte.exe program nevét, hogy nem engedélyezi a megadott TCP-portok bejövő elérését.

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>Latte.exe engedélyezése a Windows tűzfalon keresztül

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

Ha például átmásolta latte.exet a "c:\Tools" mappába, a parancs a következő lesz:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Késési tesztek futtatása

latte.exe elindítása a FOGADÓn (Futtatás CMD-ből, nem a PowerShellből):

`latte -a <Receiver IP address>:<port> -i <iterations>`

A 65k-iterációk nagyjából elég hosszúak a reprezentatív eredmények visszaküldéséhez.

Minden elérhető portszám rendben van.

Ha a virtuális gépen a 10.0.0.4 IP-címe van, akkor a következőhöz hasonlóan fog kinézni:

`latte -c -a 10.0.0.4:5005 -i 65100`

latte.exe elindítása a KÜLDŐn (Futtatás a CMD-ből, nem a PowerShellből)

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

Az eredményül kapott parancs megegyezik a fogadóval, kivéve a "-c" hozzáadásával, amely azt jelzi, hogy ez az "ügyfél" vagy a küldő

`latte -c -a 10.0.0.4:5005 -i 65100`

Várjon az eredményekre. Attól függően, hogy a virtuális gépek milyen távol vannak, néhány percet is igénybe vehet. A már elvégzett tesztek futtatása előtt érdemes lehet kevesebb iterációt kezdenie a sikeres teszteléshez.

## <a name="test-vms-running-linux"></a>Linux rendszerű virtuális gépek tesztelése

A virtuális gépek teszteléséhez használja a [SockPerf](https://github.com/mellanox/sockperf) .

### <a name="install-sockperf-on-the-vms"></a>A SockPerf telepítése a virtuális gépeken

A Linux rendszerű virtuális gépeken (a küldő és a fogadó is) futtassa a következő parancsokat a virtuális gépek SockPerf előkészítéséhez:

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS/RHEL – GIT és egyéb hasznos eszközök telepítése

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu – GIT és egyéb hasznos eszközök telepítése

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash – mind

A bash parancssorból (feltételezi, hogy a git telepítve van)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

A make lassabb, több percet is igénybe vehet

`make`

A telepítés gyors

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>SockPerf futtatása a virtuális gépeken

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>A telepítés után mintául szolgáló parancsok. Kiszolgáló/fogadó – feltételezi, hogy a kiszolgáló IP-címe 10.0.0.4

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>Ügyfél – feltételezi, hogy a kiszolgáló IP-címe 10.0.0.4

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> Győződjön meg arról, hogy nincsenek közbenső ugrások (például virtuális berendezések) a virtuális gép és az átjáró közötti átviteli sebesség tesztelése során.
> Ha a fenti iPERF-/NTTTCP-tesztek hiányos eredményekkel rendelkeznek (a teljes átviteli sebesség tekintetében), tekintse meg az alábbi cikket a probléma lehetséges kiváltó okai mögötti főbb tényezők megismeréséhez: https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

Az ügyfél és a kiszolgáló közötti párhuzamosan gyűjtött csomagok rögzítési nyomkövetési (Wireshark/Hálózatfigyelő) elemzése különösen a hibás teljesítmény felmérésében nyújt segítséget. Ezek a Nyomkövetések a csomagok elvesztését, a nagy késést és az MTU-méretet is tartalmazhatják. töredezettség, TCP 0 ablak, nem sorrendben lévő töredékek stb.

## <a name="address-slow-file-copy-issues"></a>Lassú fájlmásolási problémák megoldása

Még akkor is, ha az előző lépésekkel (iPERF/NTTTCP/etc...) mért összesített átviteli sebesség jó volt, lassú fájl is megjelenhet, ha a Windows Intézőt használja, vagy egy RDP-munkameneten keresztül húzza a fájlt. Ez a probléma általában az alábbi tényezők egyike vagy mindkettő miatt fordul elő:

* A fájlmásolási alkalmazások, például a Windows Intéző és az RDP, nem használnak több szálat a fájlok másolásakor. A jobb teljesítmény érdekében használjon egy többszálas fájlmásolási alkalmazást, például a [RichCopy](/previous-versions/technet-magazine/dd547088(v=msdn.10)) a fájlok másolását 16 vagy 32 szál használatával. Ha módosítani szeretné a RichCopy található fájlmásolás szálának számát, kattintson a **művelet**  >  **másolási beállítások** fájlmásolás elemre  >  .

   ![Lassú fájlmásolás esetén felmerülő problémák](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > Nem minden alkalmazás működik, és nem minden alkalmazás/folyamat használja az összes szálat. Ha futtatja a tesztet, láthatja, hogy néhány szál üres, és nem biztosít pontos átviteli sebességet.
   > Az alkalmazás fájlátviteli teljesítményének ellenőrzéséhez használja a több szálat, ha az alkalmazás vagy a fájlátvitel optimális átviteli sebességét szeretné megkeresni.

* Nem elegendő a virtuális gép lemezének olvasási/írási sebessége. További információ: [Azure Storage – hibaelhárítás](/previous-versions/azure/storage/common/storage-e2e-troubleshooting).

## <a name="on-premises-device-external-facing-interface"></a>Helyszíni eszköz külső felé irányuló felülete

Említettük azon helyszíni tartományok alhálózatait, amelyeket az Azure a helyi hálózati átjárón keresztül elérhet VPN-en keresztül. Ezzel párhuzamosan megadhatja a VNET az Azure-ban a helyszíni eszközhöz.

* **Route-alapú átjáró**: az útvonalakon alapuló VPN-EK házirendje vagy forgalmi választója bármely-a-a-a-any (vagy Wild Cards) értékre van konfigurálva.

* **Házirend-alapú átjáró**: a házirend-alapú VPN-ek az IPSec-alagutakon keresztül titkosítják és irányítják a csomagokat, a helyszíni hálózat és az Azure-VNet közötti címek előtagjainak kombinációja alapján. A házirend (vagy forgalomválasztó) általában egy hozzáférési listaként van megadva a VPN-konfigurációban.

* **UsePolicyBasedTrafficSelector** -kapcsolatok: ("UsePolicyBasedTrafficSelectors" a kapcsolaton keresztüli $True az Azure VPN-átjárót úgy konfigurálja, hogy a helyi házirend-alapú VPN-tűzfalhoz kapcsolódjon. Ha engedélyezi a PolicyBasedTrafficSelectors-t, gondoskodnia kell arról, hogy a VPN-eszköz megfelelő forgalmi választókkal rendelkezik, amelyek a helyszíni hálózat (helyi hálózati átjáró) előtagjainak az Azure Virtual Network előtagjaival és a-ból való használatának minden kombinációjában szerepelnek.

A nem megfelelő konfiguráció az alagúton, a csomagjain, a hibás adatátvitelen és a késésen belül gyakori leválasztást eredményezhet.

## <a name="check-latency"></a>Késések keresése

A késést a következő eszközök használatával tekintheti meg:

* WinMTR
* TCPTraceroute
* `ping` és `psping` (ezek az eszközök jó becslést nyújthatnak a RTT, de nem használhatók minden esetben.)

![Késések keresése](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

Ha nagy késleltetésű csúcsot észlel valamelyik ugrásban az MS hálózati gerinc megadása előtt, érdemes további vizsgálatokat folytatnia az internetszolgáltatóval.

Ha a "msn.net"-n belül egy nagy, szokatlan késési tüske szerepel a komlóban, további vizsgálatokért forduljon az MS támogatási szolgálatához.

## <a name="next-steps"></a>Következő lépések

További információért és segítségért tekintse meg a következő hivatkozást:

* [Microsoft támogatási szolgálat](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
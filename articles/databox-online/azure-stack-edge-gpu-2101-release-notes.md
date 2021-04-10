---
title: Azure Stack Edge Pro 2101 kibocsátási megjegyzései
description: A cikk az 2101-es kiadást futtató Azure Stack Edge Pro kritikus fontosságú nyitott problémáit és megoldásait ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 84bf14caeec163c31004a430fa954fc36f4be68b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562783"
---
# <a name="azure-stack-edge-2101-release-notes"></a>Azure Stack Edge 2101 kibocsátási megjegyzései

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

A következő kibocsátási megjegyzések azonosítják a kritikus nyitott problémákat, valamint az Azure Stack Edge-eszközök 2101-es kiadásával kapcsolatos megoldott problémákat. Ezek a kibocsátási megjegyzések Azure Stack Edge Pro GPU-ra, Azure Stack Edge Pro R-re és Azure Stack Edge mini R-eszközökre vonatkoznak. Az adott modellnek megfelelő szolgáltatásokat és problémákat minden esetben meghívjuk.

A kibocsátási megjegyzések folyamatosan frissülnek, és olyan kritikus fontosságú problémák észlelhetők, amelyek megkerülő megoldást igényelnek. Az eszköz üzembe helyezése előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

Ez a cikk a **Azure stack Edge 2101** kiadásra vonatkozik, amely a szoftver verziószámának **2.2.1473.2521** van leképezve.

## <a name="whats-new"></a>Újdonságok

A következő új szolgáltatások érhetők el az Azure Stack Edge 2101 kiadásban. 

- **A Azure stack Edge Pro r és a Azure stack Edge mini r-eszközök általános elérhetősége** – ettől a kiadástól kezdve Azure stack Edge Pro r és Azure stack Edge mini r-eszközök lesznek elérhetők. További információ: [What is Azure stack Edge Pro r](azure-stack-edge-pro-r-overview.md) és [mi az a Azure stack Edge mini r](azure-stack-edge-mini-r-overview.md).  
- **Virtual Machines Felhőbeli kezelése** – a jelen kiadás megkezdése után az eszközön létrehozhatja és kezelheti a virtuális gépeket az eszközön a Azure Portal használatával. További információ: [virtuális gépek üzembe helyezése a Azure Portal használatával](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
- **Integráció a Azure monitor** használatával – mostantól a Azure monitor segítségével figyelheti az eszközön futó számítási alkalmazások tárolóit. Ebben a kiadásban nem támogatott a Azure Monitor metrikák tárolója. További információ: [Azure monitor engedélyezése az eszközön](azure-stack-edge-gpu-enable-azure-monitor.md).
- **Edge Container Registry** – ebben a kiadásban egy Edge Container Registry áll rendelkezésre, amely az eszköz szélén található tárházat biztosít. Ezt a beállításjegyzéket használhatja a tároló lemezképek tárolásához és kezeléséhez. További információ: az [Edge Container Registry engedélyezése](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). 
- **Virtuális magánhálózat (VPN)** – a VPN használatával további titkosítási réteget biztosíthat az eszközök és a felhő közötti adatforgalomhoz. A VPN csak Azure Stack Edge Pro R és Azure Stack Edge mini R rendszeren érhető el. További információ: [VPN konfigurálása az eszközön](azure-stack-edge-mini-r-configure-vpn-powershell.md). 
- **Titkosítatlan titkosítási kulcsok elforgatása** – most már elforgathatja az eszközön található meghajtók elleni védelemhez használt titkosítási Rest-kulcsokat. Ez a funkció csak Azure Stack Edge Pro R és Azure Stack Edge mini R-eszközökhöz érhető el. További információ: a [Rest-titkosítású kulcsok elforgatása](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#manage-access-to-device-data).
- **Proaktív naplózás** – ennek a kiadásnak a megkezdése után a rendszerállapot-mutatók alapján engedélyezheti az előjelzéses napló-gyűjtést az eszközön az eszköz hibáinak hatékony elhárítása érdekében. További információ: [proaktív naplózási gyűjtemény az eszközön](azure-stack-edge-gpu-proactive-log-collection.md).


## <a name="known-issues-in-2101-release"></a>Ismert problémák a 2101 kiadásban

Az alábbi táblázat összefoglalja az 2101 kiadás ismert problémáit.

| Nem. | Szolgáltatás | Probléma | Áthidaló megoldás/megjegyzések |
| --- | --- | --- | --- |
|**1.**|Előzetes verziójú funkciók |Ebben a kiadásban a következő funkciók érhetők el: helyi Azure Resource Manager, virtuális gépek, Felhőbeli virtuális gépek kezelése, Azure arc-kompatibilis Kubernetes, VPN a Azure Stack Edge Pro R és Azure Stack Edge mini R, többfolyamatos szolgáltatás (MPS) for Azure Stack Edge Pro GPU – minden elérhető előzetes verzióban.  |Ezek a funkciók általánosan elérhetők lesznek a későbbi kiadásokban. |
|**2.**|Kubernetes-irányítópult | Az SSL-tanúsítvánnyal rendelkező Kubernetes-irányítópult *https* -végpontja nem támogatott. | |
|**3.**|Kubernetes |Az Edge Container Registry nem működik, ha a webproxy engedélyezve van.|A funkciók egy későbbi kiadásban lesznek elérhetők. |
|**4.**|Kubernetes |Az Edge Container Registry nem működik IoT Edge modulokkal.| |
|**5.**|Kubernetes |A Kubernetes nem támogatja a (z) ":" környezetet a .NET-alkalmazások által használt környezeti változók neveiben. Ez az Event Grid IoT Edge moduljának Azure Stack Edge-eszközön és más alkalmazásokban való működéséhez is szükséges. További információ: a [ASP.net Core dokumentációja](/aspnet/core/fundamentals/configuration/?tabs=basicconfiguration&view=aspnetcore-3.1&preserve-view=true#environment-variables).|A ":" kifejezést dupla aláhúzással cserélje le. További információ: [Kubernetes probléma](https://github.com/kubernetes/kubernetes/issues/53201)|
|**6.** |Azure arc + Kubernetes-fürt |Alapértelmezés szerint, ha az erőforrást `yamls` a git-tárházból törli, a rendszer nem törli a megfelelő erőforrásokat a Kubernetes-fürtből.  |Ha engedélyezni szeretné a git-tárházból törölt erőforrások törlését, állítsa `--sync-garbage-collection` az ív OperatorParams. További információ: [konfiguráció törlése](../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md#additional-parameters). |
|**7.**|NFS |Az NFS-megosztást használó alkalmazások, amelyek az eszközön az adatírást használják, kizárólagos írást kell használniuk. Ezzel biztosítható, hogy a rendszer a lemezre írja a írásokat.| |
|**8.**|Számítási konfiguráció |A számítási konfiguráció nem sikerül olyan hálózati konfigurációknál, amelyekben az átjárók, illetve kapcsolók vagy útválasztók válaszolnak a hálózaton nem létező rendszerekre vonatkozó ARP-kérelmekre.| |
|**9.**|Számítás és Kubernetes |Ha a Kubernetes először van beállítva az eszközön, az az összes rendelkezésre álló GPU-t kéri. Ezért a Kubernetes beállítása után nem hozhatók létre Azure Resource Manager virtuális gépek a GPU-k használatával. |Ha az eszköz 2 GPU-val rendelkezik, hozzon létre 1 virtuális gépet, amely a GPU-t használja, majd konfigurálja a Kubernetes. Ebben az esetben a Kubernetes a fennmaradó elérhető 1 GPU-t fogja használni. |


## <a name="known-issues-from-previous-releases"></a>A korábbi kiadásokban ismert problémák

Az alábbi táblázat az előző kiadásokból származó ismert problémák összegzését tartalmazza.

| Nem. | Szolgáltatás | Probléma | Áthidaló megoldás/megjegyzések |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro + Azure SQL | Az SQL Database létrehozásához rendszergazdai hozzáférés szükséges.   |Hajtsa végre az alábbi lépéseket a 1-2. lépés helyett a következőben: [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) . <ul><li>Az eszköz helyi felhasználói felületén engedélyezze a számítási felületet. Válassza **a számítási > port # > engedélyezés a számítási > alkalmazáshoz lehetőséget.**</li><li>Letöltés az `sqlcmd` ügyfélgépről https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Kapcsolódjon a számítási felület IP-címéhez (az engedélyezett porthoz), és adjon hozzá egy ", 1401" értéket a cím végéhez.</li><li>A végső parancs a következőképpen fog kinézni: Sqlcmd-S {Interface IP}, 1401-U SA-P "strong! Passw0rd".</li>Ezt követően az aktuális dokumentáció 3-4. lépésének meg kell egyeznie. </li></ul> |
| **2.** |Frissítés| A Blobok a **frissítésen** keresztül visszaállított növekményes módosításai nem támogatottak |BLOB-végpontok esetén a Blobok részleges frissítése a frissítés után előfordulhat, hogy a frissítés nem töltődik fel a felhőbe. Például a következő műveletek sorozatából:<ul><li>Hozzon létre egy blobot a felhőben. Vagy törölhet egy korábban feltöltött blobot az eszközről.</li><li>Frissítse a blobot a felhőből a készülékre a frissítési funkció használatával.</li><li>Csak a blob egy részét frissítse az Azure SDK REST API-k használatával.</li></ul>Ezek a műveletek a blob frissített részeit eredményezik, hogy a felhőben ne legyen frissítve. <br>**Megkerülő megoldás**: a teljes Blobok cseréjéhez használjon olyan eszközöket, mint a Robocopy vagy a hagyományos fájlmásolás az Explorer vagy a parancssor használatával.|
|**3.**|Throttling|A szabályozás során, ha az eszközre való új írások nem engedélyezettek, az NFS-ügyfél által végzett írás "engedély megtagadva" hibával meghiúsul.| A hiba az alábbi módon jelenik meg:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: a (z) "test" könyvtár nem hozható létre: az engedély megtagadva|
|**4.**|Blob Storage betöltés|Ha a AzCopy 10-es verzióját használja a blob Storage betöltéséhez, futtassa a AzCopy a következő argumentummal: `Azcopy <other arguments> --cap-mbps 2000`| Ha ezek a korlátok nincsenek megadva a AzCopy számára, előfordulhat, hogy nagy számú kérelmet küld az eszköznek, ami a szolgáltatással kapcsolatos problémákat okoz.|
|**5.**|Többplatformos Storage-fiókok|A következők érvényesek a többplatformos tárolási fiókok használata esetén:<ul><li> Csak a blokk Blobok támogatottak. Az oldal Blobok nem támogatottak.</li><li>A pillanatképek és a másolási API-k nem támogatottak.</li><li> A Hadoop munkaterhelés betöltése `distcp` nem támogatott, mert a másolási műveletet erősen használja.</li></ul>||
|**6.**|NFS-megosztási kapcsolatok|Ha több folyamat is ugyanarra a megosztásra másol, és az `nolock` attribútum nincs használatban, a másolás során hibák jelenhetnek meg.|Az `nolock` attribútumot át kell adni a csatlakoztatási parancsnak, hogy fájlokat másoljon az NFS-megosztásba. Példa: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Kubernetes-fürt|Ha kubernetes-fürtöt futtató eszközön frissít egy frissítést, akkor a kubernetes virtuális gépek újraindulnak és újraindulnak. Ebben a példányban csak a megadott replikákkal telepített hüvelyek lesznek automatikusan visszaállítva egy frissítés után.  |Ha különálló hüvelyeket hozott létre egy replikációs vezérlőn kívül egy replikakészlet megadása nélkül, akkor ezek a hüvelyek nem lesznek automatikusan visszaállítva az eszköz frissítése után. Ezeket a hüvelyeket vissza kell állítania.<br>Egy replikakészlet olyan hüvelyeket cserél le, amelyeket bármilyen okból törölnek vagy leállítottak, például csomóponti hiba vagy zavaró csomópontok frissítése esetén. Ezért azt javasoljuk, hogy akkor is használjon replikakészlet-készletet, ha az alkalmazás csak egyetlen Pod-t igényel.|
|**8.**|Kubernetes-fürt|A Azure Stack Edge Pro Kubernetes csak a Helm v3 vagy újabb verzióval támogatott. További információért látogasson el a [gyakori kérdésekre: a kormányrúd eltávolítása](https://v3.helm.sh/docs/faq/).|
|**9.**|Azure Arc-kompatibilis Kubernetes |A GA kiadás esetében az Azure arc-kompatibilis Kubernetes a 0.1.18 verzióról a 0.2.9-re frissül. Mivel az Azure arc-kompatibilis Kubernetes frissítése Azure Stack Edge-eszközön nem támogatott, újra kell telepítenie az Azure arc-kompatibilis Kubernetes.|Kövesse az alábbi lépéseket:<ol><li>[Eszköz szoftver-és Kubernetes-frissítéseinek alkalmazása](azure-stack-edge-gpu-install-update.md).</li><li>Kapcsolódjon az [eszköz PowerShell-felületéhez](azure-stack-edge-gpu-connect-powershell-interface.md).</li><li>Távolítsa el a meglévő Azure arc-ügynököt. Írja be a következőt: `Remove-HcsKubernetesAzureArcAgent` .</li><li>[Az Azure-ív üzembe helyezése egy új erőforráson](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Ne használjon meglévő Azure arc-erőforrást.</li></ol>|
|**10.**|Azure Arc-kompatibilis Kubernetes|Az Azure arc-alapú telepítések nem támogatottak, ha a webproxy konfigurálva van az Azure Stack Edge Pro-eszközön.||
|**11.**|Kubernetes |A 31000-es port a Kubernetes-irányítópult számára van fenntartva. Az 31001-es port az Edge Container Registry számára van fenntartva. Hasonlóképpen, az alapértelmezett konfigurációban az IP-címek 172.28.0.1 és 172.28.0.10 a Kubernetes szolgáltatás és az alapvető DNS-szolgáltatás számára van fenntartva.|Ne használja a fenntartott IP-címeket.|
|**12.**|Kubernetes |A Kubernetes jelenleg nem teszi lehetővé a többprotokollos terheléselosztó szolgáltatások használatát. Például egy DNS-szolgáltatás, amely a TCP és az UDP protokollal is figyelni fogja. |A Kubernetes és a MetalLB közötti korlátozás megkerüléséhez két szolgáltatás (az egyik a TCP, az egyik az UDP) hozható létre ugyanazon a pod választón. Ezek a szolgáltatások ugyanazt a megosztási kulcsot és spec. loadBalancerIP használják ugyanazon IP-cím megosztására. Az IP-címek akkor is megoszthatók, ha több szolgáltatással rendelkezik, mint az elérhető IP-címek. <br> További információ: [IP-címek megosztása](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**13.**|Kubernetes-fürt|A meglévő Azure IoT Edge Marketplace-moduloknak módosításra lehet szükségük az Azure Stack Edge-eszközön IoT Edge futtatásához.|További információ: Azure IoT Edge-modulok módosítása a piactérről Azure Stack Edge-eszközön való futtatáshoz.<!-- insert link-->|
|**14.**|Kubernetes |A fájl alapú kötési csatlakoztatások nem támogatottak az Azure Stack Edge-eszközön lévő Kubernetes Azure IoT Edge.|A IoT Edge egy fordítási réteget használ a Kubernetes-szerkezetek számára történő fordításhoz `ContainerCreate` . A `Binds` leképezések létrehozása a `hostpath` címtárban, így a fájl alapú kötési csatlakoztatások nem köthetők IoT Edge tárolók elérési útjaihoz. Ha lehetséges, képezze le a szülő könyvtárat.|
|**15.**|Kubernetes |Ha saját tanúsítványokat hoz létre a IoT Edgehoz, és hozzáadja ezeket a tanúsítványokat a Azure Stack Edge-eszközön, miután a számítás konfigurálva lett az eszközön, az új tanúsítványok nem lesznek felveszve.|A probléma megkerüléséhez töltse fel a tanúsítványokat, mielőtt beállítja az eszközön a számítást. Ha a számítás már konfigurálva van, [kapcsolódjon az eszköz PowerShell-felületéhez, és futtassa IoT Edge parancsokat](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Újraindítás `iotedged` és `edgehub` hüvely.|
|**16.**|Tanúsítványok |Bizonyos példányokban a helyi felhasználói felületen a tanúsítvány állapota több másodpercig is eltarthat a frissítéshez. |A helyi felhasználói felületen a következő forgatókönyvek befolyásolhatják a műveletet.<ul><li>**Állapot** oszlop a **tanúsítványok** lapon.</li><li>**Biztonsági** csempe az **első lépések** oldalon.</li><li>**Konfigurációs** csempe az **Áttekintés** oldalon.</li></ul>  |
|**17.**|IoT Edge |A IoT Edge használatával központilag telepített modulok nem használhatják a gazdagép hálózatot. | |
|**18.**|Számítás + Kubernetes |A számítási/Kubernetes nem támogatja az NTLM webproxyt. ||
|**19.**|Kubernetes + frissítés |A korábbi szoftververziók, például az 2008-es kiadásokban olyan versenyhelyzet-frissítési probléma merül fel, amelynek hatására a frissítés meghiúsul a ClusterConnectionException. |Az újabb buildek használata segít elkerülni ezt a problémát. Ha továbbra is ezt a problémát látja, a megkerülő megoldással próbálja megismételni a frissítést, és működnie kell.|
|**20**|Internet Explorer|Ha a fokozott biztonsági funkciók engedélyezve vannak, előfordulhat, hogy nem fog tudni hozzáférni a helyi webes felhasználói felület lapjaihoz. | Tiltsa le a fokozott biztonságot, és indítsa újra a böngészőt.|


<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->


## <a name="next-steps"></a>Következő lépések

- [Az eszköz frissítése](azure-stack-edge-gpu-install-update.md)
---
title: Azure Stack Edge Pro 2103 kibocsátási megjegyzései
description: A cikk az 2103-es kiadást futtató Azure Stack Edge Pro kritikus fontosságú nyitott problémáit és megoldásait ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/23/2021
ms.author: alkohli
ms.openlocfilehash: 846d4a259f0fcd204bcad6c898efc999c3765fd3
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962561"
---
# <a name="azure-stack-edge-2103-release-notes"></a>Azure Stack Edge 2103 kibocsátási megjegyzései

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

A következő kibocsátási megjegyzések azonosítják a kritikus nyitott problémákat, valamint az Azure Stack Edge-eszközök 2103-es kiadásával kapcsolatos megoldott problémákat. Ezek a kibocsátási megjegyzések Azure Stack Edge Pro GPU-ra, Azure Stack Edge Pro R-re és Azure Stack Edge mini R-eszközökre vonatkoznak. Az adott modellnek megfelelő szolgáltatásokat és problémákat minden esetben meghívjuk.

A kibocsátási megjegyzések folyamatosan frissülnek, és olyan kritikus fontosságú problémák észlelhetők, amelyek megkerülő megoldást igényelnek. Az eszköz üzembe helyezése előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

Ez a cikk a **Azure stack Edge 2103** kiadásra vonatkozik, amely a szoftver verziószámának **2.2.1540.2890** van leképezve. Ez a szoftver akkor alkalmazható az eszközre, ha legalább Azure Stack Edge 2010 (2.1.1377.2170) szoftvert futtat.

## <a name="whats-new"></a>Újdonságok

A következő új szolgáltatások érhetők el az Azure Stack Edge 2103 kiadásban. 
 
- **Új funkciók a Virtual Machineshoz** – ez a kiadás megkezdése után a következő felügyeleti műveleteket hajthatja végre a virtuális gépeken a Azure Portal használatával:
    - Több hálózati adapter hozzáadása vagy eltávolítása egy meglévő virtuális géphez.
    - Több lemez hozzáadása vagy eltávolítása egy meglévő virtuális géphez.
    - Méretezze át a virtuális gépet.
    - Egyéni adatértékek hozzáadása Windows vagy Linux rendszerű virtuális gép telepítésekor.

  Az [eszközön is csatlakozhat a virtuálisgép-konzolhoz](azure-stack-edge-gpu-connect-virtual-machine-console.md) , és elháríthatja a virtuális gépekkel kapcsolatos problémákat.

- **Kapcsolódás a PowerShell-felülethez HTTPS-kapcsolaton keresztül** – a kiadás megkezdése után többé nem lehet távoli PowerShell-munkamenetet megnyitni egy eszközön *http*-n keresztül. Alapértelmezés szerint a rendszer a *HTTPS protokollt* használja az összes munkamenethez. További információ: [Kapcsolódás az eszköz PowerShell-felületéhez](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) .

- **A számítások fejlesztései** – számos fejlesztést és javítást hajtottak végre, beleértve a következőket:

    - **Teljes számítási platform minősége**. Ebben a kiadásban hibajavítások vannak a teljes számítási platform minőségének javításához. Tekintse [meg a 2103 kiadásban rögzített problémákat](#issues-fixed-in-2103-release). 
    - **Számítási platform összetevői**. Biztonsági frissítések lettek alkalmazva a számítási virtuálisgép-rendszerképekre. A Kubernetes-verziók IoT Edge és az Azure arc is frissült.
    - **Diagnosztika**. A rendszer új API-t bocsát ki az erőforrás-és hálózati feltételek vizsgálatához. Kapcsolódhat az eszköz PowerShell-felületéhez, és a parancs használatával `Test-HcsKubernetesStatus` ellenőrizheti az eszköz hálózati készültségét.
    - A **naplófájlok gyűjteménye** , amely továbbfejlesztett hibakeresést eredményez. 
    - **Riasztási infrastruktúra** , amely lehetővé teszi az IP-címek ütközésének észlelését a számítási IP-címek esetében. 
    - Kubernetes és helyi Azure Resource Manager számítási feladatainak **összekeverése** . 

- **Proaktív naplózás alapértelmezés** szerint – ebben a kiadásban a proaktív naplózási gyűjtemény alapértelmezés szerint engedélyezve van az eszközön. Ez a funkció lehetővé teszi a Microsoft számára, hogy proaktív módon gyűjtsön naplókat a rendszerállapot-mutatók alapján, hogy hatékonyan hárítsa el az eszközkel kapcsolatos problémákat. További információ: [proaktív naplózási gyűjtemény az eszközön](azure-stack-edge-gpu-proactive-log-collection.md).

## <a name="issues-fixed-in-2103-release"></a>2103 kiadásban rögzített problémák

A következő táblázat felsorolja azokat a problémákat, amelyek megjelentek a korábbi kiadásokban, és a jelenlegi kiadásban rögzítettek.

| Nem. | Szolgáltatás | Probléma | 
| --- | --- | --- |
|**1.**|Kubernetes |Az Edge Container Registry nem működik, ha a webproxy engedélyezve van.|
|**2.**|Kubernetes |Az Edge Container Registry nem működik IoT Edge modulokkal.| 

## <a name="known-issues-in-2103-release"></a>Ismert problémák a 2103 kiadásban

Az alábbi táblázat összefoglalja az 2103 kiadás ismert problémáit.

| Nem. | Szolgáltatás | Probléma | Áthidaló megoldás/megjegyzések |
| --- | --- | --- | --- |
|**1.**|Előzetes verziójú funkciók |Ebben a kiadásban a következő funkciók érhetők el: helyi Azure Resource Manager, virtuális gépek, Felhőbeli virtuális gépek, Kubernetes, Azure arc-kompatibilis Kubernetes, VPN Azure Stack Edge Pro R és Azure Stack Edge mini R, többfolyamatos szolgáltatás (mp) a Azure Stack Edge Pro GPU számára – előzetes verzióban érhető el.  |Ezek a funkciók általánosan elérhetők lesznek a későbbi kiadásokban. |
|**2.**|GPU virtuális gépek |A jelen kiadás előtt a GPU VM életciklusa nem volt felügyelve a frissítési folyamat során. Ezért az 2103-es kiadásra való frissítéskor a GPU virtuális gépek nem állnak le automatikusan a frissítés során. Az eszköz frissítése előtt manuálisan le kell állítania a GPU-t használó virtuális gépeket `stop-stayProvisioned` . További információ: [a virtuális gép felfüggesztése vagy leállítása](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#suspend-or-shut-down-the-vm).<br> Az összes, a frissítés előtt futó GPU virtuális gép a frissítés után indul el. Ezekben a példányokban a virtuális gépeken futó munkaterhelések nincsenek szabályosan lezárva. A virtuális gépek a frissítés után esetleg nem kívánatos állapotba kerülhetnek. <br>Az összes, a frissítés előtt leállított GPU virtuális gép `stop-stayProvisioned` automatikusan elindul a frissítés után. <br>Ha a GPU virtuális gépeket a Azure Portal segítségével állítja le, manuálisan kell elindítania a virtuális gépet az eszköz frissítése után.| Ha GPU-alapú virtuális gépeket futtat a Kubernetes-mel, a frissítés előtt állítsa le a GPU virtuális gépeket. <br>A GPU virtuális gépek leállításakor a Kubernetes átveszi a GPU-ket, amelyeket eredetileg a virtuális gépek használnak. <br>Minél hosszabb a GPU virtuális gépek leállítása, annál nagyobb az esélye annak, hogy a Kubernetes átveszi a GPU-t. |
|**3.**|Egyéni parancsfájl-virtuálisgép-bővítmény |A korábbi kiadásokban létrehozott Windows rendszerű virtuális gépeken ismert probléma merült fel, és az eszközt 2103-re frissítették. <br> Ha ezen a virtuális gépeken egyéni parancsfájl-kiterjesztést ad hozzá, a Windowsos virtuális gép vendég ügynöke (csak 2.7.41491.901-verzió) beragad a frissítésbe, ami időtúllépést okoz a bővítmény üzembe helyezésének. | Megkerülő megoldás a problémára: <ul><li> Kapcsolódjon a Windows rendszerű virtuális géphez a Remote Desktop Protocol (RDP) használatával. </li><li> Győződjön meg arról, hogy a a `waappagent.exe` gépen fut: `Get-Process WaAppAgent` . </li><li> Ha a `waappagent.exe` nem fut, indítsa újra a `rdagent` szolgáltatást: `Get-Service RdAgent` \| `Restart-Service` . Várjon 5 percet.</li><li> A `waappagent.exe` futása közben öld meg a `WindowsAzureGuest.exe` folyamatot. </li><li>Miután megölte a folyamatot, a folyamat újra elindul az újabb verzióval.</li><li>Győződjön meg arról, hogy a Windowsos virtuális gép vendég ügynökének verziója 2.7.41491.971 a következő paranccsal: `Get-Process WindowsAzureGuestAgent` \| `fl ProductVersion` .</li><li>[Egyéni szkriptek bővítményének beállítása a Windows rendszerű virtuális gépen](azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension.md). </li><ul> | 
|**4.**|Többfolyamatos szolgáltatás (mp) |Az eszköz szoftverének és a Kubernetes-fürtnek a frissítésekor a rendszer nem őrzi meg a munkaterhelések számára az MPS-beállítást.   |[Engedélyezze újra az MPS](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) -t, és telepítse újra az MPS-t használó munkaterheléseket. |


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
|**7.**|Kubernetes-fürt|Ha Kubernetes-fürtöt futtató eszközön frissít egy frissítést, akkor a Kubernetes virtuális gépek újraindulnak és újraindulnak. Ebben a példányban csak a megadott replikákkal telepített hüvelyek lesznek automatikusan visszaállítva egy frissítés után.  |Ha különálló hüvelyeket hozott létre egy replikációs vezérlőn kívül egy replikakészlet megadása nélkül, akkor ezek a hüvelyek nem lesznek automatikusan visszaállítva az eszköz frissítése után. Ezeket a hüvelyeket vissza kell állítania.<br>Egy replikakészlet olyan hüvelyeket cserél le, amelyeket bármilyen okból törölnek vagy leállítottak, például csomóponti hiba vagy zavaró csomópontok frissítése esetén. Ezért azt javasoljuk, hogy akkor is használjon replikakészlet-készletet, ha az alkalmazás csak egyetlen Pod-t igényel.|
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
|**21.**|Kubernetes-irányítópult | Az SSL-tanúsítvánnyal rendelkező Kubernetes-irányítópult *https* -végpontja nem támogatott. | |
|**22.**|Kubernetes |A Kubernetes nem támogatja a (z) ":" környezetet a .NET-alkalmazások által használt környezeti változók neveiben. Ez az Event Grid IoT Edge moduljának Azure Stack Edge-eszközön és más alkalmazásokban való működéséhez is szükséges. További információ: a [ASP.net Core dokumentációja](/aspnet/core/fundamentals/configuration/?tabs=basicconfiguration&view=aspnetcore-3.1&preserve-view=true#environment-variables).|A ":" kifejezést dupla aláhúzással cserélje le. További információ: [Kubernetes probléma](https://github.com/kubernetes/kubernetes/issues/53201)|
|**23.** |Azure arc + Kubernetes-fürt |Alapértelmezés szerint, ha az erőforrást `yamls` a git-tárházból törli, a rendszer nem törli a megfelelő erőforrásokat a Kubernetes-fürtből.  |Ha engedélyezni szeretné a git-tárházból törölt erőforrások törlését, állítsa `--sync-garbage-collection` az ív OperatorParams. További információ: [konfiguráció törlése](../azure-arc/kubernetes/use-gitops-connected-cluster.md#additional-parameters). |
|**24.**|NFS |Az NFS-megosztást használó alkalmazások, amelyek az eszközön az adatírást használják, kizárólagos írást kell használniuk. Ezzel biztosítható, hogy a rendszer a lemezre írja a írásokat.| |
|**25.**|Számítási konfiguráció |A számítási konfiguráció nem sikerül olyan hálózati konfigurációknál, amelyekben az átjárók, illetve kapcsolók vagy útválasztók válaszolnak a hálózaton nem létező rendszerekre vonatkozó ARP-kérelmekre.| |
|**26.**|Számítás és Kubernetes |Ha a Kubernetes először van beállítva az eszközön, az az összes rendelkezésre álló GPU-t kéri. Ezért a Kubernetes beállítása után nem hozhatók létre Azure Resource Manager virtuális gépek a GPU-k használatával. |Ha az eszköz 2 GPU-val rendelkezik, hozzon létre 1 virtuális gépet, amely a GPU-t használja, majd konfigurálja a Kubernetes. Ebben az esetben a Kubernetes a fennmaradó elérhető 1 GPU-t fogja használni. |


<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->


## <a name="next-steps"></a>Következő lépések

- [Az eszköz frissítése](azure-stack-edge-gpu-install-update.md)

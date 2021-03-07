---
title: A Azure Stack Edge Pro előzetes kiadásával kapcsolatos megjegyzések | Microsoft Docs
description: Ismerteti a kritikus fontosságú nyílt problémákat és a megoldásait az Azure Stack Edge Pro-t futtató, előzetes verzió rendelkezésre állási kiadásával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: dd72865e35318c7ff43dc17b7c92b9cc2f3e9790
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102436855"
---
# <a name="azure-stack-edge-pro-with-gpu-preview-release-notes"></a>Azure Stack Edge Pro GPU előzetes kiadási megjegyzésekkel

[!INCLUDE [applies-to-Pro-GPU-sku](../../includes/azure-stack-edge-applies-to-gpu-sku.md)]

A következő kibocsátási megjegyzések azonosítják az Azure Stack Edge Pro-eszközök GPU-val való előzetes kiadásával kapcsolatos kritikus nyitott problémákat és a megoldott 2008 problémákat.

A kibocsátási megjegyzések folyamatosan frissülnek, és olyan kritikus fontosságú problémák észlelhetők, amelyek megkerülő megoldást igényelnek. A Azure Stack Edge Pro-eszköz üzembe helyezése előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

Ez a cikk a következő kiadásra vonatkozik: **Azure stack Edge Pro 2008**.

<!--- **2.1.1328.1904**-->

## <a name="whats-new"></a>Újdonságok

A következő új funkciók lettek hozzáadva a Azure Stack Edge 2008 kiadásban. A jelenleg futtatott előzetes verziójú szoftvertől függően előfordulhat, hogy a szolgáltatások egy részhalmaza látható. 

- **Tárolási osztályok** – az előző kiadásban csak statikusan kiépítheti a tárolót az SMB-vagy NFS-megosztásokon keresztül az Azure stack Edge Pro-eszközön futó Kubernetes-fürtön telepített állapot-nyilvántartó alkalmazások esetében. Ebben a kiadásban a tárolási osztályok lettek hozzáadva, amelyek lehetővé teszik a tárhely dinamikus kiépítését. További információ: a [Kubernetes Azure stack Edge Pro GPU-eszközön](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning). 
- **Kubernetes-irányítópult metrikákkal-kiszolgálóval** – ebben a kiadásban egy Kubernetes-irányítópultot ad hozzá a metrikák kiszolgálói bővítménnyel. Az irányítópult segítségével áttekintést kaphat az Azure Stack Edge Pro-eszközön futó alkalmazásokról, megtekintheti a Kubernetes állapotát, és megtekintheti az eszközön bekövetkezett hibákat. A metrikai kiszolgáló összesíti a processzor és a memória használatát az eszközön található Kubernetes-erőforrások között. További információ: [a Kubernetes-irányítópult használata az Azure stack Edge Pro GPU-eszköz figyeléséhez](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- **Azure-ív Azure stack Edge Pro** -hoz – a kiadás megkezdése után az Azure arc használatával telepítheti az alkalmazások számítási feladatait az Azure stack Edge Pro-eszközön. Az Azure arc egy hibrid felügyeleti eszköz, amely lehetővé teszi, hogy alkalmazásokat helyezzen üzembe a Kubernetes-fürtökön. További információ: a számítási [feladatok üzembe helyezése az Azure arc használatával a Azure stack Edge Pro-eszközön](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).  

## <a name="known-issues"></a>Ismert problémák 

Az alábbi táblázat összefoglalja az Azure Stack Edge Pro-eszköz ismert problémáit.

| Nem. | Szolgáltatás | Probléma | Áthidaló megoldás/megjegyzések |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro + Azure SQL | Az SQL Database létrehozásához rendszergazdai hozzáférés szükséges.   |Hajtsa végre az alábbi lépéseket a 1-2. lépés helyett a következőben: [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) . <ul><li>Az eszköz helyi felhasználói felületén engedélyezze a számítási felületet. Válassza **a számítási > port # > engedélyezés a számítási > alkalmazáshoz lehetőséget.**</li><li>Letöltés az `sqlcmd` ügyfélgépről https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Kapcsolódjon a számítási felület IP-címéhez (az engedélyezett porthoz), és adjon hozzá egy ", 1401" értéket a cím végéhez.</li><li>A végső parancs a következőképpen fog kinézni: Sqlcmd-S {Interface IP}, 1401-U SA-P "strong! Passw0rd".</li>Ezt követően az aktuális dokumentáció 3-4. lépésének meg kell egyeznie. </li></ul> |
| **2.** |Frissítés| A Blobok a **frissítésen** keresztül visszaállított növekményes módosításai nem támogatottak |BLOB-végpontok esetén a Blobok részleges frissítése a frissítés után előfordulhat, hogy a frissítés nem töltődik fel a felhőbe. Például a következő műveletek sorozatából:<ul><li>Hozzon létre egy blobot a felhőben. Vagy törölhet egy korábban feltöltött blobot az eszközről.</li><li>Frissítse a blobot a felhőből a készülékre a frissítési funkció használatával.</li><li>Csak a blob egy részét frissítse az Azure SDK REST API-k használatával.</li></ul>Ezek a műveletek a blob frissített részeit eredményezik, hogy a felhőben ne legyen frissítve. <br>**Megkerülő megoldás**: a teljes Blobok cseréjéhez használjon olyan eszközöket, mint a Robocopy vagy a hagyományos fájlmásolás az Explorer vagy a parancssor használatával.|
|**3.**|Throttling|A szabályozás során, ha az új írások nem engedélyezettek az eszközön, az NFS-ügyfél által végzett írás "engedély megtagadva" hibával meghiúsul.| A hiba az alábbi módon jelenik meg:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: a (z) "test" könyvtár nem hozható létre: az engedély megtagadva|
|**4.**|Blob Storage betöltés|Ha a AzCopy 10-es verzióját használja a blob Storage betöltéséhez, futtassa a AzCopy a következő argumentummal: `Azcopy <other arguments> --cap-mbps 2000`| Ha ezek a korlátok nincsenek megadva a AzCopy, akkor előfordulhat, hogy nagy mennyiségű kérelmet küld az eszköznek, és problémákat okozhat a szolgáltatásban.|
|**5.**|Többplatformos Storage-fiókok|A következők érvényesek a többplatformos tárolási fiókok használata esetén:<ul><li> Csak a blokk Blobok támogatottak. Az oldal Blobok nem támogatottak.</li><li>A pillanatképek és a másolási API-k nem támogatottak.</li><li> A Hadoop munkaterhelés betöltése `distcp` nem támogatott, mert a másolási műveletet erősen használja.</li></ul>||
|**6.**|NFS-megosztási kapcsolatok|Ha több folyamat is ugyanarra a megosztásra másol, és a `nolock` rendszer nem használja az attribútumot, akkor a másolás során hibákat láthat.|Az `nolock` attribútumot át kell adni a csatlakoztatási parancsnak, hogy fájlokat másoljon az NFS-megosztásba. Példa: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Kubernetes-fürt|Ha kubernetes-fürtöt futtató eszközön frissít egy frissítést, akkor a kubernetes virtuális gépek újraindulnak és újraindulnak. Ebben a példányban csak a megadott replikákkal telepített hüvelyek lesznek automatikusan visszaállítva egy frissítés után.  |Ha különálló hüvelyeket hozott létre egy replikációs vezérlőn kívül egy replikakészlet megadása nélkül, akkor ezek a hüvelyek nem lesznek automatikusan helyreállítva az eszköz frissítése után. Ezeket a hüvelyeket vissza kell állítania.<br>Egy replikakészlet olyan hüvelyeket cserél le, amelyeket bármilyen okból törölnek vagy leállítottak, például csomóponti hiba vagy zavaró csomópontok frissítése esetén. Ezért azt javasoljuk, hogy akkor is használjon replikakészlet-készletet, ha az alkalmazás csak egyetlen Pod-t igényel.|
|**8.**|Kubernetes-fürt|A Azure Stack Edge Pro Kubernetes csak a Helm v3 vagy újabb verzióval támogatott. További információért látogasson el a [gyakori kérdésekre: a kormányrúd eltávolítása](https://v3.helm.sh/docs/faq/).|
|**9.**|Azure arc + Azure Stack Edge Pro|Az Azure arc-alapú telepítések nem támogatottak, ha a webproxy konfigurálva van az Azure Stack Edge Pro-eszközön.||
|**10.**|Kubernetes |A 31000-es port a Kubernetes-irányítópult számára van fenntartva. Hasonlóképpen, az alapértelmezett konfigurációban az IP-címek 172.28.0.1 és 172.28.0.10 a Kubernetes szolgáltatás és az alapvető DNS-szolgáltatás számára van fenntartva.|Ne használja a fenntartott IP-címeket.|
|**11.**|Kubernetes |A Kubernetes jelenleg nem teszi lehetővé a többprotokollos terheléselosztó szolgáltatások használatát. Például egy DNS-szolgáltatás, amely a TCP és az UDP protokollal is figyelni fogja. |A Kubernetes és a MetalLB közötti korlátozás megkerüléséhez két szolgáltatás (az egyik a TCP, az egyik az UDP) hozható létre ugyanazon a pod választón. Ezek a szolgáltatások ugyanazt a megosztási kulcsot és spec. loadBalancerIP használják ugyanazon IP-cím megosztására. Az IP-címek akkor is megoszthatók, ha több szolgáltatással rendelkezik, mint az elérhető IP-címek. <br> További információ: [IP-címek megosztása](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**12.**|Kubernetes-fürt|A meglévő Azure IoT Edge Marketplace-modulok nem futnak a Kubernetes-fürtön az Azure Stack Edge-eszközön IoT Edge üzemeltetési platformként.|A modulokat a Azure Stack Edge-eszközön való üzembe helyezésük előtt módosítani kell. További információ: Azure IoT Edge-modulok módosítása a piactérről Azure Stack Edge-eszközön való futtatáshoz.<!-- insert link-->|
|**13.**|Kubernetes |A fájl alapú kötési csatlakoztatások nem támogatottak az Azure Stack Edge-eszközön lévő Kubernetes Azure IoT Edge.|A IoT Edge egy fordítási réteget használ a Kubernetes-szerkezetek számára történő fordításhoz `ContainerCreate` . A `Binds` leképezések létrehozásához `hostpath` vagy létrehozásához, így a fájl alapú kötési csatlakoztatásokhoz nem köthető IoT Edge tárolók elérési útjaihoz.|
|**14.**|Kubernetes |Ha saját tanúsítványokat hoz létre a IoT Edgehoz, és hozzáadja azokat a Azure Stack Edge-eszközön, az új tanúsítványok nem lesznek felhasználva a Helm-diagramok frissítésének részeként.|A probléma megoldásához [kapcsolódjon az eszköz PowerShell-felületéhez](azure-stack-edge-gpu-connect-powershell-interface.md). Újraindítás `iotedged` és `edgehub` hüvely.|
|**15.**|Tanúsítványok |Bizonyos példányokban a helyi felhasználói felületen a tanúsítvány állapota több másodpercig is eltarthat a frissítéshez. |A helyi felhasználói felületen a következő forgatókönyvek befolyásolhatják a műveletet.<ul><li>**Állapot** oszlop a **tanúsítványok** lapon.</li><li>**Biztonsági** csempe az **első lépések** oldalon.</li><li>**Konfigurációs** csempe az **Áttekintés** oldalon.</li></ul>  |

## <a name="next-steps"></a>Következő lépések

- [Felkészülés a Azure Stack Edge Pro-eszköz GPU-val való üzembe helyezésére](azure-stack-edge-gpu-deploy-prep.md)
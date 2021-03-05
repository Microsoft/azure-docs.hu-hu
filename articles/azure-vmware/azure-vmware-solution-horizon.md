---
title: Horizont üzembe helyezése az Azure VMware-megoldáson
description: Ismerje meg, hogyan helyezhet üzembe VMware horizontot az Azure VMware-megoldáson.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: c34d0ac7806f8284e893cf3ad4f3c82dd404ff41
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181397"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Horizont üzembe helyezése az Azure VMware-megoldáson 

>[!NOTE]
>Ez a dokumentum a korábban a Horizon 7 néven ismert VMware Horizon termékre koncentrál. A Horizon egy másik megoldás, mint a Horizon Cloud az Azure-ban, de vannak megosztott összetevők. Az Azure VMware megoldás fő előnyei közé tartozik az egyszerűbb méretezési módszer és a VMware Cloud Foundation-felügyelet integrálása a Azure Portalba.

A [VMware Horizon](https://www.vmware.com/products/horizon.html)® egy virtuális asztali és alkalmazások platform, amely az adatközpontban fut, és egyszerű és központosított felügyeletet biztosít. Virtuális asztalokat és alkalmazásokat biztosít bármilyen eszközön, bárhol is. A Horizon lehetővé teszi a Windows és Linux rendszerű virtuális asztalok, Távoli asztal-kiszolgáló (RDS) által üzemeltetett alkalmazások, asztali számítógépek és fizikai gépek közötti kapcsolatok létrehozását és közvetítését.

Itt kifejezetten a horizont Azure VMware-megoldáson való üzembe helyezésére koncentrálunk. A VMware horizonttal kapcsolatos általános információkért tekintse meg a horizont üzemi dokumentációját:

* [Mi az a VMware Horizon?](https://www.vmware.com/products/horizon.html)

* [További információ a VMware Horizontról](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Horizont hivatkozási architektúrája](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

A Horizon Azure VMware-megoldással való bemutatása mostantól két virtuális asztali infrastruktúra-(VDI-) megoldást kínál az Azure platformon. A következő ábra összefoglalja a legfontosabb különbségeket a magas szinten.

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Az Azure VMware-megoldás és a Horizon-felhő horizontja az Azure-ban" border="false":::

A Horizon 2006 és újabb verziói a Horizon 8 kiadási vonalon a helyszíni és az Azure VMware megoldás üzembe helyezését is támogatják. Van néhány, a helyszínen támogatott, az Azure VMware-megoldáson kívüli szolgáltatás. A horizont ökoszisztémájában további termékek is támogatottak. További információ: [szolgáltatások paritása és együttműködési képesség](https://kb.vmware.com/s/article/80850).

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>Horizont üzembe helyezése hibrid felhőben

A horizontot hibrid felhőalapú környezetben is üzembe helyezheti, ha a Horizon Cloud Pod Architecture (CPA) használatával összeköti a helyszíni és az Azure-adatközpontokat. A CPA átméretezi az üzembe helyezést, hibrid felhőt épít, és redundanciát biztosít az üzletmenet folytonossága és a vész-helyreállítás érdekében.  További információ: a [meglévő Horizon 7 környezetek kiterjesztése](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874).

>[!IMPORTANT]
>A CPA nem kiterjesztett üzembe helyezés; mindegyik Horizon Pod különálló, és az egyes hüvelyekhez tartozó összes kapcsolódási kiszolgálónak egyetlen helyen kell lennie, és ugyanazon a szórási tartományon kell futnia a hálózat szemszögéből.

A helyszíni vagy privát adatközponthoz hasonlóan a horizont is üzembe helyezhető egy Azure VMware-megoldás saját felhőben. A következő részben ismertetjük a főbb különbségeket a helyszíni és az Azure VMware-megoldás üzembe helyezésével kapcsolatban.

Az Azure Private Cloud fogalma megegyezik a VMware SDDC, amely általában a horizont dokumentációjában használatos. A dokumentum többi része az Azure Private Cloud és a VMware SDDC feltételeit használja.

Az előfizetési licencek kezeléséhez a Horizon Cloud Connector szükséges az Azure VMware-megoldásban. A Cloud Connector üzembe helyezhető az Azure Virtual Network a Horizon-kapcsolatok kiszolgálói mellett.

>[!IMPORTANT]
>A horizont Control Plant Azure VMware-megoldáson alapuló távlati támogatása még nem érhető el. Ügyeljen arra, hogy letöltse a Horizon Cloud Connector VHD-verzióját.

## <a name="vcenter-cloud-admin-role"></a>vCenter Felhőbeli rendszergazdai szerepkör

Mivel az Azure VMware megoldás egy SDDC szolgáltatás, és az Azure felügyeli az Azure VMware-megoldás SDDC életciklusát, az Azure VMware-megoldáson alapuló vCenter-engedélyezési modellt a tervezés korlátozza.

Az ügyfeleknek a felhő-rendszergazdai szerepkör használatára van szükségük, amely korlátozott vCenter engedélyekkel rendelkezik. A Horizon termék úgy lett módosítva, hogy működjön együtt a felhőalapú rendszergazdai szerepkörrel az Azure VMware-megoldáson, különösen:

* Az azonnali klónozási kiépítés úgy lett módosítva, hogy az Azure VMware-megoldáson fusson.

* Egy adott vSAN szabályzat (VMware_Horizon) lett létrehozva az Azure VMware-megoldásban a horizonttal való együttműködéshez, amelynek elérhetőnek kell lennie, és a horizonton üzembe helyezett SDDCs kell használni.

* a vSphere-alapú olvasási gyorsítótár (CBRC), más néven tárolási gyorssegéd, le van tiltva az Azure VMware-megoldás futtatásakor.

>[!IMPORTANT]
>A CBRC nem lehet újra bekapcsolni.

>[!NOTE]
>Az Azure VMware-megoldás automatikusan beállítja a megadott horizont-beállításokat, ha a Horizon 2006 (más néven Horizon 8) és újabb verziókat a Horizon 8 ágban helyezi üzembe, és kiválasztja az **Azure** lehetőséget a Horizon-kapcsolatok kiszolgálói telepítőben.

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Az Azure VMware megoldás üzembe helyezési architektúrájának horizontja

Egy tipikus horizont architektúra-kialakítás egy Pod és egy blokkos stratégiát használ. A blokk egyetlen vCenter, míg több blokk kombinálja a pod-t. A Horizon Pod a szervezeti egység, amelyet a horizont skálázhatósági korlátai határoznak meg. A Horizon Pod külön felügyeleti portált tartalmaz, ezért a standard kialakítási gyakorlat a hüvelyek számának csökkentése.

Minden felhő saját hálózati kapcsolati sémával rendelkezik. A VMware SDDC Networking/NSX Edge szolgáltatással kombinálva az Azure VMware megoldás hálózati kapcsolata egyedi követelményeket támaszt a helyi környezettől eltérő horizont üzembe helyezéséhez.

Az egyes Azure-beli privát felhő-és SDDC 4 000 asztali vagy alkalmazás-munkamenetet kezelhetnek, feltéve, hogy:

* A munkaterhelés-forgalom a LoginVSI Task Worker-profilhoz igazodik.

* A rendszer csak a protokollok forgalmát veszi figyelembe, felhasználói adatokat nem.

* Az NSX Edge nagy méretűre van konfigurálva.

>[!NOTE]
>A munkaterhelés-profil és a szükségletek eltérőek lehetnek, így az eredmények a használati esettől függően változhatnak. A felhasználói adatok kötetei csökkenthetik a méretezési korlátokat a számítási feladatok kontextusában. Ennek megfelelően tervezze meg a telepítést. További információkért tekintse meg a méretezési irányelvek az [Azure VMware-megoldás gazdagépek a horizonton üzembe helyezéséhez](#size-azure-vmware-solution-hosts-for-horizon-deployments) című szakaszt.

Az Azure Private Cloud és a SDDC maximális korlátja miatt ajánlott üzembe helyezési architektúra, ahol a Horizon-kapcsolati kiszolgálók és a VMware Unified Access Gateway (UAGs) az Azure Virtual Networkon belül futnak. Gyakorlatilag minden egyes Azure-beli privát felhőt és SDDC bekapcsol egy blokkba. Az Azure VMware-megoldáson futó horizont méretezhetőségének maximalizálása érdekében.

Az Azure Virtual Networkról az Azure privát felhőkre/SDDCs való kapcsolódást ExpressRoute FastPath kell konfigurálni. Az alábbi ábrán egy alapszintű Horizon Pod üzemelő példány látható.

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Tipikus Horizon Pod-telepítés a ExpressPath gyors elérési úttal" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Hálózati kapcsolat az Azure VMware-megoldás méretezési horizontjának használatával

Ez a szakasz magas szintű hálózati architektúrát mutat be néhány gyakori üzembe helyezési példával, amely segít a horizont Azure VMware-megoldásban való méretezésében. A hangsúly kifejezetten a kritikus hálózati elemekre vonatkozik. 

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Egy Horizon Pod az Azure VMware-megoldásban

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Egy Horizon Pod az Azure VMware-megoldásban" border="false":::

Az egyetlen Horizon Pod a legközvetlenebb üzembe helyezési forgatókönyv, mivel az USA keleti régiójában csak egy Horizon Pod-t telepíthet.  Mivel az egyes privát felhő-és SDDC a 4 000 asztali munkamenetek kezelésére becsültek, a maximális horizont Pod-méretet kell telepítenie.  Akár három privát felhők/SDDCs üzembe helyezését is megtervezheti.

Az Azure Virtual Networkban üzembe helyezett Horizon infrastruktúra-alapú virtuális gépek (VM-EK) esetében a 12 000-es munkamenetek a Horizon Pod-on érhetők el. Az egyes privát felhő-és SDDC közötti kapcsolat az Azure Virtual Network ExpressRoute gyors elérési út.  Nincs szükség a privát felhők közötti kelet-nyugati forgalomra. 

Az alapszintű központi telepítési példához tartozó főbb feltételezések a következők:

* A Cloud Pod Architecture (CPA) használatával nem rendelkezik olyan helyszíni Horizon Pod-vel, amelyhez ehhez az új Pod-hez szeretne csatlakozni.

* A végfelhasználók az interneten keresztül csatlakoznak a virtuális asztalokhoz (a kapcsolat a helyszíni adatközponton keresztül).

A helyszíni AD-t VPN-en vagy ExpressRoute áramkörön keresztül összekapcsolhatja az Azure Virtual Networkban található AD-tartományvezérlővel.

Az alapszintű példa egyik variációja lehet a helyszíni erőforrások kapcsolatának támogatása. Például a felhasználók hozzáférnek az asztalokhoz, és a virtuális asztali alkalmazások forgalmát előállítják, vagy egy helyszíni Horizon Pod-hez csatlakoznak a CPA használatával.

A diagram bemutatja, hogyan támogathatja a helyszíni erőforrások kapcsolódását. Ha a vállalati hálózathoz szeretne csatlakozni az Azure Virtual Networkhoz, szüksége lesz egy ExpressRoute-áramkörre.  Emellett a ExpressRoute Global Reach használatával össze kell kapcsolni a vállalati hálózatot a saját felhő-és SDDCs.  Lehetővé teszi, hogy a SDDC a ExpressRoute-áramkörhöz és a helyszíni erőforrásokhoz való kapcsolódást. 

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Vállalati hálózat összekötése egy Azure-Virtual Network" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Több Horizontos hüvely az Azure VMware megoldásban több régióban

Egy másik forgatókönyv több hüvelyen is méretezhető.  Ebben a forgatókönyvben két Horizontos hüvelyt helyez üzembe két különböző régióban, és összevonása azokat a CPA használatával. Az előző példában szereplő hálózati konfigurációhoz hasonló, de további régiók közötti hivatkozásokkal. 

Az Azure-Virtual Network az egyes régiókban a másik régióban található privát Felhőkkel/SDDCs kell összekötnie. A rendszer lehetővé teszi, hogy a CPA-összevonás számára a Horizon kapcsolati kiszolgálók a felügyelet alatt lévő összes asztalhoz csatlakozzanak. Ha további privát felhőket/SDDCs ad hozzá ehhez a konfigurációhoz, a teljes skálára méretezheti a 24 000-es munkameneteket. 

Ugyanezek az alapelvek érvényesek, ha két Horizontos hüvelyt helyez üzembe ugyanabban a régióban.  Ügyeljen arra, hogy a második horizont Pod-ot *külön Azure-Virtual Network* telepítse. Az egyetlen Pod-példához hasonlóan a vállalati hálózatot és a helyszíni Pod-t is összekapcsolhatja ehhez a több-Pod/régióhoz tartozó példához a ExpressRoute és a Global Reach használatával. 

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text=" Több Horizontos hüvely az Azure VMware megoldásban több régióban" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Azure VMware-megoldási gazdagépek mérete horizonton üzemelő példányokhoz 

A horizont méretezési módszertana az Azure VMware-megoldásban futó gazdagépeken egyszerűbb, mint a helyi horizonton.  Ennek oka az, hogy az Azure VMware megoldás gazdagépe szabványosítva van.  A gazdagépek pontos méretezése segít meghatározni a VDI-követelmények támogatásához szükséges gazdagépek számát.  A szolgáltatás központi szerepet játszik az asztali költségek meghatározásában.

### <a name="sizing-tables"></a>Táblázatok méretezése

A horizonton futó virtuális asztalok adott vCPU/vRAM-ra vonatkozó követelményei az ügyfél adott számítási feladatának profiljától függenek.   Az MSFT és a VMware Sales csapatának segítségével meghatározhatja a virtuális asztalok vCPU/vRAM-ra vonatkozó követelményeit. 

| vCPU virtuális gépenként | vRAM/virtuális gép (GB) | Példány | 100 virtuális gépek | 200 virtuális gépek | 300 virtuális gépek | 400 virtuális gépek | 500 virtuális gépek | 600 virtuális gépek | 700 virtuális gépek | 800 virtuális gépek | 900 virtuális gépek | 1000 virtuális gépek | 2000 virtuális gépek | 3000 virtuális gépek | 4000 virtuális gépek | 5000 virtuális gépek | 6000 virtuális gépek | 6400 virtuális gépek |
|:-----------:|:----------------:|:--------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|
|      2      |        3.5       |    AVS   |    3    |    3    |    4    |    4    |    5    |    6    |    6    |    7    |    8    |     9    |    17    |    25    |    33    |    41    |    49    |    53    |
|      2      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    6    |    7    |    8    |    9    |     9    |    18    |    26    |    34    |    42    |    51    |    54    |
|      2      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      2      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      2      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      2      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      4      |        3.5       |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      4      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      4      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      4      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      6      |        3.5       |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         4        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      6      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      6      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      8      |        3.5       |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         4        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         6        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      8      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      8      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |


### <a name="horizon-sizing-inputs"></a>Horizont méretezési bemenetei

A következőképpen kell összegyűjtenie a tervezett munkaterhelést:

* Egyidejű asztali számítógépek száma

* Szükséges vCPU asztali gépenként

* Szükséges vRAM/asztali

* Szükséges tárterület asztali gépenként

A VDI-telepítések általában a CPU vagy a RAM korlátozott, ami meghatározza a gazdagép méretét. Vegyük például a következő példát a LoginVSI Knowledge Worker típusú számítási feladathoz, amely a teljesítmény tesztelésével lett érvényesítve:

* 2 000 egyidejű asztali telepítés

* 2vCPU.

* 4 GB vRAM/asztali.

* 50 GB tárterület/asztali

Ebben a példában a gazdagépek teljes száma 18-ra, amely a virtuális gép által üzemeltetett 111-es sűrűséget eredményez.

>[!IMPORTANT]
>Az ügyfél-munkaterhelések ettől a példától különböznek a LoginVSI Knowledge Worker szolgáltatástól. Az üzembe helyezés megtervezésének részeként a VMware EUC SEs-vel dolgozhat a kívánt méretezési és teljesítményi igényekkel. Győződjön meg arról, hogy a tényleges, tervezett munkaterhelés használatával futtatja a saját teljesítménytesztet, mielőtt véglegesíti a gazdagép méretezését, és ennek megfelelően módosítja.

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Az Azure VMware megoldás licencelésének horizontja 

Négy összetevőből áll az Azure VMware-megoldás futtatási horizontjának általános költségei. 

### <a name="azure-vmware-solution-capacity-cost"></a>Azure VMware-megoldás kapacitásának díja

A díjszabással kapcsolatos információkért tekintse meg az [Azure VMware megoldás díjszabását](https://azure.microsoft.com/pricing/details/azure-vmware/) ismertető oldalt.

### <a name="horizon-licensing-cost"></a>Horizont licencelési díja

Két elérhető licenc létezik az Azure VMware-megoldással, amely akár egyidejű felhasználó (CCU) vagy nevesített felhasználó (NU) lehet:

* Horizon-előfizetés licence

* Horizon univerzális előfizetési licenc

Ha a közeljövőben csak az Azure VMware-megoldáson helyezi üzembe a horizontot, akkor a Horizon-előfizetés licencét kell használnia, mert alacsonyabb a díja.

Ha az Azure VMware megoldáson és a helyszínen is üzembe helyezhető, a vész-helyreállítási használati esethez hasonlóan válassza a Horizon Universal előfizetés licencét. Tartalmaz egy vSphere-licencet a helyszíni üzembe helyezéshez, így magasabb a díja.

Működjön együtt a VMware EUC értékesítési csapatával, és határozza meg a horizont licencelési költségeit az igényei alapján.

### <a name="azure-instance-types"></a>Azure-példányok típusai

A horizont infrastruktúra számára szükséges Azure-beli virtuálisgép-méretek megismeréséhez tekintse meg a VMware irányelveit, amelyek [itt](https://techzone.vmware.com/resource/horizon-on-azure-vmware-solution-configuration#horizon-installation-on-azure-vmware-solution)találhatók.

## <a name="references"></a>Hivatkozások
[A Linux rendszerhez készült Horizon Agent rendszerkövetelményei](https://docs.vmware.com/en/VMware-Horizon/2012/linux-desktops-setup/GUID-E268BDBF-1D89-492B-8563-88936FD6607A.html)


## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni az Azure VMware-megoldással kapcsolatos VMware Horizontról, olvassa el a [VMware Horizon – gyakori kérdések](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/horizon/vmw-horizon-on-microsoft-azure-vmware-solution-faq.pdf)című témakört.

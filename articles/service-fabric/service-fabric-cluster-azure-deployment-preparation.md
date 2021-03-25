---
title: Azure Service Fabric-fürt üzembe helyezésének megtervezése
description: Ismerje meg, hogyan tervezheti meg és készítse elő az Azure-beli üzemi Service Fabric-fürtök üzembe helyezését.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 82521487b9a3e9438784e010a32cf6df8e7be2ef
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046317"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>A fürt üzembe helyezésének megtervezése és előkészítése

Éles fürtök üzembe helyezésének tervezése és előkészítése nagyon fontos.  Számos szempontot figyelembe kell venni.  Ez a cikk végigvezeti a fürt üzembe helyezésének előkészítésének lépésein.

## <a name="read-the-best-practices-information"></a>Az ajánlott eljárásokat ismertető információk
Az Azure Service Fabric-alkalmazások és-fürtök sikeres kezeléséhez olyan műveletekre van szükség, amelyeket kifejezetten ajánlott elvégezni az éles környezet megbízhatóságának optimalizálása érdekében.  További információért olvassa el [Service Fabric alkalmazás-és fürt ajánlott eljárásait](./service-fabric-best-practices-security.md).

## <a name="select-the-os-for-the-cluster"></a>Válassza ki a fürt operációs rendszerét
Service Fabric lehetővé teszi Service Fabric-fürtök létrehozását a Windows Servert vagy Linuxot futtató virtuális gépeken vagy számítógépeken.  A fürt üzembe helyezése előtt ki kell választania az operációs rendszert: Windows vagy Linux.  A fürt minden csomópontja (virtuális gép) ugyanazt az operációs rendszert futtatja, nem keverheti össze a Windows-és Linux-alapú virtuális gépeket ugyanabban a fürtben.

## <a name="capacity-planning"></a>Kapacitástervezés
A kapacitástervezés az éles rendszerek üzembe helyezésének lényeges lépése. Az alábbiakban néhány dolog láthat, amelyet érdemes a folyamat részeként figyelembe vennie.

* A fürthöz tartozó csomópont-típusok kezdeti száma 
* A csomópontok típusának tulajdonságai (méret, példányok száma, elsődleges, internetre irányuló, virtuális gépek száma stb.)
* A fürt megbízhatóságra és tartósságra vonatkozó jellemzői.

### <a name="select-the-initial-number-of-node-types"></a>Válassza ki a csomópontok típusának kezdeti számát
Először is meg kell állapítania, hogy a létrehozandó fürt milyen módon lesz használatban. Milyen típusú alkalmazásokat kíván telepíteni a fürtbe? Az alkalmazása több szolgáltatással rendelkezik, és ezek közül bármelyiknek nyilvánosnak vagy internetkapcsolatnak kell lennie? Különböző infrastrukturális igényeket (például nagyobb RAM-ot vagy magasabb CPU-ciklusokat) igényelnek a szolgáltatásai (az alkalmazást alkotó alkalmazások)? Egy Service Fabric fürt több csomópontból állhat: egy elsődleges csomópont-típusból és egy vagy több nem elsődleges csomópont típusból. Mindegyik csomópont-típus egy virtuálisgép-méretezési csoportra van leképezve. Ezután mindegyik csomóponttípus egymástól függetlenül skálázható vertikálisan le vagy fel, eltérő nyitott portokkal rendelkezhet, és eltérő kapacitásmetrikái lehetnek. A [csomópont-tulajdonságok és elhelyezési megkötések][placementconstraints] beállítható úgy, hogy bizonyos szolgáltatásokat bizonyos csomópont-típusokra korlátozzanak.  További információ: Service Fabric a [fürt kapacitásának megtervezése](service-fabric-cluster-capacity.md).

### <a name="select-node-properties-for-each-node-type"></a>Csomópont-tulajdonságok kiválasztása minden csomópont-típushoz
A csomópont-típusok a társított méretezési csoportba tartozó virtuális gépek SKU-azonosítóját, számát és tulajdonságait határozzák meg.

Az egyes csomópont-típusokhoz tartozó virtuális gépek minimális méretét a csomópont típusához választott [tartóssági szint][durability] határozza meg.

Az elsődleges csomópont típusához tartozó virtuális gépek minimális számát a választott [megbízhatósági szint][reliability] határozza meg.

Tekintse meg az [elsődleges csomópontok típusaira](service-fabric-cluster-capacity.md#primary-node-type)vonatkozó minimális javaslatokat, a [nem elsődleges csomópont-típusokra vonatkozó állapot-nyilvántartó munkaterheléseket](service-fabric-cluster-capacity.md#stateful-workloads), valamint az [állapot nélküli munkaterheléseket a nem elsődleges csomópontok típusainál](service-fabric-cluster-capacity.md#stateless-workloads).

A csomópontok minimális számánál nagyobbnak kell lennie az ebben a csomópont-típusban futtatni kívánt alkalmazás/szolgáltatások replikáinak száma alapján.  [Service Fabric alkalmazások kapacitásának megtervezése](service-fabric-capacity-planning.md) segít megbecsülni az alkalmazások futtatásához szükséges erőforrásokat. Az alkalmazások számítási feladatainak módosításához a későbbiekben akár később, akár lejjebb is méretezheti a fürtöt. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>Ideiglenes operációsrendszer-lemezek használata a virtuálisgép-méretezési csoportokhoz

Az *ideiglenes operációsrendszer-lemezek* a helyi virtuális GÉPEN (VM) létrehozott tárolók, és nem menthetők a távoli Azure Storage-ba. A rendszer az összes Service Fabric csomópont-típushoz (elsődleges és másodlagos) ajánlott, mivel a hagyományos állandó operációsrendszer-lemezekhez, az ideiglenes operációsrendszer-lemezekhez képest:

* Olvasási/írási késés csökkentése operációsrendszer-lemezre
* Gyorsabb alaphelyzetbe állítás vagy Rendszerképbeli csomópont-felügyeleti műveletek engedélyezése
* Csökkentse a teljes költséget (a lemezek ingyenesek, és nem merülnek fel további tárolási költségek)

Az ideiglenes operációsrendszer-lemezek nem egy adott Service Fabric funkció, hanem az Azure-beli *virtuálisgép-méretezési* csoportok egyik funkciója, amely Service Fabric csomópont-típusokra van leképezve. A Service Fabric használatához a következőkre van szükség a fürt Azure Resource Manager sablonjában:

1. Győződjön meg arról, hogy a csomópont-típusok támogatják az Azure-beli [virtuális gépek támogatott méreteit](../virtual-machines/ephemeral-os-disks.md) az átmeneti operációsrendszer-lemezekhez, és hogy a virtuális gép méretének mérete az operációsrendszer-lemez méretének támogatásához (lásd az alábbi *megjegyzést* ). Például:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > Ügyeljen arra, hogy a virtuális gép operációsrendszer-lemezének méretével megegyező vagy nagyobb méretű virtuálisgép-méretet válasszon, ellenkező esetben az Azure-beli üzembe helyezés hibát eredményezhet (még akkor is, ha az eredetileg el van fogadva).

2. Adja meg a (z `vmssApiVersion` ) vagy újabb virtuálisgép-méretezési csoport verzióját `2018-06-01` :

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. A telepítési sablon virtuálisgép-méretezési csoport szakaszában adja meg a `Local` következő beállítást `diffDiskSettings` :

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

> [!NOTE]
> A felhasználói alkalmazások nem rendelkezhetnek függőséggel/fájllal/összetevővel az operációsrendszer-lemezen, mert az operációsrendszer-lemez elvész az operációsrendszer-frissítés esetén.

> [!NOTE]
> A meglévő, nem ideiglenes VMSS nem frissíthető helyben az ideiglenes lemezek használatára.
> Az áttelepítés érdekében a felhasználóknak új nodeType kell [felvenniük](./virtual-machine-scale-set-scale-node-type-scale-out.md) az ideiglenes lemezekkel, át kell helyezniük a munkaterheléseket az új nodeType & [el kell távolítani](./service-fabric-how-to-remove-node-type.md) a meglévő nodeType.
>

További információ és további konfigurációs lehetőségek: [ideiglenes operációsrendszer-lemezek Azure-beli virtuális gépekhez](../virtual-machines/ephemeral-os-disks.md) 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>A fürt tartóssági és megbízhatósági szintjeinek kiválasztása
A tartóssági szint azt jelzi, hogy a rendszer a virtuális gépek által a mögöttes Azure-infrastruktúrához tartozó jogosultságokat használja. Az elsődleges csomópont típusában ez a jogosultság lehetővé teszi, hogy a Service Fabric szüneteltetni lehessen a rendszerszolgáltatások és az állapot-nyilvántartó szolgáltatások Kvórumának követelményeit érintő virtuálisgép-szintű infrastruktúra-kérelmeket (például a virtuális gépek újraindítását, a virtuális gépek rendszerképét vagy a virtuális gépek áttelepítését). A nem elsődleges csomópontok típusainál ez a jogosultság lehetővé teszi, hogy a Service Fabric szüneteltetni lehessen a virtuálisgép-szintű infrastruktúra-kérelmeket (például a virtuális gépek újraindítását, a virtuális gép rendszerképét és a virtuális gépek áttelepítését), amelyek hatással vannak az állapot-nyilvántartó szolgáltatásokra  A különböző szintek és javaslatok előnyeit, amelyeken a használni kívánt szintet és a-t használja, tekintse meg a [fürt tartóssági jellemzőit][durability].

A megbízhatósági szint azon rendszerszolgáltatások replikáinak a megadására szolgál, amelyeket ebben a fürtben szeretne futtatni az elsődleges csomópont típusán. Minél több replikát, annál megbízhatóbb a rendszerszolgáltatások a fürtben.  A különböző szintek és javaslatok előnyeit, amelyeken a használni kívánt szintet és a-t használja, tekintse meg a [fürt megbízhatósági jellemzőit][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Fordított proxy és/vagy DNS engedélyezése
A fürtön belül egymáshoz csatlakozó szolgáltatások általában közvetlenül hozzáférhetnek más szolgáltatások végpontjait, mert a fürt csomópontjai ugyanazon a helyi hálózaton találhatók. Ahhoz, hogy könnyebb legyen csatlakozni a szolgáltatások között, Service Fabric további szolgáltatásokat nyújt: egy [DNS-szolgáltatást](service-fabric-dnsservice.md) és egy [fordított proxy szolgáltatást](service-fabric-reverseproxy.md).  A fürt telepítésekor mindkét szolgáltatás engedélyezhető.

Mivel számos szolgáltatás – különösen a tároló-szolgáltatások – rendelkezhet egy meglévő URL-névvel, amely képes a szabványos DNS protokoll használatával (a elnevezési szolgáltatás protokoll helyett), különösen az alkalmazás "lift and SHIFT" forgatókönyvekben való feloldására. Pontosan ez a DNS-szolgáltatás. Lehetővé teszi a DNS-nevek hozzárendelését a szolgáltatás neveként, így a végponti IP-címek feloldását.

A fordított proxy a fürtben a HTTP-végpontokat (a HTTPS-t is beleértve) elérhetővé teszi. A fordított proxy nagy mértékben leegyszerűsíti az egyéb szolgáltatások meghívását egy adott URI-formátum megadásával.  A fordított proxy az egyes szolgáltatásokhoz szükséges feloldási, csatlakozási és újrapróbálkozási lépéseket is kezeli.

## <a name="prepare-for-disaster-recovery"></a>Felkészülés vészhelyreállításra
A magas rendelkezésre állás megvalósításának kritikus része annak biztosítása, hogy a szolgáltatások képesek legyenek túlélni az összes különböző típusú hibát. Ez különösen fontos a nem tervezett és a vezérlőn kívüli hibák esetén. [A vész-helyreállítási előkészületek](service-fabric-disaster-recovery.md) olyan gyakori meghibásodási módokat ismertetnek, amelyek a modellezés és a kezelés nem megfelelő kezelése esetén lehetnek katasztrófák. Emellett azt is ismerteti, hogy milyen kockázatcsökkentő és műveletek történnek, ha a katasztrófa amúgy is történt.

## <a name="production-readiness-checklist"></a>Termelési készenlét ellenőrzőlistája
Készen áll az alkalmazás és a fürt a termelési forgalom elvégzésére? Mielőtt üzembe helyezné a fürtöt az éles környezetben, futtassa az [éles készültségi ellenőrzőlistát](service-fabric-production-readiness-checklist.md). Az alkalmazás és a fürt zökkenőmentesen működik az ellenőrzőlista elemeinek használatával. Erősen ajánlott az összes ilyen elemet kijelölni az éles környezetben való üzembe helyezés előtt.

## <a name="next-steps"></a>Következő lépések
* [Windows rendszerű Service Fabric-fürt létrehozása](./service-fabric-best-practices-security.md)
* [Linux rendszerű Service Fabric-fürt létrehozása](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster
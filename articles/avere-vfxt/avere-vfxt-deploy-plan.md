---
title: A avere vFXT-rendszerek megtervezése – Azure
description: Tervezze meg az igényeinek megfelelő avere-vFXT az Azure-fürthöz. Ismerkedjen meg az Azure piactéren vagy a virtuális gépek létrehozása előtt feltett kérdésekre.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: 9f852ec056f3252005bba0bc142c05a9cfbe6c20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92342399"
---
# <a name="plan-your-avere-vfxt-system"></a>Az Avere vFXT rendszer megtervezése

Ez a cikk azt ismerteti, hogyan tervezhet olyan új avere-vFXT az Azure-fürthöz, amelyet az igényeinek megfelelően kell elhelyezni és méretezni.

Az Azure Marketplace-en vagy a virtuális gépek létrehozása előtt vegye figyelembe a következő adatokat:

* Hogyan fog működni a fürt más Azure-erőforrásokkal?
* Hol kell elhelyezni a fürt elemeit a magánhálózatok és az alhálózatok között?
* Milyen típusú háttérbeli tárterületet fog használni, és hogyan fogja elérni a fürtöt?
* Milyen nagy teljesítményű csomópontok szükségesek a munkafolyamatok támogatásához?

További információért olvassa el a következőt:.

## <a name="learn-the-components-of-the-system"></a>A System összetevőinek megismerése

A tervezés megkezdése során hasznos lehet az Azure-rendszer avere-vFXT összetevőinek megismerése.

* Fürtcsomópontok – a fürt három vagy több, fürtcsomópontokként konfigurált virtuális gépről áll. Több csomópont biztosítja a rendszer nagyobb átviteli sebességét és nagyobb gyorsítótárat.

* Gyorsítótár – a gyorsítótár kapacitása egyenlően oszlik meg a fürtcsomópontok között. A fürt létrehozásakor adja meg a csomópontok gyorsítótárának méretét; a rendszer hozzáadja a csomópontok méretét a gyorsítótár teljes méretéhez.

* Fürt vezérlője – a fürt vezérlője egy másik virtuális gép, amely ugyanabban az alhálózatban található, mint a fürtcsomópontok. A vezérlő a fürt létrehozásához és a folyamatban lévő felügyeleti feladatokhoz szükséges.

* Háttérbeli tároló – a gyorsítótárazni kívánt adatai tárolási vagy Azure Blob-tárolóban hosszú távon tárolódnak. A tárolót az Azure-fürthöz tartozó avere-vFXT létrehozása után is hozzáadhatja, vagy a blob Storage használatával felveheti és konfigurálhatja a tárolót a fürt létrehozásakor.

* Ügyfelek – a gyorsítótárazott fájlokat használó ügyfélszámítógépek a virtuális fájl elérési útját használva csatlakoznak a fürthöz ahelyett, hogy közvetlenül a Storage rendszerhez férnek hozzá. (További információ: [a avere vFXT-fürt csatlakoztatása](avere-vfxt-mount-clients.md).)

## <a name="subscription-resource-group-and-network-infrastructure"></a>Előfizetés, erőforráscsoport és hálózati infrastruktúra

Vegye figyelembe, hogy az Azure-beli üzembe helyezés avere-vFXT elemei lesznek. Az alábbi ábrán az Azure-összetevőkre vonatkozó avere-vFXT lehetséges elrendezése látható:

![Egy alhálózaton belüli tartományvezérlőt és fürtözött virtuális gépeket bemutató diagram. Az alhálózati határ körül egy vnet határ. A vnet belül a Storage szolgáltatás végpontját jelképező hatszög; egy szaggatott nyíllal csatlakozik a vnet kívüli blob-tárolóhoz.](media/avere-vfxt-components-option.png)

Kövesse az alábbi irányelveket a avere vFXT-fürt hálózati infrastruktúrájának megtervezéséhez:

* Hozzon létre egy új előfizetést minden avere-vFXT az Azure-beli üzembe helyezéshez. Az előfizetés összes összetevőjének kezelése.

  Az új előfizetések használatának előnyei a következők:
  * Egyszerűbb költségek követése – az erőforrások, az infrastruktúra és a számítási ciklusok összes költségét egy előfizetésben tekintheti meg és naplózhatja.
  * Egyszerűbb tisztítás – a projekt befejezése után eltávolíthatja a teljes előfizetést.
  * Az erőforrás-kvóták kényelmes particionálása – a avere vFXT-ügyfelek és-fürtök elkülönítése egyetlen előfizetésben a lehetséges erőforrás-szabályozásból származó egyéb kritikus számítási feladatok elleni védelem érdekében. Ez az elkülönítés megakadályozza az ütközést, ha nagy mennyiségű ügyfelet hoz létre egy nagy teljesítményű számítástechnikai munkafolyamathoz.

* Keresse meg az ügyfél számítási rendszereit a vFXT-fürthöz közeledve. A háttérbeli tárterület több távoli is lehet.  

* Keresse meg a vFXT-fürtöt és a fürt vezérlő virtuális gépet – pontosabban a következőket kell tennie:

  * Ugyanabban a virtuális hálózatban
  * Ugyanabban az erőforráscsoporthoz
  * Ugyanazt a Storage-fiókot használja
  
  A fürt létrehozási sablonja a legtöbb esetben kezeli ezt a konfigurációt.

* A fürtnek a saját alhálózatán kell lennie, hogy elkerülje az IP-címek ütközését az ügyfelekkel vagy más számítási erőforrásokkal.

* A fürt létrehozási sablonja segítségével hozza létre a fürthöz szükséges infrastruktúra-erőforrások nagy részét, beleértve az erőforráscsoportokat, a virtuális hálózatokat, az alhálózatokat és a Storage-fiókokat.

  Ha már meglévő erőforrásokat szeretne használni, győződjön meg arról, hogy megfelelnek az ebben a táblázatban szereplő követelményeknek.

  | Erőforrás | Meglévőt használ? | Követelmények |
  |----------|-----------|----------|
  | Erőforráscsoport | Igen, ha üres | Üresnek kell lennie|
  | A(z) | **Igen** , ha a fürt létrehozása után csatlakoztat egy meglévő BLOB-tárolót <br/>  **Nem** , ha új BLOB-tárolót hoz létre a fürt létrehozása során | A meglévő blob-tárolónak üresnek kell lennie <br/> &nbsp; |
  | Virtuális hálózat | Yes | Új Azure Blob-tároló létrehozásakor tartalmaznia kell egy tárolási szolgáltatási végpontot |
  | Alhálózat | Yes | Nem szerepelhet más erőforrás |

## <a name="ip-address-requirements"></a>IP-címekre vonatkozó követelmények

Győződjön meg arról, hogy a fürt alhálózata elég nagy IP-címtartományt tartalmaz a fürt támogatásához.

A avere vFXT-fürt a következő IP-címeket használja:

* Egy fürt felügyeleti IP-címe. Ez a címe szükség szerint áthelyezhető a csomópontról a fürt csomópontjaira, hogy mindig elérhető legyen. Ezzel a címtől csatlakozhat a avere Vezérlőpult-konfigurációs eszközhöz.
* Minden fürtcsomóponton:
  * Legalább egy ügyfél felé irányuló IP-cím. (Az összes ügyfél felé irányuló címet a fürt *VServer* kezeli, és szükség szerint áthelyezheti az IP-címeket a csomópontok között.)
  * Egy IP-cím a fürt kommunikációjához
  * Egy példány IP-címe (a virtuális géphez rendelve)

Ha az Azure Blob Storage-t használja, a fürt virtuális hálózata IP-címeit is megkövetelheti:  

* Az Azure Blob Storage-fiókhoz legalább öt IP-cím szükséges. Tartsa szem előtt ezt a követelményt, ha a blob Storage-t a fürttel azonos virtuális hálózatban találja.
* Ha a fürt virtuális hálózatán kívüli Azure Blob Storage-t használ, hozzon létre egy tárolási szolgáltatási végpontot a virtuális hálózaton belül. A végpont nem használ IP-címet.

Lehetősége van arra, hogy megkeresse a hálózati erőforrásokat és a BLOB-tárolót (ha használatban van) a fürt különböző csoportjaiban.

## <a name="vfxt-node-size"></a>vFXT-csomópont mérete

A fürtcsomópontokként szolgáló virtuális gépek határozzák meg a gyorsítótár kérésének átviteli sebességét és tárolókapacitását. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Minden vFXT-csomópont azonos lesz. Ez azt eredményezi, hogy ha három csomópontos fürtöt hoz létre, akkor három azonos típusú és méretű virtuális géppel kell rendelkeznie.

| Példány típusa | vCPU-k | Memória  | Helyi SSD-tároló  | Adatlemezek max. száma | Nem gyorsítótárazott lemez sebessége | Hálózati adapter (darabszám) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51 200 IOPS <br/> 768 MBps | 16 000 MBps (8)  |

A lemezes gyorsítótár/csomópont konfigurálható, és 1000 GB és 8000 GB között lehet a düh. a Standard_E32s_v3-csomópontok esetében 4 TB/csomópont a javasolt gyorsítótár mérete.

További információ ezekről a virtuális gépekről: a memória- [optimalizált virtuálisgép-méretek](../virtual-machines/sizes-memory.md) Microsoft Azure dokumentációban olvashatók.

## <a name="account-quota"></a>Fiók kvótája

Győződjön meg arról, hogy az előfizetése rendelkezik a avere vFXT-fürt futtatásához szükséges kapacitással, valamint a használatban lévő számítástechnikai vagy ügyfélkapcsolati rendszerekkel. A részletekért olvassa el [a vFXT-fürtre vonatkozó kvótát](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) .

## <a name="back-end-data-storage"></a>Háttérbeli adattárolás

A háttérbeli tárolási rendszerek a fürt gyorsítótárában is megadják a fájlokat, és a gyorsítótárból is megkapják a módosult adatmennyiséget. Döntse el, hogy a munkakészletet egy új blob-tárolóban, vagy egy meglévő tárolási rendszeren (felhőben vagy hardverben) tárolja-e a hosszú ideig. Ezeket a háttérbeli tárolási rendszereket *alapszintű Filer*-nek nevezzük.

### <a name="hardware-core-filers"></a>Hardveres alapszintű filers

A fürt létrehozása után vegyen fel hardveres tárolási rendszereket a vFXT-fürtbe. Számos népszerű hardvereszközt használhat, beleértve a helyszíni rendszereket is, feltéve, hogy a tárolási rendszer elérhető a fürt alhálózatáról.

A [tároló konfigurálása](avere-vfxt-add-storage.md) című cikk részletesen ismerteti, hogyan adhat hozzá meglévő tárolási rendszereket a avere vFXT-fürthöz.

### <a name="cloud-core-filers"></a>Cloud Core filers

Az Azure rendszerhez készült avere-vFXT üres blob-tárolókat használhat a háttérbeli tároláshoz. A tárolónak üresnek kell lennie a fürthöz való hozzáadáskor – a vFXT rendszernek képesnek kell lennie az objektum-tároló felügyeletére anélkül, hogy meg kellene őriznie a meglévőket.

> [!TIP]
> Ha az Azure Blob Storage-t szeretné használni a háttérrendszer számára, hozzon létre egy új tárolót a vFXT-fürt létrehozása részeként. A fürt létrehozási sablonja létrehozhat és konfigurálhat egy új BLOB-tárolót, hogy az készen álljon a használatra, amint a fürt elérhetővé válik. A tárolók későbbi hozzáadása bonyolultabb.
>
> A részletekért olvassa el [Az Azure avere-VFXT létrehozása](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) című cikkét.

Miután hozzáadta az üres blob Storage-tárolót alapszintű Filer-ként, a fürtön keresztül másolhatja az adatfájlokat. Párhuzamos, többszálú másolási mechanizmus használata. Olvassa el [az adatáthelyezés a vFXT-fürtbe](avere-vfxt-data-ingest.md) című témakört, amelyből megtudhatja, hogyan másolhat hatékonyan a fürt új tárolóján az ügyfélszámítógépek és a avere vFXT-gyorsítótár használatával.

## <a name="cluster-access"></a>Fürthöz való hozzáférés

Az Azure-fürthöz tartozó avere-vFXT egy privát alhálózatban található, és a fürt nem rendelkezik nyilvános IP-címmel. A fürt felügyeletéhez és az ügyfélkapcsolatokhoz valamilyen módon hozzá kell férnie a privát alhálózathoz.

A hozzáférési lehetőségek a következők:

* Beugrási gazdagép – rendeljen egy nyilvános IP-címet egy különálló virtuális géphez a magánhálózaton belül, és használja azt egy TLS-alagút létrehozásához a fürtcsomópontok számára.

  > [!TIP]
  > Ha nyilvános IP-címet állít be a fürt vezérlőjén, használhatja azt a Jump hostként. További információért olvassa el a [fürtszolgáltatást Jump hostként](#cluster-controller-as-jump-host) .

* Virtuális magánhálózat (VPN) – pont – hely vagy helyek közötti VPN konfigurálása az Azure-ban és a vállalati hálózatokban lévő magánhálózat között.

* Azure ExpressRoute – privát kapcsolat konfigurálása ExpressRoute-partneren keresztül.

Ezekről a lehetőségekről az [internetes kommunikációval kapcsolatos Azure Virtual Network dokumentációban](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet)olvashat bővebben.

### <a name="cluster-controller-as-jump-host"></a>A fürt tartományvezérlője Jump hostként

Ha egy nyilvános IP-címet állít be a fürt vezérlőjén, akkor használhatja a avere vFXT-fürtöt a privát alhálózaton kívülről. Mivel azonban a vezérlő rendelkezik hozzáférési jogosultságokkal a fürtcsomópontok módosításához, ez egy kisebb biztonsági kockázatot jelent.

Egy nyilvános IP-címmel rendelkező vezérlő biztonságának növelése érdekében az üzembe helyezési parancsfájl automatikusan létrehoz egy hálózati biztonsági csoportot, amely kizárólag a 22-es portra korlátozza a bejövő hozzáférést. A rendszer további védettségét úgy biztosíthatja, hogy leállítja az IP-címek tartományához való hozzáférést, azaz csak a fürt eléréséhez használni kívánt gépek kapcsolatait engedélyezi.

A fürt létrehozásakor megadhatja, hogy létre kell-e hozni egy nyilvános IP-címet a fürt vezérlőjén.

* Ha **új virtuális hálózatot** vagy **új alhálózatot** hoz létre, a rendszer egy **nyilvános** IP-címet rendel hozzá a fürthöz.
* Ha egy meglévő virtuális hálózatot és alhálózatot választ ki, akkor a fürt csak **privát** IP-címmel rendelkezik.

## <a name="vm-access-roles"></a>VIRTUÁLIS gépek hozzáférési szerepkörei

Az Azure az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/index.yml) használatával engedélyezi a fürt virtuális gépei számára bizonyos feladatok elvégzését. A fürthöz tartozó virtuális gépek létrehozásához és konfigurálásához például engedélyeznie kell a fürtöt. A fürtcsomópontok számára lehetővé kell tenni az IP-címek hozzárendelését vagy más fürtcsomópontok számára való hozzárendelését.

A avere vFXT virtuális gépek két beépített Azure-szerepkört használnak:

* A fürt a beépített szerepkör [avere közreműködőjét](../role-based-access-control/built-in-roles.md#avere-contributor)használja.
* A fürtcsomópontok a beépített szerepkör [avere operátort](../role-based-access-control/built-in-roles.md#avere-operator)használják.

Ha testre kell szabnia a avere vFXT-összetevők hozzáférési szerepköreit, meg kell adnia a saját szerepkörét, majd hozzá kell rendelnie a virtuális gépekhez a létrehozásuk időpontjában. A központi telepítési sablon nem használható az Azure piactéren. A Microsoft ügyfélszolgálatának és támogatásának megismeréséhez nyisson meg egy jegyet a Azure Portal a [Segítség kérése a rendszerhez](avere-vfxt-open-ticket.md)című témakörben leírtak szerint.

## <a name="next-steps"></a>Következő lépések

Az [üzembe helyezés áttekintése](avere-vfxt-deploy-overview.md) az Azure-rendszer avere-vFXT létrehozásához és az adatkiszolgáláshoz való felkészüléshez szükséges lépések áttekintését nyújtja.
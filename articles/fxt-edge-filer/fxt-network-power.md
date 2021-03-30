---
title: 'Oktatóanyag: Kábelek csatlakoztatása egy Azure FXT Edge Filer-hoz'
description: Hálózati portok csatlakoztatása és az Azure FXT Edge Filer-hardver áramellátásának csatlakoztatása
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 80458d99ed6ad19c8a837f0989798d4dcc156b61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219530"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Oktatóanyag: hálózati kapcsolatok létrehozása és áramellátás az Azure FXT Edge Filer-csomóponthoz

Ez az oktatóanyag azt ismerteti, hogyan lehet hálózati kapcsolatokat létesíteni egy Azure FXT Edge Filer-hardveres csomóponthoz.

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
>
> * Hálózati kábel típusának kiválasztása a környezethez
> * Azure FXT Edge Filer-csomópont összekötése az adatközpont-hálózathoz
> * Kábelek továbbítása a kábeles felügyeleti ARM használatával (CMA)
> * A csatlakoztatott eszköz áramellátásának csatlakoztatása és bekapcsolása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt az Azure FXT Edge Filer-t a standard Equipment rackbe kell telepíteni. A CMA a Filer csomópontra kell telepíteni.

## <a name="identify-ports"></a>Portok azonosítása

Azonosítsa az Azure FXT Edge-Filer hátoldalán található különféle portokat.

![Kábeles eszköz hátoldala](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Az eszköz bekábelezése

* A [hálózati portok](#network-ports)című témakörben leírtak szerint kapcsolja össze az RJ-45 portokat az adatközpont hálózati forrásával.  
* Biztonságos DHCP-kiszolgálóval biztonságosan összekapcsolhatja a [iDRAC-portot](#idrac-port) egy különálló hálózattal.
* Az USB-portok és a VGA-port használatával kapcsolja össze a billentyűzetet és a figyelőt a csomóponthoz a kezdeti beállításhoz. A csomópont többi portjának aktiválásához el kell indítania a csomópontot, és [be kell állítania egy kezdeti jelszót](fxt-node-password.md) . A részletekért olvassa el az [első jelszavak beállítása](fxt-node-password.md) című leírást.

Ez a cikk azt is ismerteti, hogyan lehet [csatlakoztatni a hálózati](#connect-power-cables) adaptert a csomóponthoz.

A cikk azt is ismerteti, hogyan csatlakozhat a csomópont [soros portjához](#serial-port-only-when-necessary), ha szükséges a speciális hibaelhárításhoz.

### <a name="network-ports"></a>Hálózati portok

Minden egyes Azure FXT Edge Filer-csomópont a következő hálózati portokat tartalmazza:

* Hat nagy sebességű 25GbE/10 GbE Dual Rate adatport:

  * Négy portot biztosít két kétportos beépülőmodul-hálózati adapter
  * Az alapszintű hálózati adapter két portot biztosít

* Az alapszintű hálózati adapter által biztosított két 1 GbE-port

A nagy sebességű 25GbE/10 GbE adatportok standard SFP28-kompatibilis ketrecekkel rendelkeznek. Az optikai kábelek használatához telepítenie kell a SFP28 Optical adóvevő modulokat (nincs megadva).

A 1 GbE-portok standard RJ-45 összekötővel rendelkeznek.

A támogatott kábelek, kapcsolók és adóvevők teljes listájáért olvassa el a [Cavium FastlinQ 41000 Series együttműködési mátrixot](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).

A rendszerhez használandó kapcsolatok típusa az adatközpont-környezettől függ.

* Ha 25GbE hálózathoz csatlakozik, a nagy sebességű adatportok mindegyikét a következő típusú kábelek egyikével kell csatlakoztatni:

  * Optikai kábel-és SFP28 optikai adóvevő 25GbE vagy kettős sebességű 25GbE/10 GbE képességgel
  * SFP28-típus 25GbE-kompatibilis közvetlen csatolású twinaxial-kábel

* Ha 10 GbE hálózathoz csatlakozik, a nagy sebességű adatportok mindegyikét a következők egyikével csatlakoztathatja:

  * Optikai kábel-és SFP28 optikai adóvevő 10 GbE vagy kettős sebességű 25GbE/10 GbE képességgel.
  * SFP28-típus 25GbE-kompatibilis közvetlen csatolású twinaxial-kábel
  * SFP28-típus 10 GbE-kompatibilis közvetlen csatolású twinaxial-kábel

* A 1 GbE hálózati portok a fürt felügyeleti forgalmához használatosak. Ellenőrizze az **1 GB-os mgmt hálózati beállítás használatakor** a fürt létrehozásakor, ha egy fizikailag különálló hálózatot szeretne létrehozni a fürt konfigurációjához (lásd: [a felügyeleti hálózat konfigurálása](fxt-cluster-create.md#configure-the-management-network)). A portokat a támogatott kábelek listájában leírtak szerint csatlakoztassa a standard CAT5 vagy a jobb kábellel.

  Ha azt tervezi, hogy a nagy sebességű portokat használja az összes forgalomhoz, a 1 GbE portok nem jelennek meg a kábelen. Alapértelmezés szerint a rendszer nem használja a 1 GbE hálózati portokat, ha a nagyobb sebességű adatport elérhető.  

### <a name="idrac-port"></a>iDRAC-port  

A iDRAC címkével ellátott port egy 1 GB-os kapcsolat, amely lehetővé teszi a kommunikációt a hardverek felügyeletéhez és figyeléséhez használt távelérési vezérlővel. A FXT szoftver az intelligens platform Management felületét (IPMI) használja a vezérlővel kapcsolatos hibaelhárításhoz és helyreállításhoz. A beépített [iDRAC felület](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) segítségével figyelheti a hardvert ezen a porton keresztül. a iDRAC és a IPMI hozzáférés alapértelmezés szerint engedélyezve van.

> [!Note]
> A iDRAC-port megkerülheti az operációs rendszert, és közvetlenül a csomóponton található hardverekkel tud kommunikálni.

Ezeket a biztonsági stratégiákat a iDRAC-port csatlakoztatásához és konfigurálásához használhatja:

* Csak a fürthöz való hozzáféréshez használt adathálózatból fizikailag elkülönített iDRAC-portokat csatlakoztasson a hálózathoz.
* Minden csomóponton állítson be biztonságos iDRAC rendszergazdai jelszót. Ezt a jelszót kell megadnia, hogy aktiválja a hardver – kövesse a hardver [jelszavainak beállítása](fxt-node-password.md)című témakört.
* Az alapértelmezett iDRAC-port konfigurációja DHCP és IPv4 protokollt használ az IP-címek hozzárendeléséhez. Győződjön meg arról, hogy a DHCP-környezet jól védett, és hogy a kapcsolatok korlátozottak a DHCP-ügyfelek és a DHCP-kiszolgáló között. (A fürt vezérlőpultja olyan beállításokat tartalmaz, amelyekkel a fürt létrehozása után módosíthatja a csomópontok címe konfigurációs módszerét.)
* Hagyja a iDRAC portot "dedikált módra" (ez az alapértelmezett beállítás), amely a dedikált RJ-45 portra korlátozza a iDRAC/IPMI hálózati forgalmat.

A iDRAC-porthoz nincs szükség nagy sebességű hálózati csatlakozásra.
  
### <a name="serial-port-only-when-necessary"></a>Soros port (csak szükség esetén)

Bizonyos helyzetekben a Microsoft szolgáltatás és támogatás azt is megtudhatja, hogy egy terminált egy csomópont soros portjához csatlakoztasson a probléma diagnosztizálásához.

A konzol csatlakoztatása:

1. Keresse meg a soros (COM1) portot a FXT Edge Filer-csomópontjának hátulján.
1. A soros portot az ANSI-115200-8N1 konfigurált terminálhoz egy NULL értékű modemes kábellel csatlakoztassa.
1. Jelentkezzen be a konzolra, és hajtson végre további lépéseket a támogatási munkatársak utasításai szerint.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Kábelek továbbítása a kábel-felügyeleti ARM-ban (CMA)

Minden Azure FXT Edge Filer-csomópont opcionális kábelkapcsolat-felügyeleti ARM-t tartalmaz. A CMA leegyszerűsíti a kábeles útválasztást, és a kábelek leválasztása nélkül könnyebben elérhetővé teszi az alváz hátoldalát.

Kövesse ezeket az utasításokat a kábelek CMA való átirányításához:

1. A megadott döntetlen-körbefuttatások használatával a kábeleket a beléptetésük és a kosárból való kilépésük után csomagolja ki, hogy ne zavarja a szomszédos rendszereket (1).
1. A szolgáltatás pozíciójában a CMA a belső és külső kosaran keresztül irányítja a kábelt (2).
1. A (3) kábelek biztonságossá tételéhez használja az előtelepített Hook-és loop-szíjat a kosarak bármelyik végén.
1. Vigye vissza a CMA a tálcán (4).
1. Telepítse az állapotjelző kábelt a számítógép hátulján, és gondoskodjon arról, hogy a CMA keresztül irányítsa át a kábelt. Csatlakoztassa a kábel másik végét a külső CMA-kosár sarkához (5).

   > [!CAUTION]
   > Ha el szeretné kerülni a kábelek kitartásával kapcsolatos esetleges károkat, gondoskodjon róla, hogy a kábel a CMA keresztül történő útválasztása után az állapotjelző kábelen lévő összes tartalékidőt védje.

![CMA ábrája telepített kábelekkel](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
> Ha nem telepítette a CMA, használja a Rail csomagban található két Hook-és loop-pántot a kábelek a rendszer hátulján való átirányításához.
>
>  1. Keresse meg a külső CMA szögletes zárójeleit mindkét rack karimák belső oldalain.
>  2. A kábelek csatlakoztatása óvatosan, a bal és a jobb oldalon lévő rendszerösszekötők törlésével.
>  3. A hookok és a hurkok a rendszer egyes oldalain lévő külső CMA-szögletes zárójeleken keresztül, a kábeles csomagok biztonságossá tételéhez.
>
>     ![CMA nélkül átirányított kábelek](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>Az IP-címekre vonatkozó követelmények

Az Azure FXT Edge-tárolók hibrid tárolási gyorsítótárában lévő hardveres csomópontok esetén az IP-címeket a fürt szoftvere kezeli.

Mindegyik csomóponthoz legalább egy IP-cím szükséges, de a csomópontok hozzárendelése akkor történik meg, amikor a fürthöz hozzáadják vagy eltávolítják a csomópontokat.

A szükséges IP-címek teljes száma a gyorsítótárat alkotó csomópontok számától függ.

Konfigurálja az IP-címtartományt a Vezérlőpult szoftver használatával a csomópontok telepítése után. További információért olvassa el [a fürt adatainak összegyűjtése](fxt-cluster-create.md#gather-information-for-the-cluster)című témakört.  

## <a name="connect-power-cables"></a>Energiaellátási kábelek csatlakoztatása

Mindegyik Azure FXT Edge Filer-csomópont két tápegységet (PSUs) használ.

> [!TIP]
> Ahhoz, hogy kihasználhassa a két redundáns PSUs előnyeit, csatolja az egyes hálózati adaptereket egy energiagazdálkodási egységhez (PDU) egy független ág-áramkörön.
>
> A UPS használatával további védelmet biztosíthat a PDU számára.

1. A mellékelt tápkábelek csatlakoztatása a tápegységekhez az alvázon. Győződjön meg arról, hogy a zsinórok és a tápegységek teljes mértékben vannak összefoglalva.
1. Csatlakoztassa a tápkábeleket a berendezések állványon található energiagazdálkodási egységekhez. Ha lehetséges, használjon két különálló áramforrást a két vezeték számára.

### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Az Azure FXT Edge Filer-csomópontjának bekapcsolása

A csomópont bekapcsolásához nyomja meg a rendszer elején található főkapcsoló gombot. A gomb a jobb oldali panelen található.

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Azure FXT Edge Filer-csomópont kikapcsolása

A főkapcsoló gomb használatával leállíthatja a rendszer a tesztelés során, és még a fürthöz való hozzáadása előtt. Ha azonban egy Azure FXT Edge Filer-csomópont már használatban van egy fürt részeként, akkor a fürt Vezérlőpultjának szoftverét kell használnia a hardver leállításához. A részletekért olvassa el az [Azure FXT Edge Filer Hardver biztonságos](fxt-power-off.md) kikapcsolását ismertető témakört.

## <a name="next-steps"></a>Következő lépések

Miután befejezte a hardver kábelezését, kapcsolja be mindegyik csomópontot, majd inicializálja őket a legfelső szintű jelszavaik beállításával.
> [!div class="nextstepaction"]
> [Kezdeti jelszavak beállítása](fxt-node-password.md)

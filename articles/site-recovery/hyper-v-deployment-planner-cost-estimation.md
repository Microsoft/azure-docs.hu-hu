---
title: Tekintse át az Azure Site Recovery Deployment Planner költségbecslési jelentést a Hyper-V virtuális gépek vészhelyreállítása az Azure cost |} A Microsoft Docs
description: Ez a cikk ismerteti a költségek áttekintése költségbecslési jelentést hoz létre az Azure Site Recovery Deployment Planner a Hyper-V vész-helyreállítási az Azure-bA.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: bced6a9e6c59dc32657dbabef986e29e0447b28b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60947226"
---
# <a name="cost-estimation-report-by-azure-site-recovery-deployment-planner"></a>Az Azure Site Recovery Deployment Planner költségbecslési jelentése 

Az Azure Site Recovery Deployment Planner jelentése a [Javaslatok](hyper-v-deployment-planner-analyze-report.md#recommendations) táblázatokban költségbecslési összefoglalást, a Költségbecslés táblázatban pedig részletes költségelemzést biztosít. Részletes költségelemzést tartalmaz virtuális gépenként. 

### <a name="cost-estimation-summary"></a>Költségbecslés összefoglalása 
Az ábra az Azure-ba irányuló vészhelyreállítás (DR) becsült teljes költségének összefoglaló nézetét jeleníti meg a jelentéskészítéshez megadott pénznemben és célrégióra vonatkozóan.

![Költségbecslés összefoglalása](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation-summary-h2a.png)

Az összefoglalás segít áttekinteni a tárolás, számítás, hálózat és licenc költségeit, amelyet akkor kell fizetnie, ha kompatibilis virtuális gépeit az Azure Site Recovery használatával védi. A költségek kiszámítása a kompatibilis virtuális gépek, nem pedig a profilkészítésben részt vevő virtuális gépek alapján történik. 
 
A költségeket havi vagy éves bontásban tekintheti meg. További információkat olvashat a [támogatott célrégiókról](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) és a [támogatott pénznemekről](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies).

**Összetevők szerinti költségek**: A Vészhelyreállítás teljes költsége négy összetevőből áll: számítási, tárolási, hálózati és a Site Recovery licencköltségei. A költségeket a használat alapján számoljuk ki, amely költségek a replikáció során és a DR működési ideje során jelentkeznek. A számítás a számítási igény, a tárterület (prémium és standard), a helyszíni hely és az Azure között konfigurált ExpressRoute/VPN, valamint a Site Recovery-licenc figyelembe vételével történik.

**Állapot szerinti költségek**: A teljes vész-helyreállítási költségeinek kategóriái van két különböző állapoton alapulnak: replikáció és Dr. 

**Replikáció költségei**: A replikáció során felmerülő költségek. Ez fedezi a tárolás, a hálózat és a Site Recovery-licenc költségeit. 

**Dr költsége**: A tesztek során felmerülő költségek. A Site Recovery virtuális gépeket indít el a feladatátvételi tesztek során. A DR működési költségei a futó virtuális gépek számítási és tárolási költségeit fedezik. 

**Az Azure storage költsége / hónap/év**: A prémium és standard tárterület esetén a replikáció és Dr felmerülő tárterület teljes költségeit.

## <a name="detailed-cost-analysis"></a>Részletes költségelemzés
Az Azure számítási, tárolási és hálózati díjszabása Azure-régiónként eltérő. Létrehozhat egy költségbecslési jelentést a legújabb Azure-árakkal az előfizetése alapján az előfizetéséhez tartozó ajánlat figyelembe vételével, az adott cél Azure-régióhoz a megadott pénznemben. Alapértelmezés szerint az eszköz Azure-régióként az USA 2. nyugati régióját, pénznemként pedig az amerikai dollárt (USD) használja. Ha bármely más régiót vagy pénznemet használ, a következő alkalommal, amikor előfizetés-azonosító, ajánlatazonosító, célrégió és pénznem nélkül hoz létre jelentést, az eszköz a legutóbb használt célrégió díjszabását és a legutóbb használt pénznemet fogja felhasználni a költségbecsléshez.

Ebben a szakaszban a jelentés létrehozásához használt előfizetés-azonosítót és ajánlatazonosítót tekintheti meg. Ha ezek nincsenek használatban, a mezők üresek.

A jelentésben a szürkével jelölt cellák írásvédettek. A fehér színű cellák igény szerint módosíthatók.

![A költségbecslés részletei](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-costs-by-components"></a>A vészhelyreállítás teljes költsége összetevőnként
Az első szakaszban a teljes vészhelyreállítási költség látható összetevőnként, valamint a vészhelyreállítási költség állapotonként. 

**COMPUTE**: A Vészhelyreállítási igényekhez szükséges Azure-on futtatott IaaS virtuális gépek költsége. A Site Recovery által a DR működése (feladatátvételi tesztek) során létrehozott virtuális gépeket tartalmazza. Ezenkívül tartalmazza az Azure-on futó virtuális gépeket is (például SQL Server Always On rendelkezésre állási csoportokkal és tartományvezérlőkkel vagy tartományi névkiszolgálókkal).

**Tárolási**: A Vészhelyreállítási igényekhez szükséges Azure-tárhelyhasználat költségei. A replikáció és a DR működés tárhelyhasználatát tartalmazza.

**Hálózati**: ExpressRoute- és helyek közötti VPN költségei a Vészhelyreállítási igényekhez szükséges. 

**Az Azure Site Recovery-licenc**: A Site Recovery-licenc költségei minden kompatibilis virtuális gépek. Ha manuálisan adta meg a virtuális gépet a részletes költségelemzési táblában, akkor a tábla a virtuális gép Site Recovery-licencköltségeit is tartalmazza.

### <a name="overall-dr-costs-by-states"></a>A vészhelyreállítás teljes költsége állapotonként
A vészhelyreállítás (DR) teljes költségének kategóriái két különböző állapoton alapulnak – replikáció és a DR működése.

**Replikációs**: A replikáció során felmerült költségek. Ez fedezi a tárolás, a hálózat és a Site Recovery-licenc költségeit. 

**Dr működésének**: A DR működése során felmerült költségek. A Site Recovery virtuális gépeket indít el a DR működése során. A DR működési költségei a futó virtuális gépek számítási és tárolási költségeit fedezik.

* A DR teljes működési időtartama egy évben = DR működéseinek száma x DR működéseinek időtartama (napokban)
* DR működésének átlagos költsége (havonta) = DR működésének teljes költsége / 12

### <a name="storage-cost-table"></a>A tárolási költségek táblázata
Ez a táblázat a replikáció és a DR működése során használt prémium és standard tárterület költségeit mutatja kedvezményekkel és kedvezmények nélkül.

### <a name="site-to-azure-network"></a>Hely–Azure hálózat
Válassza ki az igényeinek megfelelő beállítást. 

**ExpressRoute:** Alapértelmezés szerint az eszköz kiválasztja a legközelebbi ExpressRoute-tervet, amely megfelel a változásreplikációhoz szükséges hálózati sávszélességet. Igény szerint módosíthatja a tervet.

**VPN Gateway típusa**: Válassza ki az Azure VPN Gatewayen, ha rendelkezik ilyennel a környezetében. Alapértelmezés szerint NA értékű.

**Célrégió**: A Vészhelyreállításhoz megadott Azure régió. A jelentésben a számításhoz, tároláshoz, hálózathoz és licenchez használt árak a régió Azure-díjszabásán alapulnak. 

### <a name="vm-running-on-azure"></a>Az Azure-ban futó virtuális gép
Ha az Azure-ban tartományvezérlő, DNS virtuális gép vagy Always On rendelkezésre állási csoportokkal rendelkező, SQL Server rendszerű virtuális gép fut vészhelyreállítási céllal, megadhatja a virtuális gépek számát és méretét, hogy azok számítási költségei is figyelembe legyenek véve a vészhelyreállítás teljes költségeiben. 

### <a name="apply-overall-discount-if-applicable"></a>Általános kedvezmény alkalmazása, ha van
Ha Ön Azure-partner vagy -ügyfél, és a teljes Azure-díjszabásból bármilyen kedvezményre jogosult, használja ezt a mezőt. Az eszköz minden összetevőre alkalmazza a kedvezményt (százalékban).

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>A virtuális gépek típusainak száma és a számítási költségek (évente)
Ez a táblázat a Windows és nem Windows rendszerű virtuális gépek számát és a hozzájuk tartozó DR működés számítási költségeit tartalmazza.

### <a name="settings"></a>Beállítások 
**Felügyelt lemez használata**: Ez a beállítás megadja, hogy egy felügyelt lemezt: a DR működése során. Az alapértelmezett érték az **Igen**. Ha a **-UseManagedDisks** lehetőséget **Nem** értékre állította, a rendszer a nem felügyelt lemezek díjszabását használja a költségek kiszámításához.

**Pénznem**: A pénznem, amelyben a jelentéskészítés.

**Költség időtartama**: A havi vagy az egész évre vonatkozó összes költséget tekintheti meg. 

## <a name="detailed-cost-analysis-table"></a>Részletes költségelemzési táblázat
![Részletes költségelemzés](media/hyper-v-azure-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png)

A táblázatban a kompatibilis virtuális gépek lebontott költségeinek listája látható. Ezt a táblázatot a profil nélküli virtuális gépek becsült Azure-beli vészhelyreállítási költségeinek megismeréséhez is használhatja a virtuális gépek manuális hozzáadásával. Ez az adat olyan esetekben hasznos, amikor egy új vészhelyreállítás üzembe helyezésének Azure-költségeit kell megbecsülni részletes profilkészítés nélkül.

Virtuális gépek manuális hozzáadása:

1. Válassza a **Sor beszúrása** lehetőséget egy új sor beszúrásához a **kezdő-** és a **zárósor** közé.

1. Töltse ki az alábbi oszlopokat a virtuális gép hozzávetőleges mérete és a konfigurációnak megfelelő virtuális gépek száma alapján: 

    a. **Virtuális gépek száma**

    b. **IaaS-méret (felhasználó választása)**

    c. **Tárolás típusa (Standard/Prémium)**

    d. **Virtuális gép tárterületének teljes mérete (GB)**

    e. **DR működéseinek száma évente**

    f. **DR egyes működéseinek időtartama (nap)**

    g. **Operációs rendszer típusa**

    h. **Adatredundancia**

    i. **Azure Hybrid Use Benefit**

1. Alkalmazhatja ugyanazokat az értékeket minden virtuális gépre a táblázatban az **Alkalmazás az összesre** lehetőséget kiválasztva a következőknél: **DR működéseinek száma évente**, **DR egyes működéseinek időtartama (nap)**, **Adatredundancia** és az **Azure Hybrid Use Benefit**.

1. A költségek frissítéséhez kattintson a **Költségek újraszámítása** lehetőségre.

**Virtuális gép neve**: A virtuális gép nevét.

**Virtuális gépek száma**: A konfigurációnak megfelelő virtuális gépek száma. Frissítheti a meglévő virtuális gépek számát, ha a hasonló konfigurációjú virtuális gépekről nem készül profil, de védve lesznek.

**IaaS-méret (javaslat)**: A kompatibilis virtuális gép az eszköz által javasolt virtuális gépi szerepkörének mérete. 

**IaaS-méret (felhasználó választása)**: Alapértelmezés szerint a mérete megegyezik a javasolt virtuális gépi szerepkör mérete. Igény szerint módosíthatja a szerepkört. A számítási költség a kiválasztott virtuális gépi szerepkör méretén alapul.

**Tárolási típus**: A virtuális gép által használt tároló típusa. Lehet standard vagy prémium szintű tároló.

**Virtuális gép tárterületének teljes mérete (GB)**: A virtuális gép teljes tárterülete.

**DR működéseinek száma évente**: Hányszor évente végezhet a DR működése során. Alapértelmezés szerint az értéke évente négy. Módosíthatja adott virtuális gépek időszakait, vagy alkalmazhatja az új értéket az összes virtuális gépre. A felső sorba írja be az új értéket, és kattintson az **Alkalmazás az összesre** gombra. A DR működéseinek száma évente és a DR egyes működéseinek időtartama alapján a rendszer kiszámítja a DR működésének teljes költségét. 

**Minden egyes DR működések időtartama (nap)**: Egyes működéseinek időtartama. Alapértelmezés szerint ez 90 naponként 7 nap a [Vészhelyreállítási frissítési garancia előnynek](https://azure.microsoft.com/pricing/details/site-recovery) megfelelően. Módosíthatja adott virtuális gépek időszakait, vagy alkalmazhat egy új értéket az összes virtuális gépre. A felső sorba írja be az új értéket, és kattintson az **Alkalmazás az összesre** gombra. A DR teljes működési költségének kiszámítása a DR évi működéseinek számán és a DR egyes működéseinek időtartamán alapul.
 
**Operációs rendszer típusa**: A virtuális gép operációs rendszerének típusa. Lehet Windows vagy Linux. Ha az operációs rendszer típusa Windows, az Azure Hybrid Use Benefit alkalmazható a virtuális gépre. 

**Adatredundancia**: Helyileg redundáns tárolás, georedundáns tárolás vagy írásvédett georedundáns tárolás lehet. Az alapértelmezett érték a helyileg redundáns tárolás. Módosíthatja a típust adott virtuális gépek tárfiókja alapján, vagy alkalmazhatja az új típust minden virtuális gépre. Ehhez módosítsa a felső sor típusát, és kattintson az **Alkalmazás az összesre** gombra. A replikáció tárolási költsége a kiválasztott adatredundancia ára alapján lesz kiszámítva. 

**Az Azure Hybrid Use Benefit**: Az Azure Hybrid Use Benefit Windows virtuális gépek, alkalmazhatja, ha van ilyen. Az alapértelmezett érték az **Igen**. Módosíthatja adott virtuális gépek beállításait, vagy frissítheti az összes virtuális gépet. Kattintson az **Alkalmazás az összesre** gombra.

**Teljes Azure-használat**: A számítási, tárolási és Site Recovery-licenc költségei a Vészhelyreállításhoz. A választott beállítás alapján a költségeket havi vagy éves bontásban jeleníti meg.

**Stabil állapotú replikáció költsége**: A replikáció tárolási költsége.

**Teljes működéseinek költsége (átlag)**: A DR működése során a számítási és tárolási költségeit.

**Az Azure Site Recovery-licencköltségeit**: A Site Recovery-licenc költsége.

## <a name="supported-target-regions"></a>Támogatott célrégiók
A Site Recovery Deployment Planner a következő Azure-régiókhoz biztosít költségbecslést. Ha a régiója nem található meg ebben a listában, a következő régiók közül használhatja azt, amelynek díjszabása a legközelebb áll az Ön régiójához:

eastus, eastus2, westus, centralus, northcentralus, southcentralus, northeurope, westeurope, eastasia, southeastasia, japaneast, japanwest, australiaeast, australiasoutheast, brazilsouth, southindia, centralindia, westindia, canadacentral, canadaeast, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth 

## <a name="supported-currencies"></a>Támogatott pénznemek
A Site Recovery Deployment Planner az alábbi pénznemek bármelyikének használatával létre tudja hozni a költségjelentést.

|Currency (Pénznem)|Name (Név)||Currency (Pénznem)|Name (Név)||Currency (Pénznem)|Name (Név)|
|---|---|---|---|---|---|---|---|
|ARS|Argentin peso ($)||AUD|Ausztrál dollár ($)||BRL|Brazil real (R$)|
|CAD|Kanadai dollár ($)||CHF|Svájci frank (CHF)||DKK|Dán korona (kr)|
|EUR|Euró (€)||GBP|Angol font (l)||HKD|Hongkongi dollár (HK$)|
|IDR|Indonéz rúpia (Rp)||INR|Indiai rúpia (₹)||JPY|Japán jen y|
|KRW|Koreai won (₩)||MXN|Mexikói peso (MX$)||MYR|Maláj ringgit (RM$)|
|NOK|Norvég korona (kr)||NZD|Új-zélandi dollár ($)||RUB|Orosz rubel (руб)|
|SAR|Szaúdi riál (SR)||SEK|Svéd korona (kr)||TWD|Tajvani új dollár (NT$)|
|TRY|Török líra (TL)||USD| Amerikai dollár ($)||ZAR|Dél-afrikai rand (R)|

## <a name="next-steps"></a>További lépések
Többet tudhat meg az [Azure-ba replikált Hyper-V virtuális gépek védelméről a Site Recoveryvel](hyper-v-azure-tutorial.md).

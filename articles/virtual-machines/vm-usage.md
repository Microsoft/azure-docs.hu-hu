---
title: Az Azure-beli virtuális gépek használatának ismertetése
description: A virtuális gépek használati adatainak megismerése
services: virtual-machines
documentationcenter: ''
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 07/28/2020
ms.openlocfilehash: ba973bd5609dacf05eca842025d4e828d8a9f841
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550947"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Az Azure-beli virtuális gépek használatának ismertetése
Az Azure-beli használati adatok elemzésével hatékony fogyasztási adatokat nyerhetnek ki – olyan elemzéseket készíthet, amelyek lehetővé teszik a szervezeten belüli jobb költséghatékonyságot és kiosztást. Ez a dokumentum részletesen ismerteti az Azure-beli számítási felhasználás részleteit. Az Azure általános használatának részletes ismertetését a [számla megismerése](../cost-management-billing/understand/review-individual-bill.md)című rész ismerteti.

## <a name="download-your-usage-details"></a>Használati adatok letöltése
A kezdéshez [töltse le a használati adatokat](../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). Az alábbi táblázat a Azure Resource Manager használatával üzembe helyezett Virtual Machines használatának definícióját és példáit tartalmazza. Ez a dokumentum nem tartalmaz részletes információkat a klasszikus modellen keresztül üzembe helyezett virtuális gépekről.


| Mező | Értelmezés | Példa értékek | 
|---|---|---|
| Usage Date (Használat dátuma) | Az erőforrás használatának dátuma | `11/23/2017` |
| Meter ID | Meghatározza azt a legfelső szintű szolgáltatást, amelynek a használata a következőhöz tartozik:| `Virtual Machines`|
| Meter Sub-Category | A kiszámlázott mérő azonosítója. <br><br> A számítási órák használata esetén a rendszer minden virtuálisgép-mérethez (Windows, nem Windows) és régióhoz tartozó mérőszámot használ. <br><br> A prémium szintű szoftverek használata esetén minden egyes szoftver esetében van egy mérőszám. A prémium szintű szoftverek többsége minden alapmérethez különböző mérőszámokat tartalmaz. További információért látogasson el a [számítási díjszabás oldalára](https://azure.microsoft.com/pricing/details/virtual-machines/) .</li></ul>| `2005544f-659d-49c9-9094-8e0aea1be3a5`|
| Meter Name| Ez az egyes Azure-szolgáltatásokra jellemző. A számítási feladatokhoz mindig "Üzemóra".| `Compute Hours`|
| Meter Region| Az igénybe vett vagy üzemeltető adatközpont elhelyezkedése, ha a szolgáltatás díjszabása az adatközpontok elhelyezkedésétől is függ.|  `JA East`|
| Unit (Egység)| A szolgáltatás számlázási egységét jelöli. A számítási erőforrások számlázása óránként történik.| `Hours`|
| Consumed (Felhasznált mennyiség)| Az adott napra felhasznált erőforrás mennyisége. A számítási feladatokhoz minden percben a virtuális gép egy adott órában futott (legfeljebb 6 tizedesjegy pontossággal).| `1, 0.5`|
| Resource Location  | Az az adatközpont, amelyen az erőforrás fut.| `JA East`|
| Consumed Service | Az Azure platform szolgáltatás, amelyet használt.| `Microsoft.Compute`|
| Erőforráscsoport | Az az erőforráscsoport, amelyben az üzembe helyezett erőforrás fut. További információ: [Azure Resource Manager Overview (áttekintés).](../azure-resource-manager/management/overview.md)|`MyRG`|
| Instance ID | Az erőforrás azonosítója. Az azonosító tartalmazza az erőforrás számára a létrehozáskor megadott nevet. A virtuális gépek esetében a példány azonosítója a SubscriptionId, a ResourceGroupName és a VMName (vagy a méretezési csoportnak a méretezési csoport használatának nevét) fogja tartalmazni.| `/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1`<br><br>vagy<br><br>`/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1`|
| Címkék| Az erőforráshoz hozzárendelt címke. Címkék használatával csoportosíthatja a számlázási rekordokat. Megtudhatja, hogyan címkézheti Virtual Machines a [CLI](./tag-cli.md) vagy a [PowerShell](./tag-portal.md) használatával ez csak Resource Manager-alapú virtuális gépek esetén érhető el.| `{"myDepartment":"RD","myUser":"myName"}`|
| Additional Info (További információ) | Szolgáltatásspecifikus metaadatok. Virtuális gépek esetén a további információ mezőben a következő adatokat töltjük fel: <br><br> Képtípusra jellemző rendszerkép, amelyet futtatott. A támogatott sztringek teljes listáját megtalálja a képtípusok alatt.<br><br> Szolgáltatás típusa: a telepített méret.<br><br> VMName: a virtuális gép neve. Ez a mező csak a méretezési csoport virtuális gépei esetében van kitöltve. Ha szüksége van a virtuális gép nevére a méretezési csoport virtuális gépei számára, akkor azt a fenti AZONOSÍTÓJÚ példányban találja.<br><br> Usagetype értékre: Ez határozza meg az ezt jelképező használati típust.<br><br> A ComputeHR az alapul szolgáló virtuális gép számítási óradíjának használata, például Standard_D1_v2.<br><br> ComputeHR_SW a prémium szintű szoftverek díja, ha a virtuális gép prémium szintű szoftvert használ, például Microsoft R Server. | Virtual Machines<br>`{"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}`<br><br>Virtual Machine Scale Sets<br> `{"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}`<br><br>Prémium szintű szoftver<br> `{"ImageType":"","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR_SW"}` |

## <a name="image-type"></a>Rendszerkép típusa
Az Azure-katalógusban található egyes rendszerképeknél a kép típusa a további információ mezőben lesz kitöltve. Ez lehetővé teszi a felhasználók számára, hogy megértsék és nyomon kövessék, mire vannak telepítve a virtuális gépen. A következő értékek, amelyek az üzembe helyezett lemezkép alapján jelennek meg ebben a mezőben:
- BitRock 
- Kanonikus FreeBSD 
- Logika megnyitása 
- Oracle 
- SLES az SAP-hoz 
- SQL Server 14 előzetes verzió a Windows Server 2012 R2 előzetes verziójában 
- SUSE
- SUSE prémium
- StorSimple Cloud Appliance 
- Red Hat
- Red Hat az SAP Business Applications     
- Red Hat SAP HANA 
- Windows-ügyfél BYOL 
- Windows Server BYOL 
- Windows Server előzetes verzió 

## <a name="service-type"></a>Szolgáltatástípus
A további információ mezőben a szolgáltatás típusa mező a telepített virtuális gép méretének felel meg. A Premium Storage virtuális gépek (SSD-alapú) és a nem prémium szintű Storage-beli virtuális gépek (HDD-alapú) díjszabása azonos. Ha SSD-alapú méretet (például standard \_ DS2 \_ v2) helyez üzembe, a nem SSD méret () jelenik meg a `Standard\_D2\_v2 VM` mérő Sub-Category OSZLOPBAN és az SSD-Size () elemnél `Standard\_DS2\_v2` a további információ mezőben.

## <a name="region-names"></a>Régiók nevei
A használati adatok erőforrás helye mezőjében kitöltött régió neve eltér a Azure Resource Manager használt régió nevétől. Itt látható a régiók értékei közötti leképezés:

| **Resource Manager-régió neve** | **Erőforrás helye a használat részleteiben** |
|---|---|
| australiaeast |Ausztrália keleti régiója|
| australiasoutheast | Délkelet-Ausztrália|
| brazilsouth | Dél-Brazília|
| CanadaCentral | Közép-Kanada|
| CanadaEast | Kelet-Kanada|
| CentralIndia | Közép-India|
| centralus | Az USA középső régiója|
| chinaeast | Kelet-Kína|
| chinanorth | Észak-Kína|
| eastasia | Kelet-Ázsia|
| eastus | USA keleti régiója|
| eastus2 | USA 2. keleti régiója|
| GermanyCentral | Közép-Németország|
| GermanyNortheast | Északkelet-Németország|
| japaneast | Kelet-Japán|
| japanwest | Nyugat-Japán|
| KoreaCentral | Korea középső régiója|
| KoreaSouth | Korea déli régiója|
| northcentralus | USA északi középső régiója|
| northeurope | Észak-Európa|
| southcentralus | USA déli középső régiója|
| southeastasia | Délkelet-Ázsia|
| SouthIndia | Dél-India|
| UKNorth | USA északi régiója|
| uksouth | Az Egyesült Királyság déli régiója|
| UKSouth2 | Az Egyesült Királyság 2. déli régiója|
| ukwest | Az Egyesült Királyság nyugati régiója|
| USDoDCentral | US DoD – Középső régió|
| USDoDEast | US DoD – Kelet|
| USGovArizona | USGov Arizona|
| usgoviowa | USGov Iowa|
| USGovTexas | USGov Texas|
| usgovvirginia | USGov Virginia|
| USA nyugati középső régiója | USA nyugati középső régiója|
| westeurope | Nyugat-Európa|
| WestIndia | Nyugat-India|
| westus | USA nyugati régiója|
| westus2 | USA 2. nyugati régiója|


## <a name="virtual-machine-usage-faq"></a>Virtuális gépek használata – gyakori kérdések
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Milyen erőforrásokra számítunk fel a virtuális gépek telepítésekor?    
A virtuális gépek költségei megvásárlása maga a virtuális gép, a virtuális gépen futó prémium szoftverek, a virtuális géphez társított account\managed lemez, valamint a hálózati sávszélességnek a virtuális gépről való átvitele után.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Honnan tudhatom meg, hogy egy virtuális gép használ-e Azure Hybrid Benefitt a CSV-használatban?
Ha a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)használatával végzi a telepítést, akkor a nem Windows RENDSZERű virtuális gépek díját számítjuk fel, mivel a saját licencét a felhőbe hozza. A számlán megszabhatja, hogy mely Resource Manager-alapú virtuális gépek futnak Azure Hybrid Benefit \_ , mert a ImageType oszlopban a "Windows Server BYOL" vagy a "Windows \_ Client BYOL" szerepel.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Miben különböznek az alapszintű és a szabványos virtuálisgép-típusok a használatban lévő CSV-ben?
Az alapszintű és A standard A-sorozatú virtuális gépek egyaránt elérhetők. Ha alapszintű virtuális gépet helyez üzembe, a mérőszám alkategóriájában az "alapszintű" karakterlánc szerepel. Ha standard A sorozatú virtuális gépet telepít, akkor a virtuális gép mérete "a1 virtuális gép" lesz, mivel a standard érték az alapértelmezett. Ha többet szeretne megtudni az alapszintű és a standard közötti különbségekről, tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Mik azok a ExtraSmall, kis-, közepes, nagy és ExtraLarge méretek?
A ExtraSmall-ExtraLarge a standard \_ a0 – standard a4 örökölt nevei \_ . A klasszikus virtuálisgép-használati rekordokban láthatja ezt az egyezményt, ha telepítette ezeket a méreteket.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Mi a különbség a mérési régió és az erőforrás helye között?
A mérési régió a mérőhöz van társítva. Néhány olyan Azure-szolgáltatás esetében, amely minden régió esetében egy árat használ, üres lehet a mérési régió mezője. Mivel azonban a virtuális gépek a Virtual Machines régiónként külön díjszabással rendelkeznek, ez a mező fel van töltve. Hasonlóképpen, a Virtual Machines erőforrásának helye a virtuális gép üzembe helyezési helye. Az Azure-régiók mindkét mezőben azonosak, bár a régió nevéhez eltérő karakterlánc-konvenció tartozhat.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Miért üres a ImageType érték a további információ mezőben?
A ImageType mező csak a lemezképek egy részhalmazára van kitöltve. Ha nem telepítette a fenti rendszerképek egyikét, a ImageType üres.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Miért üres a VMName a további információban?
A VMName csak a méretezési csoportba tartozó virtuális gépek további információ mezőjében van kitöltve. A InstanceID mező tartalmazza a virtuális gép nevét a nem méretezési csoport virtuális gépei számára.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>Mit jelent a ComputeHR a további információ Usagetype értékre mezőjében?
A ComputeHR a számítási órákra vonatkozik, amely a mögöttes infrastruktúra-díj használati eseményét jelöli. Ha a Usagetype értékre ComputeHR \_ SW, a használati esemény a virtuális gép prémium szintű szoftveres díját jelenti.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Hogyan tudni, hogy a prémium szintű szoftverekért kell fizetni?
Tekintse meg az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute)-t, amikor kideríti, melyik virtuálisgép-rendszerkép megfelel az igényeinek. A rendszerképnek a szoftver csomagjának aránya van. Ha "ingyenes" érték jelenik meg a díjszabásban, a szoftverre nem vonatkozik további díj. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Mi a különbség a Microsoft. ClassicCompute és a Microsoft. számítás között a felhasznált szolgáltatásban?
A Microsoft. ClassicCompute az Azure Service Manager használatával üzembe helyezett klasszikus erőforrásokat képviseli. Ha a Resource Managerrel végzi a telepítést, akkor a Microsoft. számítás a felhasználható szolgáltatásban van feltöltve. További információ az [Azure üzembe helyezési modelljeiről](../azure-resource-manager/management/deployment-models.md).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Miért üres a InstanceID mező a virtuális gépek használatakor?
Ha a klasszikus üzemi modellen keresztül végzi a telepítést, a InstanceID karakterlánc nem érhető el.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Miért nem áramlanak a használati adatokba a virtuális gépekhez tartozó címkék?
A címkék csak a Resource Manager-alapú virtuális gépek használati CSV-fájlját használják. A klasszikus erőforrás címkéi nem érhetők el a használat részleteiben.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Hogyan lehet a felhasznált mennyiség több mint 24 órát egy nap múlva?
A klasszikus modellben az erőforrások számlázása a Cloud Service szintjén összesítve történik. Ha egy felhőalapú szolgáltatásban több virtuális gép is ugyanazt a számlázási mérőszámot használja, a használatot együtt összesítjük. A Resource Managerrel üzembe helyezett virtuális gépek számlázása virtuálisgép-szinten történik, így ez az Összesítés nem érvényes.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Miért nem érhető el az árképzés a DS/FS/GS/LS méretekhez a díjszabási oldalon?
A Premium Storage-kompatibilis virtuális gépek számlázása ugyanolyan sebességgel történik, mint a nem prémium szintű Storage-kompatibilis virtuális gépek. Csak a tárolási költségek különböznek. További információért látogasson el a [Storage díjszabási oldalára](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) .

## <a name="next-steps"></a>Következő lépések
A használati adatokkal kapcsolatos további információkért lásd: [Microsoft Azure számlájának ismertetése.](../cost-management-billing/understand/review-individual-bill.md)
---
title: Privát ajánlatok a Microsoft kereskedelmi piactéren
description: Privát ajánlatok a Microsoft kereskedelmi piactéren az alkalmazások és szolgáltatások közzétevői számára.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 02/22/2021
ms.openlocfilehash: 7c02c41379cfb886fd7e6f84486d815d3cdbfb5b
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097176"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Privát ajánlatok a Microsoft kereskedelmi piactéren

A privát ajánlatok, más néven a privát csomagok lehetővé teszik a kiadók számára, hogy olyan csomagokat hozzanak létre, amelyek csak a célközönség számára láthatók. Ez a cikk a privát ajánlatok lehetőségeit és előnyeit ismerteti.

## <a name="unlock-enterprise-deals-with-private-offers"></a>A vállalati ajánlatok feloldása privát ajánlatokkal

A privát ajánlatok létrehozásával a kiadók olyan testreszabott megoldásokat használhatnak, amelyek a vállalat által igényelt képességeket biztosítanak a felhasználóknak:

- Az *egyeztetett díjszabás* lehetővé teszi, hogy a kiadók a nyilvánosan elérhető ajánlatokból származó kedvezményeket és off-listázási díjszabást terjesszenek.
- A *privát feltételek és kikötések* lehetővé teszik a kiadók számára a használati feltételek egy adott ügyfélhez való személyre szabását.
- A *speciális konfigurációk* lehetővé teszik a kiadók számára a Virtual Machines, az Azure-alkalmazások és a szolgáltatott szoftverek (SaaS) személyre szabását az ügyfelek igényei szerint. Ez a beállítás azt is lehetővé teszi, hogy a kiadók előzetes hozzáférést biztosítanak az új termékek szolgáltatásaihoz, mielőtt az összes ügyfélnek elindítsák azokat.

A privát ajánlatok lehetővé teszik a kiadók számára, hogy kihasználják a nyilvános piactér skálázhatóságát és globális rendelkezésre állását, valamint az egyéni ajánlatok és konfigurációk egyeztetéséhez és megvalósításához szükséges rugalmasságot és irányítást. A vállalatok mostantól a várt módon vásárolhatnak és adhatnak el értékesítést.

## <a name="create-private-offers-using-plans"></a>Privát ajánlatok létrehozása csomagok használatával

A *csomagokkal rendelkező új vagy meglévő ajánlatok* esetében a kiadók egyszerűen létrehozhatnak új, privát változatokat új csomagok (korábbi nevén SKU) létrehozásával és magánjellegűként való megjelölésével. Minden ajánlat legfeljebb 45 privát csomaggal rendelkezhet.

<!--- [Private SKUs]() --->

A Private-csomagok a következő típusú ajánlatokhoz érhetők el:

- Azure-beli virtuális gép
- Azure-alkalmazás (megoldási sablonként vagy felügyelt alkalmazásként implementálva)
- Felügyelt szolgáltatás
- SaaS-ajánlatok

A Private-csomagok egy ajánlat összetevői, és csak a megtervezett ügyfelek számára láthatók és megvásárolhatóak. A privát csomagok csak a megcélzó ügyfelek számára láthatók és megvásárolhatóak. Privát csomagok is elérhetők az Azure globális és Azure Government ügyfelei számára.

A privát csomagok felhasználhatják a nyilvános csomagokhoz már közzétett alaplemezképeket és/vagy ajánlati metaadatokat. Ez a beállítás lehetővé teszi, hogy a kiadók a nyilvános ajánlatok több privát változatát is létrehozzák anélkül, hogy ugyanazon alaprendszerkép több verzióját kellene közzétenni, és a metaadatokat is. Az Azure-beli virtuális gépek és az Azure-alkalmazások esetében csak akkor érhető el, ha egy privát csomag nyilvános csomaggal rendelkező alaprendszerképet oszt meg, az ajánlat alaprendszerképének minden változása az adott alaprendszerkép használatával lesz propagálva az összes nyilvános és privát csomagon.

Az olyan *új ajánlatok esetében, amelyek csak privát csomagokat foglalnak magukban*, a kiadók az ajánlatokat bármely más ajánlatként hozhatják létre, majd megjelölik magánjellegűként a terveket. Azok az ajánlatok, amelyek csak privát csomagokkal rendelkeznek, nem lesznek felderíthetők, vagy az ajánlathoz nem társított ügyfelek által [Azure Portal](https://azure.microsoft.com/features/azure-portal/) elérhetők.

>[!NOTE]
>A csak privát csomagokat tartalmazó ajánlat nem jelenik meg a nyilvános Azure piactéren vagy AppSource.

## <a name="target-customers-with-private-offers"></a>Ügyfelek megcélzása privát ajánlatokkal

A kiadók az új és a meglévő privát ajánlatok esetében is megcélozhatja az ügyfeleket az előfizetés-azonosítók használatával. Az Azure-beli virtuális gépek, az Azure-alkalmazások és a felügyelt szolgáltatások ajánlatai esetében a kiadók egy egyéni Azure-előfizetési AZONOSÍTÓra korlátozhatja a privát csomag rendelkezésre állását, vagy akár 10 000 Azure-előfizetés azonosítóját tartalmazó CSV-t is feltölthet Az SaaS-ajánlatok esetében a kiadók hozzárendelhet egy Azure Active Directory bérlői azonosítót, hogy korlátozzák a privát csomag rendelkezésre állását a manuális vagy CSV-feltöltési módszer használatával.

Az ajánlat minősítésének és közzétételének megtörténtét követően a privát előfizetések szinkronizálása funkció használatával frissítheti vagy eltávolíthatja a csomagot. Ez a funkció lehetővé teszi a kiadók számára, hogy gyorsan és egyszerűen frissítse azon ügyfelek listáját, amelyekhez a privát csomag az ajánlat hitelesítése vagy közzététele nélkül jelenik meg.

## <a name="deploying-private-offers"></a>Privát ajánlatok üzembe helyezése

Miután bejelentkezett a Azure Portalba, az ügyfelek az alábbi lépéseket követve választhatják ki saját ajánlatait.

1. Jelentkezzen be [Azure Portal](https://ms.portal.azure.com/).
1. Az **Azure-szolgáltatások** területen válassza **az erőforrás létrehozása** lehetőséget.
1. Az **új** lapon az **Azure Marketplace** mellett kattintson az **összes** megjelenítése elemre. Megjelenik a piactér lap.
1. A bal oldali navigációs sávon válassza a **privát ajánlatok** lehetőséget.

> [!NOTE]
> A privát ajánlatok csak [Azure Portalban](https://azure.microsoft.com/features/azure-portal/)felderíthetők. Nem jelennek meg [Microsoft AppSource](https://appsource.microsoft.com/) vagy az [Azure Marketplace](https://azuremarketplace.microsoft.com)-en. Ha többet szeretne megtudni arról, hogyan lehet közzétenni a különböző kereskedelmi piactér online áruházait, tekintse meg a [listázási lehetőségek bemutatása](./determine-your-listing-type.md)című témakört.

A privát ajánlatok is megjelennek a keresési eredmények között, és parancssorból és Azure Resource Manager-sablonokból is üzembe helyezhetők, mint bármely más ajánlat.

[![[A keresési eredmények között megjelenő privát ajánlatok]](media/marketplace-publishers-guide/private-offer.png)](media/marketplace-publishers-guide/private-offer.png#lightbox)

A privát ajánlatok is megjelennek a keresési eredmények között. Csak keresse meg a **privát** jelvényt.

>[!Note]
>A nyilvános ajánlatok nem támogatottak a Cloud Solution Provider (CSP) program viszonteladóján keresztül létesített előfizetésekkel.

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->

---
title: A felügyelt alkalmazások áttekintése
description: Ismerteti a Azure Managed Applications fogalmakat, amelyek olyan felhőalapú megoldásokat biztosítanak, amelyek megkönnyítik a felhasználók számára az üzembe helyezést és a működést.
author: tfitzmac
ms.topic: overview
ms.date: 07/12/2019
ms.author: tomfitz
ms.openlocfilehash: 65b8c21251f092311150a50480f5abecb1ad7c17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434566"
---
# <a name="azure-managed-applications-overview"></a>Az Azure Managed Applications áttekintése

Az Azure Managed Applications segítségével az ügyfelek által könnyedén üzembe helyezhető és üzemeltethető felhőalapú megoldások tehetők elérhetővé. Az Ön feladata az infrastruktúra implementálása és a folyamatos támogatás biztosítása. Ha egy felügyelt alkalmazást minden ügyfél számára elérhetővé kíván tenni, tegye közzé az Azure Marketplace-en. Ha csak a saját vállalatán belüli felhasználók számára kívánja elérhetővé tenni, tegye közzé egy belső katalógusban. 

A felügyelt alkalmazások sokban hasonlítanak a Marketplace megoldássablonjaihoz, egy fontos különbséget leszámítva. A felügyelt alkalmazások esetében az erőforrások üzembe helyezése egy erőforráscsoportban történik, amelyet az alkalmazás közzétevője felügyel. Az erőforráscsoport az ügyfél előfizetésében található meg, de a közzétevő bérlőjének egy identitása rendelkezik hozzáféréssel az erőforráscsoporthoz. A megoldás folyamatos támogatásának költségeit a közzétevő határozza meg.

> [!NOTE]
> Az Azure egyéni szolgáltatók dokumentációját korábban a felügyelt alkalmazások dokumentációja tartalmazza. A dokumentáció át lett helyezve. Most tekintse meg az Azure-beli [egyéni szolgáltatókat](../custom-providers/overview.md).

## <a name="advantages-of-managed-applications"></a>A felügyelt alkalmazások előnyei

A felügyelt alkalmazások csökkentik a megoldások ügyfeleire vonatkozó korlátozásokat. A megoldásoknak nem alapfeltétele a felhőinfrastruktúrák használatában való jártasság. Az ügyfelek korlátozott hozzáféréssel rendelkeznek a kritikus erőforrásokhoz, ezért nem kell aggódniuk a használatuk során vétett esetleges hibák miatt. 

A felügyelt alkalmazásokkal folyamatos kapcsolatot tarthat fenn az ügyfelekkel. Ön határozhatja meg az alkalmazás felügyeletének feltételeit, a díjak kifizetése pedig az Azure számlázási rendszerén keresztül történik.

Habár az ügyfelek saját maguk telepítik az előfizetésükhöz tartozó felügyelt alkalmazásokat, de a karbantartással, frissítéssel és javítással nem kell foglalkozniuk. Garantálható, hogy mindig minden ügyfél a jóváhagyott verziót használja. Az ügyfeleknek nem kell alkalmazásspecifikus tartományi ismeretekkel rendelkezniük az alkalmazások felügyeletéhez. Az ügyfelek automatikusan megkapják az alkalmazások frissítéseit, és nem kell foglalkozniuk az esetleges hibák keresésével vagy diagnosztizálásával. 

A felügyelt alkalmazások lehetővé teszik, hogy az informatikai részleg előre jóváhagyott megoldásokat nyújtson a vállalat felhasználói számára. Így biztos lehet abban, hogy a megoldások megfelelnek a vállalati szabványoknak.

A felügyelt alkalmazások támogatják [Az Azure-erőforrások felügyelt identitásait](./publish-managed-identity.md).

## <a name="types-of-managed-applications"></a>A felügyelt alkalmazások típusai

A felügyelt alkalmazások közzétehetők külsőleg vagy belsőleg.

![Külső vagy belső közzététel](./media/overview/manage_app_options.png)

### <a name="service-catalog"></a>Szolgáltatáskatalógus

A szolgáltatáskatalógus egy belső katalógus azokról a megoldásokról, amelyeket egy vállalat jóváhagy a felhasználói számára. A katalógus segítségével biztosítható, hogy a vállalat számára elérhető megoldások megfeleljenek bizonyos vállalati szabványoknak. Az alkalmazottak a katalógussal könnyen felfedezhetik az informatikai részleg által jóváhagyott és ajánlott alkalmazásokat. Láthatják, hogy a vállalat más dolgozói milyen felügyelt alkalmazásokat osztottak meg velük.

A szolgáltatáskatalógusban elérhető felügyelt alkalmazások közzétételével kapcsolatban lásd a [szolgáltatáskatalógusban elérhető alkalmazások létrehozását](publish-service-catalog-app.md) ismertető témakört.

### <a name="marketplace"></a>Piactér

A szolgáltatásaikat értékesíteni kívánó szállítók a felügyelt alkalmazásokat elérhetővé tehetik az Azure Marketplace-en. Miután a szállító közzétesz egy alkalmazást, az elérhetővé válik a vállalaton kívüli felhasználók számára. Ezzel a módszerrel a felügyelt szolgáltatások szolgáltatói (MSP-k), a független szoftverszállítók (ISV-k) és a rendszerintegrátorok (SI-k) minden Azure-ügyfél számára felkínálhatják a megoldásaikat.

A felügyelt alkalmazások Azure Marketplace-en való közzétételével kapcsolatban lásd a [Marketplace-alkalmazás létrehozását](../../marketplace/create-new-azure-apps-offer.md) ismertető témakört.

## <a name="resource-groups-for-managed-applications"></a>Felügyelt alkalmazások erőforráscsoportjai

A felügyelt alkalmazás erőforrásai általában két erőforráscsoporthoz tartoznak. Ezek közül egyet az ügyfél kezel, a másikat pedig a közzétevő felügyeli. A felügyelt alkalmazás meghatározásakor a közzétevő határozza meg a hozzáférési szinteket. A közzétevő egy állandó szerepkör-hozzárendelést igényelhet, vagy igény szerinti [hozzáférést](request-just-in-time-access.md) biztosíthat egy adott időszakra korlátozott hozzárendeléshez.

Az [adatműveletekhez](../../role-based-access-control/role-definitions.md) való hozzáférés korlátozása jelenleg nem támogatott az Azure összes adatszolgáltatójánál.

Az alábbi képen egy olyan forgatókönyv látható, ahol a közzétevő a felügyelt erőforráscsoport tulajdonosi szerepkörét kéri. A közzétevő az ügyfél számára csak olvashatóvá tette az erőforráscsoportot. A kezelt erőforráscsoporthoz hozzáféréssel rendelkező közzétevői identitások mentesítve vannak a zárolás alól.

![Hozzáférés az erőforráscsoporthoz](./media/overview/access.png)

### <a name="application-resource-group"></a>Alkalmazás erőforráscsoportja

Ez az erőforrás tartalmazza a felügyelt alkalmazás példányát. Ez az erőforrás csak egy erőforrást tartalmazhat. A felügyelt alkalmazás erőforrástípusa: **Microsoft.Solutions/applications**.

Az ügyfél teljes hozzáféréssel rendelkezik az erőforráscsoporthoz, így kezelni tudja a felügyelt alkalmazás életciklusát.

### <a name="managed-resource-group"></a>Felügyelt erőforráscsoportok

Ez az erőforráscsoport a felügyelt alkalmazáshoz szükséges összes erőforrást tartalmazza. Ez az erőforrás például a megoldáshoz szükséges virtuális gépeket, tárfiókokat és virtuális hálózatokat is tartalmazza. Az ügyfél korlátozott hozzáféréssel rendelkezik ehhez az erőforráscsoporthoz, mivel nem az ügyfél kezeli a felügyelt alkalmazás különálló erőforrásait. A közzétevő hozzáférése az erőforráscsoporthoz megfelel a felügyelt alkalmazás definíciójában megadott szerepkörnek. Például a közzétevő kérheti a tulajdonosi vagy közreműködői szerepkört az erőforráscsoporthoz. A hozzáférés vagy állandó, vagy egy adott időre korlátozódik.

Amikor közzéteszi a [felügyelt alkalmazást a piactéren](../../marketplace/create-new-azure-apps-offer.md), a közzétevő engedélyezheti a felhasználók számára, hogy meghatározott műveleteket hajtsanak végre a felügyelt erőforráscsoport erőforrásain. A közzétevő például megadhatja, hogy a felhasználók újra tudják indítani a virtuális gépeket. Az olvasási műveleteken túl minden más művelet is megtagadva. A felügyelt erőforráscsoport erőforrásainak egy adott művelettel rendelkező fogyasztó általi változásaira a felügyelt erőforráscsoport belefoglalása a felhasználói bérlőn belüli [Azure Policy](../../governance/policy/overview.md) -hozzárendelésekre vonatkozik.

Ha az ügyfél törli a felügyelt alkalmazást, az erőforráscsoport is törlődik.

## <a name="azure-policy"></a>Azure Policy

A felügyelt alkalmazások naplózásához [Azure Policy](../../governance/policy/overview.md) is alkalmazhat. A szabályzat-definíciók alkalmazásával gondoskodhat arról, hogy a felügyelt alkalmazás telepített példányai megfeleljenek az adat-és biztonsági követelményeknek. Ha az alkalmazás bizalmas adatokkal lép interakcióba, mindenképpen értékelje ki, hogyan kell védeni az adatokat. Ha például az alkalmazás a Microsoft 365ból származó adatokkal kommunikál, alkalmazza a házirend-definíciót, hogy az adatok titkosítása engedélyezve legyen.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megismerkedett a felügyelt alkalmazások használatának előnyeivel. A következő cikkben egy felügyelt alkalmazás definícióját hozza létre.

> [!div class="nextstepaction"]
> [Rövid útmutató: Azure-beli felügyelt alkalmazás definíciójának közzététele](publish-service-catalog-app.md)
---
title: Azure Migrate projektek kezelése az Azure Lighthouse-vel
description: Ismerje meg, hogyan használható hatékonyan a Azure Migrate a delegált ügyfelek erőforrásain.
ms.date: 01/29/2021
ms.topic: how-to
ms.openlocfilehash: 8e8ba21881ea5dad36ae640632b6307cd9a22a73
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99093608"
---
# <a name="manage-azure-migrate-projects-at-scale-with-azure-lighthouse"></a>Azure Migrate projektek kezelése az Azure Lighthouse-vel

Ez a témakör áttekintést nyújt arról, hogy az [Azure Lighthouse](../overview.md) hogyan segít a [Azure Migrate](../../migrate/migrate-services-overview.md) skálázható módon történő használatát több Azure Active Directory (Azure ad-) bérlőn keresztül.

Az Azure Lighthouse lehetővé teszi a szolgáltatók számára, hogy egyszerre több bérlőn is elvégezzék a műveleteket, így hatékonyabbá téve a felügyeleti feladatokat.

A Azure Migrate központosított központot biztosít az Azure helyszíni kiszolgálóira, infrastruktúrára, alkalmazásokra és adatszolgáltatásokra való kiértékeléséhez és átköltöztetéséhez. Az értékeléseket és a nagy mennyiségű áttelepítést végző partnereket általában a [CSP (Cloud Solution Provider) előfizetési modell](/partner-center/customers-revoke-admin-privileges) használatával, vagy [egy vendég felhasználó létrehozásával, az ügyfél bérlője](../../active-directory/external-identities/what-is-b2b.md)számára külön kell elérniük.

Az Azure Lighthouse és a Azure Migrate integrációja lehetővé teszi, hogy a szolgáltatók több különböző ügyfél számára is felfedezzék, értékeljék és áttelepítsek a számítási feladatokat, miközben lehetővé teszik, hogy az ügyfelek teljes körű láthatóságot és irányítást nyújtsanak a környezetekhez Az Azure-beli delegált erőforrás-kezelésen keresztül a szolgáltatók egyetlen nézettel rendelkeznek az összes olyan Azure Migrate-projektről, amelyet több ügyfél bérlője kezel.

> [!NOTE]
> Az Azure világítótoronyban a partnerek a helyszíni VMware virtuális gépek, a Hyper-V virtuális gépek, a fizikai kiszolgálók és az AWS/GCP példányok felderítését, értékelését és áttelepítését is elvégezhetik. A [VMWare virtuális gépek áttelepítésének](../../migrate/server-migrate-overview.md)két lehetősége van. Jelenleg csak az ügynök-alapú áttelepítési módszer használható, ha egy áttelepítési projekten dolgozik egy delegált ügyfél-előfizetésben; az ügynök nélküli replikációt használó Migrálás jelenleg nem támogatott az ügyfél hatókörének delegált hozzáférésén keresztül.

> [!TIP]
> Bár a jelen témakörben a szolgáltatók és az ügyfelekre is hivatkozunk, ez az útmutató az [Azure Lighthouse-t használó vállalatoknak is vonatkozik több bérlő kezelésére](../concepts/enterprise.md).

A forgatókönyvtől függően előfordulhat, hogy létre kívánja hozni a Azure Migratet az ügyfél bérlőben vagy az Ön által kezelt bérlőben. Tekintse át az alábbi szempontokat, és határozza meg, melyik modell felel meg legjobban az ügyfél áttelepítési igényeinek.

## <a name="create-an-azure-migrate-project-in-the-customer-tenant"></a>Azure Migrate projekt létrehozása az ügyfél bérlőben

Az Azure Lighthouse használata esetén az egyik lehetőség az Azure Migrate projekt létrehozása az ügyfél bérlője számára. Az áttelepítési projekt létrehozásakor a bérlőben lévő felhasználók kiválaszthatják az ügyfél-előfizetést. A bérlők felügyeletével a szolgáltató elvégezheti a szükséges áttelepítési műveleteket. Ez magában foglalhatja a Azure Migrate berendezés üzembe helyezését a számítási feladatok felderítéséhez, a munkaterhelések felméréséhez a virtuális gépek csoportosításával, a felhővel kapcsolatos költségek számításával, a virtuális gép készültségének áttekintésével és az áttelepítés végrehajtásával.

Ebben a forgatókönyvben egyetlen erőforrás sem lesz létrehozva és tárolva a bérlő kezelése során, még akkor is, ha a felderítési és értékelési lépések a bérlőtől indíthatók el és hajthatók végre. Az ügyfél-előfizetésben az összes erőforrás, például az áttelepítési projektek, a helyszíni számítási feladatokhoz és az áttelepített erőforrásokhoz tartozó értékelési jelentések lesznek telepítve. A szolgáltató azonban az összes ügyfél-projektet elérheti a bérlő és a portál felhasználói felületéről.

Ez a megközelítés a több ügyfélen dolgozó szolgáltatók környezeti váltását is lekicsinyíti, miközben az ügyfelek az összes erőforrását megőrzik a saját bérlők számára.

A modell munkafolyamata a következőhöz hasonló lesz:

1. Az ügyfél az [Azure Lighthouse-](onboard-customer.md)ban van bevezetve. A közreműködő beépített szerepköre szükséges ahhoz, hogy a Azure Migrate használni kívánt identitást használja. A szerepkört használó példához tekintse meg a [delegált erőforrás-felügyeleti-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) minta sablonját.
1. A kijelölt felhasználó bejelentkezik a Azure Portal felügyelő bérlőbe, majd a Azure Migratera lép. Ez a felhasználó [létrehoz egy Azure Migrate projektet](../../migrate/create-manage-projects.md), kiválasztja a megfelelő delegált ügyfél-előfizetést.
1. A felhasználó ezután [elvégzi a felderítés és az értékelés lépéseit](../../migrate/tutorial-discover-vmware.md).

   A VMware virtuális gépek esetében a berendezés konfigurálása előtt korlátozhatja a felderítést vCenter Server adatközpontokra, fürtökre, fürtökre, gazdagépekre, gazdagépekre vagy egyedi virtuális gépekre vonatkozó mappára. A hatókör beállításához rendeljen engedélyeket arra a fiókra, amelyet a készülék használ a vCenter Server eléréséhez. Ez akkor hasznos, ha több ügyfél virtuális gépe is fut a hypervisorban. A Hyper-V felderítési hatóköre nem korlátozható.

    > [!NOTE]
    > A VMware virtuális gépeket az Azure Lighthouse által biztosított delegált hozzáférés segítségével derítheti fel és értékelheti át a Azure Migrate használatával történő áttelepítéshez. A VMware virtuális gépek áttelepítéséhez jelenleg csak az ügynök-alapú módszer támogatott, ha egy áttelepítési projekten dolgozik egy delegált ügyfél-előfizetésben.

1. Ha a cél ügyfél-előfizetés elkészült, folytassa az áttelepítést az Azure Lighthouse által biztosított hozzáféréssel. Az értékelés eredményeit és az áttelepített erőforrásokat tartalmazó áttelepítési projektet a rendszer a cél előfizetés alatt hozza létre az ügyfél-bérlőben.

> [!TIP]
> A Migrálás előtt telepíteni kell egy bevezetési zónát az alapinfrastruktúra-erőforrások kiépítéséhez és az előfizetés előkészítéséhez, amelybe a virtuális gépek migrálva lesznek. A kilépési zónában lévő erőforrások eléréséhez vagy létrehozásához a tulajdonos beépített szerepkörére lehet szükség, amely jelenleg nem támogatott az Azure világítótoronyban. Ilyen esetekben előfordulhat, hogy az ügyfélnek vendég hozzáférési szerepkört kell biztosítania, vagy rendszergazdai hozzáférést kell delegálnia a CSP-előfizetési modell használatával. A több-bérlős kilépési zónák létrehozásáról a GitHubon, a [több-bérlős-Landing-Zones bemutató megoldásban](https://github.com/Azure/Multi-tenant-Landing-Zones) talál további információt.

## <a name="create-an-azure-migrate-project-in-the-managing-tenant"></a>Azure Migrate projekt létrehozása a bérlő kezelése szolgáltatásban

Ebben az esetben az áttelepítéssel kapcsolatos műveleteket, például a felderítést és az értékelést továbbra is a bérlők kezelik a felhasználók. Az áttelepítési projekt és az összes kapcsolódó erőforrás azonban a partner bérlőben található, és az ügyfél nem fog közvetlenül megtekinteni a projektbe (bár a felmérések igény szerint megoszthatók az ügyfelekkel). Az ügyfelek áttelepítési célja az ügyfél előfizetése lesz.

Ez a megközelítés lehetővé teszi, hogy a szolgáltatók gyorsan elindítsák az áttelepítési felderítési és értékelési projekteket, így az ügyfél-előfizetések és a bérlők kezdeti lépéseit.

A modell munkafolyamata a következőhöz hasonló lesz:

1. Az ügyfél az [Azure Lighthouse-](onboard-customer.md)ban van bevezetve. A közreműködő beépített szerepköre szükséges ahhoz, hogy a Azure Migrate használni kívánt identitást használja. A szerepkört használó példához tekintse meg a [delegált erőforrás-felügyeleti-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) minta sablonját.
1. A kijelölt felhasználó bejelentkezik a Azure Portal felügyelő bérlőbe, majd a Azure Migratera lép. Ez a felhasználó [létrehoz egy Azure Migrate projektet](../../migrate/create-manage-projects.md) egy előfizetésben, amely a bérlői felügyelethez tartozó.
1. A felhasználó ezután [elvégzi a felderítés és az értékelés lépéseit](../../migrate/tutorial-discover-vmware.md). A helyszíni virtuális gépek felderítése és értékelése a bérlő kezelése során létrehozott áttelepítési projekten belül történik, majd onnan migrálva.

   Ha ugyanazon a Hyper-V-gazdagépen több ügyfelet kezel, az összes munkaterhelést egyszerre is felderítheti. Az ügyfél-specifikus virtuális gépek kiválaszthatók ugyanabban a csoportban, majd egy értékelés is létrehozható, és az áttelepítés elvégezhető úgy, hogy kiválasztja a megfelelő ügyfél-előfizetést célként célként. A felderítési hatókört nem kell korlátozni, és egy áttelepítési projektben teljes körű áttekintést kaphat az összes ügyfél-munkaterhelésről.

1. Ha készen áll, folytassa az áttelepítést úgy, hogy kijelöli a delegált ügyfél-előfizetést célként a feladatok replikálásához és áttelepítéséhez. Az újonnan létrehozott erőforrások megtalálhatók az ügyfél-előfizetésben, míg az áttelepítési projekthez tartozó értékelési és erőforrás-információk a bérlők felügyelete alatt maradnak.

Megjegyzés: a telepítés előtt módosítania kell a paramétert a környezetnek megfelelően. https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate

## <a name="partner-recognition-for-customer-migrations"></a>Partneri felismerés az ügyfelek áttelepítéséhez

A [Microsoft Partner Network](https://partner.microsoft.com)tagjaként összekapcsolhatja a Partner azonosítóját a delegált ügyfelek erőforrásainak kezeléséhez használt hitelesítő adatokkal. Ez lehetővé teszi, hogy a Microsoft az ügyfelek számára végrehajtott feladatok, például az áttelepítési projektek alapján befolyásolja és az Azure által felhasznált bevételt a szervezet számára.

További információért lásd [a partnerazonosító a delegált erőforrásokra gyakorolt hatás nyomon követéséhez történő csatolását](partner-earned-credit.md) ismertető részt.

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Migrateról](../../migrate/migrate-services-overview.md).
- Ismerje meg az Azure Lighthouse által támogatott egyéb, [több-bérlős felügyeleti tapasztalatokat](../concepts/cross-tenant-management-experience.md) .

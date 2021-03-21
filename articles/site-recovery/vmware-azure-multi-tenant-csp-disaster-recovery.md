---
title: A VMware vész-helyreállítás beállítása az Azure-ba egy több-bérlős környezetben a Site Recovery és a Cloud Solution Provider (CSP) program használatával | Microsoft Docs
description: Leírja, hogyan állítható be a VMware vész-helyreállítás egy több-bérlős környezetben, Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: fb2a8a7bb14758ab21eb2183a119f456b53c8562
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654949"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>A VMware vész-helyreállítás beállítása több-bérlős környezetben a Cloud Solution Provider (CSP) program keretében

A [CSP program](https://partner.microsoft.com/cloud-solution-provider) a Microsoft Cloud Services, többek között a Microsoft 365, a nagyvállalati mobilitási csomag és a Microsoft Azure jobb összekapcsolását segíti elő. A CSP-vel a partnerek a végpontok közötti kapcsolatot az ügyfelekkel, és az elsődleges kapcsolati kapcsolattartási pontként válnak. A partnerek telepíthetnek Azure-előfizetéseket az ügyfelek számára, és kombinálják az előfizetéseket saját hozzáadott értékekkel, testreszabott ajánlatokkal.

A [Azure site Recovery](site-recovery-overview.md)segítségével a partnerek közvetlenül a CSP-n keresztül kezelhetik a vész-helyreállítási felhasználókat. A CSP használatával Site Recovery környezeteket állíthat be, és lehetővé teheti, hogy az ügyfelek önkiszolgáló módon kezeljék saját vész-helyreállítási igényeiket. Mindkét esetben a partnerek a Site Recovery és az ügyfeleik közötti összekötők. A partnerek felhasználják az ügyfélkapcsolatot, és számlázzák ügyfeleit Site Recovery használatra.

Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhet bérlői előfizetéseket a CSP-n keresztül egy több-bérlős VMware replikálási forgatókönyvhöz.

## <a name="prerequisites"></a>Előfeltételek

A VMware-replikáció beállításához a következőket kell tennie:

- [Felkészülés](tutorial-prepare-azure.md) Azure-erőforrások, beleértve az Azure-előfizetést, az Azure-beli virtuális hálózatot és a Storage-fiókot.
- [Készítse elő](vmware-azure-tutorial-prepare-on-premises.md) a helyszíni VMware-kiszolgálókat és virtuális gépeket.
- Mindegyik bérlőhöz hozzon létre egy különálló felügyeleti kiszolgálót, amely képes kommunikálni a bérlői virtuális gépekkel és a vCenter-kiszolgálókkal. Csak Ön, mint partnernek van hozzáférési jogosultsága ehhez a felügyeleti kiszolgálóhoz. További információ a [több-bérlős környezetekről](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Bérlői fiók létrehozása

1. A [Microsoft partner centeren](https://partnercenter.microsoft.com/)keresztül jelentkezzen be a CSP-fiókjába.
2. Az **irányítópult** menüben válassza az **ügyfelek** lehetőséget.
3. A megnyíló lapon kattintson az **ügyfél hozzáadása** gombra.
4. Az **új ügyfél** lapon adja meg a bérlői fiókadatok adatait.

    ![A fiók adatai lap](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Ezután kattintson a **Tovább: előfizetések** elemre.
6. Az előfizetések kiválasztása lapon jelölje be a **Microsoft Azure** jelölőnégyzetet. Más előfizetéseket most vagy bármikor hozzáadhat.
7. Az **Áttekintés** lapon erősítse meg a bérlő adatait, majd kattintson a **Submit (Küldés**) gombra.
8. Miután létrehozta a bérlői fiókot, megjelenik egy megerősítő lap, amely megjeleníti az alapértelmezett fiók és az előfizetés jelszavának részleteit. Mentse az adatokat, és szükség szerint módosítsa a jelszót a Azure Portal bejelentkezési oldalán.

Ezt az információt megoszthatja a Bérlővel, vagy létrehozhat és megoszthat egy külön fiókot, ha szükséges.

## <a name="access-the-tenant-account"></a>Hozzáférés a bérlői fiókhoz

A bérlő előfizetését a Microsoft partner Center irányítópultján érheti el.

1. Az **ügyfelek** lapon kattintson a bérlői fiók nevére.
2. A bérlői fiók **előfizetések** lapján figyelheti a meglévő fiók-előfizetéseket, és szükség szerint további előfizetéseket is hozzáadhat.
3. A bérlő vész-helyreállítási műveleteinek kezeléséhez válassza a **minden erőforrás (Azure Portal)** lehetőséget. Ez hozzáférést biztosít a bérlő Azure-előfizetéséhez.

    ![A minden erőforrás hivatkozás](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. A hozzáférést a Azure Portal jobb felső sarkában található Azure Active Directory hivatkozásra kattintva ellenőrizheti.

    ![Azure Active Directory hivatkozás](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Most már elvégezheti és kezelheti a bérlő összes Site Recovery műveletét a Azure Portalban. A bérlői előfizetés a felügyelt vész-helyreállításhoz a CSP-n keresztüli eléréséhez kövesse az előzőekben ismertetett folyamatot.

## <a name="assign-tenant-access-to-the-subscription"></a>Bérlői hozzáférés kiosztása az előfizetéshez

1. Győződjön meg arról, hogy a vész-helyreállítási infrastruktúra be van állítva. A partnerek a CSP-portálon keresztül érik el a bérlői előfizetéseket, függetlenül attól, hogy a vész-helyreállítási felügyelet vagy az önkiszolgáló szolgáltatás. Állítsa be a tárolót, és regisztrálja az infrastruktúrát a bérlői előfizetésekben.
1. Adja meg a bérlőt a [létrehozott fiókkal](#create-a-tenant-account).
1. A következő módon adhat hozzá új felhasználót a bérlői előfizetéshez a CSP-portálon keresztül:

    1. Lépjen a bérlő CSP-előfizetése oldalra, majd válassza a **felhasználók és licencek** lehetőséget.

       ![A bérlő CSP-előfizetési lapja](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    1. Most hozzon létre egy új felhasználót a megfelelő részletek beírásával és az engedélyek kiválasztásával, vagy a CSV-fájlban lévő felhasználók listájának feltöltésével.

    1. Miután létrehozott egy új felhasználót, térjen vissza a Azure Portal. Az **előfizetés** lapon válassza ki a megfelelő előfizetést.

    1. Válassza a **hozzáférés-vezérlés (iam)** lehetőséget, majd kattintson a **szerepkör-hozzárendelések** elemre.

    1. Kattintson a **szerepkör-hozzárendelés hozzáadása** lehetőségre a megfelelő hozzáférési szinttel rendelkező felhasználó hozzáadásához. A CSP-portálon létrehozott felhasználók a szerepkör-hozzárendelések lapon jelennek meg.

        ![Felhasználó hozzáadása](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- A legtöbb felügyeleti művelethez elegendő a *közreműködő* szerepkör. Az ezzel a hozzáférési szinttel rendelkező felhasználók mindent elvégezhetnek egy előfizetésen, kivéve a hozzáférési szintek módosítását (amelyekhez *tulajdonosi* szintű hozzáférés szükséges).
- A Site Recovery három [előre definiált felhasználói szerepkörrel](site-recovery-role-based-linked-access-control.md)is rendelkezik, amelyekkel igény szerint tovább korlátozhatja a hozzáférési szinteket.

## <a name="multi-tenant-environments"></a>Több-bérlős környezetek

Három fő több-bérlős modell létezik:

* **Megosztott üzemeltetési szolgáltatások szolgáltatója (HSP)**: a partner tulajdonosa a fizikai infrastruktúra, és megosztott erőforrásokat (vCenter, adatközpontokat, fizikai tárhelyet stb.) használ a több bérlős virtuális gép ugyanazon az infrastruktúrán való üzemeltetéséhez. A partner felügyelt szolgáltatásként is biztosíthatja a vész-helyreállítási felügyeletet, a bérlő pedig önkiszolgáló megoldásként is saját vész-helyreállítási megoldást kínál.

* **Dedikált üzemeltetési szolgáltató**: a partner a fizikai infrastruktúrát használja, de dedikált erőforrásokat (több vCenter, fizikai adattárolót stb.) használ az egyes bérlők virtuális gépei külön infrastruktúrán való üzemeltetéséhez. A partner felügyelt szolgáltatásként is biztosíthatja a vész-helyreállítási felügyeletet, a bérlő pedig önkiszolgáló megoldásként is rendelkezhet.

* **Felügyelt szolgáltató (msp)**: az ügyfél tulajdonosa a virtuális gépeket üzemeltető fizikai infrastruktúra, a partner pedig vész-helyreállítási engedélyezést és felügyeletet biztosít.

Az ebben a cikkben leírtak szerint a bérlői előfizetések beállításával gyorsan megkezdheti az ügyfelek engedélyezését a kapcsolódó több-bérlős modellekben. További információt a különböző több-bérlős modellekről és a helyszíni hozzáférés-vezérlések engedélyezéséről [itt](vmware-azure-multi-tenant-overview.md)talál.

## <a name="next-steps"></a>Következő lépések
- További információ az [Azure szerepköralapú hozzáférés-vezérlésről (Azure RBAC)](site-recovery-role-based-linked-access-control.md) Azure site Recovery üzemelő példányok kezeléséhez.
- További információ a VMware – Azure [replikálási architektúráról](vmware-azure-architecture.md).
- [Tekintse át a](vmware-azure-tutorial.md) VMWare virtuális gépek Azure-ba történő replikálására vonatkozó oktatóanyagot.
További információ a VMware virtuális gépek Azure-ba történő replikálásához használható [több-bérlős környezetekről](vmware-azure-multi-tenant-overview.md) .

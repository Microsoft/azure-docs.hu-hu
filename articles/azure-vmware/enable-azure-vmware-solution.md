---
title: A gazdagép kvótájának kérése és az Azure VMware-megoldás engedélyezése
description: Megtudhatja, hogyan kérheti le a gazdagép kvótáját/kapacitását, és engedélyezheti az Azure VMware megoldás erőforrás-szolgáltatóját. További gazdagépeket is igényelhet egy meglévő Azure VMware-megoldás saját felhőben.
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 02/17/2021
ms.openlocfilehash: 5d154f5c63ffccdbf1729e641133b54be478d884
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653166"
---
# <a name="request-host-quota-and-enable-azure-vmware-solution"></a>A gazdagép kvótájának kérése és az Azure VMware-megoldás engedélyezése

Ebben a útmutatóban megtudhatja, hogyan kérheti le a gazdagép kvótáját/kapacitását, és engedélyezheti az [Azure VMware megoldás](introduction.md) erőforrás-szolgáltatóját, amely lehetővé teszi a szolgáltatást. Az Azure VMware-megoldás engedélyezéséhez be kell nyújtania egy támogatási jegyet, hogy a gazdagépek le legyenek foglalva. Ha van egy meglévő Azure VMware-megoldás saját felhője, és több gazdagépet szeretne lefoglalni, ugyanezt a folyamatot fogja követni.

>[!IMPORTANT]
>A kért számtól függően néhány napig is eltarthat a gazdagépek foglalása.  Ezért kérje meg, hogy mire van szükség a kiépítés során, így nem kell a kvóta növelését gyakran igénybe venni.


A teljes folyamat egyszerű, és két lépést tartalmaz:
- További gazdagépi kvóta/kapacitás kérése az [EA-ügyfelek](#request-host-quota-for-ea-customers) vagy a CSP- [ügyfelek](#request-host-quota-for-csp-customers) számára 
- A Microsoft. AVS erőforrás-szolgáltató engedélyezése

## <a name="eligibility-criteria"></a>Megfelelőségi feltételek

Egy Azure-előfizetésben Azure-fiókra lesz szüksége. Az Azure-előfizetést a következő feltételek egyikével kell követni:

- Egy [Azure nagyvállalati szerződés (EA)](../cost-management-billing/manage/ea-portal-agreements.md) alá tartozó előfizetés a Microsofttal.
- Egy meglévő CSP Azure-szerződés vagy egy Azure-csomag keretében kezelt felhőalapú megoldás-szolgáltató (CSP) által felügyelt előfizetés.

## <a name="request-host-quota-for-ea-customers"></a>Nagyvállalati szerződés kérése a gazda számára

1. A Azure Portal a Súgó és **támogatás** területen hozzon létre egy **[új támogatási kérést](https://rc.portal.azure.com/#create/Microsoft.Support)** , és adja meg a következő információkat a jegyhez:
   - **Probléma típusa:** Technikai
   - **Előfizetés:** Előfizetés kiválasztása
   - **Szolgáltatás:** Minden szolgáltatás > Azure VMware-megoldás
   - **Erőforrás:** Általános kérdés 
   - **Összefoglalás:** Kapacitás szükséges
   - **Probléma típusa:** Kapacitás-felügyeleti problémák
   - **Probléma altípusa:** Ügyfél iránti kérelem további gazdagép-kvótához/kapacitáshoz

1. A támogatási jegy **leírásában** a **részletek** lapon adja meg a következőt:

   - POC vagy éles üzem 
   - Régiónév
   - Gazdagépek száma
   - Bármilyen más részlet

   >[!NOTE]
   >Az Azure VMware-megoldás legalább három gazdagép használatát javasolja a saját felhő és a redundancia N + 1 gazdagépek számára. 

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a kérelem elküldéséhez.


## <a name="request-host-quota-for-csp-customers"></a>Kérelem gazdagép-kvótája a CSP-ügyfelek számára 

A CSP-nek a [Microsoft partner centert](https://partner.microsoft.com) kell használnia az Azure VMware-megoldás az ügyfelek számára történő engedélyezéséhez. Ez a cikk a [CSP Azure-csomagot](/partner-center/azure-plan-lp) használja példaként a partnereknek szóló vásárlási eljárás szemléltetésére.

A Azure Portal hozzáférése a (z) partner Center által a (z) (AOBO) eljárás **rendszergazdai nevével** .

>[!IMPORTANT] 
>Az Azure VMware megoldási szolgáltatás nem biztosít több-bérlőt. Az azt igénylő partnerek nem támogatottak. 

1. A CSP Azure-csomag konfigurálása:

   1. A **partner Centerben** válassza a **CSP** lehetőséget az **ügyfelek** területen való hozzáféréshez.
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Microsoft partner Center – ügyfelek" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::
   
   1. Válassza ki az ügyfelet, majd válassza a **termékek hozzáadása** elemet.
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft Partnerközpont" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::
   
   1. Válassza az **Azure-csomag** lehetőséget, majd válassza **a Hozzáadás a kosárhoz** lehetőséget. 
   
   1. Tekintse át és fejezze be az Azure-csomag előfizetésének általános beállítását az ügyfél számára. További információt a [Microsoft partner Center dokumentációjában](/partner-center/azure-plan-manage)talál.

1. Miután konfigurálta az Azure-csomagot, és rendelkezik az előfizetéshez szükséges [Azure RBAC-engedélyekkel](/partner-center/azure-plan-manage) , az Azure-csomag előfizetéséhez tartozó kvótát kell kérnie. 

   1. Hozzáférési Azure Portal a [Microsoft partner Centertől](https://partner.microsoft.com) a (z) (AOBO) eljárásban található **rendszergazda** használatával.
   
   1. Válassza a **CSP** lehetőséget az **ügyfelek** területen való hozzáféréshez.
   
   1. Bontsa ki az ügyfél adatait, és válassza a **a Microsoft Azure felügyeleti portálja** lehetőséget.
   
   1. Azure Portal a Súgó és **támogatás** területen hozzon létre egy **[új támogatási kérést](https://rc.portal.azure.com/#create/Microsoft.Support)** , és adja meg a következő információkat a jegyhez:
      - **Probléma típusa:** Technikai
      - **Előfizetés:** Előfizetés kiválasztása
      - **Szolgáltatás:** Minden szolgáltatás > Azure VMware-megoldás
      - **Erőforrás:** Általános kérdés 
      - **Összefoglalás:** Kapacitás szükséges
      - **Probléma típusa:** Kapacitás-felügyeleti problémák
      - **Probléma altípusa:** Ügyfél iránti kérelem további gazdagép-kvótához/kapacitáshoz
   
   1. A támogatási jegy **leírásában** a **részletek** lapon adja meg a következőt:
   
      - POC vagy éles üzem 
      - Régiónév
      - Gazdagépek száma
      - Bármilyen más részlet
      - Több ügyfél üzemeltetésére szolgál?
   
      >[!NOTE]
      >Az Azure VMware-megoldás legalább három gazdagép használatát javasolja a saját felhő és a redundancia N + 1 gazdagépek számára. 
   
   1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a kérelem elküldéséhez.

## <a name="register-the-microsoftavs-resource-provider"></a>A **Microsoft. AVS** erőforrás-szolgáltató regisztrálása

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="next-steps"></a>Következő lépések

Miután engedélyezte az erőforrást és a megfelelő hálózatkezelést, [létrehozhat egy privát felhőt](tutorial-create-private-cloud.md).

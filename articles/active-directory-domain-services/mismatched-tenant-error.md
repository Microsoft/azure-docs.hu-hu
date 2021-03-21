---
title: Nem egyező címtárbeli hibák javítása a Azure AD Domain Servicesban | Microsoft Docs
description: Megtudhatja, hogy mi a nem egyező címtárbeli hiba, és hogyan oldható fel a Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: ee8174114f1b892210e8ee9173ce0eb1d09c7e31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619300"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-active-directory-domain-services-managed-domains"></a>A meglévő Azure Active Directory Domain Services felügyelt tartományokhoz tartozó eltérő címtárbeli hibák elhárítása

Ha egy Azure Active Directory Domain Services (Azure AD DS) felügyelt tartomány nem egyező bérlői hibát jelez, a felügyelt tartomány addig nem felügyelhető, amíg meg nem oldódik. Ez a hiba akkor fordul elő, ha az alapul szolgáló Azure-beli virtuális hálózatot egy másik Azure AD-címtárba helyezi át.

Ez a cikk a hiba okait és megoldását ismerteti.

## <a name="what-causes-this-error"></a>Mi okozza ezt a hibát?

Nem egyező címtárbeli hiba történik, amikor egy Azure AD DS felügyelt tartomány és virtuális hálózat két különböző Azure AD-bérlőhöz tartozik. Előfordulhat például, hogy rendelkezik egy *aaddscontoso.com* nevű felügyelt tartománnyal, amely a contoso Azure ad-bérlőben fut. A felügyelt tartományhoz tartozó Azure-beli virtuális hálózat azonban a fabrikam Azure AD-bérlő részét képezi.

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) az erőforrásokhoz való hozzáférés korlátozására szolgál. Az Azure AD DS Azure AD-bérlőben való engedélyezésekor a rendszer a hitelesítő adatok kivonatait szinkronizálja a felügyelt tartományba. Ehhez a művelethez az Azure AD-címtárhoz tartozó bérlői rendszergazdának kell lennie, és a hitelesítő adatokhoz való hozzáférés szabályozása szükséges.

Ahhoz, hogy erőforrásokat helyezzen üzembe az Azure-beli virtuális hálózatra, és szabályozza a forgalmat, rendszergazdai jogosultságokkal kell rendelkeznie azon a virtuális hálózaton, amelyben a felügyelt tartományt telepíti.

Ahhoz, hogy az Azure RBAC konzisztens és biztonságos hozzáférést biztosítson az Azure AD DS által használt összes erőforráshoz, a felügyelt tartománynak és a virtuális hálózatnak ugyanahhoz az Azure AD-bérlőhöz kell tartoznia.

A központi telepítések esetében a következő szabályok érvényesek:

- Az Azure AD-címtár több Azure-előfizetéssel is rendelkezhet.
- Az Azure-előfizetések több erőforrással rendelkezhetnek, például virtuális hálózatokkal.
- Egyetlen felügyelt tartomány van engedélyezve egy Azure AD-címtárhoz.
- A felügyelt tartományokat engedélyezheti egy olyan virtuális hálózaton, amely az adott Azure AD-bérlőn belül bármely Azure-előfizetéshez tartozhat.

### <a name="valid-configuration"></a>Érvényes konfiguráció

A következő példa telepítési forgatókönyvben a contoso által felügyelt tartomány engedélyezve van a contoso Azure AD-bérlőben. A felügyelt tartomány olyan virtuális hálózatban van telepítve, amely a contoso Azure AD-bérlő által birtokolt Azure-előfizetéshez tartozik.

A felügyelt tartomány és a virtuális hálózat is ugyanahhoz az Azure AD-bérlőhöz tartozik. Ez a példa konfiguráció érvényes és teljes mértékben támogatott.

![Érvényes Azure AD DS bérlői konfiguráció az azonos Azure AD-bérlő felügyelt tartományával és virtuális hálózati részével](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Nem egyező bérlői konfiguráció

Ebben a példában a contoso által felügyelt tartomány engedélyezve van a contoso Azure AD-bérlőben. A felügyelt tartományt azonban olyan virtuális hálózatban helyezi üzembe, amely a fabrikam Azure AD-bérlő tulajdonában lévő Azure-előfizetéshez tartozik.

A felügyelt tartomány és a virtuális hálózat két különböző Azure AD-bérlőhöz tartozik. Ez a példában szereplő konfiguráció nem egyezik a Bérlővel, és nem támogatott. A virtuális hálózatot a felügyelt tartományba tartozó Azure AD-bérlőre kell áthelyezni.

![Nem egyező bérlői konfiguráció](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Nem egyező bérlői hiba elhárítása

A következő két lehetőség a nem egyező könyvtárat oldja fel:

* Először [törölje a felügyelt tartományt](delete-aadds.md) a meglévő Azure ad-címtárból. Ezután [hozzon létre egy helyettesítő felügyelt tartományt](tutorial-create-instance.md) a használni kívánt virtuális hálózattal azonos Azure ad-címtárban. Ha elkészült, csatlakoztassa a korábban a törölt tartományhoz csatlakozó összes gépet az újból létrehozott felügyelt tartományhoz.
* Helyezze át a virtuális hálózatot tartalmazó [Azure-előfizetést](../cost-management-billing/manage/billing-subscription-transfer.md) ugyanahhoz az Azure ad-címtárhoz, amely a felügyelt tartomány.

## <a name="next-steps"></a>Következő lépések

Az Azure AD DS hibaelhárításával kapcsolatos további információkért tekintse meg a [hibaelhárítási útmutatót](troubleshoot.md).

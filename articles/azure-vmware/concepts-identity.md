---
title: Fogalmak – identitás és hozzáférés
description: Ismerje meg az Azure VMware-megoldás identitás-és hozzáférési fogalmait
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 0f58cbdaa641af24cef53c3cfd9604cf1b0c1c3b
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538941"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Az Azure VMware megoldás identitásával kapcsolatos fogalmak

Az Azure VMware-megoldás privát felhők a vCenter-kiszolgálóval és a NSX-T kezelővel vannak kiépítve. A vCenter használatával kezelheti a virtuális gépek (VM) munkaterheléseit. A NSX-T kezelőjével kiterjesztheti a privát felhőt.

A hozzáférés és az Identitáskezelés a CloudAdmin csoport jogosultságait használja a vCenter és a korlátozott rendszergazdai jogokkal a NSX-T Manager számára. Gondoskodik arról, hogy a saját felhőalapú platformja automatikusan, a legújabb funkciókkal és javításokkal legyen frissítve.  További információ: [Private Cloud upgrade Concepts cikk][concepts-upgrades].

## <a name="vcenter-access-and-identity"></a>vCenter-hozzáférés és-identitás

A CloudAdmin csoport a vCenter-ben biztosítja a jogosultságokat. A csoportot helyileg kezelheti a vCenter-ben. Egy másik lehetőség a vCenter LDAP-alapú egyszeri bejelentkezés és a Azure Active Directory integrációja. A privát felhő üzembe helyezése után engedélyezheti az integrációt. 

A táblázat a **CloudAdmin** és a **CloudGlobalAdmin** jogosultságokat jeleníti meg.

|  Jogosultság-készlet           | CloudAdmin | CloudGlobalAdmin | Megjegyzés |
| :---                     |    :---:   |       :---:      |   :--:  |
|  Riasztások                  | A CloudAdmin-felhasználók riasztási jogosultságokkal rendelkeznek a Compute-ResourcePool és a virtuális gépek riasztásához.     |          --        |  -- |
|  Automatikus üzembe helyezés             |  --  |        --        |  A Microsoft üzemelteti a gazdagépek felügyeletét.  |
|  Tanúsítványok            |  --  |        --       |  A Microsoft végzi a Tanúsítványkezelőt.  |
|  Tartalomtár         | Egy CloudAdmin-felhasználónak van jogosultsága fájlok létrehozására és használatára a dokumentumtárakban.    |         Engedélyezve az SSO-val.         |  A Microsoft a tartalmi könyvtárban lévő fájlokat a ESXi-gazdagépek számára terjeszti.  |
|  Adatközpont              |  --  |        --          |  A Microsoft az összes adatközpont-műveletet elvégzi.  |
|  Adattár               | Adattár. AllocateSpace, adattár. Tallózás, Datastore.Config, adattár. DeleteFile, adattár. FileManagement, adattár. UpdateVirtualMachineMetadata     |    --    |   -- |
|  ESX-ügynök kezelője       |  --  |         --       |  A Microsoft minden műveletet elvégez.  |
|  Mappa                  |  A CloudAdmin-felhasználók minden mappa jogosultsággal rendelkeznek.     |  --  |  --  |
|  Globális                  |  Global. CancelTask, Global. GlobalTag, Global. Health, Global. LogEvent, Global. ManageCustomFields, Global. ServiceManagers, Global. SetCustomField, Global.SystemTag         |                  |    |
|  Gazdagép                    |  Host. HBR. HbrManagement      |        --          |  A Microsoft minden más gazdagép-műveletet hajt végre.  |
|  InventoryService        |  InventoryService. tagging      |        --          |  --  |
|  Network (Hálózat)                 |  Network. assign    |                  |  A Microsoft minden más hálózati műveletet hajt végre.  |
|  Engedélyek             |  --  |        --       |  A Microsoft minden engedély műveletet végez.  |
|  Profil alapú tárolás  |  --  |        --       |  A Microsoft minden profillal kapcsolatos műveletet végez.  |
|  Erőforrás                |  A CloudAdmin-felhasználók minden erőforrás-jogosultsággal rendelkeznek.        |      --       | --   |
|  Ütemezett feladat          |  A CloudAdmin-felhasználók minden ScheduleTask jogosultsággal rendelkeznek.   |   --   | -- |
|  Munkamenetek                |  Sessions. GlobalMessage, Sessions. ValidateSession      |   --   |  A Microsoft minden más munkamenet-műveletet hajt végre.  |
|  Tárolási nézetek           |  StorageViews. View   |        --          |  A Microsoft az összes többi tárolási nézeti műveletet (szolgáltatás konfigurálása) végzi.  |
|  Feladatok                   |  --  |  --   |  A Microsoft kezeli a feladatokat kezelő bővítményeket.  |
|  vApp                    |  A CloudAdmin-felhasználók minden vApp jogosultsággal rendelkeznek.  |  --  |  --  |
|  Virtuális gép         |  A CloudAdmin-felhasználók minden VirtualMachine jogosultsággal rendelkeznek.  |  --  |  --  |
|  vService                |  A CloudAdmin-felhasználók minden vService jogosultsággal rendelkeznek.  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>NSX – T Manager-hozzáférés és-identitás

A "rendszergazda" fiók használatával férhet hozzá a NSX-T kezelőhöz. Teljes körű jogosultságokkal rendelkezik, és lehetővé teszi T1 útválasztók, logikai kapcsolók és minden szolgáltatás létrehozását és kezelését. A jogosultságok hozzáférést biztosítanak az NSX-T T0 útválasztóhoz. A t0 útválasztóra való váltás a hálózati teljesítmény romlása vagy a saját Felhőbeli hozzáférés nélkül is csökkenhet. Nyisson meg egy támogatási kérést a Azure Portalban, hogy az NSX-T T0-útválasztón bármilyen módosítást igényeljen.
  
## <a name="next-steps"></a>Következő lépések

Most, hogy lefedette az Azure VMware-megoldás hozzáférési és identitási fogalmait, érdemes megismernie az alábbiakat:

- [Saját Felhőbeli frissítési fogalmak](concepts-upgrades.md).
- [vSphere szerepköralapú hozzáférés-vezérlés az Azure VMware-megoldáshoz](concepts-role-based-access-control.md).
- [Az Azure VMware megoldás erőforrásának engedélyezése](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
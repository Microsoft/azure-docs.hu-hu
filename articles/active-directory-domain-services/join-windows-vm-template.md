---
title: Windows rendszerű virtuális gép csatlakoztatása az Azure AD DShoz – sablon használata | Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat Azure Resource Manager-sablonokkal új vagy meglévő Windows Server rendszerű virtuális géphez egy Azure Active Directory Domain Services felügyelt tartományhoz.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e7245e8e468ea051ee095d97cc250ad303aa80a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619436"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Windows Server rendszerű virtuális gép csatlakoztatása egy Azure Active Directory Domain Services felügyelt tartományhoz Resource Manager-sablon használatával

Az Azure Virtual Machines (VM) üzembe helyezésének és konfigurálásának automatizálásához Resource Manager-sablont is használhat. Ezek a sablonok lehetővé teszik, hogy minden alkalommal konzisztens központi telepítéseket hozzon létre. A bővítmények a sablonokban is szerepelhetnek, így a virtuális gép automatikusan konfigurálható a telepítés részeként. Az egyik hasznos bővítmény csatlakozik a virtuális gépekhez egy tartományhoz, amely Azure Active Directory Domain Services (Azure AD DS) által felügyelt tartományokhoz használható.

Ebből a cikkből megtudhatja, hogyan hozhat létre és csatlakozhat Windows Server rendszerű virtuális gépekhez egy Azure AD DS felügyelt tartományhoz Resource Manager-sablonok használatával. Azt is megtudhatja, hogyan csatlakozhat egy meglévő Windows Server rendszerű virtuális géphez egy Azure AD DS-tartományhoz.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, az első oktatóanyag [egy Azure Active Directory Domain Services felügyelt tartományt hoz létre és konfigurál][create-azure-ad-ds-instance].
* A felügyelt tartomány részét képező felhasználói fiók.

## <a name="azure-resource-manager-template-overview"></a>Azure Resource Manager sablonok áttekintése

A Resource Manager-sablonok lehetővé teszik az Azure-infrastruktúra definiálását a kódban. A szükséges erőforrások, hálózati kapcsolatok vagy virtuális gépek konfigurációja a sablonban is meghatározhatók. Ezek a sablonok minden alkalommal konzisztens, reprodukálható központi telepítéseket hoznak létre, és a módosítások elvégzése közben is telepíthetők. További információ: [Azure Resource Manager sablonok áttekintése][template-overview].

Az egyes erőforrások JavaScript Object Notation (JSON) használatával vannak definiálva egy sablonban. A következő JSON-példa a *Microsoft. számítás/virtualMachines/Extensions* erőforrástípus használatával telepíti a Active Directory tartományhoz való csatlakozás bővítményt. A rendszer a telepítéskor megadott paramétereket használja. A bővítmény telepítésekor a virtuális gép a megadott felügyelt tartományhoz van csatlakoztatva.

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

Ez a virtuálisgép-bővítmény akkor is üzembe helyezhető, ha nem hoz létre virtuális gépet ugyanabban a sablonban. A jelen cikkben szereplő példák a következő megközelítéseket mutatják be:

* [Windows Server rendszerű virtuális gép létrehozása és csatlakoztatása felügyelt tartományhoz](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [Meglévő Windows Server rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Windows Server rendszerű virtuális gép létrehozása és csatlakoztatása felügyelt tartományhoz

Ha Windows Server rendszerű virtuális gépre van szüksége, hozzon létre és konfiguráljon egy Resource Manager-sablon használatával. A virtuális gép telepítésekor a rendszer telepíti a bővítményt, hogy csatlakozzon a virtuális géphez egy felügyelt tartományhoz. Ha már rendelkezik egy felügyelt tartományhoz csatlakozni kívánó virtuális géppel, ugorjon a [meglévő Windows Server rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz](#join-an-existing-windows-server-vm-to-a-managed-domain)lehetőségre.

Ha Windows Server rendszerű virtuális gépet szeretne létrehozni, csatlakoztassa egy felügyelt tartományhoz, hajtsa végre a következő lépéseket:

1. Keresse meg a gyors üzembe helyezési [sablont](https://azure.microsoft.com/resources/templates/201-vm-domain-join/). Válassza ki az **Azure-ba való üzembe helyezés** lehetőségét.
1. Az **Egyéni telepítés** lapon adja meg a következő adatokat egy Windows Server rendszerű virtuális gép a felügyelt tartományhoz való létrehozásához és csatlakoztatásához:

    | Beállítás                   | Érték |
    |---------------------------|-------|
    | Előfizetés              | Válassza ki ugyanazt az Azure-előfizetést, amelyben engedélyezte a Azure AD Domain Services. |
    | Erőforráscsoport            | Válassza ki a virtuális géphez tartozó erőforráscsoportot. |
    | Hely                  | Válassza ki a virtuális gép helyét. |
    | Meglévő VNET neve        | Annak a meglévő virtuális hálózatnak a neve, amelyhez a virtuális gépet (például *myVnet*) szeretné kapcsolni. |
    | Létező alhálózat neve      | A meglévő virtuális hálózat alhálózatának neve, például *munkaterhelések*. |
    | DNS-címke előtagja          | Adja meg a virtuális gép számára használandó DNS-nevet (például *myvm*). |
    | Virtuális gép mérete                   | Meg kell adni a virtuális gép méretét, például *Standard_DS2_v2*. |
    | Csatlakozás tartományhoz            | A felügyelt tartomány DNS-neve, például *aaddscontoso.com*. |
    | Tartomány felhasználóneve           | A felügyelt tartomány felhasználói fiókja, amelyet a virtuális gép a felügyelt tartományhoz való csatlakoztatásához kell használni, például: `contosoadmin@aaddscontoso.com` . Ennek a fióknak a felügyelt tartomány részét kell képeznie. |
    | Tartományi jelszó           | Az előző beállításban megadott felhasználói fiók jelszava. |
    | Választható szervezeti egység elérési útja          | Az az egyéni szervezeti egység, amelyben hozzá szeretné adni a virtuális gépet. Ha nem ad meg értéket ehhez a paraméterhez, a rendszer hozzáadja a virtuális gépet az alapértelmezett *HRE DC számítógépek* szervezeti egységhez. |
    | Virtuális gép rendszergazdai felhasználóneve         | A virtuális gépen létrehozandó helyi rendszergazdai fiókot kell megadnia. |
    | Virtuális gép rendszergazdai jelszava         | Helyi rendszergazdai jelszót kell megadni a virtuális géphez. Hozzon létre egy erős helyi rendszergazdai jelszót a jelszó-kényszerített támadásokkal szembeni védelem érdekében. |

1. Tekintse át a használati feltételeket, majd jelölje be az Elfogadom **a fenti feltételeket és kikötéseket** jelölőnégyzetet. Ha elkészült, válassza a **vásárlás** lehetőséget a virtuális gép felügyelt tartományhoz való létrehozásához és csatlakoztatásához.

> [!WARNING]
> **A jelszavakat körültekintően kezelheti.**
> A sablon-paraméter fájlja a felügyelt tartomány részét képező felhasználói fiók jelszavát kéri le. Ne adja meg manuálisan az értékeket ebbe a fájlba, és hagyja azt elérhetővé a fájlmegosztás vagy más megosztott helyen.

A telepítés sikeres befejezéséhez néhány percet is igénybe vehet. Ha elkészült, a Windows rendszerű virtuális gép létrejön, és csatlakozik a felügyelt tartományhoz. A virtuális gép felügyelhető vagy bejelentkezhet tartományi fiókok használatával.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>Meglévő Windows Server rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz

Ha rendelkezik egy meglévő virtuális géppel vagy virtuálisgép-csoporttal, amelyet egy felügyelt tartományhoz szeretne csatlakoztatni, akkor egy Resource Manager-sablonnal egyszerűen üzembe helyezheti a virtuálisgép-bővítményt.

Ha meglévő Windows Server rendszerű virtuális gépet szeretne csatlakoztatni egy felügyelt tartományhoz, hajtsa végre a következő lépéseket:

1. Keresse meg a gyors üzembe helyezési [sablont](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/). Válassza ki az **Azure-ba való üzembe helyezés** lehetőségét.
1. Az **Egyéni telepítés** lapon adja meg a következő adatokat a virtuális géphez a felügyelt tartományhoz való csatlakozáshoz:

    | Beállítás                   | Érték |
    |---------------------------|-------|
    | Előfizetés              | Válassza ki ugyanazt az Azure-előfizetést, amelyben engedélyezte a Azure AD Domain Services. |
    | Erőforráscsoport            | Válassza ki az erőforráscsoportot a meglévő virtuális géppel. |
    | Hely                  | Válassza ki a meglévő virtuális gép helyét. |
    | VIRTUÁLIS gépek listája                   | Adja meg a meglévő virtuális gép (ek) vesszővel tagolt listáját a felügyelt tartományhoz való csatlakozáshoz, például: *myVM1, myVM2*. |
    | Tartományhoz való csatlakozás felhasználóneve     | A felügyelt tartomány felhasználói fiókja, amelyet a virtuális gép a felügyelt tartományhoz való csatlakoztatásához kell használni, például: `contosoadmin@aaddscontoso.com` . Ennek a fióknak a felügyelt tartomány részét kell képeznie. |
    | Tartományhoz való csatlakozás felhasználói jelszava | Az előző beállításban megadott felhasználói fiók jelszava. |
    | Választható szervezeti egység elérési útja          | Az az egyéni szervezeti egység, amelyben hozzá szeretné adni a virtuális gépet. Ha nem ad meg értéket ehhez a paraméterhez, a rendszer hozzáadja a virtuális gépet az alapértelmezett *HRE DC számítógépek* szervezeti egységhez. |

1. Tekintse át a használati feltételeket, majd jelölje be az Elfogadom **a fenti feltételeket és kikötéseket** jelölőnégyzetet. Ha elkészült, válassza a **vásárlás** lehetőséget a virtuális géphez a felügyelt tartományhoz való csatlakozáshoz.

> [!WARNING]
> **A jelszavakat körültekintően kezelheti.**
> A sablon-paraméter fájlja a felügyelt tartomány részét képező felhasználói fiók jelszavát kéri le. Ne adja meg manuálisan az értékeket ebbe a fájlba, és hagyja azt elérhetővé a fájlmegosztás vagy más megosztott helyen.

Néhány percet vesz igénybe, hogy a központi telepítés sikeresen befejeződjön. Ha elkészült, a megadott Windows rendszerű virtuális gépek csatlakoznak a felügyelt tartományhoz, és felügyelhetők vagy bejelentkezhetnek a tartományi fiókok használatával.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben a Azure Portal segítségével konfigurálta és telepítette az erőforrásokat sablonok használatával. Az erőforrásokat [Azure PowerShell][deploy-powershell] vagy az [Azure CLI][deploy-cli]használatával is üzembe helyezheti Resource Manager-sablonokkal.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md

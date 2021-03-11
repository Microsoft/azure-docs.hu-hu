---
title: Bejelentkezés Azure Active Directory hitelesítő adatokkal rendelkező linuxos virtuális gépre
description: Megtudhatja, hogyan hozhat létre és konfigurálhat Linux rendszerű virtuális gépeket Azure Active Directory hitelesítéssel való bejelentkezéshez.
author: SanDeo-MSFT
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: sandeo
ms.openlocfilehash: 44dfd07a5b749d88552bb1dcac2ee4b4e5ce65e4
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565240"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Előzetes verzió: bejelentkezés az Azure-beli linuxos virtuális gépre Azure Active Directory hitelesítéssel

Az Azure-beli linuxos virtuális gépek (VM-EK) biztonságának növelése érdekében integrálható Azure Active Directory (AD) hitelesítéssel. Ha az Azure AD-hitelesítést Linux rendszerű virtuális gépekhez használja, központilag szabályozza és érvényesíti a virtuális gépekhez való hozzáférést engedélyező vagy megtagadó házirendeket. Ez a cikk bemutatja, hogyan hozhat létre és konfigurálhat Linux rendszerű virtuális gépeket az Azure AD-hitelesítés használatához.


> [!IMPORTANT]
> Azure Active Directory hitelesítés jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Ezt a szolgáltatást olyan teszt virtuális gépen használja, amelyet a tesztelés után el kell vetni.
>


Az Azure AD-hitelesítés számos előnnyel jár az Azure-beli linuxos virtuális gépekre való bejelentkezéshez, beleértve a következőket:

- **Fokozott biztonság:**
  - A vállalati AD hitelesítő adataival jelentkezhet be az Azure Linux rendszerű virtuális gépekre. Nincs szükség helyi rendszergazdai fiókok létrehozására és a hitelesítő adatok élettartamának kezelésére.
  - A helyi rendszergazdai fiókokra való támaszkodás csökkentésével nem kell aggódnia a hitelesítő adatok elvesztése/ellopása, a gyenge hitelesítő adatokat konfiguráló felhasználók stb.
  - Az Azure AD-címtárhoz konfigurált jelszó-bonyolultsági és jelszó-élettartam szabályzatok a Linux rendszerű virtuális gépeket is biztonságossá teszik.
  - Az Azure Virtual Machines szolgáltatásba való bejelentkezés további biztonságossá tételéhez konfigurálhatja a többtényezős hitelesítést.
  - A Linux rendszerű virtuális gépekre való bejelentkezés lehetősége Azure Active Directory is működik az [összevonási szolgáltatásokat](../../active-directory/hybrid/how-to-connect-fed-whatis.md)használó ügyfelek számára.

- **Zökkenőmentes együttműködés:** Az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC) megadhatja, hogy ki tud bejelentkezni egy adott virtuális gépre normál felhasználóként vagy rendszergazdai jogosultságokkal. Amikor a felhasználók csatlakoznak vagy elhagynak egy csapatot, frissítheti a virtuális gép Azure RBAC-szabályzatát, hogy a megfelelő módon biztosítson hozzáférést. Ez a felhasználói élmény sokkal egyszerűbb, mint a virtuális gépek felesleges SSH nyilvános kulcsainak eltávolításához. Ha az alkalmazottak elhagyják a szervezetét, és a felhasználói fiókja le van tiltva vagy el lett távolítva az Azure AD-ből, már nem férnek hozzá az erőforrásaihoz.

## <a name="supported-azure-regions-and-linux-distributions"></a>Támogatott Azure-régiók és Linux-disztribúciók

A következő Linux-disztribúciók jelenleg a funkció előzetes verziójában támogatottak:

| Disztribúció | Verzió |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE LEAP 42,3 |
| RedHat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14,04 LTS, Ubuntu Server 16,04 és Ubuntu Server 18,04 |


A szolgáltatás előzetes verziójában jelenleg a következő Azure-régiók támogatottak:

- Minden globális Azure-régió

>[!IMPORTANT]
> Az előzetes verziójú funkció használatához csak egy támogatott Linux-disztribúciót és egy támogatott Azure-régiót telepítsen. A funkció Azure Government vagy szuverén felhőkben nem támogatott.
>
> Ez a bővítmény nem használható az Azure Kubernetes Service-(ak-) fürtökön. További információ: [támogatási szabályzat az AK](../../aks/support-policies.md)-hoz.


Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.31 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

Ha engedélyezni szeretné az Azure AD-hitelesítést az Azure-beli linuxos virtuális gépeken, gondoskodnia kell arról, hogy a virtuális gépek hálózati konfigurációja engedélyezze a kimenő hozzáférést a 443-as TCP-porton keresztül a következő végpontokhoz:

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https: \/ /Device.login.microsoftonline.com
* https: \/ /Pas.Windows.net
* https:\//management.azure.com
* https: \/ /packages.microsoft.com

> [!NOTE]
> Jelenleg az Azure hálózati biztonsági csoportjai nem konfigurálhatók az Azure AD-hitelesítéssel engedélyezett virtuális gépekhez.

## <a name="create-a-linux-virtual-machine"></a>Linux rendszerű virtuális gép létrehozása

Hozzon létre egy erőforráscsoportot az [az Group Create](/cli/azure/group#az-group-create)paranccsal, majd hozzon létre egy virtuális gépet az [az VM Create](/cli/azure/vm#az-vm-create) használatával egy támogatott disztribúcióval és egy támogatott régióban. A következő példa egy *myVM* nevű virtuális gépet telepít, amely *Ubuntu 16,04 LTS* -et használ egy *myResourceGroup* nevű erőforráscsoporthoz a *southcentralus* régióban. Az alábbi példákban megadhatja a saját erőforráscsoport és a virtuális gépek nevét igény szerint.

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A virtuális gép és a kapcsolódó erőforrások létrehozása csak néhány percet vesz igénybe.

## <a name="install-the-azure-ad-login-vm-extension"></a>Az Azure AD bejelentkezési virtuálisgép-bővítményének telepítése

> [!NOTE]
> Ha a bővítményt egy korábban létrehozott virtuális gépre telepíti, győződjön meg arról, hogy a gép legalább 1 GB memóriát foglalt le, és a bővítmény telepítése sikertelen lesz.

Ha Azure AD-beli hitelesítő adatokkal szeretne bejelentkezni egy Linux rendszerű virtuális gépre, telepítse a Azure Active Directory login VM-bővítményt. A virtuálisgép-bővítmények olyan kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-beli virtuális gépeken. Az az [VM Extension set](/cli/azure/vm/extension#az-vm-extension-set) paranccsal telepítse a *AADLoginForLinux* -bővítményt a *MyVM* nevű virtuális gépre a *myResourceGroup* erőforráscsoporthoz:

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

A *sikeres* *provisioningState* akkor jelenik meg, ha a bővítmény telepítése sikeresen megtörtént a virtuális gépen. A virtuális gépnek egy futó virtuálisgép-ügynökre van szüksége a bővítmény telepítéséhez. További információ: virtuálisgép- [ügynök áttekintése](../extensions/agent-windows.md).

## <a name="configure-role-assignments-for-the-vm"></a>Szerepkör-hozzárendelések konfigurálása a virtuális géphez

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) házirend határozza meg, hogy ki jelentkezhet be a virtuális gépre. A VM-bejelentkezés engedélyezéséhez két Azure-szerepkör használható:

- **Virtuális gép rendszergazdai bejelentkezése**: az ehhez a szerepkörhöz hozzárendelt felhasználók bejelentkezhetnek egy Azure-beli virtuális gépre a Windows rendszergazdai vagy a Linux root felhasználói jogosultságokkal.
- **Virtuális gép felhasználói bejelentkezése**: az ehhez a szerepkörhöz hozzárendelt felhasználók rendszeres felhasználói jogosultságokkal jelentkezhetnek be egy Azure-beli virtuális gépre.

> [!NOTE]
> Annak engedélyezéséhez, hogy a felhasználó SSH-kapcsolaton keresztül jelentkezzen be a virtuális gépre, hozzá kell rendelnie a *virtuális gép rendszergazdai felhasználónevét* vagy a *virtuális gép felhasználói bejelentkezési* szerepkörét. A virtuális gép rendszergazdai felhasználónevét és a virtuális gép felhasználói bejelentkezési szerepköreit a dataActions használja, ezért nem rendelhető hozzá a felügyeleti csoport hatóköréhez. Jelenleg ezek a szerepkörök csak az előfizetéshez, az erőforráscsoport vagy az erőforrás-hatókörhöz rendelhetők hozzá. Egy virtuális géphez hozzárendelt *tulajdonosi* vagy *közreműködői* szerepkörökkel rendelkező Azure-felhasználó nem jogosult automatikusan bejelentkezni a virtuális gépre SSH-kapcsolaton keresztül. 

Az alábbi példa az [az role hozzárendelés Create](/cli/azure/role/assignment#az-role-assignment-create) paranccsal rendeli hozzá a *virtuális gép rendszergazdai bejelentkezési* szerepkörét a virtuális géphez az aktuális Azure-felhasználóhoz. Az aktív Azure-fiókjának felhasználónevét az az [Account show](/cli/azure/account#az-account-show)paranccsal szerezheti be, a *hatókör* pedig az előző lépésben létrehozott virtuális gépre az [az VM show](/cli/azure/vm#az-vm-show)paranccsal. A hatókör egy erőforráscsoport vagy előfizetés szintjén is hozzárendelhető, és a szokásos Azure RBAC öröklési engedélyek is érvényesek. További információ: [Azure RBAC](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Ha a HRE tartomány és a bejelentkezési Felhasználónév tartománya nem egyezik, meg kell adnia a felhasználói fiók objektumazonosítóát a *--megbízott-Object-ID azonosítóval*, nem csak a-- *megbízott* felhasználónevet. A felhasználói fiókhoz tartozó objektumazonosítót az [az ad User List](/cli/azure/ad/user#az-ad-user-list)paranccsal kérheti le.

Az Azure-előfizetési erőforrásokhoz való hozzáférés az Azure RBAC való kezelésével kapcsolatos további információkért lásd az [Azure CLI](../../role-based-access-control/role-assignments-cli.md), [Azure Portal](../../role-based-access-control/role-assignments-portal.md)vagy [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)használatát ismertető témakört.

## <a name="using-conditional-access"></a>Feltételes hozzáférés használata

A feltételes hozzáférési szabályzatok, például a többtényezős hitelesítés vagy a felhasználói bejelentkezés kockázatának érvényesítése előtt engedélyezheti a hozzáférést az Azure-beli Linux rendszerű virtuális gépekhez, amelyek engedélyezve vannak az Azure AD-bejelentkezéssel. A feltételes hozzáférési szabályzat alkalmazásához ki kell választania az "Azure Linux VM-bejelentkezés" alkalmazást a Cloud apps vagy a műveletek hozzárendelési beállításból, majd a bejelentkezési kockázatot feltételként kell használnia, és/vagy a többtényezős hitelesítést kell megadni hozzáférés-vezérlésként. 

> [!WARNING]
> Felhasználónkénti engedélyezett/kényszerített Azure AD-Multi-Factor Authentication nem támogatott a virtuális gép bejelentkezéséhez.

## <a name="log-in-to-the-linux-virtual-machine"></a>Bejelentkezés a Linux rendszerű virtuális gépre

Először tekintse meg a virtuális gép nyilvános IP-címét az [az VM show](/cli/azure/vm#az-vm-show)paranccsal:

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Jelentkezzen be az Azure Linux rendszerű virtuális gépre az Azure AD-beli hitelesítő adataival. A `-l` paraméter segítségével megadhatja saját Azure ad-fiókjának a címeit. Cserélje le a példában szereplő fiókot a sajátra. A fiók címét minden kisbetűs értékben kell megadni. Cserélje le a példában szereplő IP-címet a virtuális gép nyilvános IP-címére az előző parancsból.

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

A rendszer felszólítja, hogy jelentkezzen be az Azure AD-be egy egyszeri használatú kóddal a következő címen: [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) . Másolja és illessze be az egyszer használatos kódot az eszköz bejelentkezési oldalára.

Ha a rendszer kéri, adja meg az Azure AD bejelentkezési hitelesítő adatait a bejelentkezési oldalon. 

A következő üzenet jelenik meg a böngészőben a sikeres hitelesítés után: `You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

A böngészőablak bezárásához térjen vissza az SSH-parancssorba, majd nyomja le az **ENTER** billentyűt. 

Most bejelentkezett az Azure Linux rendszerű virtuális gépre a hozzárendelt szerepkör-engedélyekkel, mint például a *VM-felhasználó* vagy a VM- *rendszergazda*. Ha a felhasználói fiókja a *virtuális gép rendszergazdai bejelentkezési* szerepköréhez van rendelve, akkor a paranccsal olyan `sudo` parancsokat futtathat, amelyek rendszergazdai jogosultságokat igényelnek.

## <a name="sudo-and-aad-login"></a>Sudo és HRE bejelentkezés

Amikor először futtatja a sudo-t, a rendszer kérni fogja, hogy másodszor is hitelesítse magát. Ha nem szeretné ismét hitelesíteni a sudo-t, szerkesztheti a sudoers-fájlt, `/etc/sudoers.d/aad_admins` és lecserélheti a következő sort:

```bash
%aad_admins ALL=(ALL) ALL
```

Ezzel a sorral:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Bejelentkezési problémák elhárítása

Az Azure AD-beli hitelesítő adatokkal való SSH-val való próbálkozáskor előforduló gyakori hibák közé tartozik az Azure-szerepkörök hozzárendelése sem, és a rendszer ismételten kéri a bejelentkezést. Ezeket a problémákat a következő fejezetek segítségével orvosolhatja.

### <a name="access-denied-azure-role-not-assigned"></a>Hozzáférés megtagadva: az Azure-szerepkör nincs hozzárendelve

Ha a következő hibaüzenet jelenik meg az SSH-parancssorban, ellenőrizze, hogy konfigurálta-e az Azure RBAC-házirendeket a virtuális géphez, amely a felhasználónak vagy a *virtuális gép rendszergazdai felhasználónevét* vagy a *virtuális gép felhasználói bejelentkezési* szerepkörét biztosítja:

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```
> [!NOTE]
> Ha az Azure szerepkör-hozzárendelésekkel kapcsolatos problémákba ütközik, tekintse meg az [Azure-RBAC hibaelhárítását](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit)ismertető témakört.

### <a name="continued-ssh-sign-in-prompts"></a>További SSH bejelentkezési kérések

Ha sikeresen elvégezte a hitelesítési lépést egy webböngészőben, akkor előfordulhat, hogy a rendszer azonnal felszólítja, hogy új kóddal jelentkezzen be. Ezt a hibát általában az SSH-parancssorban megadott bejelentkezési név és az Azure AD-be a-nal bejelentkezett fiók közötti eltérés okozza. A probléma megoldásához:

- Győződjön meg arról, hogy az SSH-parancssorban megadott bejelentkezési név helyes. A bejelentkezési név elírása eltérést okozhat az SSH-parancssorban megadott bejelentkezési név és az Azure AD-be a-ban bejelentkezett fiók között. Tegyük fel például, hogy a *azuresuer \@ contoso.onmicrosoft.com* -et adta meg az *azureuser \@ contoso.onmicrosoft.com* helyett.
- Ha több felhasználói fiókkal rendelkezik, ügyeljen arra, hogy a böngészőablakban ne adjon meg másik felhasználói fiókot az Azure AD-ba való bejelentkezéskor.
- A Linux egy kis-és nagybetűket megkülönböztető operációs rendszer. Különbség van a (z) és a (z) között Azureuser@contoso.onmicrosoft.com azureuser@contoso.onmicrosoft.com , ami eltérő lehet. Győződjön meg arról, hogy az UPN-t a megfelelő kis-és nagybetűkkel határozza meg az SSH-parancssorban.

### <a name="other-limitations"></a>Egyéb korlátozások

Azok a felhasználók, akik beágyazott csoportokon vagy szerepkör-hozzárendeléseken keresztül örökölnek hozzáférési jogosultságokat, jelenleg nem támogatottak. A felhasználónak vagy csoportnak közvetlenül hozzá kell rendelnie a [szükséges szerepkör-hozzárendeléseket](#configure-role-assignments-for-the-vm). A felügyeleti csoportok vagy a beágyazott csoportok szerepkör-hozzárendelések használata például nem biztosítja a megfelelő engedélyeket a felhasználó bejelentkezésének engedélyezéséhez.

## <a name="preview-feedback"></a>Az előzetes verzióval kapcsolatos visszajelzés

Ossza meg visszajelzését erről az előzetes verziójú szolgáltatásról, vagy jelentse az [Azure ad visszajelzési fórumát](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) használó problémákat

## <a name="next-steps"></a>Következő lépések

További információ a Azure Active Directoryről: [Mi az Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
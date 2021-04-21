---
title: Bejelentkezés Linux rendszerű virtuális gépre Azure Active Directory hitelesítő adatokkal
description: Megtudhatja, hogyan hozhat létre és konfigurálhatja a Linux rendszerű virtuális gépeket a Azure Active Directory való bejelentkezéshez.
author: SanDeo-MSFT
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: sandeo
ms.openlocfilehash: 654d47102685c04d6440d7c155e4d6eb931abcae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788114"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Előzetes verzió: Bejelentkezés Linux rendszerű virtuális gépre az Azure-ban Azure Active Directory hitelesítéssel

Az Azure-beli Linux rendszerű virtuális gépek biztonságának növelése érdekében integrálhatja az Azure Active Directory (AD) hitelesítéssel. Ha Linux rendszerű virtuális gépekhez Azure AD-hitelesítést használ, központilag vezérelhető és érvényesíthetőek azok a szabályzatok, amelyek engedélyezik vagy megtagadják a virtuális gépekhez való hozzáférést. Ez a cikk bemutatja, hogyan hozhat létre és konfigurálható Linux rendszerű virtuális gép az Azure AD-hitelesítés használatára.


> [!IMPORTANT]
> Azure Active Directory hitelesítés jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Ezt a funkciót olyan teszt virtuális gépen használja, amely a tesztelés után várhatóan el lesz vetve.
>


Az Azure-beli Linux rendszerű virtuális gépekre való bejelentkezésnek számos előnye van, többek között a következők:

- **Továbbfejlesztett biztonság:**
  - A vállalati AD-beli hitelesítő adatokkal bejelentkezhet az Azure-beli Linux rendszerű virtuális gépekre. Nincs szükség helyi rendszergazdai fiókok létrehozására és a hitelesítő adatok élettartamának kezelésére.
  - A helyi rendszergazdai fiókokra való függés csökkentésével nem kell aggódnia a hitelesítő adatok elvesztése/ellopása, a gyenge hitelesítő adatokat konfiguráló felhasználók stb. miatt.
  - Az Azure AD-címtárhoz konfigurált jelszó-összetettségi és jelszó-élettartamra vonatkozó szabályzatok segítenek a Linux rendszerű virtuális gépek biztonságának beállításában is.
  - Az Azure-beli virtuális gépekre való bejelentkezés további biztonságossá való beállítása érdekében konfigurálhatja a többtényezős hitelesítést.
  - A Linux rendszerű virtuális gépekre a virtuális gépekkel való Azure Active Directory összevonási szolgáltatásokat igénybe vevő [ügyfelek esetében is működik.](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

- **Zökkenőmentes együttműködés:** Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével megadhatja, hogy ki jelentkezhet be egy adott virtuális gépre normál felhasználóként vagy rendszergazdai jogosultságokkal. Amikor a felhasználók csatlakoznak a csapathoz, vagy elhagyják a csapatot, frissítheti a virtuális gép Azure RBAC-szabályzatát, hogy a megfelelő módon biztosítsa a hozzáférést. Ez a folyamat sokkal egyszerűbb, mint a virtuális gépek eltávolítása a szükségtelen nyilvános SSH-kulcsok eltávolításához. Ha az alkalmazottak elhagyják a szervezetet, és a felhasználói fiókjuk le van tiltva vagy el van távolítva az Azure AD-ból, többé nem férnek hozzá az erőforrásokhoz.

## <a name="supported-azure-regions-and-linux-distributions"></a>Támogatott Azure-régiók és Linux-disztribúciók

A szolgáltatás előzetes verziója jelenleg a következő Linux-disztribúciókat támogatja:

| Disztribúció | Verzió |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Leap 42.3 |
| RedHat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 és Ubuntu Server 18.04 |


A szolgáltatás előzetes verziója jelenleg a következő Azure-régiókat támogatja:

- Minden globális Azure-régió

>[!IMPORTANT]
> Az előzetes verziójú funkció használatához csak támogatott Linux-disztribúciót telepítsen egy támogatott Azure-régióban. A szolgáltatás nem támogatott az Azure Government szuverén felhőkben.
>
> Ez a bővítmény nem használható Azure Kubernetes Service (AKS-) fürtökön. További információ: Támogatási [szabályzatok az AKS-hez.](../../aks/support-policies.md)


Ha a CLI helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.31-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

Ha engedélyezni szeretné az Azure AD-hitelesítést a Linux rendszerű virtuális gépeken az Azure-ban, meg kell győződni arról, hogy a virtuális gépek hálózati konfigurációja engedélyezi a következő végpontok kimenő hozzáférését a 443-as TCP-porton keresztül:

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https: \/ /device.login.microsoftonline.com
* https: \/ /pas.windows.net
* https:\//management.azure.com
* https: \/ /packages.microsoft.com

> [!NOTE]
> Az Azure-beli hálózati biztonsági csoportok jelenleg nem konfigurálhatók az Azure AD-hitelesítéssel engedélyezett virtuális gépekhez.

## <a name="create-a-linux-virtual-machine"></a>Linux rendszerű virtuális gép létrehozása

Hozzon létre egy erőforráscsoportot [az az group create használatával,](/cli/azure/group#az_group_create)majd hozzon létre egy virtuális gépet az az vm [create](/cli/azure/vm#az_vm_create) használatával egy támogatott disztribúció és egy támogatott régióban. Az alábbi példa egy *myVM* nevű virtuális gépet helyez üzembe, amely *Ubuntu 16.04 LTS-t* használ egy *myResourceGroup* nevű erőforráscsoportban a *southcentralus régióban.* Az alábbi példákban szükség szerint meg lehet adni a saját erőforráscsoportját és virtuálisgép-nevét.

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

## <a name="install-the-azure-ad-login-vm-extension"></a>Az Azure AD bejelentkezési virtuálisgép-bővítmény telepítése

> [!NOTE]
> Ha ezt a bővítményt korábban létrehozott virtuális gépre telepíti, győződjön meg arról, hogy a gép legalább 1 GB lefoglalt memóriával rendelkezik, különben a bővítmény telepítése sikertelen lesz

A Linux rendszerű virtuális gépre Azure AD hitelesítő adatokkal való bejelentkezéshez telepítse a Azure Active Directory virtuálisgép-bővítményt. A virtuálisgép-bővítmények kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-beli virtuális gépeken. Az [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) használatával telepítse az *AADLoginForLinux* bővítményt a *myResourceGroup* erőforráscsoport *myVM* nevű virtuális gépén:

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

Ha a bővítmény sikeresen telepítve van a virtuális gépen, a *provisioningState* (Sikeres) jelenik meg.  A virtuális gépnek futó virtuálisgép-ügynökre van szüksége a bővítmény telepítéséhez. További információ: [Virtuálisgép-ügynök áttekintése.](../extensions/agent-windows.md)

## <a name="configure-role-assignments-for-the-vm"></a>Szerepkör-hozzárendelések konfigurálása a virtuális géphez

Az Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) szabályzata határozza meg, hogy ki jelentkezhet be a virtuális gépre. A virtuális gépekre való bejelentkezéshez két Azure-szerepkör használható:

- **Virtuális gép rendszergazdai bejelentkezése:** Az ezzel a szerepkörsel rendelkező felhasználók Windows-rendszergazdai vagy Linux-rendszergazdai jogosultságokkal jelentkezhetnek be egy Azure-beli virtuális gépre.
- **Virtuális gép felhasználói bejelentkezése:** Az ezzel a szerepkörsel rendelkező felhasználók normál felhasználói jogosultságokkal jelentkezhetnek be egy Azure-beli virtuális gépre.

> [!NOTE]
> Ahhoz, hogy egy felhasználó SSH-kapcsolaton keresztül bejelentkezhet  a virtuális gépre, hozzá kell rendelnie a Virtuális gép rendszergazdai bejelentkezése vagy a Virtuális gép felhasználói *bejelentkezése szerepkört.* A virtuális gép rendszergazdai bejelentkezési és felhasználói bejelentkezési szerepkörei a dataActions szerepkört használják, így a felügyeleti csoport hatókörében nem rendelhetők hozzá. Ezek a szerepkörök jelenleg csak az előfizetésben, az erőforráscsoportban vagy az erőforrás-hatókörben rendelhetők hozzá. A virtuális géphez hozzárendelt *Tulajdonos* *vagy* Közreműködő szerepkörökkel rendelkező Azure-felhasználó nem rendelkezik automatikusan jogosultsággal ahhoz, hogy SSH-val jelentkezzen be a virtuális gépre. 

Az alábbi példa az [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) parancsot használja a virtuális gép rendszergazdai *bejelentkezési* szerepkörének a virtuális géphez való hozzárendeléshez az aktuális Azure-felhasználóhoz. Az aktív Azure-fiók felhasználónevét az az  [account show](/cli/azure/account#az_account_show)használatával lehet beszerezni, és a hatókör az előző lépésben az az vm show használatával létrehozott virtuális gépre [van beállítva.](/cli/azure/vm#az_vm_show) A hatókör egy erőforráscsoport vagy előfizetés szintjén is hozzárendelhető, és az Azure RBAC normál öröklési engedélyei érvényesek. További információ: [Azure RBAC](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Ha az AAD-tartomány és a bejelentkezési felhasználónév tartománya nem egyezik, a felhasználói fiók objektumazonosítóját *a --assignee-object-id* azonosítóval kell megadnia, nem csak a *--assignee felhasználónevét.* A felhasználói fiók objektumazonosítóját az az ad user list listában [szerezheti be.](/cli/azure/ad/user#az_ad_user_list)

Az Azure-előfizetés erőforrásaihoz való hozzáférés Azure RBAC használatával való kezelésével kapcsolatos további információkért lásd: az [Azure CLI,](../../role-based-access-control/role-assignments-cli.md) [a Azure Portal](../../role-based-access-control/role-assignments-portal.md)vagy a [Azure PowerShell.](../../role-based-access-control/role-assignments-powershell.md)

## <a name="using-conditional-access"></a>Feltételes hozzáférés használata

A feltételes hozzáférési szabályzatokat, például a többtényezős hitelesítést vagy a felhasználói bejelentkezési kockázatellenőrzést az Azure-beli Linux rendszerű virtuális gépekhez való hozzáférés engedélyezése előtt kényszerítheti, amelyeken engedélyezve van az Azure AD-bejelentkezés. A feltételes hozzáférési szabályzat alkalmazásához válassza Microsoft Azure "Microsoft Azure Linux rendszerű virtuális gép bejelentkezési alkalmazása" lehetőséget a felhőalkalmazások vagy műveletek hozzárendelési lehetőségében, majd használja a bejelentkezési kockázatot feltételként, és/vagy többtényezős hitelesítést kell megkövetelnie hozzáférés-vezérlésként. 

> [!WARNING]
> A felhasználónkénti engedélyezett/kényszerített Azure AD Multi-Factor Authentication nem támogatott a virtuális gépek bejelentkezéséhez.

## <a name="log-in-to-the-linux-virtual-machine"></a>Bejelentkezés a Linux rendszerű virtuális gépre

Először tekintse meg a virtuális gép nyilvános IP-címét [az az vm show címmel:](/cli/azure/vm#az_vm_show)

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Jelentkezzen be az Azure Linux rendszerű virtuális gépre az Azure AD hitelesítő adataival. A `-l` paraméterrel megadhatja saját Azure AD-fiókjának címét. Cserélje le a példafiókot a sajátjára. A fiókcímeket kisbetűsen kell megadni. Cserélje le a példa IP-címet az előző parancsban található virtuális gép nyilvános IP-címére.

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

A rendszer arra kéri, hogy jelentkezzen be az Azure AD-be a következő egy egyszer használható kóddal: [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) . Másolja és illessze be az egyszer használható kódot az eszköz bejelentkezési oldalára.

Amikor a rendszer kéri, adja meg az Azure AD bejelentkezési hitelesítő adatait a bejelentkezési oldalon. 

A sikeres hitelesítés után a következő üzenet jelenik meg a webböngészőben: `You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Zárja be a böngészőablakot, térjen vissza az SSH-parancssorhoz, és nyomja le az **Enter** billentyűt. 

Ezzel bejelentkezett az Azure-beli Linux rendszerű virtuális gépre a hozzárendelt szerepkör-engedélyekkel, például a *virtuális* gép felhasználójával vagy *rendszergazdájával.* Ha a felhasználói fiókjához hozzá van rendelve a Virtuális gép *rendszergazdai bejelentkezési* szerepköre, a használatával futtathatja a gyökér szintű jogosultságokat igénylő `sudo` parancsokat.

## <a name="sudo-and-aad-login"></a>Sudo és AAD-bejelentkezés

A sudo első futtatásakor a rendszer másodszor is kérni fogja a hitelesítést. Ha nem szeretné újra hitelesíteni magát a sudo futtatásához, szerkessze a sudoers fájlt, és cserélje le ezt `/etc/sudoers.d/aad_admins` a sort:

```bash
%aad_admins ALL=(ALL) ALL
```

Ezzel a sokkal:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Bejelentkezési problémák elhárítása

Az SSH Azure AD hitelesítő adatokkal való megadásakor előforduló gyakori hibák közé tartoznak a hozzárendelt Azure-szerepkörök, valamint a bejelentkezésre vonatkozó ismételt kérések. A problémák megoldásához használja a következő szakaszokat.

### <a name="access-denied-azure-role-not-assigned"></a>Hozzáférés megtagadva: Nincs hozzárendelve Azure-szerepkör

Ha a következő hibaüzenet jelenik meg az SSH-parancssorban, ellenőrizze, hogy konfigurálta-e  az Azure RBAC-szabályzatokat a virtuális géphez, amelyek a virtuális gép rendszergazdai bejelentkezési vagy virtuálisgép-felhasználói bejelentkezési szerepkörét *biztosítják* a felhasználónak:

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```
> [!NOTE]
> Ha problémái vannak az Azure-beli szerepkör-hozzárendelésekkel, tekintse meg [az Azure RBAC hibaelhárításával kapcsolatos témakört.](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit)

### <a name="continued-ssh-sign-in-prompts"></a>Folyamatos SSH-bejelentkezési kérések

Ha sikeresen befejezte a hitelesítési lépést egy webböngészőben, a rendszer azonnal felszólítja, hogy jelentkezzen be újra egy új kóddal. Ezt a hibát általában az SSH-parancssorban megadott bejelentkezési név és az Azure AD-be bejelentkezett fiók közötti eltérés okozza. A probléma megoldása:

- Ellenőrizze, hogy az SSH-parancssorban megadott bejelentkezési név helyes-e. A bejelentkezési név elírása eltérést okozhat az SSH-parancssorban megadott bejelentkezési név és az Azure AD-be bejelentkezett fiók között. Például az azureuser helyett az *azuresuer \@ contoso.onmicrosoft.com* az *\@ contoso.onmicrosoft.com.*
- Ha több felhasználói fiókkal is bejelentkezik, ügyeljen arra, hogy ne adjon meg másik felhasználói fiókot a böngészőablakban, amikor bejelentkezik az Azure AD-be.
- A Linux operációs rendszer megkülönbözteti a kis- és nagybetűket. A " és a " között van Azureuser@contoso.onmicrosoft.com azureuser@contoso.onmicrosoft.com különbség, ami eltérést okozhat. Győződjön meg arról, hogy az UPN-t a megfelelő kis- és nagybetűs bizalmasságsal adja meg az SSH-parancssorban.

### <a name="other-limitations"></a>Egyéb korlátozások

Azok a felhasználók, akik beágyazott csoportok vagy szerepkör-hozzárendelések révén öröklik a hozzáférési jogosultságokat, jelenleg nem támogatottak. A felhasználóhoz vagy csoporthoz közvetlenül hozzá kell rendelni a szükséges [szerepkör-hozzárendeléseket.](#configure-role-assignments-for-the-vm) A felügyeleti csoportok vagy a beágyazott csoportok szerepkör-hozzárendeléseinek használata például nem adja meg a megfelelő engedélyeket ahhoz, hogy a felhasználó bejelentkezhet.

## <a name="preview-feedback"></a>Az előzetes verzióval kapcsolatos visszajelzés

Ossza meg az előzetes verziójú funkcióval kapcsolatos visszajelzéseit, vagy jelentse a problémákat az [Azure AD visszajelzési fórumán.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Következő lépések

További információ a [Azure Active Directory: Mi a Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)

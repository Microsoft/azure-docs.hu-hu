---
title: Bejelentkezés a Windows rendszerű virtuális gépre az Azure-ban Azure Active Directory használatával (előzetes verzió)
description: Azure AD-bejelentkezés Windows rendszerű Azure-beli virtuális gépre
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.custom: references_regions, devx-track-azurecli
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee392666a6c6807497eeac2a2291dac915c4e136
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644306"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Bejelentkezés az Azure-beli Windows rendszerű virtuális gépre Azure Active Directory hitelesítéssel (előzetes verzió)

A szervezetek mostantól Azure Active Directory (AD) hitelesítést használhatnak a **Windows Server 2019 Datacenter Edition** vagy **Windows 10 1809** vagy újabb rendszert futtató Azure-beli virtuális gépek (VM-EK) számára. Az Azure AD használatával a virtuális gépek hitelesítése lehetővé teszi a házirendek központi felügyeletét és betartatását. Az olyan eszközök, mint az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és az Azure AD feltételes hozzáférése lehetővé teszi, hogy ki férhet hozzá a virtuális géphez. Ebből a cikkből megtudhatja, hogyan hozhat létre és konfigurálhat egy Windows Server 2019 rendszerű virtuális gépet az Azure AD-hitelesítés használatához.

> [!NOTE]
> Az Azure AD-bejelentkezés az Azure-beli Windows rendszerű virtuális gépek nyilvános előzetes verziója Azure Active Directory. További információ az előzetes verziókról: a  [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure AD-hitelesítés használatának számos előnye van az Azure-beli Windows rendszerű virtuális gépekre való bejelentkezéshez, beleértve a következőket:

- Használja ugyanazt az összevont vagy felügyelt Azure AD-beli hitelesítő adatokat, amelyeket általában használ.
- A továbbiakban nem kell helyi rendszergazdai fiókokat kezelnie.
- Az Azure RBAC lehetővé teszi a megfelelő hozzáférés megadását a virtuális gépek igény szerinti eléréséhez, és ha már nincs rá szükség, távolítsa el azt.
- A virtuális géphez való hozzáférés engedélyezése előtt az Azure AD feltételes hozzáférése további követelményeket is kikényszerítheti, például: 
   - Többtényezős hitelesítés
   - Bejelentkezési kockázat-ellenőrzési
- Automatizálhatja és méretezheti az Azure AD Joint az Azure Windows rendszerű virtuális gépekhez, amelyek részét képezik a VDI üzembe helyezésének.

> [!NOTE]
> Ha engedélyezi ezt a funkciót, az Azure-beli Windows rendszerű virtuális gépek az Azure AD-vel lesznek csatlakoztatva. Nem csatlakoztatható más tartományhoz, például a helyszíni AD-hez vagy az Azure AD DShoz. Ha így tesz, le kell választania a virtuális gépet az Azure AD-bérlőről a bővítmény eltávolításával.

## <a name="requirements"></a>Követelmények

### <a name="supported-azure-regions-and-windows-distributions"></a>Támogatott Azure-régiók és Windows-disztribúciók

A következő Windows-disztribúciók jelenleg a funkció előzetes verziójában támogatottak:

- Windows Server 2019 Datacenter
- Windows 10 1809 és újabb verziók

> [!IMPORTANT]
> Az Azure AD-hez csatlakoztatott virtuális gépekkel létesített távoli kapcsolódás csak olyan Windows 10 rendszerű számítógépeken engedélyezett, amelyeken az Azure AD regisztrálva van (Windows 10 20H1), az Azure AD-hez csatlakoztatott vagy hibrid Azure AD-hez **ugyanahhoz** a címtárhoz csatlakozik, mint a virtuális gép. 

A szolgáltatás előzetes verziójában jelenleg a következő Azure-régiók támogatottak:

- Minden Azure globális régió

> [!IMPORTANT]
> Az előzetes verziójú funkció használatához csak egy támogatott Windows-disztribúciót és egy támogatott Azure-régiót telepítsen. A szolgáltatás jelenleg nem támogatott Azure Government vagy szuverén felhőkben.

### <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

Ha engedélyezni szeretné az Azure AD-hitelesítést az Azure-beli Windows rendszerű virtuális gépeken, biztosítania kell, hogy a virtuális gépek hálózati konfigurációja a 443-as TCP-porton keresztül engedélyezze a kimenő hozzáférést a következő végpontokhoz:

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://pas.windows.net`

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Azure AD-bejelentkezés engedélyezése a Windows rendszerű virtuális gépeken az Azure-ban

Ha Azure AD-bejelentkezést szeretne használni a Windows rendszerű virtuális gépeken az Azure-ban, először engedélyeznie kell az Azure AD bejelentkezési lehetőséget a Windows rendszerű virtuális gépen, majd konfigurálnia kell az Azure szerepkör-hozzárendeléseket azon felhasználók számára, akik jogosultak a virtuális gépre való bejelentkezésre.
A Windows rendszerű virtuális gépekhez több módon is engedélyezheti az Azure AD-bejelentkezést:

- Windows rendszerű virtuális gép létrehozásakor a Azure Portal élmény használata
- Windows rendszerű virtuális gép **vagy meglévő Windows rendszerű virtuális gép** létrehozásakor a Azure Cloud Shell élmény használata

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Az Azure AD-bejelentkezés engedélyezése Azure Portal virtuális gép létrehozásakor

Engedélyezheti az Azure AD-bejelentkezést a Windows Server 2019 Datacenter vagy a Windows 10 1809 és újabb rendszerű virtuálisgép-lemezképekhez. 

Windows Server 2019 Datacenter rendszerű virtuális gép létrehozása az Azure-ban Azure AD-bejelentkezéssel: 

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com)egy olyan fiókkal, amely hozzáfér a virtuális gépek létrehozásához, majd válassza az **+ erőforrás létrehozása** lehetőséget.
1. Írja be a **Windows Server** kifejezést a piactér keresési sávjában.
   1. Kattintson a **Windows Server** lehetőségre, és válassza a **Windows Server 2019 Datacenter** elemet a szoftvercsomag kiválasztása listából.
   1. Kattintson a **Létrehozás** gombra.
1. A "felügyelet" lapon engedélyezze a **HRE hitelesítő adatokkal (előzetes verzió) való bejelentkezést** a Azure Active Directory szakasz alatt, a ki és **be** lehetőségnél.
1. Győződjön **meg** arról, hogy a **rendszerhez rendelt felügyelt identitás** az identitás szakaszban be értékre van állítva. A műveletnek automatikusan kell történnie, ha engedélyezi a bejelentkezést az Azure AD-beli hitelesítő adatokkal.
1. Ugorjon végig a virtuális gép létrehozásának további tapasztalatain. Ebben az előzetes verzióban létre kell hoznia egy rendszergazdai felhasználónevet és jelszót a virtuális géphez.

![Bejelentkezés Azure AD-beli hitelesítő adatokkal virtuális gép létrehozása](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Ahhoz, hogy az Azure AD-beli hitelesítő adataival jelentkezzen be a virtuális gépre, először konfigurálnia kell a virtuális géphez tartozó szerepkör-hozzárendeléseket az alábbi szakaszokban leírtak szerint.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Az Azure AD-bejelentkezés engedélyezésének Azure Cloud Shelli felületének használata

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések végrehajtására használhat. A gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak a Cloud Shellben a fiókjával történő használathoz. A Másolás gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz. A Cloud Shell többféleképpen is megnyitható:

- Kattintson a **Kipróbálás** elemre egy kódblokk jobb felső sarkában.
- Nyissa meg a Cloud Shellt a böngészőben.
- Az [Azure Portal](https://portal.azure.com) jobb felső sarkában található menüben kattintson a Cloud Shell gombra.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.0.31 vagy újabb verzióját kell futtatnia. A verzió megkereséséhez futtassa a következő parancsot: az --version. Ha telepíteni vagy frissíteni szeretne, olvassa el az [Azure CLI telepítése](/cli/azure/install-azure-cli)című cikket.

1. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. 
1. Hozzon létre egy virtuális gépet az [az VM Create](/cli/azure/vm#az-vm-create) használatával egy támogatott régió támogatott eloszlásával. 
1. Telepítse az Azure AD login VM-bővítményt. 

A következő példa egy myVM nevű virtuális gépet telepít, amely az Win2019Datacenter-t használja egy myResourceGroup nevű erőforráscsoporthoz a southcentralus régióban. Az alábbi példákban megadhatja a saját erőforráscsoport és a virtuális gépek nevét igény szerint.

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> Az Azure AD bejelentkezési virtuálisgép-bővítmény telepítése előtt engedélyeznie kell a rendszerhez rendelt felügyelt identitást a virtuális gépen.

A virtuális gép és a kapcsolódó erőforrások létrehozása csak néhány percet vesz igénybe.

Végül telepítse az Azure AD bejelentkezési virtuálisgép-bővítményét, hogy engedélyezze az Azure AD-bejelentkezést a Windows rendszerű virtuális gépeken. A virtuálisgép-bővítmények olyan kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-beli virtuális gépeken. Az az [VM Extension](/cli/azure/vm/extension#az-vm-extension-set) SET paranccsal telepítse a AADLoginForWindows-bővítményt az erőforráscsoport nevű virtuális gépre `myVM` `myResourceGroup` :

> [!NOTE]
> A AADLoginForWindows bővítményt telepítheti egy meglévő Windows Server 2019 vagy Windows 10 1809 és újabb rendszerű virtuális gépre az Azure AD-hitelesítés engedélyezéséhez. Alább látható egy példa az az parancssori felületre.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

Ekkor megjelenik a (z) `provisioningState` `Succeeded` , ha a bővítmény telepítve van a virtuális gépen.

## <a name="configure-role-assignments-for-the-vm"></a>Szerepkör-hozzárendelések konfigurálása a virtuális géphez

Most, hogy létrehozta a virtuális gépet, konfigurálnia kell az Azure RBAC-szabályzatot annak meghatározásához, hogy ki tud bejelentkezni a virtuális gépre. A VM-bejelentkezés engedélyezéséhez két Azure-szerepkör használható:

- **Virtuális gép rendszergazdai bejelentkezése**: az ehhez a szerepkörhöz hozzárendelt felhasználók rendszergazdai jogosultságokkal jelentkezhetnek be egy Azure-beli virtuális gépre.
- **Virtuális gép felhasználói bejelentkezése**: az ehhez a szerepkörhöz hozzárendelt felhasználók rendszeres felhasználói jogosultságokkal jelentkezhetnek be egy Azure-beli virtuális gépre.

> [!NOTE]
> Annak engedélyezéséhez, hogy a felhasználó RDP-en keresztül jelentkezzen be a virtuális GÉPRE, hozzá kell rendelnie a virtuális gép rendszergazdai felhasználónevét vagy a virtuális gép felhasználói bejelentkezési szerepkörét. Egy virtuális géphez hozzárendelt tulajdonosi vagy közreműködői szerepkörökkel rendelkező Azure-felhasználó nem jogosult automatikusan a virtuális gépre RDP-kapcsolaton keresztül bejelentkezni. Ez a virtuális gépeket vezérlő személyek, illetve a virtuális gépeket elérő személyek között naplózható elkülönítést biztosít.

A virtuális gép szerepkör-hozzárendelései több módon is konfigurálhatók:

- Az Azure AD portál felületének használata
- A Azure Cloud Shell felület használata

> [!NOTE]
> A virtuális gép rendszergazdai felhasználónevét és a virtuális gép felhasználói bejelentkezési szerepköreit a dataActions használja, ezért nem rendelhető hozzá a felügyeleti csoport hatóköréhez. Jelenleg ezek a szerepkörök csak az előfizetéshez, az erőforráscsoport vagy az erőforrás-hatókörhöz rendelhetők hozzá.

### <a name="using-azure-ad-portal-experience"></a>Az Azure AD Portal felületének használata

Szerepkör-hozzárendelések konfigurálása az Azure AD-ben engedélyezett Windows Server 2019 Datacenter virtuális gépekhez:

1. Navigáljon az adott virtuális gép áttekintő oldalára
1. Válassza a **hozzáférés-vezérlés (iam)** lehetőséget a menüpontok közül.
1. Válassza a **Hozzáadás**, **szerepkör-hozzárendelés hozzáadása** lehetőséget a szerepkör-hozzárendelés hozzáadása ablaktábla megnyitásához.
1. A **szerepkör** legördülő listában válasszon ki egy szerepkört, például a **virtuális gép rendszergazdai felhasználónevét** vagy a **virtuális gép felhasználói bejelentkezését**.
1. A **Select (kiválasztás** ) mezőben válasszon ki egy felhasználót, egy csoportot, egy szolgáltatásnevet vagy egy felügyelt identitást. Ha a listában nem látja a rendszerbiztonsági tagot, írhat a **Kiválasztás** mezőbe megjelenítendő nevek, e-mail-címek és objektumazonosítók a címtárban történő kereséséhez.
1. A szerepkör hozzárendeléséhez válassza a **Mentés** lehetőséget.

Néhány pillanat elteltével a rendszerbiztonsági tag a kiválasztott hatókörhöz rendeli a szerepkört.

![Szerepkörök társítása a virtuális géphez hozzáférő felhasználókhoz](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>A Azure Cloud Shell felület használata

Az alábbi példa az [az role hozzárendelés Create](/cli/azure/role/assignment#az-role-assignment-create) paranccsal rendeli hozzá a virtuális gép rendszergazdai bejelentkezési szerepkörét a virtuális géphez az aktuális Azure-felhasználóhoz. Az aktív Azure-fiókjának felhasználónevét az az [Account show](/cli/azure/account#az-account-show)paranccsal szerezheti be, a hatókör pedig az előző lépésben létrehozott virtuális gépre az [az VM show](/cli/azure/vm#az-vm-show)paranccsal. A hatókör egy erőforráscsoport vagy előfizetés szintjén is hozzárendelhető, és a szokásos Azure RBAC öröklési engedélyek is érvényesek. További információ: [Bejelentkezés egy linuxos virtuális gépre az Azure-ban Azure Active Directory hitelesítés használatával](../../virtual-machines/linux/login-using-aad.md).

```   AzureCLI
$username=$(az account show --query user.name --output tsv)
$vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Ha a HRE tartomány és a bejelentkezési Felhasználónév tartománya nem egyezik, meg kell adnia a felhasználói fiókjához tartozó objektumazonosítót `--assignee-object-id` , nem csak a felhasználónevét `--assignee` . A felhasználói fiókhoz tartozó objektumazonosítót az [az ad User List](/cli/azure/ad/user#az-ad-user-list)paranccsal kérheti le.

Az Azure-RBAC az Azure-előfizetési erőforrásokhoz való hozzáférés kezeléséhez való használatáról a következő cikkekben talál további információt:

- [Azure-szerepkörök kiosztása az Azure CLI-vel](../../role-based-access-control/role-assignments-cli.md)
- [Azure-szerepkörök kiosztása a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md)
- [Azure-szerepkörök Kiosztása Azure PowerShell használatával](../../role-based-access-control/role-assignments-powershell.md).

## <a name="using-conditional-access"></a>Feltételes hozzáférés használata

A feltételes hozzáférési szabályzatok, például a többtényezős hitelesítés vagy a felhasználói bejelentkezés kockázatának érvényesítése előtt engedélyezheti a hozzáférést az Azure-beli Windows rendszerű virtuális gépekhez, amelyek engedélyezve vannak az Azure AD-bejelentkezéssel. A feltételes hozzáférési szabályzat alkalmazásához ki kell választania az "Azure Windows virtuális gép bejelentkezési" alkalmazását a Cloud apps vagy a műveletek hozzárendelési beállításból, majd a bejelentkezési kockázatot feltételként és/vagy a többtényezős hitelesítés megkövetelése engedélyezési hozzáférés-vezérlésként. 

> [!NOTE]
> Ha a "többtényezős hitelesítés megkövetelése" lehetőséget használja hozzáférés-vezérlésre az "Azure Windows rendszerű virtuális gép bejelentkezési" alkalmazáshoz való hozzáféréshez, akkor az ügyfél részeként meg kell adnia a többtényezős hitelesítési jogcímet, amely az RDP-munkamenetet az Azure-beli cél Windows rendszerű virtuális gépre indítja. Ezt csak akkor érheti el, ha egy Windows 10-ügyfélen a Windows Hello for Business PIN-kódot vagy biometrikus hitelesítést használja az RDP-ügyféllel. A biometrikus hitelesítés támogatása a Windows 10 1809-es verziójának RDP-ügyfeléhez lett hozzáadva. A Windows Hello for Business hitelesítést használó távoli asztal csak a tanúsítvány-megbízhatósági modellt használó központi telepítések esetén érhető el, és jelenleg nem érhető el a kulcs megbízhatósági modellje számára.

> [!WARNING]
> Felhasználónkénti engedélyezett/kényszerített Azure AD-Multi-Factor Authentication nem támogatott a virtuális gép bejelentkezéséhez.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Bejelentkezés Azure AD-beli hitelesítő adatokkal egy Windows rendszerű virtuális gépen

> [!IMPORTANT]
> Az Azure AD-hez csatlakoztatott virtuális gépekkel létesített távoli kapcsolódás csak olyan Windows 10 rendszerű számítógépeken engedélyezett, amelyeken az Azure AD regisztrálva van (a minimálisan szükséges Build 20H1), vagy az Azure AD-hez csatlakoztatott vagy a hibrid Azure AD-hez **ugyanahhoz** a címtárhoz csatlakozik. Emellett az Azure AD-beli hitelesítő adatok használatával történő RDP-hez a felhasználónak a két Azure-szerepkör, a virtuális gép rendszergazdai bejelentkezése vagy a virtuális gép felhasználói bejelentkezési adatai közé kell tartoznia. Ha az Azure AD-ben regisztrált Windows 10 rendszerű számítógépet használ, meg kell adnia a hitelesítő adatokat a következő `AzureAD\UPN` formátumban (például `AzureAD\john@contoso.com` ). Jelenleg az Azure Bastion nem használható Azure Active Directory hitelesítéssel az AADLoginForWindows bővítménnyel való bejelentkezéshez. csak a közvetlen RDP használata támogatott.

Bejelentkezés a Windows Server 2019 rendszerű virtuális gépre az Azure AD használatával: 

1. Navigáljon a virtuális gép áttekintés lapjára, amely engedélyezve van az Azure AD-bejelentkezéssel.
1. Válassza a **Kapcsolódás** lehetőséget a Kapcsolódás a virtuális géphez panel megnyitásához.
1. Válassza az **RDP-fájl letöltése** lehetőséget.
1. Válassza a **Megnyitás** lehetőséget az távoli asztali kapcsolat-ügyfél elindításához.
1. Válassza a **Kapcsolódás** lehetőséget a Windows bejelentkezési párbeszédpanelének elindításához.
1. Jelentkezzen be az Azure AD-beli hitelesítő adataival.

Most bejelentkezett a Windows Server 2019 Azure-beli virtuális gépre a hozzárendelt szerepkör-engedélyekkel, mint például a VM-felhasználó vagy a VM-rendszergazda. 

> [!NOTE]
> Mentheti a t. RDP-fájl helyileg a számítógépen a távoli asztali kapcsolatok a virtuális géphez való elindításához ahelyett, hogy a Azure Portal a virtuális gép áttekintő lapjára kellene lépnie, és a csatlakozás lehetőséget kellene használnia.

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="troubleshoot-deployment-issues"></a>Üzembe helyezési problémák elhárítása

A AADLoginForWindows-bővítményt sikeresen kell telepíteni ahhoz, hogy a virtuális gép végre lehessen hajtani az Azure AD JOIN folyamatát. Ha a virtuálisgép-bővítményt nem sikerül megfelelően telepíteni, hajtsa végre a következő lépéseket.

1. RDP-t a virtuális géphez a helyi rendszergazdai fiók használatával, és vizsgálja meg a fájlt a következő `CommandExecution.log` területen:
   
   `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0.`

   > [!NOTE]
   > Ha a bővítmény a kezdeti hiba után újraindul, a rendszer a telepítési hibát tartalmazó naplót fogja menteni `CommandExecution_YYYYMMDDHHMMSSSSS.log` . "
1. Nyisson meg egy PowerShell-parancssort a virtuális gépen, és ellenőrizze ezeket a lekérdezéseket az Azure-gazdagépen futó Instance Metadata Service (IMDS) végponton:

   | Futtatandó parancs | Várt kimenet |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Az Azure-beli virtuális géppel kapcsolatos adatok javítása |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Az Azure-előfizetéshez társított érvényes bérlői azonosító |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | A virtuális géphez hozzárendelt felügyelt identitás Azure Active Directory által kiállított érvényes hozzáférési jogkivonat |

   > [!NOTE]
   > A hozzáférési jogkivonat dekódolható egy olyan eszköz használatával, mint a [calebb.net](http://calebb.net/). Ellenőrizze, hogy a `appid` hozzáférési jogkivonat megfelel-e a virtuális géphez hozzárendelt felügyelt identitásnak.

1. Győződjön meg arról, hogy a szükséges végpontok elérhetők a virtuális gépről a parancssor használatával:
   
   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`

   > [!NOTE]
   > Cserélje le az t az Azure- `<TenantID>` előfizetéshez társított Azure ad-bérlői azonosítóra.

   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`

1. Az eszköz állapotát a futtatásával lehet megtekinteni `dsregcmd /status` . A cél az eszköz állapotának megjelenítésére szolgál `AzureAdJoined : YES` .

   > [!NOTE]
   > Az Azure AD JOIN tevékenység a naplóban az eseménynaplóban van rögzítve `User Device Registration\Admin` .

Ha a AADLoginForWindows bővítmény bizonyos hibakód esetén meghiúsul, a következő lépéseket hajthatja végre:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>1. probléma: a AADLoginForWindows bővítmény nem telepíthető a következő terminál-hibakódtal: "1007", kilépési kód:-2145648574.

Ez a kilépési kód azért lett lefordítva, `DSREG_E_MSI_TENANTID_UNAVAILABLE` mert a bővítmény nem tudja lekérdezni az Azure ad-bérlő adatait.

1. Ellenőrizze, hogy az Azure-beli virtuális gép lekérheti-e a TenantID a Instance Metadata Service.

   - Az RDP-t a virtuális géphez helyi rendszergazdaként, és ellenőrizze, hogy a végpont érvényes bérlői azonosítót ad vissza, ha a parancsot egy emelt szintű parancssorból futtatja a virtuális gépen:
      
      - `curl -H Metadata:true http://169.254.169.254/metadata/identity/info?api-version=2018-02-01`

1. A virtuálisgép-rendszergazda megkísérli a AADLoginForWindows-bővítmény telepítését, de a rendszerhez rendelt felügyelt identitások nem engedélyezték először a virtuális gépet. Navigáljon a virtuális gép Identity (identitás) paneljére. A rendszerhez rendelt lapon ellenőrizze, hogy az állapot be van-e kapcsolva.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>2. probléma: a AADLoginForWindows bővítmény nem telepíthető a következő kilépési kóddal:-2145648607

Ez a kilépési kód azért lett lefordítva, `DSREG_AUTOJOIN_DISC_FAILED` mert a bővítmény nem tudja elérni a `https://enterpriseregistration.windows.net` végpontot.

1. Ellenőrizze, hogy a szükséges végpontok elérhetők-e a virtuális gépről a parancssor használatával:

   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`
   
   > [!NOTE]
   > Cserélje le az t az Azure- `<TenantID>` előfizetéshez társított Azure ad-bérlői azonosítóra. Ha meg kell találnia a bérlő AZONOSÍTÓját, a fiók neve fölé helyezheti a címtár/bérlő AZONOSÍTÓját, vagy kiválaszthatja **Azure Active Directory > tulajdonságok > Directory-azonosító** a Azure Portalban.

   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`

1. Ha a parancsok bármelyike sikertelen "nem sikerült feloldani a gazdagépet `<URL>` ", próbálja meg futtatni ezt a parancsot a virtuális gép által használt DNS-kiszolgáló meghatározásához.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Cserélje le a `<URL>` kifejezéseket a végpontok által használt teljes tartománynévre, például: `login.microsoftonline.com` .

1. Ezután ellenőrizze, hogy a nyilvános DNS-kiszolgáló meghatározása lehetővé teszi-e a parancs sikerességét:

   `nslookup <URL> 208.67.222.222`

1. Szükség esetén módosítsa a hálózati biztonsági csoporthoz hozzárendelt DNS-kiszolgálót, amelyhez az Azure-beli virtuális gép tartozik.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>3. probléma: a AADLoginForWindows bővítmény nem telepíthető a következő kilépési kóddal: 51

A 51-es kilépési kód lefordítja a következőre: "Ez a kiterjesztés nem támogatott a virtuális gép operációs rendszerében".

A nyilvános előzetes verzióban a AADLoginForWindows-bővítmény csak Windows Server 2019 vagy Windows 10 rendszerre (Build 1809 vagy újabb verzió) készült. Győződjön meg arról, hogy a Windows verziója támogatott. Ha a Windows-Build nem támogatott, távolítsa el a virtuálisgép-bővítményt.

### <a name="troubleshoot-sign-in-issues"></a>Bejelentkezési problémák elhárítása

Az Azure AD-beli hitelesítő adatokkal való RDP-vel való kísérlet során előforduló gyakori hibák közé tartoznak a hozzárendelt Azure-szerepkörök, a jogosulatlan ügyfelek vagy a 2FA bejelentkezési módszere. Az alábbi információk segítségével javítsa ki ezeket a problémákat.

Az eszköz és az egyszeri bejelentkezés állapota a futtatásával tekinthető meg `dsregcmd /status` . A cél az eszköz állapotának megjelenítése `AzureAdJoined : YES` és `SSO State` megjelenítése `AzureAdPrt : YES` .

Emellett az Azure AD-fiókokat használó RDP-bejelentkezés az eseménynaplókban rögzítve van az eseménynaplóban `AAD\Operational` .

#### <a name="azure-role-not-assigned"></a>Az Azure-szerepkör nincs hozzárendelve

Ha a következő hibaüzenet jelenik meg, amikor távoli asztali kapcsolattal kezdeményezi a virtuális gépet: 

- A fiókja úgy van konfigurálva, hogy megakadályozza az eszköz használatát. További információért forduljon a rendszergazdához.

![A fiókja úgy van konfigurálva, hogy megakadályozza az eszköz használatát.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Ellenőrizze, hogy konfigurálta-e az [Azure RBAC-házirendeket](../../virtual-machines/linux/login-using-aad.md) a virtuális gép számára, amely megadja a felhasználónak a virtuális gép rendszergazdai felhasználónevét vagy a virtuális gép felhasználói bejelentkezési szerepkörét:

> [!NOTE]
> Ha az Azure szerepkör-hozzárendelésekkel kapcsolatos problémákba ütközik, tekintse meg az [Azure-RBAC hibaelhárítását](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit)ismertető témakört.
 
#### <a name="unauthorized-client"></a>Jogosulatlan ügyfél

Ha a következő hibaüzenet jelenik meg, amikor távoli asztali kapcsolattal kezdeményezi a virtuális gépet: 

- A hitelesítő adatai nem működnek.

![A hitelesítő adatai nem működnek](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Győződjön meg arról, hogy a távoli asztali kapcsolat kezdeményezéséhez használt Windows 10 rendszerű számítógép vagy az Azure AD-hez csatlakoztatott vagy a hibrid Azure AD ugyanahhoz az Azure AD-címtárhoz van csatlakoztatva, amelyben a virtuális gép csatlakoztatva van. Az eszköz identitásával kapcsolatos további információkért tekintse meg az [eszköz identitását](./overview.md)ismertető cikket.

> [!NOTE]
> A Windows 10 Build 20H1 támogatja az Azure AD-ban regisztrált SZÁMÍTÓGÉPeket, hogy RDP-kapcsolatokat kezdeményezzenek a virtuális géppel. Ha egy regisztrált Azure AD-t (nem az Azure AD-hez csatlakoztatott vagy hibrid Azure AD-csatlakoztatott) futtató számítógépet használ a virtuális géphez való kapcsolatok kezdeményezéséhez, a hitelesítő adatokat a következő formátumban kell megadni: `AzureAD\UPN` (például: `AzureAD\john@contoso.com` ).

Győződjön meg arról, hogy a AADLoginForWindows bővítmény nem lett eltávolítva az Azure AD-csatlakozás befejeződése után.

Győződjön meg arról is, hogy a "hálózati biztonság: a PKU2U hitelesítési kérések engedélyezése a számítógépen az online identitások használatára" biztonsági házirend engedélyezve van a kiszolgálón **és** az ügyfélen is.
 
#### <a name="mfa-sign-in-method-required"></a>MFA bejelentkezési módszer szükséges

Ha a következő hibaüzenet jelenik meg, amikor távoli asztali kapcsolattal kezdeményezi a virtuális gépet: 

- A használni kívánt bejelentkezési módszer nem engedélyezett. Próbálkozzon másik bejelentkezési módszerrel, vagy forduljon a rendszergazdához.

![A használni kívánt bejelentkezési módszer nem engedélyezett.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Ha olyan feltételes hozzáférési szabályzatot állított be, amely a többtényezős hitelesítés (MFA) használatát igényli az erőforráshoz való hozzáféréshez, meg kell győződnie arról, hogy a Windows 10 rendszerű számítógép egy erős hitelesítési módszer, például a Windows Hello használatával kezdeményezi a távoli asztali kapcsolatot a virtuális géppel. Ha nem használ erős hitelesítési módszert a távoli asztali kapcsolathoz, az előző hibaüzenet jelenik meg.

Ha még nem telepítette a vállalati Windows Hello szolgáltatást, és ha ez nem lehetséges, kizárhatja az MFA-követelményt úgy, hogy a feltételes hozzáférési szabályzatot, amely kizárja az "Azure Windows VM-bejelentkezés" alkalmazást az MFA-t igénylő felhőalapú alkalmazások listájából. A vállalati Windows Hello szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [vállalati Windows Hello áttekintése](/windows/security/identity-protection/hello-for-business/hello-identity-verification)című témakört.

> [!NOTE]
> A Windows Hello for Business PIN-kódjának RDP protokollal való hitelesítését a Windows 10 több verzióra is támogatja, azonban a Windows 10 1809-es verziójában hozzá lett adva a biometrikus hitelesítés támogatása az RDP használatával. A vállalati Windows Hello-alapú hitelesítés használata csak a CERT megbízhatósági modellt használó központi telepítések esetén érhető el, és a kulcs-megbízhatósági modellhez jelenleg nem érhető el.
 
## <a name="preview-feedback"></a>Az előzetes verzióval kapcsolatos visszajelzés

Ossza meg visszajelzését erről az előzetes verziójú szolgáltatásról, vagy jelentse a problémát az [Azure ad visszajelzési fórumának](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)használatával.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Active Directoryről: [Mi az Azure Active Directory](../fundamentals/active-directory-whatis.md).

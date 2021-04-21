---
title: Bejelentkezés Windows rendszerű virtuális gépre az Azure-ban Azure Active Directory (előzetes verzió)
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
ms.openlocfilehash: 418741c10dfe5f0678d7771d046781697512bafe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776500"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Bejelentkezés Windows rendszerű virtuális gépre az Azure-ban Azure Active Directory (előzetes verzió)

A szervezetek mostantól a **Windows Server 2019 Datacenter** editiont vagy Azure Active Directory Windows 10 **1809-es** vagy újabb verziókat futtató Azure-beli virtuális gépeikhez (AD) Windows 10 hitelesítést. Ha az Azure AD-vel hitelesíti magát a virtuális gépeken, központilag vezérelheti és kényszerítheti a szabályzatokat. Az olyan eszközök, mint az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és az Azure AD feltételes hozzáférés, lehetővé teszik annak szabályozását, hogy ki férhet hozzá egy virtuális géphez. Ez a cikk bemutatja, hogyan hozhat létre és konfigurálhatja a Windows Server 2019 virtuális gépeket Az Azure AD-hitelesítés használatára.

> [!NOTE]
> Az Azure AD-bejelentkezés az Azure-beli Windows rendszerű virtuális gépekre a virtuális gépek nyilvános előzetes Azure Active Directory. További információ az előzetes verziókról: Kiegészítő használati feltételek [a Microsoft Azure előzetes verziókhoz.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Az Azure-beli Windows rendszerű virtuális gépekre való bejelentkezésnek számos előnye van, többek között az alábbiak:

- Használja a megszokott összevont vagy felügyelt Azure AD-beli hitelesítő adatokat.
- A továbbiakban nem kell helyi rendszergazdai fiókokat felügyelni.
- Az Azure RBAC lehetővé teszi, hogy szükség alapján megfelelő hozzáférést biztosítson a virtuális gépekhez, és eltávolítsa, amikor már nincs rá szükség.
- Mielőtt engedélyezi a virtuális géphez való hozzáférést, az Azure AD feltételes hozzáférés további követelményeket kényszeríthet, például: 
   - Többtényezős hitelesítés
   - Bejelentkezési kockázat ellenőrzése
- Automatizálhatja és skálázhatja az Azure AD-csatlakozást olyan Azure-beli Windows rendszerű virtuális gépekhez, amelyek részei a VDI-környezetek üzembe helyezésének.

> [!NOTE]
> Miután engedélyezi ezt a képességet, az Azure-beli Windows rendszerű virtuális gépek csatlakoznak az Azure AD-hez. Nem csatlakozhat más tartományhoz, például a helyszíni AD-hez vagy a Azure AD DS. Ha szükséges, a bővítmény eltávolításával le kell bontani a virtuális gépet az Azure AD-bérlőről.

## <a name="requirements"></a>Követelmények

### <a name="supported-azure-regions-and-windows-distributions"></a>Támogatott Azure-régiók és Windows-disztribúciók

A szolgáltatás előzetes verziója jelenleg a következő Windows-disztribúciókat támogatja:

- Windows Server 2019 Datacenter
- Windows 10 1809-es vagy újabb

> [!IMPORTANT]
> Az Azure A Windows 10 D-hez csatlakozó virtuális gépekhez való távoli csatlakozás csak olyan számítógépekről engedélyezett, amelyek regisztrálva vannak az Azure AD-ben  (Windows 10 20H1-től kezdődően), azure AD-hez csatlakozott vagy hibrid Azure AD-hez csatlakozva ugyanahhoz a címtárhoz, mint a virtuális gép. 

A szolgáltatás előzetes verziója jelenleg a következő Azure-régiókat támogatja:

- Minden globális Azure-régió

> [!IMPORTANT]
> Az előzetes verziójú funkció használatához csak támogatott Windows-disztribúciót telepítsen egy támogatott Azure-régióban. A szolgáltatás jelenleg nem támogatott az Azure Government szuverén felhőkben.

### <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

Ha engedélyezni szeretné az Azure AD-hitelesítést a Windows rendszerű virtuális gépek számára az Azure-ban, meg kell győződni arról, hogy a virtuális gépek hálózati konfigurációja engedélyezi a következő végpontok kimenő hozzáférését a 443-as TCP-porton keresztül:

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://pas.windows.net`

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Azure AD-bejelentkezés engedélyezése Windows rendszerű virtuális gépeken az Azure-ban

Az Azure AD-bejelentkezés Azure-beli Windows rendszerű virtuális gépeken való használatával először engedélyeznie kell az Azure AD bejelentkezési beállítást a Windows rendszerű virtuális gépen, majd konfigurálnia kell az Azure-beli szerepkör-hozzárendeléseket a virtuális gépre való bejelentkezésre jogosult felhasználók számára.
Több módon is engedélyezheti az Azure AD-bejelentkezést a Windows rendszerű virtuális géphez:

- Az Azure Portal használata Windows rendszerű virtuális gép létrehozásakor
- A Azure Cloud Shell használata Windows rendszerű virtuális gép létrehozásakor vagy meglévő **Windows rendszerű virtuális gép esetén**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Virtuálisgép Azure Portal létrehozása funkció használata az Azure AD-bejelentkezés engedélyezéséhez

Engedélyezheti az Azure AD-bejelentkezést a Windows Server 2019 Datacenterhez vagy Windows 10 1809-es vagy újabb virtuálisgép-rendszerképekhez. 

Windows Server 2019 Datacenter rendszerű virtuális gép létrehozása az Azure-ban Azure AD-bejelentkezéssel: 

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com)fiókkal, amely hozzáféréssel rendelkezik a virtuális gépek létrehozásához, majd válassza az + Erőforrás létrehozása **lehetőséget.**
1. Írja be **a Windows Servert** a Keresés a Marketplace keresősávba mezőbe.
   1. Kattintson a **Windows Server elemre,** és válassza a **Windows Server 2019 Datacenter** lehetőséget a Szoftvercsomag kiválasztása legördülő menüből.
   1. Kattintson a **Létrehozás gombra.**
1. A "Felügyelet" lapon engedélyezze a Bejelentkezés AAD hitelesítő adatokkal **(előzetes verzió)** lehetőséget a Ki Azure Active Directory a **Be lehetőségnél.**
1. Győződjön meg arról, hogy az Identitás **szakaszban** a Rendszer által hozzárendelt felügyelt identitás beállítás be **van állítva.** Ennek a műveletnek automatikusan kell történnie, ha engedélyezi a bejelentkezést az Azure AD hitelesítő adataival.
1. A virtuális gép létrehozásának további tapasztalatait is végig kell mennie. Ebben az előzetes verzióban létre kell hoznia egy rendszergazdai felhasználónevet és jelszót a virtuális géphez.

![Bejelentkezés Azure AD hitelesítő adatokkal virtuális gép létrehozása](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Ahhoz, hogy az Azure AD hitelesítő adataival jelentkezzen be a virtuális gépre, először konfigurálnia kell a virtuális gép szerepkör-hozzárendelését az alábbi szakaszok egyikében leírtak szerint.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Az Azure AD-Azure Cloud Shell engedélyezése a felhasználói élmény használatával

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések végrehajtására használhat. A gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak a Cloud Shellben a fiókjával történő használathoz. A Másolás gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz. A Cloud Shell többféleképpen is megnyitható:

- Kattintson a **Kipróbálás** elemre egy kódblokk jobb felső sarkában.
- Nyissa meg a Cloud Shellt a böngészőben.
- Az [Azure Portal](https://portal.azure.com) jobb felső sarkában található menüben kattintson a Cloud Shell gombra.

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.0.31-es vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következő parancsot: az --version. Ha telepíteni vagy frissítenie kell, tekintse meg az [Azure CLI telepítését.](/cli/azure/install-azure-cli)

1. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. 
1. Hozzon létre egy virtuális gépet [az az vm create](/cli/azure/vm#az_vm_create) használatával egy támogatott régióban található támogatott disztribúció használatával. 
1. Telepítse az Azure AD bejelentkezési virtuálisgép-bővítményt. 

Az alábbi példa a Win2019Datacentert használó myVM nevű virtuális gépet helyez üzembe egy myResourceGroup nevű erőforráscsoportban a southcentralus régióban. Az alábbi példákban szükség szerint meg lehet adni a saját erőforráscsoportját és virtuálisgép-nevét.

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
> Az Azure AD bejelentkezési virtuálisgép-bővítmény telepítése előtt engedélyeznie kell a rendszer által hozzárendelt felügyelt identitást a virtuális gépen.

A virtuális gép és a kapcsolódó erőforrások létrehozása csak néhány percet vesz igénybe.

Végül telepítse az Azure AD bejelentkezési virtuálisgép-bővítményt, hogy engedélyezze az Azure AD-bejelentkezést a Windows rendszerű virtuális gépeken. A virtuálisgép-bővítmények kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-beli virtuális gépeken. Az [az vm extension](/cli/azure/vm/extension#az_vm_extension_set) set használatával telepítse az AADLoginForWindows bővítményt az erőforráscsoportban megnevezett virtuális `myVM` `myResourceGroup` gépre:

> [!NOTE]
> Az AADLoginForWindows bővítményt meglévő Windows Server 2019 vagy Windows 10 1809 vagy újabb rendszerű virtuális gépen telepítheti az Azure AD-hitelesítés engedélyezéséhez. Alább látható egy példa az AZ CLI-re.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

Az a virtuális gépre való telepítése után jelenik `provisioningState` `Succeeded` meg.

## <a name="configure-role-assignments-for-the-vm"></a>Szerepkör-hozzárendelések konfigurálása a virtuális géphez

Most, hogy létrehozta a virtuális gépet, konfigurálnia kell az Azure RBAC-szabályzatot annak meghatározásához, hogy ki jelentkezhet be a virtuális gépre. A virtuális gépekre való bejelentkezéshez két Azure-szerepkör használható:

- **Virtuális gép rendszergazdai bejelentkezése:** Az ezzel a szerepkörsel rendelkező felhasználók rendszergazdai jogosultságokkal jelentkezhetnek be egy Azure-beli virtuális gépre.
- **Virtuális gép felhasználói bejelentkezése:** Az ezzel a szerepkörsel rendelkező felhasználók normál felhasználói jogosultságokkal jelentkezhetnek be egy Azure-beli virtuális gépre.

> [!NOTE]
> Ahhoz, hogy egy felhasználó RDP-n keresztül bejelentkezhet a virtuális gépre, hozzá kell rendelnie a Virtuális gép rendszergazdai bejelentkezése vagy a Virtuális gép felhasználói bejelentkezése szerepkört. A virtuális géphez hozzárendelt Tulajdonos vagy Közreműködő szerepkörsel rendelkező Azure-felhasználó nem rendelkezik automatikusan jogosultsággal a virtuális gépre való RDP-n keresztüli bejelentkezéshez. Ez a virtuális gépeket vezérlő személyek és a virtuális gépekhez hozzáférő személyek halmaza közötti naplók elkülönítését biztosítja.

A virtuális gépek szerepkör-hozzárendelését többféleképpen is konfigurálhatja:

- Az Azure AD portál használata
- A Azure Cloud Shell használata

> [!NOTE]
> A virtuális gép rendszergazdai bejelentkezési és felhasználói bejelentkezési szerepkörei dataActions adatokat használnak, így nem rendelhetők hozzá a felügyeleti csoport hatókörében. Ezek a szerepkörök jelenleg csak az előfizetésben, az erőforráscsoportban vagy az erőforrás-hatókörben rendelhetők hozzá.

### <a name="using-azure-ad-portal-experience"></a>Az Azure AD portál használata

Az Azure AD-t engedélyező Windows Server 2019 Datacenter virtuális gépek szerepkör-hozzárendelésének konfigurálása:

1. Lépjen az adott virtuális gép áttekintő oldalára
1. A menübeállítások között válassza a Hozzáférés-vezérlés **(IAM)** lehetőséget
1. Válassza **a Hozzáadás,** **Szerepkör-hozzárendelés hozzáadása lehetőséget** a Szerepkör-hozzárendelés hozzáadása panel megnyitásához.
1. A Szerepkör **legördülő** listában válasszon ki egy szerepkört, például a Virtuális gép rendszergazdai bejelentkezése vagy a Virtuális **gép** felhasználói **bejelentkezése szerepkört.**
1. A **Kijelölés mezőben** válasszon ki egy felhasználót, csoportot, szolgáltatásnévt vagy felügyelt identitást. Ha a listában nem látja a rendszerbiztonsági tagot, írhat a **Kiválasztás** mezőbe megjelenítendő nevek, e-mail-címek és objektumazonosítók a címtárban történő kereséséhez.
1. A **szerepkör hozzárendeléshez** válassza a Mentés lehetőséget.

Néhány pillanat múlva a rendszerbiztonsági taghoz a rendszer hozzárendeli a szerepkört a kiválasztott hatókörben.

![Szerepkörök hozzárendelése a virtuális géphez hozzáférő felhasználókhoz](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>A Azure Cloud Shell használata

Az alábbi példa az [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) parancsot használja a virtuális gép rendszergazdai bejelentkezési szerepkörének a virtuális géphez való hozzárendeléshez az aktuális Azure-felhasználóhoz. Az aktív Azure-fiók felhasználónevét az [az account show](/cli/azure/account#az_account_show)használatával lehet beszerezni, és a hatókör az előző lépésben az az vm show használatával létrehozott virtuális [gépre van beállítva.](/cli/azure/vm#az_vm_show) A hatókör egy erőforráscsoport vagy előfizetés szintjén is hozzárendelhető, és a normál Azure RBAC-öröklési engedélyek érvényesek. További információ: Bejelentkezés Linux rendszerű virtuális gépre az [Azure-ban Azure Active Directory használatával.](../../virtual-machines/linux/login-using-aad.md)

```   AzureCLI
$username=$(az account show --query user.name --output tsv)
$vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Ha az AAD-tartomány és a bejelentkezési felhasználónév tartománya nem egyezik, a felhasználói fiók objektumazonosítóját a értékével kell megadnia, nem csak a `--assignee-object-id` `--assignee` felhasználónevét. A felhasználói fiók objektumazonosítóját az az ad user list segítségével [szerezheti be.](/cli/azure/ad/user#az_ad_user_list)

Az Azure-előfizetés erőforrásaihoz való hozzáférés Azure RBAC használatával való kezelésével kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Azure-szerepkörök hozzárendelése az Azure CLI használatával](../../role-based-access-control/role-assignments-cli.md)
- [Azure-szerepkörök hozzárendelése a Azure Portal](../../role-based-access-control/role-assignments-portal.md)
- [Azure-szerepkörök hozzárendelése a Azure PowerShell.](../../role-based-access-control/role-assignments-powershell.md)

## <a name="using-conditional-access"></a>Feltételes hozzáférés használata

Az Azure AD-bejelentkezéssel engedélyezett Windows rendszerű virtuális gépekhez való hozzáférés engedélyezése előtt kényszerítheti a feltételes hozzáférési szabályzatokat, például a többtényezős hitelesítést vagy a felhasználói bejelentkezési kockázatellenőrzést. A feltételes hozzáférési szabályzat alkalmazásához ki kell választania az "Azure Windows rendszerű virtuális gép bejelentkezési" alkalmazását a felhőalkalmazások vagy műveletek hozzárendelési lehetőségből, majd a bejelentkezési kockázatot feltételként kell használnia, és/vagy többtényezős hitelesítést kell használnia hozzáférés-vezérlési engedélyként. 

> [!NOTE]
> Ha a "Többtényezős hitelesítés megkövetelését" használja hozzáférés-vezérlési engedélyként az "Azure Windows rendszerű virtuális gép bejelentkezése" alkalmazáshoz való hozzáférés kéréséhez, akkor többtényezős hitelesítési jogcímet kell használnia az ügyfél részeként, amely elindítja az RDP-munkamenetet a cél Windows rendszerű virtuális géphez az Azure-ban. Ezt csak úgy érheti el egy Windows 10 ügyfélen, ha az RDP Vállalati Windows Hello pin-kódot vagy biometrikus hitelesítést használ. A biometrikus hitelesítés támogatása az RDP-ügyfélhez az 1809 Windows 10 verzióban lett hozzáadva. A hitelesítést Vállalati Windows Hello távoli asztal csak olyan üzemelő példányok esetében érhető el, amelyek a tanúsítvány megbízhatósági modelljét használják, és jelenleg nem érhetők el a kulcs megbízhatósági modellhez.

> [!WARNING]
> A felhasználónkénti engedélyezett/kényszerített Azure AD Multi-Factor Authentication nem támogatott a virtuális gépek bejelentkezéséhez.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Bejelentkezés Azure AD hitelesítő adatokkal Windows rendszerű virtuális gépre

> [!IMPORTANT]
> Az Azure A Windows 10 D-hez csatlakozó virtuális gépekhez való távoli kapcsolat csak olyan számítógépekről engedélyezett, amelyek regisztrálva vannak az Azure AD-ben  (a minimálisan szükséges build 20H1), vagy az Azure AD-hez csatlakozó vagy a virtuális géphez ugyanahhoz a címtárhoz csatlakozó hibrid Azure AD-ről. Ezenkívül az Azure AD hitelesítő adatait használó RDP-hez a felhasználónak a két Azure-szerepkör egyikének, a virtuális gép rendszergazdai bejelentkezésének vagy a virtuális gép felhasználói bejelentkezésének egyikében kell lennie. Ha egy Azure AD-t regisztrált Windows 10 számítógépen, a hitelesítő adatokat a következő formátumban kell `AzureAD\UPN` megadnia:(például `AzureAD\john@contoso.com` ). Jelenleg nem Azure Bastion az AADLoginForWindows bővítmény Azure Active Directory használatával való bejelentkezéshez; csak a közvetlen RDP támogatott.

Bejelentkezés Windows Server 2019 rendszerű virtuális gépre az Azure AD használatával: 

1. Lépjen az Azure AD-bejelentkezéssel engedélyezett virtuális gép áttekintő oldalára.
1. Válassza **a Csatlakozás** lehetőséget a Csatlakozás virtuális géphez panel megnyitásához.
1. Válassza az **RDP-fájl letöltése** lehetőséget.
1. Válassza **a Megnyitás lehetőséget** a Távoli asztali kapcsolat elindításához.
1. Válassza **a Csatlakozás** lehetőséget a Windows bejelentkezési párbeszédpanel elindításához.
1. Jelentkezzen be az Azure AD hitelesítő adataival.

Ezzel bejelentkezett a Windows Server 2019 Azure-beli virtuális gépre a hozzárendelt szerepkör-engedélyekkel, például a virtuális gép felhasználójával vagy rendszergazdájával. 

> [!NOTE]
> Mentheti a következőt: . Helyi RDP-fájl a számítógépen a virtuális gép jövőbeli távoli asztali kapcsolatainak elindításához ahelyett, hogy a virtuális gép áttekintő oldalára navigálna a virtuális gép Azure Portal a csatlakozás lehetőség használatával.

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="troubleshoot-deployment-issues"></a>Üzembe helyezési problémák elhárítása

Az AADLoginForWindows bővítményt sikeresen telepíteni kell, hogy a virtuális gép befejezheti az Azure AD-beléptatási folyamatot. Ha a virtuálisgép-bővítmény telepítése sikertelen, hajtsa végre a következő lépéseket.

1. RdP-kapcsolat a virtuális géphez a helyi rendszergazdai fiókkal, és vizsgálja meg `CommandExecution.log` a fájlt a következő területen:
   
   `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0.`

   > [!NOTE]
   > Ha a bővítmény a kezdeti hiba után újraindul, a telepítési hibával együtt a napló a következőként lesz mentve: `CommandExecution_YYYYMMDDHHMMSSSSS.log` . "
1. Nyisson meg egy PowerShell-parancssort a virtuális gépen, és ellenőrizze, hogy ezek a lekérdezések az Azure-gazdagépen futó Instance Metadata Service(IMDS) végponton futnak-e:

   | Futtatandó parancs | Várt kimenet |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Az Azure-beli virtuális gépre vonatkozó információk kijavítva |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Az Azure-előfizetéshez társított érvényes bérlőazonosító |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | A virtuális géphez Azure Active Directory felügyelt identitáshoz kiadott érvényes hozzáférési jogkivonat |

   > [!NOTE]
   > A hozzáférési jogkivonat a következőhöz hasonló eszközzel [dekódolható calebb.net.](http://calebb.net/) Ellenőrizze, `appid` hogy a hozzáférési jogkivonatban a megfelel-e a virtuális géphez rendelt felügyelt identitásnak.

1. Győződjön meg arról, hogy a szükséges végpontok elérhetők a virtuális gépről a parancssorból:
   
   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`

   > [!NOTE]
   > Cserélje le a helyére az Azure-előfizetéshez társított `<TenantID>` Azure AD-bérlőazonosítót.

   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`

1. Az eszközállapotot a futtatásával lehet `dsregcmd /status` megtekinteni. A cél az, hogy az Eszközállapot a következőként mutasson: `AzureAdJoined : YES` .

   > [!NOTE]
   > Az Azure AD-beléptetéses tevékenység az Eseménynaplóban, a napló alatt `User Device Registration\Admin` van rögzíti.

Ha az AADLoginForWindows bővítmény bizonyos hibakóddal meghiúsul, a következő lépéseket hajthatja végre:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>1. probléma: Az AADLoginForWindows bővítmény telepítése sikertelen, és a következő terminálhibakódot tartalmazza: 1007, kilépési kód: -2145648574.

Ez a kilépési kód a következőre lesz lefordítva: , mert a `DSREG_E_MSI_TENANTID_UNAVAILABLE` bővítmény nem tudja lekérdezni az Azure AD-bérlő adatait.

1. Ellenőrizze, hogy az Azure-beli virtuális gép képes-e lekérni a TenantID Instance Metadata Service.

   - Helyi rendszergazdaként RDP-n keresztül a virtuális géphez, és ellenőrizze, hogy a végpont érvényes bérlőazonosítót ad-e vissza a következő parancs futtatásával egy emelt szintű parancssorból a virtuális gépen:
      
      - `curl -H Metadata:true http://169.254.169.254/metadata/identity/info?api-version=2018-02-01`

1. A virtuális gép rendszergazdája megpróbálja telepíteni az AADLoginForWindows bővítményt, de a rendszer által hozzárendelt felügyelt identitás nem engedélyezte először a virtuális gépet. Nyissa meg a virtuális gép Identitás paneljét. A Rendszer által hozzárendelt lapon ellenőrizze, hogy az Állapot be van-e osztva.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>2. probléma: Az AADLoginForWindows bővítmény telepítése sikertelen a következő kilépési kóddal: -2145648607

Ez a kilépési kód a következőre lesz `DSREG_AUTOJOIN_DISC_FAILED` lefordítva: , mert a bővítmény nem tudja elérni a `https://enterpriseregistration.windows.net` végpontot.

1. Ellenőrizze, hogy a szükséges végpontok elérhetők-e a virtuális gépről a parancssor használatával:

   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`
   
   > [!NOTE]
   > Cserélje le a helyére az `<TenantID>` Azure-előfizetéshez társított Azure AD-bérlőazonosítót. Ha meg kell találnia a bérlőazonosítót, vigye a kurzort a fiók nevére a címtár/bérlő azonosítójának le Azure Active Directory >, vagy válassza Azure Active Directory > Tulajdonságok > **címtár-azonosító** lehetőséget a Azure Portal.

   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`

1. Ha a parancsok bármelyike a "Nem oldható fel a gazdagép feloldása" hiba miatt meghiúsul, próbálja meg a parancs futtatásával meghatározni a virtuális gép által használt `<URL>` DNS-kiszolgálót.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Cserélje le a helyére a végpontok által `<URL>` használt teljes tartományneveket, `login.microsoftonline.com` például: .

1. Ezután nézze meg, hogy a nyilvános DNS-kiszolgáló megadása lehetővé teszi-e, hogy a parancs sikeres legyen:

   `nslookup <URL> 208.67.222.222`

1. Ha szükséges, módosítsa azt a DNS-kiszolgálót, amely ahhoz a hálózati biztonsági csoporthoz van rendelve, amelyhez az Azure-beli virtuális gép tartozik.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>3. probléma: Az AADLoginForWindows bővítmény telepítése sikertelen az 51-es kilépési kóddal

Az 51-es kilépési kód a következőt jelenti: "Ez a bővítmény nem támogatott a virtuális gép operációs rendszerében".

A Nyilvános előzetes verzióban az AADLoginForWindows bővítmény csak Windows Server 2019 vagy Windows 10 (1809-es vagy újabb build) rendszeren telepíthető. Győződjön meg arról, hogy a Windows verziója támogatott. Ha a Windows build nem támogatott, távolítsa el a virtuálisgép-bővítményt.

### <a name="troubleshoot-sign-in-issues"></a>Bejelentkezési problémák elhárítása

Az Azure AD hitelesítő adataival rdp-kapcsolaton keresztüli RDP-k során előforduló hibák közé tartoznak a hozzárendelt Azure-szerepkörök, a jogosulatlan ügyfelek vagy a 2FA bejelentkezési módszer használata. A problémák megoldásához használja az alábbi információkat.

Az eszköz és az SSO állapota a következő futtatásával `dsregcmd /status` megtekinthető: . A cél az, hogy az Eszközállapot a és a `AzureAdJoined : YES` `SSO State` állapotként `AzureAdPrt : YES` mutasson.

Emellett az Azure AD-fiókokkal való RDP-bejelentkezést az eseménynaplókban rögzíti a rendszer az `AAD\Operational` Eseménynaplóban.

#### <a name="azure-role-not-assigned"></a>Nincs hozzárendelve Azure-szerepkör

Ha távoli asztali kapcsolatot kezdeményez a virtuális géphez, a következő hibaüzenet jelenik meg: 

- A fiókja úgy van konfigurálva, hogy megakadályozza az eszköz használatának megakadályozását. További információért forduljon a rendszergazdához.

![A fiókja úgy van konfigurálva, hogy megakadályozza az eszköz használatának megakadályozását.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Ellenőrizze, hogy konfigurálta-e az [Azure RBAC-szabályzatokat](../../virtual-machines/linux/login-using-aad.md) a virtuális géphez, amely a virtuális gép rendszergazdai bejelentkezési vagy felhasználói bejelentkezési szerepkörét biztosítja a felhasználónak:

> [!NOTE]
> Ha problémái vannak az Azure-beli szerepkör-hozzárendelésekkel, tekintse meg [az Azure RBAC hibaelhárításával kapcsolatos témakört.](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit)
 
#### <a name="unauthorized-client"></a>Jogosulatlan ügyfél

Ha távoli asztali kapcsolatot kezdeményez a virtuális géphez, a következő hibaüzenet jelenik meg: 

- A hitelesítő adatok nem működtek.

![A hitelesítő adatok nem működtek](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Ellenőrizze, hogy a távoli asztali kapcsolat kezdeményezéséhez használt Windows 10-számítógép Azure AD-hez vagy hibrid Azure AD-hez csatlakozik-e ahhoz az Azure AD-címtárhoz, amelybe a virtuális gép csatlakozik. További információ az eszközidentitásról: [Mi az eszközidentitás?](./overview.md)

> [!NOTE]
> Windows 10 Build 20H1 parancs hozzáadta az Azure AD-ben regisztrált számítógép támogatását, hogy RDP-kapcsolatot kezdeményezzen a virtuális géppel. Ha egy regisztrált (nem Azure AD-hez csatlakozó vagy hibrid Azure AD-hez csatlakozó) számítógépet használ RDP-ügyfélként a virtuális géphez való csatlakozás kezdeményezéséhez, meg kell adnia a hitelesítő adatokat a következő `AzureAD\UPN` formátumban: (például `AzureAD\john@contoso.com` ).

Ellenőrizze, hogy az AADLoginForWindows bővítmény nem lett-e eltávolítva az Azure AD-csatlakozás befejezése után.

Győződjön meg arról is, hogy a "Hálózati biztonság: PKU2U hitelesítési kérelmek engedélyezése a számítógépnek  online identitások használatára" biztonsági szabályzat engedélyezve van a kiszolgálón és az ügyfélen is.
 
#### <a name="mfa-sign-in-method-required"></a>MFA bejelentkezési módszer szükséges

Ha távoli asztali kapcsolatot kezdeményez a virtuális géphez, a következő hibaüzenet jelenik meg: 

- A használni próbált bejelentkezési módszer nem engedélyezett. Próbálkozzon egy másik bejelentkezési módszerrel, vagy forduljon a rendszergazdához.

![A használni próbált bejelentkezési módszer nem engedélyezett.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Ha olyan feltételes hozzáférési szabályzatot konfigurált, amely megköveteli a többtényezős hitelesítést (MFA) az erőforrás eléréséhez, akkor meg kell győződni arról, hogy a virtuális gép távoli asztali kapcsolatát kezdeményező Windows 10-számítógép erős hitelesítési módszerrel( például a Windows Hello) jelentkezik be. Ha nem használ erős hitelesítési módszert a távoli asztali kapcsolathoz, az előző hibaüzenet jelenik meg.

Ha még nem telepítette az Vállalati Windows Hello-t, és ez jelenleg nem lehetséges, kizárhatja az MFA-követelményt olyan feltételes hozzáférési szabályzat konfigurálásával, amely kizárja az "Azure Windows rendszerű virtuális gép bejelentkezési" alkalmazását az MFA-t igénylő felhőalkalmazások listájából. További információ a Vállalati Windows Hello: [Vállalati Windows Hello áttekintés.](/windows/security/identity-protection/hello-for-business/hello-identity-verification)

> [!NOTE]
> Vállalati Windows Hello RDP-val történő pin-kódos hitelesítést az Windows 10 több verzióban is támogatja, az RDP-val történő biometrikus hitelesítés azonban az 1809-es Windows 10 bővült. A Vállalati Windows Hello RDP-n való hitelesítés csak olyan üzemelő példányok esetében érhető el, amelyek a tanúsítvány megbízhatósági modelljét használják, és jelenleg nem érhetők el a kulcs megbízhatósági modellhez.
 
## <a name="preview-feedback"></a>Az előzetes verzióval kapcsolatos visszajelzés

Ossza meg az előzetes verziójú funkcióval kapcsolatos visszajelzéseit, vagy jelentse a problémákat az [Azure AD visszajelzési fórumán.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Következő lépések

További információ a [Azure Active Directory: Mi a Azure Active Directory?](../fundamentals/active-directory-whatis.md)

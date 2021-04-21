---
title: Azure AD DS megosztások hitelesítésének engedélyezése
description: Megtudhatja, hogyan engedélyezheti a Active Directory Domain Services SMB-alapú hitelesítést az Azure-fájlmegosztások számára. A tartományhoz csatlakozott Windows rendszerű virtuális gépek ezután hozzáférhetnek az Azure-fájlmegosztáshoz a AD DS hitelesítő adataival.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/13/2020
ms.author: rogarana
ms.openlocfilehash: 7187563824fd7e371d6352510b9ab71c920fc1ef
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835109"
---
# <a name="part-one-enable-ad-ds-authentication-for-your-azure-file-shares"></a>Első rész: AD DS azure-fájlmegosztások hitelesítésének engedélyezése 

A Active Directory Domain Services (AD DS) hitelesítés engedélyezése előtt olvassa el az áttekintő cikket a támogatott forgatókönyvek és követelmények áttekintéséhez. [](storage-files-identity-auth-active-directory-enable.md)

Ez a cikk a tárfiókon Active Directory Domain Services (AD DS) hitelesítés engedélyezésének folyamatát ismerteti. A funkció engedélyezése után úgy kell konfigurálnia a tárfiókot és a AD DS, hogy AD DS hitelesítő adatokat használjanak az Azure-fájlmegosztásban való hitelesítéshez. Az Azure AD DS megosztások SMB-protokollon keresztüli hitelesítésének engedélyezéséhez regisztrálnia kell a tárfiókot az AD DS-ban, majd be kell állítania a szükséges tartománytulajdonságokat a tárfiókon.

Ha regisztrálnia kell a tárfiókot a AD DS, hozzon létre egy fiókot, amely azt jelképez a AD DS. Ez a folyamat olyan, mintha egy helyszíni Windows-fájlkiszolgálót képviselő fiókot hoz létre a AD DS. Ha a szolgáltatás engedélyezve van a tárfiókon, az a fiókban lévő összes új és meglévő fájlmegosztásra vonatkozik.

## <a name="option-one-recommended-use-azfileshybrid-powershell-module"></a>Első lehetőség (ajánlott): Az AzFilesHybrid PowerShell-modul használata

Az AzFilesHybrid PowerShell-modul parancsmagja biztosítja a szükséges módosításokat, és engedélyezi a funkciót. Mivel a parancsmagok egyes részei együttműködnek a helyszíni AD DS-sel, elmagyarázzuk a parancsmagok műveletét, így megállapíthatja, hogy a módosítások megfelelnek-e a megfelelőségi és biztonsági szabályzatának, és biztosíthatja a megfelelő engedélyeket a parancsmagok végrehajtásához. Bár javasoljuk az AzFilesHybrid modul használatát, ha ezt nem tudja megtenni, meg kell adnia a lépéseket, hogy manuálisan is végrehajtsa őket.

### <a name="download-azfileshybrid-module"></a>Az AzFilesHybrid modul letöltése

- [Töltse le és csomagolja ki az AzFilesHybrid modult (GA modul: v0.2.0+)](https://github.com/Azure-Samples/azure-files-samples/releases) Vegye figyelembe, hogy az AES 256 Kerberos titkosítás a 0.2.2-es vagy magasabb verziókban támogatott. Ha a 0.2.2-es verziónál régebbi AzFilesHybrid verzióval engedélyezte a funkciót, és frissíteni szeretne az AES 256 Kerberos titkosítás támogatásához, tekintse meg ezt a [cikket.](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption) 
- Telepítse és hajtsa végre a modult egy olyan eszközön, amely tartományhoz csatlakozik a helyszíni AD DS AD DS-hoz olyan hitelesítő adatokkal, amelyek engedéllyel rendelkezik egy szolgáltatás-bejelentkezési fiók vagy egy számítógépfiók létrehozásához a cél AD-ben.
-  Futtassa a szkriptet egy helyszíni AD DS, amely szinkronizálva van az Azure AD-be. A helyszíni AD DS hitelesítő adatoknak rendelkezniük kell a tárfiók tulajdonosával vagy a közreműködő Azure-szerepkör engedélyével.

### <a name="run-join-azstorageaccountforauth"></a>Futtassa Join-AzStorageAccountForAuth

A parancsmag az offline tartományhoz való csatlakozásnak megfelelő műveletet hajt végre `Join-AzStorageAccountForAuth` a megadott tárfiók nevében. A szkript a parancsmag használatával hoz létre [számítógépfiókot](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) az AD-tartományban. Ha bármilyen okból nem használhat számítógépfiókot, módosíthatja a parancsfájlt, hogy ehelyett szolgáltatás-bejelentkezési [fiókot hozzon](/windows/win32/ad/about-service-logon-accounts) létre. Ha úgy dönt, hogy manuálisan futtatja a parancsot, válassza ki a környezetének leginkább megfelelő fiókot.

A AD DS által létrehozott fiók jelöli a tárfiókot. Ha a AD DS-fiókot a jelszó lejáratát kikényszerő szervezeti egységben (OU) hozták létre, a jelszót a jelszó maximális kora előtt kell frissítenie. A fiók jelszavának ezen dátum előtti frissítése hitelesítési hibákat okozhat az Azure-fájlmegosztások elérésekor. A jelszó frissítésének mikéntjére a fiókjelszó [frissítését AD DS olvashat.](storage-files-identity-ad-ds-update-password.md)

Cserélje le a helyőrző értékeket a saját értékeire az alábbi paraméterekben, mielőtt végrehajtja őket a PowerShellben.
> [!IMPORTANT]
> A tartományhoz való csatlakozás parancsmagja létrehoz egy AD-fiókot, amely a tárfiókot (fájlmegosztást) képviseli az AD-ben. Dönthet úgy, hogy számítógépfiókként vagy szolgáltatás bejelentkezési fiókként regisztrál, a részletekért tekintse meg a [gyakori](./storage-files-faq.md#security-authentication-and-access-control) kérdéseket. A számítógépfiókok esetében a jelszó alapértelmezett lejárati ideje az AD-ben 30 nap. Hasonlóképpen, előfordulhat, hogy a szolgáltatás bejelentkezési fiókjához az AD-tartomány vagy szervezeti egység (OU) jelszó-lejárati ideje van beállítva.
> Javasoljuk, hogy mindkét fióktípus esetében ellenőrizze az AD-környezetben [](storage-files-identity-ad-ds-update-password.md) konfigurált jelszó lejárati életkorát, és a jelszó maximális kora előtt tervezze meg frissíteni az AD-fiók tárfiók-identitásának jelszavát. Érdemes lehet létrehozni egy új AD szervezeti egységet [(OU)](/powershell/module/addsadministration/new-adorganizationalunit) az [](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)) AD-ben, és ennek megfelelően letiltani a jelszó-elévülési szabályzatot a számítógépfiókok vagy szolgáltatás-bejelentkezési fiókok esetében. 

```PowerShell
# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

# Login with an Azure AD credential that has either storage account owner or contributer Azure role assignment
# If you are logging into an Azure environment other than Public (ex. AzureUSGovernment) you will need to specify that.
# See https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-ps
# for more information.
Connect-AzAccount

# Define parameters, $StorageAccountName currently has a maximum limit of 15 characters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

# Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account (default parameter value), depends on the AD permission you have and preference. 
# Run Get-Help Join-AzStorageAccountForAuth for more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -StorageAccountName $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" <# Default is set as ComputerAccount #> `
        -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" <# If you don't provide the OU name as an input parameter, the AD identity that represents the storage account is created under the root directory. #> `
        -EncryptionType "<AES256/RC4/AES256,RC4>" <# Specify the encryption agorithm used for Kerberos authentication. Default is configured as "'RC4','AES256'" which supports both 'RC4' and 'AES256' encryption. #>

#Run the command below if you want to enable AES 256 authentication. If you plan to use RC4, you can skip this step.
Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, see Azure Files Windows troubleshooting guide.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="option-2-manually-perform-the-enablement-actions"></a>2. lehetőség: Az engedélyező műveletek manuális végrehajtása

Ha már sikeresen végrehajtotta a fenti szkriptet, kattintson a Funkció engedélyezése `Join-AzStorageAccountForAuth` [szakaszra.](#confirm-the-feature-is-enabled) A következő manuális lépéseket nem kell végrehajtania.

### <a name="checking-environment"></a>Környezet ellenőrzése

Először ellenőriznie kell a környezet állapotát. Pontosabban azt kell ellenőriznie, Active Directory telepítve van-e a [PowerShell,](/powershell/module/addsadministration/) és hogy a rendszerhéj végrehajtása rendszergazdai jogosultságokkal történik-e. Ezután ellenőrizze, hogy az [Az.Storage 2.0 modul](https://www.powershellgallery.com/packages/Az.Storage/2.0.0) telepítve van-e, és ha nincs, telepítse. Az ellenőrzések elvégzése után ellenőrizze a AD DS-ben, hogy van-e olyan [](/windows/win32/ad/about-service-logon-accounts) számítógépfiók [(alapértelmezett)](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) vagy szolgáltatás bejelentkezési fiók, amely már létre lett hozva "cifs/your-storage-account-name-here.file.core.windows.net" néven. Ha a fiók nem létezik, hozzon létre egyet a következő szakaszban leírtak szerint.

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>A tárfiókot képviselő identitás manuális létrehozása az AD-ban

A fiók manuális létrehozásához hozzon létre egy új Kerberos-kulcsot a tárfiókhoz. Ezután használja ezt a Kerberos-kulcsot a fiókja jelszavaként az alábbi PowerShell-parancsmagokkal. Ez a kulcs csak a telepítés során használatos, és nem használható a tárfiókon bármilyen vezérlő- vagy adatsíkművelethez. 

```PowerShell
# Create the Kerberos key on the storage account and get the Kerb1 key as the password for the AD identity to represent the storage account
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName kerb1
Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -ListKerbKey | where-object{$_.Keyname -contains "kerb1"}
```

Ha már megvan a kulcs, hozzon létre egy szolgáltatás- vagy számítógépfiókot a szervezeti egységben. Használja a következő specifikációt (ne felejtse el lecserélni a példaszöveget a tárfiók nevére):

SPN: "cifs/your-storage-account-name-here.file.core.windows.net" Jelszó: Kerberos-kulcs a tárfiókhoz.

Ha a szervezeti egység a jelszó lejáratát kényszeríti, a jelszó maximális kora előtt frissítenie kell a jelszót, hogy megakadályozza a hitelesítési hibákat az Azure-fájlmegosztások elérésekor. A részletekért lásd: A tárfiók [identitásának jelszavának](storage-files-identity-ad-ds-update-password.md) frissítése az AD-ban.

Tartsa meg az újonnan létrehozott identitás biztonsági azonosítóját, mert a következő lépéshez szüksége lesz rá. A tárfiókot képviselő identitást nem kell szinkronizálni az Azure AD-be.

### <a name="enable-the-feature-on-your-storage-account"></a>A funkció engedélyezése a tárfiókon

Most már engedélyezheti a funkciót a tárfiókon. Adja meg a következő parancsban a tartománytulajdonságok néhány konfigurációs részletét, majd futtassa azt. A tárfiók következő parancsban szükséges biztonsági azonosítója az előző szakaszban létrehozott AD DS [SID.](#creating-an-identity-representing-the-storage-account-in-your-ad-manually)

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```

### <a name="debugging"></a>Hibakeresés

A Debug-AzStorageAccountAuth parancsmag futtatásával alapszintű ellenőrzéseket futtathat az AD-konfiguráción a bejelentkezett AD-felhasználóval. Az AzFilesHybrid v0.1.2+ verziója támogatja ezt a parancsmagot. Az ebben a parancsmagban végrehajtott ellenőrzésekkel kapcsolatos további információkért lásd a Windows hibaelhárítási útmutatójának Nem csatlakoztatható Azure Files [az AD](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) hitelesítő adataival.

```PowerShell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="confirm-the-feature-is-enabled"></a>Ellenőrizze, hogy a funkció engedélyezve van-e

A következő szkript használatával ellenőrizheti, hogy a funkció engedélyezve van-e a tárfiókban:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Sikeres művelet esetén a kimenetnek így kell kinéznie:

```PowerShell
DomainName:<yourDomainHere>
NetBiosDomainName:<yourNetBiosDomainNameHere>
ForestName:<yourForestNameHere>
DomainGuid:<yourGUIDHere>
DomainSid:<yourSIDHere>
AzureStorageID:<yourStorageSIDHere>
```
## <a name="next-steps"></a>Következő lépések

Sikeresen engedélyezte a funkciót a tárfiókon. A funkció használatához megosztási szintű engedélyeket kell hozzárendelnie. Folytassa a következő szakaszokkal.

[Második rész: megosztásszintű engedélyek hozzárendelése egy identitáshoz](storage-files-identity-ad-ds-assign-permissions.md)
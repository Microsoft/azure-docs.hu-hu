---
title: Office 365-szolgáltatások kezelése az Azure Automation használatával
description: Ez a cikk azt ismerteti, hogyan használható a Azure Automation az Office 365 előfizetési szolgáltatások kezeléséhez.
services: automation
ms.date: 11/05/2020
ms.topic: conceptual
ms.openlocfilehash: 70c8892969a3b13175c60a4e20e0cf9086112abe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93398045"
---
# <a name="manage-office-365-services"></a>Office 365-szolgáltatások kezelése

Az Office 365 előfizetési szolgáltatások felügyeletéhez Azure Automation használhatja a Microsoft Word és a Microsoft Outlook termékekhez. Az Office 365-mel folytatott interakciókat [Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md)engedélyezték. Lásd: az Azure [ad használata Azure Automation az Azure-ban való hitelesítéshez](automation-use-azure-ad.md).

## <a name="prerequisites"></a>Előfeltételek

Az Office 365 előfizetési szolgáltatásainak Azure Automation-ben való kezeléséhez a következőkre lesz szüksége.

* Azure-előfizetés. Lásd: [előfizetés-döntési útmutató](/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Egy Automation-objektum az Azure-ban a felhasználói fiók hitelesítő adatainak és runbookok tárolásához. Tekintse [meg a Azure Automation bevezetését](./automation-intro.md).
* Azure AD. Lásd: az Azure [ad használata Azure Automation az Azure-ban való hitelesítéshez](automation-use-azure-ad.md).
* Office 365-bérlő, fiókkal. Lásd: [Office 365-bérlő beállítása](/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="install-the-msonline-and-msonlineext-modules"></a>A MSOnline és a MSOnlineExt modulok telepítése

A Azure Automationon belüli Office 365 használatához Microsoft Azure Active Directory szükséges a Windows PowerShellhez ( `MSOnline` modul). Emellett a modulra is szüksége lesz [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35) , amely leegyszerűsíti az Azure ad-felügyeletet az egy-és több-bérlős környezetekben. Telepítse a modulokat az Azure [ad-ban Azure Automation az Azure-ban való hitelesítéshez](automation-use-azure-ad.md)című témakörben leírtak szerint.

>[!NOTE]
>A MSOnline PowerShell használatához az Azure AD-tagnak kell lennie. A vendég felhasználók nem használhatják a modult.

## <a name="create-an-azure-automation-account"></a>Azure Automation-fiók létrehozása

A cikkben szereplő lépések végrehajtásához egy Azure Automation fiókra van szükség. Lásd: [Azure Automation fiók létrehozása](automation-quickstart-create-account.md).
 
## <a name="add-msonline-and-msonlineext-as-assets"></a>MSOnline és MSOnlineExt hozzáadása eszközként

Most adja hozzá a telepített MSOnline-és MSOnlineExt-modulokat az Office 365 funkcióinak engedélyezéséhez. Tekintse át a [Azure Automation moduljainak kezelése](shared-resources/modules.md)című témakört.

1. A Azure Portal válassza az **Automation-fiókok** elemet.
2. Válassza ki az Automation-fiókját.
3. Válassza a **modulok tárat** a **megosztott erőforrások** területen.
4. MSOnline keresése.
5. Válassza ki a `MSOnline` PowerShell-modult, és kattintson az **Importálás** gombra a modul eszközként való importálásához.
6. A modul megkereséséhez és importálásához ismételje meg a 4. és az 5. lépést `MSOnlineExt` .

## <a name="create-a-credential-asset-optional"></a>Hitelesítőadat-eszköz létrehozása (nem kötelező)

Nem kötelező létrehozni a hitelesítő adatokat az Office 365 rendszergazda felhasználó számára, aki rendelkezik a parancsfájl futtatásához szükséges engedélyekkel. Ez segíthet azonban abban, hogy a felhasználói neveket és jelszavakat a PowerShell-szkripteken belül ne tegye közzé. Útmutatásért tekintse meg [a hitelesítőadat-eszköz létrehozása](automation-use-azure-ad.md#create-a-credential-asset)című témakört.

## <a name="create-an-office-365-service-account"></a>Office 365-szolgáltatásfiók létrehozása

Az Office 365 előfizetési szolgáltatásainak futtatásához szüksége van egy Office 365-szolgáltatásfiók, amely a kívánt műveletekre vonatkozó engedélyekkel rendelkezik. Használhat egy globális rendszergazdai fiókot, egy-egy fiókot, vagy egy függvényt vagy szkriptet a végrehajtáshoz. A szolgáltatási fióknak minden esetben összetett és biztonságos jelszót kell megadnia. Lásd: [az Office 365 for Business beállítása](/microsoft-365/admin/setup/setup).

## <a name="connect-to-the-azure-ad-online-service"></a>Kapcsolódás az Azure AD online szolgáltatáshoz

>[!NOTE]
>A MSOnline modul-parancsmagok használatához futtatnia kell őket a Windows PowerShellből. A PowerShell Core nem támogatja ezeket a parancsmagokat.

A MSOnline modul használatával kapcsolódhat az Azure AD-hez az Office 365-előfizetésből. A kapcsolatok egy Office 365-felhasználónevet és-jelszót használnak, vagy többtényezős hitelesítést (MFA) használnak. Az Azure Portal vagy egy Windows PowerShell-parancssor használatával is csatlakozhat (nem kell megemelni).

Alább látható egy PowerShell-példa. A [Get-hitelesítőadat](/powershell/module/microsoft.powershell.security/get-credential) parancsmag megkéri a hitelesítő adatokat, és tárolja azokat a `Msolcred` változóban. Ezután a [MsolService](/powershell/module/msonline/connect-msolservice) parancsmag a hitelesítő adatok használatával csatlakozik az Azure Directory online szolgáltatáshoz. Ha egy adott Azure-környezethez szeretne csatlakozni, használja a (z `AzureEnvironment` ) paramétert.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Ha nem kap hibát, sikeresen kapcsolódott. A gyors tesztelés egy Office 365-parancsmag futtatása, például `Get-MsolUser` :, és az eredmények megtekintése. Ha hibaüzenetet kap, vegye figyelembe, hogy egy gyakori probléma helytelen jelszó.

>[!NOTE]
>A AzureRM modul vagy az az modul használatával is csatlakozhat az Azure AD-hez az Office 365-előfizetésből. A fő csatlakozási parancsmag a kapcsolat [– AzureAD](/powershell/module/azuread/connect-azuread). Ez a parancsmag a `AzureEnvironmentName` megadott Office 365-környezetek paraméterét támogatja.

## <a name="create-a-powershell-runbook-from-an-existing-script"></a>PowerShell-runbook létrehozása meglévő parancsfájlból

Az Office 365 funkcióit egy PowerShell-szkriptből érheti el. Az alábbi példa egy, a felhasználónévvel ellátott hitelesítő adathoz tartozó parancsfájlt mutat be `Office-Credentials` `admin@TenantOne.com` . `Get-AutomationPSCredential`Az Office 365 hitelesítő adatának importálására használja.

```powershell
$emailFromAddress = "admin@TenantOne.com"
$emailToAddress = "servicedesk@TenantOne.com"
$emailSMTPServer = "outlook.office365.com"
$emailSubject = "Office 365 License Report"

$credObject = Get-AutomationPSCredential -Name "Office-Credentials"
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body $O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="run-the-script-in-a-runbook"></a>Parancsfájl futtatása runbook

A parancsfájlt Azure Automation runbook használhatja. Például a PowerShell runbook típusát fogjuk használni.

1. Hozzon létre egy új PowerShell-runbook. Tekintse át [Azure Automation Runbook létrehozását](./automation-quickstart-create-runbook.md)ismertető témakört.
2. Az Automation-fiókjából válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.
3. Válassza ki az új runbook, és kattintson a **Szerkesztés** gombra.
4. Másolja a szkriptet, és illessze be a runbook szöveges szerkesztőjébe.
5. Válassza az **eszközök** lehetőséget, majd bontsa ki a **hitelesítő adatokat** , és ellenőrizze, hogy az Office 365 hitelesítő adatai vannak-e.
6. Kattintson a **Mentés** gombra.
7. Válassza a **teszt panel** lehetőséget, majd kattintson az **Indítás** gombra a runbook tesztelésének megkezdéséhez. Lásd: [Runbookok kezelése Azure Automationban](./manage-runbooks.md).
8. A tesztelés befejezésekor lépjen ki a teszt panelről.

## <a name="publish-and-schedule-the-runbook"></a>A runbook közzététele és beosztása

A runbook közzétételéhez és a runbookok való bevezetéséhez tekintse meg a következő témakört: [a Azure Automation kezelése](./manage-runbooks.md).

## <a name="next-steps"></a>Következő lépések

* A hitelesítő adatok használatának részleteit lásd: [a hitelesítő adatok kezelése Azure Automationban](shared-resources/credentials.md).
* A modulokkal kapcsolatos további információkért lásd: [modulok kezelése Azure Automationban](shared-resources/modules.md).
* Ha runbook kell elindítania, tekintse meg [a Runbook elindítása a Azure Automationban](start-runbooks.md)című témakört.
* A PowerShell részleteiért lásd: [PowerShell-dokumentumok](/powershell/scripting/overview).

---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 6d95e40623f17a39145778a2fc067dccc68fd872
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95562420"
---
## <a name="steps-to-enable-single-sign-on"></a>Az egyszeri bejelentkezés engedélyezésének lépései
A Felhőbeli kiépítés egyszeri bejelentkezéssel működik.  Jelenleg nincs lehetőség az egyszeri bejelentkezés engedélyezésére az ügynök telepítésekor, azonban az alábbi lépésekkel engedélyezheti az egyszeri bejelentkezést, és használhatja azt. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>1. lépés: Azure AD Connect fájlok letöltése és kinyerése
1.  Először töltse le a [Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594) legújabb verzióját
2.  Nyisson meg egy parancssort rendszergazdai jogosultságokkal, és navigáljon az imént letöltött MSI-hez.
3.  Futtassa a következőt:  `msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. Módosítsa a filepath és a extractfolder, hogy egyezzen a fájl elérési útjával és a kibontási mappa nevével.  A tartalomnak most a kibontási mappában kell lennie.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>2. lépés: a zökkenőmentes SSO PowerShell-modul importálása

1. Töltse le és telepítse az [Azure ad PowerShellt](/powershell/azure/active-directory/overview).
2. Tallózással keresse meg a `%programfiles%\Microsoft Azure Active Directory Connect` mappát.
3. Importálja a zökkenőmentes SSO PowerShell-modult a következő parancs használatával: `Import-Module .\AzureADSSO.psd1` .

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>3. lépés: azon Active Directory erdők listájának beolvasása, amelyeken engedélyezve van a zökkenőmentes egyszeri bejelentkezés

1. Futtassa a PowerShellt rendszergazdaként. A PowerShellben hívja meg a t `New-AzureADSSOAuthenticationContext` . Ha a rendszer kéri, adja meg a bérlő globális rendszergazdai hitelesítő adatait.
2. Hívás `Get-AzureADSSOStatus` . Ez a parancs a Active Directory erdők listáját tartalmazza (tekintse meg a "tartományok" listát), amelyen a funkció engedélyezve van.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>4. lépés: a zökkenőmentes SSO engedélyezése minden Active Directory erdőben

1. Hívás `Enable-AzureADSSOForest` . Ha a rendszer kéri, adja meg a kívánt Active Directory erdő tartományi rendszergazdai hitelesítő adatait.

   > [!NOTE]
   >A tartományi rendszergazdai hitelesítő adatok felhasználónevet a SAM-fiók nevének formátumában kell megadni (contoso\johndoe vagy contoso. com\johndoe). A Felhasználónév tartomány részeként a tartományi rendszergazda tartományvezérlőjét kell megkeresni a DNS használatával.

   >[!NOTE]
   >A használt tartományi rendszergazdai fiók nem lehet a Protected Users csoport tagja. Ha igen, a művelet sikertelen lesz.

2. Ismételje meg az előző lépést minden olyan Active Directory erdőben, amelyben be szeretné állítani a szolgáltatást.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>5. lépés A szolgáltatás engedélyezése a bérlőn

Ha be szeretné kapcsolni a szolgáltatást a bérlőn, hívja a következőt: `Enable-AzureADSSO -Enable $true` .
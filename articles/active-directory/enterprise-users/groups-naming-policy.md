---
title: Csoport elnevezési szabályzatának betartatása Azure Active Directoryban | Microsoft Docs
description: Microsoft 365 csoportok elnevezési szabályzatának beállítása Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00291907ddea75ba4507779d1226502dc8343aea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100365701"
---
# <a name="enforce-a-naming-policy-on-microsoft-365-groups-in-azure-active-directory"></a>Elnevezési szabályzat érvénybe léptetése Microsoft 365 csoportokban Azure Active Directory

A felhasználók által létrehozott vagy szerkesztett Microsoft 365 csoportok konzisztens elnevezési konvencióinak kikényszerített megadásához állítson be egy csoport elnevezési szabályzatot az Azure Active Directory (Azure AD) szervezete számára. Az elnevezési házirend segítségével például a csoport, a tagság, a földrajzi régió vagy a csoport létrehozásával kommunikálhat. Használhatja az elnevezési házirendet is a címjegyzékben lévő csoportok kategorizálásához. A szabályzat használatával letilthat bizonyos szavakat a csoportok neveiben és az Aliasokban.

> [!IMPORTANT]
> Az Microsoft 365-csoportok Azure AD elnevezési házirendjének használata megköveteli, hogy rendelkezzen, de ne rendeljen hozzá prémium szintű Azure Active Directory P1-licencet vagy alapszintű Azure AD EDU-licencet minden olyan egyedi felhasználóhoz, amely egy vagy több Microsoft 365 csoport tagja.

A rendszer az elnevezési házirendet alkalmazza a munkaterhelések között létrehozott csoportok létrehozására vagy szerkesztésére (például Outlook, Microsoft Teams, SharePoint, Exchange vagy Planner). A csoport nevére és a csoport aliasnevére is vonatkozik. Ha az Azure AD-ben beállítja az elnevezési szabályzatot, és rendelkezik egy meglévő Exchange-csoport elnevezési házirenddel, az Azure AD elnevezési szabályzata kényszerítve lesz a szervezetében.

Ha a csoport elnevezési házirend konfigurálva van, a szabályzatot a rendszer a végfelhasználók által létrehozott új Microsoft 365 csoportokra alkalmazza. Az elnevezési házirend nem vonatkozik bizonyos címtárbeli szerepkörökre, például a globális rendszergazda vagy a felhasználó rendszergazdájára (lásd alább a csoport elnevezési házirend alól mentesített szerepkörök teljes listáját). Meglévő Microsoft 365 csoportok esetén a házirend nem vonatkozik azonnal a konfiguráció időpontjában. Miután a csoport tulajdonosa szerkeszti a csoportok nevét, a rendszer kikényszeríti a névadási házirendet.

## <a name="naming-policy-features"></a>Elnevezési házirend funkciói

Két különböző módon kényszerítheti ki a csoportok elnevezési szabályzatát:

- **Előtag – utótag elnevezési házirend** Megadhatja azokat az előtagokat vagy utótagokat, amelyeket a rendszer automatikusan hozzáad a csoportok elnevezési konvenciójának érvényesítéséhez (például a "GRP \_ japán \_ My Group \_ Engineering" csoportban \_ ), a GRP Japán az \_ előtag, a \_ mérnöki pedig az utótag. 

- **Egyéni tiltott szavak** Feltölthet olyan tiltott szavakat is, amelyek a szervezet számára jellemzőek, hogy a felhasználók által létrehozott csoportokban le legyenek tiltva (például "VEZÉRIGAZGATÓ, bérszámfejtés, HR").

### <a name="prefix-suffix-naming-policy"></a>Előtag – utótag elnevezési házirend

Az elnevezési konvenció általános szerkezete a "prefix [csoportnév] utótag". Míg több előtagot és utótagot is meghatározhat, a beállításban csak a [csoportnév] egyetlen példánya lehet. Az előtagok vagy az utótagok lehetnek rögzített karakterláncok vagy felhasználói attribútumok, például \[ részlegek \] , amelyek a csoportot létrehozó felhasználó alapján helyettesíthetők. Az előtag és az utótag sztringek teljes megengedett száma, beleértve a csoportnév 53 karaktert. 

Az előtagok és az utótagok olyan speciális karaktereket tartalmazhatnak, amelyek a csoport neve és a csoport aliasában is támogatottak. Az előtag vagy utótag azon karaktereit, amelyeket a csoport aliasa nem támogat, továbbra is a csoport nevében lesznek alkalmazva, de a csoport aliasa el lesz távolítva. Ennek a korlátozásnak a miatt a csoport nevére alkalmazott előtagok és utótagok eltérőek lehetnek a csoport aliasán alkalmazott értékekkel. 

#### <a name="fixed-strings"></a>Rögzített karakterláncok

A karakterláncok segítségével könnyebben vizsgálhatja és megkülönböztetheti a csoportokat a globális címlistában, valamint a csoportos munkaterhelések bal oldali navigációs hivatkozásait. Néhány gyakori előtagok olyan kulcsszavak, mint például a "GRP \_ neve", a " \# Name", a " \_ Name"

#### <a name="user-attributes"></a>Felhasználói attribútumok

Használhatja azokat az attribútumokat, amelyek segítségével a felhasználók megadhatják, hogy melyik részleget, irodát vagy földrajzi régiót hozta létre a csoport. Ha például a (z) és a (z) elnevezési házirendet definiálja, `PrefixSuffixNamingRequirement = "GRP [GroupName] [Department]"` `User’s department = Engineering` akkor a kényszerített csoport neve lehet "a csoportom megtervezése". A támogatott Azure AD-attribútumok a következők: \[ részleg \] , \[ vállalat \] , \[ iroda \] , \[ StateOrProvince \] , \[ CountryorRegion \] , \[ cím \] . A nem támogatott felhasználói attribútumok rögzített karakterláncként vannak kezelve; például: " \[ Irányítószám \] ". A bővítmény attribútumai és az egyéni attribútumok nem támogatottak.

Javasoljuk, hogy a szervezet összes felhasználója számára kitöltött értékeket tartalmazó attribútumokat használjon, és ne használjon hosszú értékeket tartalmazó attribútumokat.

### <a name="custom-blocked-words"></a>Egyéni tiltott szavak

A tiltott szólisták a csoportok neveiben és Aliasokban blokkolni kívánt kifejezések vesszővel tagolt listája. Nem végeznek alkarakterlánc-kereséseket. A hiba kiváltásához pontosan egyeznie kell a csoport neve és egy vagy több egyéni blokkolt szó között. Az alkarakterlánc-keresés nem hajtható végre, így a felhasználók a "class" kifejezéseket is használhatják, például ha a "lány" egy letiltott szó.

Tiltott szólisták szabályai:

- A tiltott szavak nem bizalmasak.
- Ha a felhasználó egy csoport neve részeként letiltott szót ad meg, a rendszer hibaüzenetet jelenít meg a letiltott Szóval.
- A letiltott szavakat nem korlátozzák a karakterek.
- A letiltott szavak listájában legfeljebb 5000 kifejezés állítható be. 

### <a name="roles-and-permissions"></a>Szerepkörök és engedélyek

Az elnevezési házirend konfigurálásához a következő szerepkörök egyike szükséges:
- Globális rendszergazda
- Csoport rendszergazdája
- Könyvtár-író


A kiválasztott rendszergazdák mentesülnek a szabályzatokból, az összes csoportos munkaterhelésre és végpontra vonatkozóan, így a csoportok a letiltott szavak és a saját elnevezési konvencióik alapján hozhatók létre. A következő lista a csoport elnevezési házirendjé alól mentesített rendszergazdai szerepkörök listáját tartalmazza.

- Globális rendszergazda
- Felhasználói rendszergazda

## <a name="configure-naming-policy-in-azure-portal"></a>Elnevezési házirend konfigurálása a Azure Portalban

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) egy csoport-rendszergazdai fiókkal.
1. Válassza a **csoportok** lehetőséget, majd válassza az **elnevezési házirend** elemet az elnevezési házirend lap megnyitásához.

    ![az elnevezési szabályzat lap megnyitása a felügyeleti központban](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Az előtag-utótag elnevezési házirend megtekintése vagy szerkesztése

1. Az **elnevezési házirend** lapon válassza a **csoport elnevezési házirend** elemet.
1. Az aktuális előtag-vagy utótag-elnevezési házirendeket egyenként tekintheti meg vagy szerkesztheti. Ehhez válassza ki azokat az attribútumokat vagy karakterláncokat, amelyeket az elnevezési szabályzat részeként szeretne kikényszeríteni.
1. Ha el szeretne távolítani egy előtagot vagy utótagot a listából, válassza ki az előtagot vagy utótagot, majd válassza a **Törlés** lehetőséget. Egyszerre több elem is törölhető.
1. Mentse az új szabályzat módosításait, hogy a **Mentés** gombra kattintva lépjen életbe.

### <a name="edit-custom-blocked-words"></a>Egyéni tiltott szavak szerkesztése

1. A **névadási házirend** lapon válassza a **tiltott szavak** elemet.

    ![tiltott szavak listájának szerkesztése és feltöltése elnevezési házirendhez](./media/groups-naming-policy/blockedwords.png)

1. A **Letöltés** lehetőség kiválasztásával megtekintheti vagy szerkesztheti az egyéni tiltott szavak aktuális listáját.
1. Töltse fel az egyéni tiltott szavak új listáját a fájl ikon kiválasztásával.
1. Mentse az új szabályzat módosításait, hogy a **Mentés** gombra kattintva lépjen életbe.

## <a name="install-powershell-cmdlets"></a>PowerShell-parancsmagok telepítése

A PowerShell-parancsok futtatása előtt mindenképpen távolítsa el a Windows PowerShellhez készült Azure Active Directory PowerShell for Graph modul összes régebbi verzióját, és telepítse az [Azure Active Directory PowerShell for Graph 2.0.0.137-es nyilvános előzetes kiadását](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

1. Nyissa meg a Windows PowerShell alkalmazást rendszergazdaként.
2. Távolítsa el az AzureADPreview korábbi verzióit.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   ```

3. Telepítse az AzureADPreview legújabb verzióját.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```

   Ha a rendszer felszólítja a nem megbízható adattár elérésére, írja be az **Y** értéket. Az új modul telepítéséhez néhány percet is igénybe vehet.

## <a name="configure-naming-policy-in-powershell"></a>Elnevezési házirend konfigurálása a PowerShellben

1. Nyisson meg egy Windows PowerShell-ablakot a számítógépen. Emelt szintű jogosultságok nélkül is megnyithatja.

1. Futtassa a következő parancsokat a parancsmagok futtatásának előkészítéséhez.
  
   ``` PowerShell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   A megjelenő **Bejelentkezés a fiókba** párbeszédpanelen adja meg a rendszergazdai fiókot és jelszót, hogy kapcsolatot létesítsen a szolgáltatással, majd válassza a **Bejelentkezés** lehetőséget.

1. A csoport beállításainak [konfigurálásához kövesse az Azure Active Directory-parancsmagok](../enterprise-users/groups-settings-cmdlets.md) című szakasz lépéseit a szervezeti beállítások létrehozásához.

### <a name="view-the-current-settings"></a>Aktuális beállítások megtekintése

1. Az aktuális névhasználati házirend beolvasása a jelenlegi beállítások megtekintéséhez.
  
   ``` PowerShell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
1. Jelenítse meg az aktuális csoportbeállításokat.
  
   ``` PowerShell
   $Setting.Values
   ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Az elnevezési házirend és az egyéni tiltott szavak beállítása

1. Állítsa be a csoportnév előtagjait és utótagjait az Azure AD PowerShellben. Ahhoz, hogy a szolgáltatás megfelelően működjön, [csoportnév] szerepelnie kell a beállításban.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
1. Állítsa be az egyéni letiltott szavakat, amelyek használatát korlátozni szeretné. A következő példa szemlélteti, hogyan adhatja hozzá saját egyéni szavait.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
1. Mentse az új szabályzat beállításait, hogy azok érvénybe lépjenek, például a következő példában.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
Ennyi az egész. Beállította az elnevezési házirendet, és hozzáadta a blokkolt szavakat.

## <a name="export-or-import-custom-blocked-words"></a>Egyéni tiltott szavak exportálása vagy importálása

További információ: [Azure Active Directory parancsmagok a csoportházirend-beállítások konfigurálásához](../enterprise-users/groups-settings-cmdlets.md).

Íme egy példa egy PowerShell-szkriptre több blokkolt szó exportálásához:

``` PowerShell
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
```

Íme egy példa a több blokkolt szó importálására szolgáló PowerShell-szkriptre:

``` PowerShell
$BadWords = Get-Content "C:\work\currentblockedwordslist.txt"
$BadWords = [string]::join(",", $BadWords)
$Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}
if ($Settings.Count -eq 0)
    {$Template = Get-AzureADDirectorySettingTemplate | Where-Object {$_.DisplayName -eq "Group.Unified"}
    $Settings = $Template.CreateDirectorySetting()
    New-AzureADDirectorySetting -DirectorySetting $Settings
    $Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}}
$Settings["CustomBlockedWordsList"] = $BadWords
Set-AzureADDirectorySetting -Id $Settings.Id -DirectorySetting $Settings 
```

## <a name="remove-the-naming-policy"></a>Az elnevezési házirend eltávolítása

### <a name="remove-the-naming-policy-using-azure-portal"></a>Az elnevezési házirend eltávolítása Azure Portal használatával

1. Az **elnevezési házirend** lapon válassza a **házirend törlése** lehetőséget.
1. A Törlés megerősítése után a rendszer eltávolítja az elnevezési házirendet, beleértve az összes előtag-utótag elnevezési házirendet és az egyéni letiltott szavakat is.

### <a name="remove-the-naming-policy-using-azure-ad-powershell"></a>Az elnevezési házirend eltávolítása az Azure AD PowerShell használatával

1. Ürítse ki az Azure AD PowerShell csoport neve előtagjait és utótagját.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```
  
1. Ürítse ki az egyéni letiltott szavakat.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=""
   ```
  
1. Mentse a beállításokat.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="experience-across-microsoft-365-apps"></a>Microsoft 365 alkalmazások közötti élmény

Miután beállította a csoport elnevezési házirendjét az Azure AD-ben, amikor egy felhasználó létrehoz egy csoportot egy Microsoft 365 alkalmazásban, a következőt látják:

- A név előnézete az elnevezési szabályzatnak megfelelően (előtagokkal és utótagokkal), amint a felhasználó típusa szerepel a csoport nevében.
- Ha a felhasználó letiltott szavakat ír be, hibaüzenet jelenik meg, hogy el tudják távolítani a blokkolt szavakat.

Számítási feladat | Megfelelőség
----------- | -------------------------------
Azure Active Directory portálok | Az Azure AD-portál és a hozzáférési panel portálon az elnevezési szabályzat kényszerített neve jelenik meg, amikor a felhasználó a csoport nevében egy csoport létrehozásakor vagy szerkesztésekor begépeli a nevet. Ha a felhasználó egy egyéni letiltott szót ad meg, a rendszer hibaüzenetet jelenít meg a letiltott Szóval, hogy a felhasználó el tudja távolítani.
Outlook Web Access (OWA) | Az Outlook Web Access megjeleníti az elnevezési házirend kényszerített nevét, ha a felhasználó egy csoportnév vagy egy csoport aliasnevét adja meg. Amikor egy felhasználó egyéni letiltott szót ad meg, hibaüzenet jelenik meg a felhasználói felületen a blokkolt Szóval együtt, hogy a felhasználó el tudja távolítani.
Outlook asztal | Az Outlook Desktopban létrehozott csoportok megfelelnek a névadási házirend beállításainak. Az Outlook asztali alkalmazás még nem jeleníti meg a kényszerített csoport nevét, és nem adja vissza az egyéni blokkolt Word-hibákat, amikor a felhasználó megadja a csoport nevét. Az elnevezési házirendet azonban automatikusan alkalmazza a rendszer a csoportok létrehozásakor vagy szerkesztésekor, a felhasználók pedig hibaüzeneteket látnak, ha a csoport neve vagy aliasa egyéni letiltott szavakat tartalmaz.
Microsoft Teams | A Microsoft Teams a csoport elnevezési szabályzatának kikényszerített nevét jeleníti meg, amikor a felhasználó beírja a csapat nevét. Ha egy felhasználó egyéni letiltott szót ad meg, hibaüzenet jelenik meg a letiltott Szóval együtt, hogy a felhasználó el tudja távolítani.
SharePoint  |  A SharePoint megjeleníti az elnevezési házirend kényszerített nevét, ha a felhasználó a hely nevét vagy a csoport e-mail-címét adja meg. Ha egy felhasználó egy egyéni letiltott szót ad meg, hibaüzenet jelenik meg a letiltott Szóval együtt, hogy a felhasználó el tudja távolítani.
Microsoft Stream | Microsoft Stream megjeleníti a csoport elnevezési szabályzatának kikényszerített nevét, ha a felhasználó a csoport nevét vagy a csoport e-mail aliasát adja meg. Ha egy felhasználó egyéni letiltott szót ad meg, a rendszer hibaüzenetet jelenít meg a letiltott Szóval, így a felhasználó el tudja távolítani.
Outlook iOS-és Android-alkalmazás | Az Outlook-alkalmazásokban létrehozott csoportok megfelelnek a konfigurált elnevezési házirendnek. Az Outlook Mobile alkalmazás még nem jeleníti meg az elnevezési szabályzat által kényszerített név előnézetét, és nem ad vissza egyéni blokkolt Word-hibákat, amikor a felhasználó belép a csoport nevébe. Az elnevezési házirendet azonban automatikusan alkalmazza a létrehozás/szerkesztés és a felhasználók a hibaüzenetek megjelenítéséhez, ha a csoport neve vagy aliasa egyéni letiltott szavakat tartalmaz.
A Mobile App csoportok | A groups Mobile alkalmazásban létrehozott csoportok megfelelnek az elnevezési házirendnek. A groups Mobile App nem jeleníti meg az elnevezési házirend előnézetét, és nem ad vissza egyéni blokkolt Word-hibákat, amikor a felhasználó belép a csoport nevére. Az elnevezési házirendet azonban automatikusan alkalmazza a rendszer a csoportok létrehozásakor vagy szerkesztésekor, és a felhasználók a megfelelő hibákkal jelennek meg, ha a csoport neve vagy aliasa egyéni tiltott szavakat tartalmaz.
Planner | A Planner megfelel az elnevezési házirendnek. A Planner megjeleníti a névadási házirend előzetes verzióját a csomag nevének megadásakor. Ha egy felhasználó egyéni letiltott szót ad meg, a terv létrehozásakor hibaüzenet jelenik meg.
Dynamics 365 for Customer Engagement | A Dynamics 365 for Customer engagement megfelel az elnevezési házirendnek. A Dynamics 365 megjeleníti az elnevezési szabályzatot, amely akkor lép érvénybe, ha a felhasználó a csoport nevét vagy a csoport e-mail aliasát adja meg. Ha a felhasználó egy egyéni letiltott szót ad meg, hibaüzenet jelenik meg a letiltott Szóval, így a felhasználó el tudja távolítani.
School-adatszinkronizálás (SDS) | Az SDS használatával létrehozott csoportok megtartják az elnevezési házirendet, de az elnevezési házirend nincs automatikusan alkalmazva. Az SDS-rendszergazdáknak hozzá kell fűzni az előtagokat és az utótagokat azokhoz a csoportokhoz, amelyekhez létre kell hozni, majd fel kell tölteni őket az SDS-be. A csoport létrehozása vagy szerkesztése másként nem fog működni.
Tantermi alkalmazás | Az osztályterem alkalmazásban létrehozott csoportok megfelelnek az elnevezési házirendnek, de az elnevezési házirend nem lesz automatikusan alkalmazva, és az elnevezési szabályzat előzetes verziója nem jelenik meg a felhasználók számára az osztályterem csoport nevének megadásakor. A felhasználóknak meg kell adniuk a kényszerített osztályterem csoport nevét előtagokkal és utótagokkal. Ha nem, az osztályterem csoport létrehozás vagy szerkesztés művelete hibákkal meghiúsul.
Power BI | Power BI munkaterületek megfelelnek az elnevezési házirendnek.    
Yammer | Amikor egy felhasználó a Yammer-be bejelentkezett a Azure Active Directory-fiókjával, létrehoz egy csoportot, vagy szerkeszti a csoport nevét, a csoportnév megfelel az elnevezési házirendnek. Ez a Microsoft 365 csatlakoztatott csoportokra és az összes többi Yammer is vonatkozik.<br>Ha az elnevezési házirend betartását megelőzően egy Microsoft 365 csatlakoztatott csoport lett létrehozva, akkor a csoport neve nem követi automatikusan a névadási házirendeket. Amikor egy felhasználó szerkeszti a csoport nevét, a rendszer kérni fogja, hogy adja hozzá az előtagot és utótagot.
StaffHub  | A StaffHub csapatok nem követik az elnevezési házirendet, de az alapul szolgáló Microsoft 365 csoport nem. A StaffHub-csoport neve nem alkalmazza az előtagokat és az utótagokat, és nem keres egyéni tiltott szavakat. A StaffHub azonban alkalmazza az előtagokat és az utótagokat, és eltávolítja a letiltott szavakat az alapul szolgáló Microsoft 365 csoportból.
Exchange PowerShell | Az Exchange PowerShell-parancsmagok megfelelnek az elnevezési házirendnek. A felhasználók a javasolt előtagokkal és utótagokkal, illetve az egyéni blokkolt szavak esetén kapják meg a megfelelő hibaüzeneteket, ha nem követik az elnevezési házirendet a csoport neve és a csoport aliasában (mailNickname).
PowerShell-parancsmagok Azure Active Directory | Azure Active Directory PowerShell-parancsmagok megfelelnek az elnevezési házirendnek. A felhasználók a javasolt előtagokkal és utótagokkal, illetve az egyéni blokkolt szavak esetén kapják meg a megfelelő hibaüzeneteket, ha nem követik az elnevezési konvenciót a csoportok neveiben és a csoport aliasnevében.
Exchange felügyeleti központ | Az Exchange felügyeleti központ megfelel az elnevezési házirendnek. A felhasználók a javasolt előtagokkal és utótagokkal, illetve az egyéni blokkolt szavak esetén kapják meg a megfelelő hibaüzeneteket, ha nem követik az elnevezési konvenciót a csoport neve és a csoport aliasában.
Microsoft 365 felügyeleti központ | Microsoft 365 felügyeleti központ megfelel az elnevezési házirendnek. Amikor egy felhasználó létrehoz vagy szerkeszt egy csoportnevet, a rendszer automatikusan alkalmazza az elnevezési házirendet, és a felhasználók a megfelelő hibákat kapják meg, amikor egyéni tiltott szavakat hoznak létre. A Microsoft 365 felügyeleti központ még nem jeleníti meg az elnevezési házirend előnézetét, és nem ad vissza egyéni blokkolt Word-hibákat, amikor a felhasználó belép a csoport nevére.

## <a name="next-steps"></a>Következő lépések

Ezek a cikkek további információkat nyújtanak az Azure AD-csoportokról.

- [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Microsoft 365 csoportok elévülési szabályzata](groups-lifecycle.md)
- [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Csoporttagok kezelése](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)

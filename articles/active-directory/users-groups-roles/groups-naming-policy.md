---
title: Az Office 365-csoportok – Azure Active Directory csoportelnevezési házirend kényszerítése |} A Microsoft Docs
description: Office 365-csoportok az Azure Active Directoryban elnevezési szabályzat beállítása
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 05/06/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c13b95028975c5463217455c940bb84c3867899
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734783"
---
# <a name="enforce-a-naming-policy-on-office-365-groups-in-azure-active-directory"></a>Az Office 365-csoportok az Azure Active Directoryban elnevezési szabályzat kényszerítése

Az Office 365-csoportokat a felhasználók által létrehozott vagy módosított egységes elnevezési konvenciók kényszerítéséhez állítsa be a csoportelnevezési házirend a bérlők számára az Azure Active Directoryban (Azure AD). Például használhatja a kiosztási szabályzat való kommunikációra, a függvény egy csoport, a tagsági, a földrajzi régióban, vagy ki hozta létre a csoportot. A kiosztási szabályzat kategorizálása a címjegyzékben csoportok segítségével is használhat. A házirend segítségével adott szó azoktól van használatban a csoporthoz tartozó nevek és aliasok letiltása.

> [!IMPORTANT]
> Azure AD-kiosztási szabályzat használata az Office 365-csoportok igényel, hogy rendelkezik, de nem feltétlenül hozzárendelése egy Azure Active Directory Premium P1 licenc- vagy Azure AD alapszintű EDU licencet mindegyik egyedi felhasználói, amely legalább egy Office 365-csoportok tagjai.

A kiosztási szabályzat létrehozásakor és szerkesztésekor számítási feladatokhoz (például az Outlook, a Microsoft Teams, SharePoint, Exchange vagy Planner) létrehozott csoportok vonatkozik. Ez a csoport nevét és a csoport aliasa is érvényes. Az Azure ad-ben a kiosztási szabályzat beállításához, és egy meglévő Exchange-csoportelnevezési házirend, ha a szervezet az Azure AD-elnevezési szabályzatában foglalt lép érvénybe.

## <a name="naming-policy-features"></a>Kiosztási szabályzat funkciói

Két különböző módon kényszerítheti a csoportokra vonatkozó elnevezési szabályzat:

- **Előtag-utótag csoportelnevezési házirend** meghatározhatja az előtagok vagy utótagok, majd automatikusan hozzáadott kényszeríteni a csoportok elnevezési (például a csoport nevét a "csoport\_japán\_saját csoport\_ Engineering", csoport\_japán\_ előtagja, és \_mérnöki pedig utótag). 

- **Egyéni letiltott szavakat** letiltott szavakat meghatározott készletét feltöltheti a szervezet számára, a felhasználók (például "CEO, Bérszámfejtés, HR") által létrehozott csoportok letiltása.

### <a name="prefix-suffix-naming-policy"></a>Előtag-utótag csoportelnevezési házirend

Általános elnevezési struktúráját "Előtag [GroupName] utótag". Meghatározhatja, hogy több előtagokat és utótagot, miközben csak rendelkezhet egy példányát a [GroupName] beállításban. Az előtagok vagy utótagok lehet rögzített karakterláncok vagy a felhasználói attribútumok például \[részleg\] , amely alapján a felhasználó, aki a csoport létrehozása helyettesítik. A kombinált előtagokkal és utótagokkal karakterláncok karakter megengedett száma 53 karakter. 

Előtagok és utótagok tartalmazhat speciális karaktereket, amelyek támogatottak a csoport nevét és a csoport aliasa. Az összes karaktert a-előtag vagy utótag által nem támogatott a csoport aliasa továbbra is a csoport nevét a alkalmazni, de a csoport aliasa távolítva. A korlátozás miatt az előtagok és utótagokat a alkalmazni a csoport neve eltérő lehet a alkalmazni a csoport aliasa állók közül. 

#### <a name="fixed-strings"></a>Rögzített karakterláncok

Karakterláncok könnyebb vizsgálata és a globális címlista és a bal oldali navigációs hivatkozások csoport számítási feladatok a csoportok megkülönböztetésére használható. A közös előtagok néhány kulcsszavakra, mint "csoport\_neve", "\#neve","\_neve"

#### <a name="user-attributes"></a>Felhasználói attribútumok

Attribútumok, amelyek segítségével is használhatja, és a felhasználók azonosítására, melyik részleg, az office vagy a földrajzi régióban, amely a csoport létrehozásának. Például, ha a csoportelnevezési házirend, megadhat `PrefixSuffixNamingRequirement = "GRP [GroupName] [Department]"`, és `User’s department = Engineering`, akkor lehet, hogy az érvényben levő csoport neve "Csoport saját csoport mérnöki." Támogatott az Azure AD-attribútumok vannak \[részleg\], \[vállalati\], \[Office\], \[StateOrProvince\], \[CountryOrRegion \], \[Cím\]. Nem támogatott felhasználói attribútumok rögzített karakterláncként kell kezelni. például "\[postalCode\]". A bővítményattribútumok és egyéni attribútumok nem támogatottak.

Azt javasoljuk, hogy használja-e az attribútumokat, amelyek kitölti a rendszer az összes felhasználó számára a cégnél értékeket, és ne használja az attribútumokat, amelyek hosszú értékeket.

### <a name="custom-blocked-words"></a>Egyéni letiltott szavakat

Letiltott szó listáját az kifejezéseket a csoporthoz tartozó nevek és aliasok egytől vesszővel tagolt listája. Nincs karakterláncrészletet keresések történik. A csoport nevét, és a egy vagy több egyéni letiltott szó között pontos egyezés szükséges aktiválása sikertelen. Részleges karakterlánc-keresés nem történik meg, hogy a felhasználók használhatják a gyakori szavakat, például a "Class", akkor is, ha "osztály" tiltott szót.

Letiltott szó lista szabályok:

- Letiltott szavakat nagybetűk nem számítanak.
- Amikor a felhasználó egy letiltott szó egy csoport nevének részeként, megjelenik egy hibaüzenet jelenik meg a letiltott szó.
- Nincsenek letiltott szavakat a karakter korlátozások.
- Nincs konfigurálható a letiltott szavakat listában 5000 kifejezések közül egy felső korlátot. 

### <a name="administrator-override"></a>Rendszergazdai felülbírálása

Kijelölt rendszergazdák kivonhatók a ezek a házirendek összes csoport számítási feladatok és végpontok, úgy, hogy miképpen hozhatnak létre csoportokat a letiltott szavakat használ, és a saját elnevezési konvenciókat. Az alábbiakban a csoportelnevezési házirend alól rendszergazdai szerepkörök listája.

- Globális rendszergazda
- 1\. szintű Partnertámogatás
- 2\. szintű Partnertámogatás
- Felhasználói adminisztrátor
- Címtárírók

## <a name="configure-naming-policy-in-azure-portal"></a>Kiosztási szabályzat konfigurálása az Azure Portalon

1. Jelentkezzen be a [Azure AD felügyeleti központ](https://aad.portal.azure.com) rendszergazdai felhasználói fiókkal.
1. Válassza ki **csoportok**, majd **elnevezési szabályzatában foglalt** elnevezési szabályzat lapjának megnyitásához.

    ![Nyissa meg az elnevezési szabályzat oldalt a felügyeleti központ](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Zobrazí nebo upraví az előtag-utótag csoportelnevezési házirend

1. Az a **elnevezési szabályzatában foglalt** lapon jelölje be **elnevezési csoportházirend**.
1. Megtekintheti vagy szerkesztheti a jelenlegi előtag vagy utótag házirendek külön-külön elnevezési az attribútumok vagy karakterláncok kényszeríteni a kiosztási szabályzat részeként szeretné kiválasztásával.
1. Elő- vagy utótag eltávolítása a listából, válassza ki a előtag vagy utótag, majd jelölje ki **törlése**. Egyszerre több elem is lehet törölni.
1. Mentse a módosításokat az új házirend érvénybe kiválasztásával megnyithatja az **mentése**.

### <a name="edit-custom-blocked-words"></a>Egyéni letiltott szavakat szerkesztése

1. Az a **elnevezési szabályzatában foglalt** lapon jelölje be **letiltott szavakat**.

    ![szerkesztése és feltöltése az elnevezési szabályzatában foglalt letiltott szavakat listázása](./media/groups-naming-policy/blockedwords.png)

1. Zobrazí nebo upraví egyéni letiltott szavakat aktuális listájának kiválasztásával **letöltése**.
1. A fájl ikonra kattintva töltse fel az új egyéni letiltott szavak listáját.
1. Mentse a módosításokat az új házirend érvénybe kiválasztásával megnyithatja az **mentése**.

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

   Ha egy nem megbízható adattár elérése kéri, adja meg a **Y**. Az új modul telepítése igénybe vehet néhány percet.

## <a name="configure-naming-policy-in-powershell"></a>A PowerShellben elnevezési szabályzat konfigurálása

1. Nyisson meg egy Windows PowerShell-ablakot a számítógépen. Azt is nyissa meg emelt szintű jogosultságok nélkül.

1. Futtassa a következő parancsokat a parancsmagok futtatásának előkészítéséhez.
  
   ``` PowerShell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   A megjelenő **Bejelentkezés a fiókba** párbeszédpanelen adja meg a rendszergazdai fiókot és jelszót, hogy kapcsolatot létesítsen a szolgáltatással, majd válassza a **Bejelentkezés** lehetőséget.

1. Kövesse az [Azure Active Directory-parancsmagok a csoportbeállítások konfigurálásához](groups-settings-cmdlets.md) című rész lépéseit a bérlő csoportbeállításainak létrehozásához.

### <a name="view-the-current-settings"></a>Az aktuális beállítások megtekintése

1. Beolvassa az aktuális csoportelnevezési házirend, a jelenlegi beállítások megtekintéséhez.
  
   ``` PowerShell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
1. Jelenítse meg az aktuális csoportbeállításokat.
  
   ``` PowerShell
   $Setting.Values
   ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Adja meg a csoportelnevezési házirend és egyéni letiltott szavakat

1. Állítsa be a csoportnév előtagjait és utótagjait az Azure AD PowerShellben. A funkció megfelelő működéséhez [GroupName] szerepelnie kell a beállítást.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
1. Állítsa be az egyéni letiltott szavakat, amelyek használatát korlátozni szeretné. A következő példa szemlélteti, hogyan adhatja hozzá saját egyéni szavait.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
1. Mentse a beállításokat az új házirend a lép érvénybe, mint például az alábbi példában.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
Ennyi az egész. Hogy a kiosztási szabályzat beállítása és a letiltott szavakat hozzáadva.

## <a name="export-or-import-custom-blocked-words"></a>Exportál vagy importál egyéni letiltott szavakat

További információkért tekintse meg a cikket [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](groups-settings-cmdlets.md).

Íme egy példa a PowerShell-parancsfájl exportálása letiltott szavakat több:

``` PowerShell
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
```

Íme egy példa a PowerShell-parancsprogram több letiltott szó importálása:

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

## <a name="remove-the-naming-policy"></a>A kiosztási szabályzat eltávolítása

### <a name="remove-the-naming-policy-using-azure-portal"></a>Távolítsa el a csoportelnevezési házirend, az Azure portal használatával

1. Az a **elnevezési szabályzatában foglalt** lapon jelölje be **házirend törlése**.
1. Miután meggyőződött a törlés, a kiosztási szabályzat törlődik a, beleértve az összes előtag-utótag elnevezési házirend és egyéni letiltott szavakat.

### <a name="remove-the-naming-policy-using-azure-ad-powershell"></a>Távolítsa el a kiosztási szabályzat Azure AD PowerShell-lel

1. A csoport előtagok és az Azure AD PowerShell utótagok üres.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```
  
1. Az egyéni letiltott szavakat üres.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=""
   ```
  
1. A beállítások mentéséhez.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="experience-across-office-365-apps"></a>Office 365-alkalmazások élmény

Miután beállította a csoportelnevezési házirend az Azure ad-ben, amikor egy felhasználó létrehoz egy csoportot az Office 365 alkalmazásokban láthatják:

- A előzetes verziója a nevét (a előtagot és utótagot), a csoportelnevezési házirend szerint, amint a felhasználó begépeli a csoport neve
- Ha a felhasználó sikeresen megadja a letiltott szavakat, megjelennek egy hibaüzenet, eltávolíthatják a letiltott szavakat.

Számítási feladat | Megfelelőség
----------- | -------------------------------
Az Azure Active Directory portálok | Az Azure ad-ben és a hozzáférési Panel portálhoz az elnevezési szabályzat érvénybe léptetését nevének megjelenítése, amikor a felhasználó egy csoportnevet létrehozása vagy egy csoport szerkesztése során. Amikor a felhasználó egy egyéni letiltott szó, egy hibaüzenet jelenik meg a letiltott szó jelenik meg, hogy a felhasználó el is távolíthatja.
Outlook Web Access (OWA) | Az Outlook Web Access jeleníti meg, a csoportelnevezési házirend kényszerítve a neve, amikor a felhasználó egy csoport neve vagy a csoport aliasa. Amikor egy felhasználó egyéni tiltott szót, egy hibaüzenet jelenik meg a felhasználói felület és a letiltott szó, hogy a felhasználó távolíthatja el.
Az asztali Outlook | Az asztali Outlook létrehozott csoportok megfeleljenek a kiosztási házirend-beállításokat. Asztali Outlook alkalmazás még nem jeleníti meg az előzetes verziója az érvényben levő csoport nevét, és a letiltott szó egyéni hibákat nem ad vissza, ha a felhasználó megadja a csoport nevét. Azonban a rendszer automatikusan alkalmazza a kiosztási szabályzat létrehozásakor vagy szerkesztésekor egy csoportot, és a felhasználók hibaüzeneteket látják, van-e a csoport nevét vagy aliasát egyéni letiltott szavakat.
Microsoft Teams | Microsoft Teams, a csoportelnevezési kényszerített házirend neve, amikor a felhasználó a csoport nevét jeleníti meg. Amikor a felhasználó egy egyéni letiltott szó, egy hibaüzenet jelenik meg együtt a letiltott szó, hogy a felhasználó el is távolíthatja.
SharePoint  |  A SharePoint az elnevezési szabályzat érvénybe léptetését neve jelenik meg, amikor a felhasználó begépeli a hely neve, vagy a csoport e-mail-cím. Amikor egy felhasználó ír be egy egyéni letiltott szó, egy hibaüzenet jelenik meg, és a letiltott szó, hogy a felhasználó távolíthatja el.
Microsoft Stream | Microsoft Stream a csoportelnevezési kényszerített házirend neve, amikor a felhasználó egy csoport nevét vagy e-mail-alias jeleníti meg. Amikor egy felhasználó egyéni tiltott szót, egy hibaüzenet mellett látható a letiltott szó, a felhasználó távolíthatja el.
Az Outlook iOS és Android-alkalmazás | Az Outlook alkalmazásokat létrehozott csoportok megfelelnek a konfigurált kiosztási szabályzattal. Az Outlook mobilalkalmazás előzetes verziójának a kiosztási szabályzat érvénybe léptetését neve még nem jeleníti meg, és egyéni letiltott szó hibák nem ad vissza, ha a felhasználó megadja a csoport nevét. Azonban a rendszer automatikusan alkalmazza a csoportelnevezési házirend gombra való kattintás után létrehozása/szerkesztése, és a felhasználók hibaüzeneteket látják, van-e a csoport nevét vagy aliasát egyéni letiltott szavakat.
Csoportok mobilalkalmazás | A csoportok mobilalkalmazásban létrehozott csoportok megfeleljenek az elnevezési szabályzat. Csoportok mobilalkalmazás előzetes verziójának a kiosztási szabályzat nem jelenik meg, és nem ad vissza egyéni letiltott szó hibákat, amikor a felhasználó megadja a csoport nevét. De rendszer automatikusan alkalmazza a kiosztási szabályzat létrehozásakor vagy szerkesztésekor a csoport, és felhasználók egyike megfelelő hibák van-e a csoport nevét vagy aliasát egyéni letiltott szavakat.
Planner | Planner a szabályzatnak megfelelő elnevezési. A csomagnév megadása Planner mutatja az elnevezési policy előzetes verziójára. Amikor a felhasználó egy egyéni letiltott szó, egy hibaüzenet jelenik meg a csomag létrehozásakor.
Dynamics 365 for Customer Engagement | Dynamics 365 for Customer Engagement a szabályzatnak megfelelő elnevezési. Dynamics 365 a kiosztási szabályzat érvénybe léptetését neve jelenik meg, amikor a felhasználó egy csoport nevét vagy e-mail-alias. Amikor a felhasználó egy egyéni letiltott szó, egy hibaüzenet mellett látható a letiltott szó, a felhasználó távolíthatja el.
School Data Sync (SDS) | Az SDS segítségével létrehozott csoportok megfelelnek a kiosztási szabályzat, de a kiosztási szabályzat automatikusan nem alkalmazza. SDS rendszergazdák rendelkeznek az előtagok és utótagok hozzáfűzni kívánt osztály nevét, amelynek csoportokat kell létrehozni és SDS majd feltölteni. Csoport létrehozása vagy szerkesztése ellenkező esetben sikertelen lesz.
Az Outlook Customer Managerbe (OCM) | Az Outlook Customer Managerbe a szabályzatnak megfelelő elnevezési, amely a rendszer automatikusan alkalmazza az Outlook Customer Manager létrehozta a csoportot. Ha egyéni tiltott szót észlel, csoport létrehozása a OCM le van tiltva, és a felhasználó blokkolva van a OCM alkalmazással.
Az osztályterem alkalmazás | Az osztályterem alkalmazással létrehozott csoportok megfelelnek a csoportelnevezési házirend, de a kiosztási szabályzat nem alkalmazza automatikusan, és az elnevezési policy előzetes verziójára nem jelenik meg a felhasználók számára a tanterem csoportnév beírása közben. A kényszerített osztályterem csoportnév előtagot és utótagot kell megadni. Ha nem, akkor az osztályterem-csoport létrehozása vagy szerkesztése a művelet sikertelen lesz, és a hibák.
Power BI | A Power BI-munkaterületek megfeleljenek az elnevezési szabályzat.    
Yammer | Amikor egy felhasználó bejelentkezett az Azure Active Directory-fiókját a Yammer egy csoportot hoz létre, vagy szerkeszti a csoport nevét, a csoport nevének köteles betartani elnevezési szabályzatában foglalt. Ez vonatkozik a csatlakoztatott Office 365-csoportokat és más Yammer-csoportok.<br>Ha egy Office 365-höz csatlakoztatott csoport lett létrehozva, mielőtt a csoportelnevezési házirend van beállítva, a csoport neve nem automatikusan követi az elnevezési szabályait. Amikor egy felhasználó szerkeszti a csoport nevét, kéri, adja hozzá az előtagot és utótagot.
StaffHub  | StaffHub-csapatok ne hajtsa végre a csoportelnevezési házirend, azonban az alapul szolgáló Office 365-csoport nem lehetséges. StaffHub Csapatnév nem vonatkozik az előtagok és utótagot, és nem ellenőrzi az egyéni letiltott szavakat. De StaffHub nem alkalmazza az előtagok és utótagok és a letiltott szavakat távolít el az alapul szolgáló Office 365-csoportot.
Exchange PowerShell | Exchange PowerShell-parancsmagok megfeleljenek az elnevezési szabályzat. A felhasználók megfelelő hibaüzeneteket a javasolt előtag- és utótagok és a letiltott szavakat egyéni kapnak, ha azok nem követi a csoportelnevezési házirend, a csoport nevét és a csoport aliasa (mailNickname).
Az Azure Active Directory PowerShell-parancsmagok | Az Azure Active Directory PowerShell-parancsmagok felelnek meg a kiosztási szabályzat. A felhasználók megfelelő hibaüzeneteket a javasolt előtag- és utótagok és a letiltott szavakat egyéni kapnak, ha azok nem követi az elnevezési szabályt követik a csoport nevét és a csoport aliasa.
Az Exchange felügyeleti központban | Az Exchange felügyeleti központban az elnevezési szabályzatában foglalt felelnek. A felhasználók megfelelő hibaüzeneteket a javasolt előtag- és utótagok és a letiltott szavakat egyéni kapnak, ha azok nem követi az elnevezési szabályt követik a csoport nevét és a csoport aliasa.
Microsoft 365 admin center | A Microsoft 365 felügyeleti központ az elnevezési szabályzatában foglalt felelnek. Amikor egy felhasználó hoz létre vagy módosításokat a csoportneveket, a csoportelnevezési házirend a rendszer automatikusan alkalmazza és felhasználók megfelelő hibák lépnek fel, amikor belép az egyéni letiltott szavakat. A Microsoft 365 felügyeleti központ előzetes verziója a kiosztási szabályzat még nem jeleníti meg, és egyéni letiltott szó hibák nem ad vissza, ha a felhasználó megadja a csoport nevét.

## <a name="next-steps"></a>További lépések

E cikkekben további információk az Azure AD-csoportokat.

- [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Elévülési szabályzatot, az Office 365-csoportok](groups-lifecycle.md)
- [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Csoporttagok kezelése](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)

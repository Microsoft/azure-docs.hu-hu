---
title: Nem felügyeleti címtár rendszergazdai feladatátvétele – Azure AD-| Microsoft Docs
description: DNS-tartománynév átveszése egy nem nem azure AD-szervezetben (árnyékbérlőben).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 04/18/2021
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 816f4645626675ae19a462ac8707e995c3b4045e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739367"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Nem felügyelt címtár átvétele rendszergazdaként az Azure Active Directoryban

Ez a cikk két módszert ismertet a DNS-tartománynév egy nem felügyeleti címtárban való Azure Active Directory (Azure AD). Amikor egy önkiszolgáló felhasználó regisztrál egy Azure AD-t használó felhőszolgáltatásra, a rendszer az e-mail-címe alapján hozzáadja egy nem felügyelt Azure AD-címtárhoz. A szolgáltatások önkiszolgáló vagy "felfelé való" regisztrációval kapcsolatos további információkért lásd: Mi az önkiszolgáló regisztráció a [Azure Active Directory?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Döntse el, hogyan szeretné át venni egy nem felügyeleti könyvtárat
A rendszergazdai átvétel során az [Egyéni tartománynév hozzáadása az Azure Active Directoryhoz](../fundamentals/add-custom-domain.md) című cikkben leírt módon bizonyíthatja tulajdonjogát. A következő szakaszok részletesen ismertetik a rendszergazdai feladatokat, amelyek összegzése itt olvasható:

* Ha [„belső” rendszergazdai átvételt](#internal-admin-takeover) végez egy nem felügyelt Azure-címtáron, a rendszer hozzáadja Önt a nem felügyelt címtár globális rendszergazdájaként. Felhasználók, tartományok és szolgáltatáscsomagok nem kerülnek át Ön által felügyelt további címtárakba.

* Ha [„külső” rendszergazdai átvételt](#external-admin-takeover) végez egy nem felügyelt Azure-címtáron, hozzáadja a nem felügyelt címtár DNS-tartománynevét az Ön által felügyelt Azure-címtárhoz. A tartománynév hozzáadásakor az Ön által felügyelt Azure-címtárban létrejön a felhasználók és erőforrások közötti kapcsolatok leképezése, így a felhasználók továbbra is megszakítás nélkül érhetik el a szolgáltatásokat. 

## <a name="internal-admin-takeover"></a>Belső rendszergazdai átvétel

A SharePointot és a OneDrive-ot is magukban foglaló termékek, például a Microsoft 365, nem támogatják a külső feladatátvételt. Ha Ön ezt a forgatókönyvet használja, vagy ha Ön rendszergazda, és át szeretné venni az Önkiszolgáló regisztrációt használó felhasználók által létrehozott nem felügyelet vagy "árnyék" Azure AD-szervezetet, ezt belső rendszergazdai feladatátvételsel is meg tudja tenni.

1. Hozzon létre egy felhasználói környezetet a nem Power BI. Az egyszerűség kedvéért ezek a lépések ezt az útvonalat feltételezik.

2. Nyissa meg [a Power BI webhelyet,](https://powerbi.com) és válassza a **Start Free (Ingyenes kezdés) lehetőséget.** Adjon meg egy felhasználói fiókot, amely a szervezet tartománynevét használja; például: `admin@fourthcoffee.xyz` . Miután beírja az ellenőrző kódot, ellenőrizze az e-mailben a visszaigazoló kódot.

3. A megerősítést kérő e-Power BI válassza az **Igen, ez vagyok én** lehetőséget.

4. Jelentkezzen be a [Microsoft 365 Felügyeleti központ](https://portal.office.com/admintakeover) a Power BI felhasználói fiókkal. Megjelenik egy üzenet, amely  arra utasítja, hogy legyen a nem hitelesített szervezetben már ellenőrzött tartománynév rendszergazdája. Válassza **az Igen, szeretnék rendszergazda lenni lehetőséget.**
  
   ![az első képernyőkép a Become the Admin (Adminisztrátorsá válni) képernyőről](./media/domains-admin-takeover/become-admin-first.png)
  
5. Adja hozzá a TXT rekordot annak bizonyítására, hogy Ön a tartománynév fourthcoffee.xyz **a** tartománynév-regisztrálónál. Ebben a példában ez GoDaddy.com.
  
   ![Txt-rekord hozzáadása a tartománynévhez](./media/domains-admin-takeover/become-admin-txt-record.png)

Ha a DNS TXT-rekordokat a tartománynév-regisztráló ellenőrzi, kezelheti az Azure AD-szervezetet.

Az előző lépések befejezése után Ön lesz a negyedik kávészervezet globális rendszergazdája a Microsoft 365. A tartománynév más Azure-szolgáltatásokkal való integrálásához eltávolíthatja azt a Microsoft 365, és hozzáadhatja egy másik felügyelt szervezethez az Azure-ban.

### <a name="adding-the-domain-name-to-a-managed-organization-in-azure-ad"></a>Tartománynév hozzáadása egy felügyelt szervezethez az Azure AD-ban

1. Nyissa meg [a Microsoft 365 Felügyeleti központ.](https://admin.microsoft.com)
2. Válassza **a Felhasználók** lapot, és hozzon létre egy új felhasználói fiókot olyan névvel, fourthcoffeexyz.onmicrosoft.com az egyéni tartománynevet nem használja. *\@* 
3. Győződjön meg arról, hogy az új felhasználói fiók globális rendszergazdai jogosultságokkal rendelkezik az Azure AD-szervezet számára.
4. Nyissa **meg a** Tartományok lapot a Microsoft 365 Felügyeleti központ, válassza ki a tartománynevet, majd válassza az Eltávolítás **lehetőséget.** 
  
   ![Távolítsa el a tartománynevet a Microsoft 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Ha vannak olyan felhasználók vagy csoportok a Microsoft 365 amelyek az eltávolított tartománynévre hivatkoznak, át kell nevezni őket a .onmicrosoft.com tartományra. Ha kényszeríti a tartománynév törlését, a rendszer minden felhasználót automatikusan átnevez, ebben a példában a következőre: *\@ fourthcoffeexyz.onmicrosoft.com.*
  
6. Jelentkezzen be az [Azure AD felügyeleti központba](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) egy olyan fiókkal, amely az Azure AD-szervezet globális rendszergazdája.
  
7. Válassza **az Egyéni tartománynevek lehetőséget,** majd adja hozzá a tartománynevet. A tartománynév tulajdonjogának ellenőrzéséhez meg kell adnia a DNS TXT-rekordokat. 
  
   ![az Azure AD-hez hozzáadottként ellenőrzött tartomány](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> A Power BI vagy Azure Rights Management-szolgáltatás minden olyan felhasználójának, aki a Microsoft 365-szervezethez hozzárendelt licencekkel rendelkezik, mentenie kell az irányítópultokat, ha a tartománynevet eltávolítják. A felhasználónevet (például felhasználónevet) kell fourthcoffeexyz.onmicrosoft.com a *fourthcoffee.xyz. \@* *\@*

## <a name="external-admin-takeover"></a>Külső rendszergazdai átvétel

Ha már rendelkezik Azure-szolgáltatásokkal vagy -fiókokkal egy szervezet Microsoft 365, nem adhat hozzá egyéni tartománynevet, ha az már ellenőrizve van egy másik Azure AD-szervezetben. Az Azure AD-beli felügyelt szervezetből azonban külső rendszergazdai feladatátvételként átveheti a nem felügyelt szervezetet. Az általános eljárás az Egyéni tartomány hozzáadása [az Azure AD-hez cikket követi.](../fundamentals/add-custom-domain.md)

Amikor ellenőrzi a tartománynév tulajdonjogát, az Azure AD eltávolítja a tartománynevet a nem birtokolt szervezetből, és áthelyezi azt a meglévő szervezetbe. A nem felügyeleti címtár külső rendszergazdai feladatátvételéhez ugyanaz a DNS TXT érvényesítési folyamat szükséges, mint a belső rendszergazdai feladatátvételhez. A különbség az, hogy a következőt is áthelyezi a tartománynévvel:

- Felhasználók
- Előfizetések
- Licenc-hozzárendelések

### <a name="support-for-external-admin-takeover"></a>Külső rendszergazdai feladatátvétel támogatása
A külső rendszergazdai feladatátvételt a következő lépések online szolgáltatások:

- Azure Rights Management
- Exchange Online

A támogatott szolgáltatás csomagok a következők:

- Ingyenes PowerApps
- Ingyenes PowerFlow
- RMS egyéni felhasználók számára
- Microsoft Stream
- Dynamics 365 – ingyenes próbaverzió

A külső rendszergazdai feladatátvétel nem támogatott a SharePointot, a OneDrive-ot vagy a Skype Vállalati verziót is magában foglaló szolgáltatás-csomagokkal; például egy ingyenes Office-előfizetésen keresztül. 

Igény szerint a [ **ForceTakeover**](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) beállítással eltávolíthatja a tartománynevet a nem használt szervezetből, és ellenőrizheti azt a kívánt szervezeten. 

#### <a name="more-information-about-rms-for-individuals"></a>További információ az RMS egyéni személyek számára szolgáltatásról

Ha az [RMS egyéni](/azure/information-protection/rms-for-individuals)felhasználó számára szolgáltatás ugyanabban a régióban található, mint a tulajdonában található szervezet, [](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) az automatikusan létrehozott Azure Information Protection szervezeti kulcs és alapértelmezett védelmi sablonok is átkerülnek [a](/azure/information-protection/plan-implement-tenant-key) tartománynévre.

A kulcsot és a sablonokat nem lehet áthelyezni, ha a nem felügyeleti szervezet egy másik régióban van. Ha például a nem egy szervezet Európában van, a tulajdonában pedig a vállalat, amely a Észak-Amerika.

Bár az RMS egyéni felhasználók számára szolgáltatás úgy lett kialakítva, hogy támogassa az Azure AD-hitelesítést a védett tartalmak megnyitásához, nem akadályozza meg, hogy a felhasználók a tartalmakat is védjék. Ha a felhasználók az RMS egyéni felhasználók számára előfizetéssel védték a tartalmat, és a kulcsot és a sablonokat nem helyezték át, akkor a tartalom a tartomány átvédése után nem lesz elérhető.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Azure AD PowerShell-parancsmagok a ForceTakeover beállításhoz
Ezeket a parancsmagokat a [PowerShell-példában használjuk.](#powershell-example)

Parancsmag | Használat
------- | -------
`connect-msolservice` | Amikor a rendszer kéri, jelentkezzen be a felügyelt szervezetbe.
`get-msoldomain` | Megjeleníti az aktuális szervezethez társított tartományneveket.
`new-msoldomain –name <domainname>` | Nem ellenőrzöttként hozzáadja a tartománynevet a szervezethez (dns-ellenőrzés még nem történt meg).
`get-msoldomain` | A tartománynév most már szerepel a felügyelt szervezethez társított tartománynevek listájában, de nem ellenőrzöttként szerepel a **listában.**
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | A tartomány új DNS TXT-rekordba (MS=xxxxx) vonatkozó információit adja meg. Előfordulhat, hogy az ellenőrzés nem azonnal történik meg, mert a TXT-rekord propagálása némi időt vesz igénybe, ezért várjon néhány percet, mielőtt megfontolja a **-ForceTakeover kapcsolót.** 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Ha a tartománynév még mindig nincs ellenőrizve, folytathatja a **-ForceTakeover beállítással.** Ellenőrzi, hogy létrejött-e a TXT-rekord, és el is indul a feladatátvételi folyamat.<li>A **-ForceTakeover** kapcsolót csak akkor kell hozzáadni a parancsmaghoz, ha külső rendszergazdai átvételt kényszerít ki, például ha a nem Microsoft 365 szervezet blokkolja az átvételt.
`get-msoldomain` | A tartománylista most Ellenőrzöttként jeleníti meg **a tartománynevet.**

> [!NOTE]
> A rendszer 10 nappal a külső feladatátvételi kényszerítés gyakorlata után törli a nem azure AD-szervezetet.

### <a name="powershell-example"></a>PowerShell-példa

1. Csatlakozzon az Azure AD-hez az önkiszolgáló ajánlatra való válaszadáshoz használt hitelesítő adatokkal:
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. A tartományok listájának lekért listája:
  
   ```powershell
   Get-MsolDomain
   ```
3. Futtassa a Get-MsolDomainVerificationDns parancsmagot egy feladat létrehozásához:
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    Például:
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Másolja ki a parancs által visszaadott értéket (a feladatot). Például:
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. A nyilvános DNS-névtérben hozzon létre egy DNS txt-rekordot, amely az előző lépésben kimáselt értéket tartalmazza. A rekord neve a szülőtartomány neve, így ha ezt az erőforrásrekordot a Windows Server DNS-szerepkörével hozza létre, hagyja üresen a Rekord neve mezőt, és illessze be az értéket a Szöveg mezőbe.
6. Futtassa a Confirm-MsolDomain parancsmagot a feladat ellenőrzéséhez:
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Például:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

A sikeres feladat hiba nélkül ad vissza a kérdésre.

## <a name="next-steps"></a>Következő lépések

* [Egyéni tartománynév hozzáadása az Azure AD-hez](../fundamentals/add-custom-domain.md)
* [How to install and configure Azure PowerShell (Az Azure PowerShell telepítése és konfigurálása)](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Azure parancsmag-referencia](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0&preserve-view=true)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png

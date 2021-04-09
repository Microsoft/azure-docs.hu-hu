---
title: Nem felügyelt könyvtár rendszergazdai átvétele – Azure AD | Microsoft Docs
description: DNS-tartománynév átvétele nem felügyelt Azure AD-szervezetben (árnyékmásolat-bérlő).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0af74967e2de47afeb357e2ac31b1a0ee849ef36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96547100"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Nem felügyelt címtár átvétele rendszergazdaként az Azure Active Directoryban

Ez a cikk két módszert ismertet a DNS-tartománynév átvételére egy nem felügyelt címtárban Azure Active Directory (Azure AD). Amikor egy önkiszolgáló felhasználó regisztrál egy Azure AD-t használó felhőszolgáltatásra, a rendszer az e-mail-címe alapján hozzáadja egy nem felügyelt Azure AD-címtárhoz. További információ az önkiszolgáló vagy a "vírusos" szolgáltatással való regisztrációról: [Mi az a Azure Active Directory önkiszolgáló regisztrációja?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Döntse el, hogyan szeretné átvenni a nem felügyelt címtárat
A rendszergazdai átvétel során az [Egyéni tartománynév hozzáadása az Azure Active Directoryhoz](../fundamentals/add-custom-domain.md) című cikkben leírt módon bizonyíthatja tulajdonjogát. A következő szakaszok részletesen ismertetik a rendszergazdai feladatokat, amelyek összegzése itt olvasható:

* Ha ["belső" rendszergazdai átvételt](#internal-admin-takeover) végez egy nem felügyelt Azure-címtárból, a rendszer hozzáadja a nem felügyelt könyvtár globális rendszergazdájaként. Felhasználók, tartományok és szolgáltatáscsomagok nem kerülnek át Ön által felügyelt további címtárakba.

* Ha ["külső" rendszergazdai átvételt](#external-admin-takeover) végez egy nem felügyelt Azure-címtárból, adja hozzá a nem felügyelt CÍMTÁR DNS-tartománynevét a felügyelt Azure-címtárhoz. A tartománynév hozzáadásakor az Ön által felügyelt Azure-címtárban létrejön a felhasználók és erőforrások közötti kapcsolatok leképezése, így a felhasználók továbbra is megszakítás nélkül érhetik el a szolgáltatásokat. 

## <a name="internal-admin-takeover"></a>Belső rendszergazdai átvétel

Egyes, a SharePoint-és OneDrive tartalmazó termékek, például a Microsoft 365 nem támogatják a külső átvételt. Ha ez a forgatókönyv, vagy ha Ön rendszergazda, és nem felügyelt vagy "Shadow" Azure AD-szervezetet szeretne létrehozni az önkiszolgáló regisztrációt használó felhasználók számára, ezt egy belső rendszergazdai átvételsel teheti meg.

1. Hozzon létre egy felhasználói környezetet a nem felügyelt szervezetben a Power BI regisztrálásával. Például a következő lépések elvégzésével feltételezhető, hogy az elérési út.

2. Nyissa meg az [Power bi webhelyet](https://powerbi.com) , és válassza az **ingyenes indítás** lehetőséget. Adjon meg egy olyan felhasználói fiókot, amely a szervezet tartománynevét használja; például: `admin@fourthcoffee.xyz` . Az ellenőrző kód megadása után tekintse meg az e-mailt a megerősítő kódhoz.

3. A Power BI megerősítő e-mailben válassza az **Igen, ez nekem** lehetőséget.

4. Jelentkezzen be a [Microsoft 365 felügyeleti központba](https://portal.office.com/admintakeover) a Power bi felhasználói fiókkal. Megjelenik egy üzenet, amely arra utasítja Önt, hogy a nem felügyelt szervezetben már ellenőrzött tartománynév **rendszergazdája legyen** . válassza **az igen, szeretnék a rendszergazdát**.
  
   ![a rendszergazdai jogosultságok első képernyőképe](./media/domains-admin-takeover/become-admin-first.png)
  
5. A TXT-rekord hozzáadásával igazolhatja, hogy a tartománynév-regisztráló **fourthcoffee.xyz** a tartománynevet. Ebben a példában ez a GoDaddy.com.
  
   ![TXT-rekord hozzáadása a tartománynévhez](./media/domains-admin-takeover/become-admin-txt-record.png)

Ha a DNS TXT-rekordok ellenőrzése a tartománynév-regisztrálónál történik, akkor felügyelheti az Azure AD-szervezetet.

Az előző lépések elvégzése után a Microsoft 365 negyedik kávéházának globális rendszergazdája lesz. Ha a tartománynevet a többi Azure-szolgáltatással szeretné integrálni, távolítsa el a Microsoft 365ból, és vegye fel egy másik felügyelt szervezetbe az Azure-ban.

### <a name="adding-the-domain-name-to-a-managed-organization-in-azure-ad"></a>Tartománynév hozzáadása felügyelt szervezethez az Azure AD-ben

1. Nyissa meg a [Microsoft 365 felügyeleti központot](https://admin.microsoft.com).
2. Válassza a **felhasználók** fület, és hozzon létre egy olyan nevű új felhasználói fiókot, amely nem használja az egyéni tartománynevet (például *felhasználói \@ fourthcoffeexyz.onmicrosoft.com* ). 
3. Győződjön meg arról, hogy az új felhasználói fiók globális rendszergazdai jogosultságokkal rendelkezik az Azure AD-szervezethez.
4. Nyissa meg a **tartományok** fület a Microsoft 365 felügyeleti központban, válassza ki a tartománynevet, és válassza az **Eltávolítás** lehetőséget. 
  
   ![A tartománynév eltávolítása Microsoft 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Ha az eltávolított tartománynévre hivatkozó Microsoft 365-felhasználók vagy-csoportok találhatók, akkor azokat a. onmicrosoft.com tartományba kell átnevezni. Ha kényszeríti a tartománynév törlését, az összes felhasználó automatikusan átnevezve lesz, ebben a példában a *felhasználói \@ fourthcoffeexyz.onmicrosoft.com*.
  
6. Jelentkezzen be az [Azure ad felügyeleti központba](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) egy olyan fiókkal, amely az Azure ad-szervezet globális rendszergazdája.
  
7. Válassza az **Egyéni tartománynevek** lehetőséget, majd adja hozzá a tartománynevet. A tartománynév tulajdonjogának ellenőrzéséhez meg kell adnia a DNS TXT-rekordokat. 
  
   ![Az Azure AD-hez hozzáadott tartomány ellenőrizve](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> A Power BI vagy az Azure Rights Management szolgáltatás azon felhasználóinak, akiknek a Microsoft 365 szervezethez hozzárendelt licenccel rendelkeznek, menteniük kell az irányítópultokat, ha a tartománynév el lett távolítva. Felhasználói *\@ fourthcoffee.xyz* helyett felhasználói névvel kell bejelentkezniük, például a *felhasználói \@ fourthcoffeexyz.onmicrosoft.com* .

## <a name="external-admin-takeover"></a>Külső rendszergazdai átvétel

Ha már felügyel egy szervezetet az Azure-szolgáltatásokkal vagy a Microsoft 365okkal, akkor nem adhat hozzá egyéni tartománynevet, ha már ellenőrizve van egy másik Azure AD-szervezetben. Az Azure AD-beli felügyelt szervezete azonban átveheti a nem felügyelt szervezeteket külső rendszergazdai átvételre. Az általános eljárás az [egyéni tartomány hozzáadása az Azure ad-hoz](../fundamentals/add-custom-domain.md)című cikket követi.

A tartománynév tulajdonjogának ellenőrzésekor az Azure AD eltávolítja a tartománynevet a nem felügyelt szervezetből, és áthelyezi a meglévő szervezetbe. A nem felügyelt címtár külső rendszergazdai átvételéhez ugyanazt a DNS TXT-ellenőrzési folyamatot kell megadnia, mint a belső rendszergazdai átvétel. A különbség az, hogy az alábbiak is át lesznek helyezve a tartománynévvel:

- Felhasználók
- Előfizetések
- Licenc-hozzárendelések

### <a name="support-for-external-admin-takeover"></a>Külső rendszergazdai átvétel támogatása
A külső rendszergazdai átvételt a következő online szolgáltatások támogatja:

- Azure Rights Management
- Exchange Online

A támogatott szolgáltatási csomagok a következők:

- Ingyenes PowerApps
- Ingyenes PowerFlow
- RMS egyéni felhasználók számára
- Microsoft Stream
- Dynamics 365 ingyenes próbaverzió

A külső rendszergazdai átvétel nem támogatott bármely olyan szolgáltatás esetében, amely tartalmazza a SharePoint, a OneDrive vagy a Skype vállalati verziójának szolgáltatási csomagját. például egy Office ingyenes előfizetésen keresztül. 

Igény szerint a [ **ForceTakeover** beállítással](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) is eltávolíthatja a tartománynevet a nem felügyelt szervezetből, és ellenőrizheti a kívánt szervezeten. 

#### <a name="more-information-about-rms-for-individuals"></a>További információ az RMS egyéni felhasználók számára szolgáltatásról

Az [RMS egyéni felhasználók](/azure/information-protection/rms-for-individuals)számára, ha a nem felügyelt szervezet ugyanabban a régióban található, mint a saját szervezete, akkor az automatikusan létrehozott [Azure Information Protection szervezeti kulcs](/azure/information-protection/plan-implement-tenant-key) és az [alapértelmezett védelmi sablonok](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) is át lesznek helyezve a tartománynévvel.

A kulcs és a sablonok nem lesznek áthelyezve, amikor a nem felügyelt szervezet egy másik régióban található. Ha például a nem felügyelt szervezet Európában található, és a saját szervezete Észak-Amerika van.

Bár az RMS egyéni felhasználók számára készült, hogy támogassa az Azure AD-hitelesítést a védett tartalom megnyitásához, nem akadályozza meg, hogy a felhasználók is megóvják a tartalmat. Ha a felhasználók védik a tartalmat az RMS egyéni felhasználók számára előfizetéssel, és a kulcs és a sablonok nem lettek áthelyezve, akkor ez a tartalom nem érhető el a tartomány átvétele után.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Azure AD PowerShell-parancsmagok a ForceTakeover beállításhoz
Ezeket a parancsmagokat a [PowerShell-példában](#powershell-example)is láthatja.

parancsmag | Használat
------- | -------
`connect-msolservice` | Ha a rendszer kéri, jelentkezzen be a felügyelt szervezetbe.
`get-msoldomain` | Megjeleníti az aktuális szervezethez társított tartományneveket.
`new-msoldomain –name <domainname>` | Nem ellenőrzöttként adja hozzá a tartománynevet a szervezethez (a DNS-ellenőrzés még nem lett elvégezve).
`get-msoldomain` | A tartománynév már szerepel a felügyelt szervezethez társított tartománynevek listáján, de nem **ellenőrzöttként** van felsorolva.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Megadja a tartományhoz tartozó új DNS TXT-rekordba (MS = XXXXX) való üzembe helyezéshez szükséges adatokat. Előfordulhat, hogy az ellenőrzés nem történik meg azonnal, mert eltart egy ideig a TXT-rekord propagálásához, ezért várjon néhány percet, mielőtt fontolóra venné a **-ForceTakeover** kapcsolót. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Ha a tartománynevet még nem ellenőrzi, folytathatja a **-ForceTakeover** kapcsolót. Ellenőrzi, hogy létrejött-e a TXT-rekord, és elindította-e az átvételi folyamatot.<li>A **-ForceTakeover** beállítást csak akkor adja hozzá a parancsmaghoz, ha a külső rendszergazdai átvételt kényszeríti, például ha a nem felügyelt szervezet Microsoft 365 szolgáltatás blokkolja az átvételt.
`get-msoldomain` | A tartománynevek mostantól **ellenőrzöttként** jelenítik meg a tartománynevet.

> [!NOTE]
> A nem felügyelt Azure AD-szervezet 10 nappal a külső átvételi kényszerítési lehetőség gyakorlása után törlődik.

### <a name="powershell-example"></a>PowerShell-példa

1. Kapcsolódjon az Azure AD-hez az önkiszolgáló ajánlatra való válaszadáshoz használt hitelesítő adatok használatával:
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. Tartományok listájának lekérése:
  
   ```powershell
   Get-MsolDomain
   ```
3. Hozzon létre egy kihívást a Get-MsolDomainVerificationDns parancsmag futtatásával:
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    Például:
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Másolja ki a parancs által visszaadott értéket (a kihívást). Például:
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. A nyilvános DNS-névtérben hozzon létre egy olyan DNS TXT-rekordot, amely az előző lépésben másolt értéket tartalmazza. A rekord neve a szülőtartomány neve, így ha létrehozza ezt az erőforrásrekordot a Windows Server DNS-szerepkörének használatával, hagyja üresen a rekord nevét, és illessze be az értéket a szövegmezőbe.
6. A probléma ellenőrzéséhez futtassa a Confirm-MsolDomain parancsmagot:
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Például:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

A sikeres kihívás egy hiba nélkül visszaadja a kérést.

## <a name="next-steps"></a>Következő lépések

* [Egyéni tartománynév hozzáadása az Azure AD-hez](../fundamentals/add-custom-domain.md)
* [How to install and configure Azure PowerShell (Az Azure PowerShell telepítése és konfigurálása)](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Azure parancsmag-referencia](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0&preserve-view=true)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png

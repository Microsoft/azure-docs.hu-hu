---
title: Hibrid Azure Active Directory-csatlakoztatott eszközök manuális konfigurálása | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja manuálisan a hibrid Azure Active Directory csatlakoztatott eszközöket.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28cc8a858d1779e17c893d64eda5f907bb4c808e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577988"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Oktatóanyag: Az Azure Active Directoryhoz csatlakoztatott hibrid eszközök manuális konfigurálása

Az Azure Active Directory (Azure AD) eszközkezelés révén biztosíthatja, hogy a felhasználók hozzáférhessenek az erőforrásokhoz a biztonsági és megfelelőségi szabványoknak megfelelő eszközökről. További információ: a [Azure Active Directory eszköz-felügyeletének bemutatása](overview.md).

> [!TIP]
> Ha a Azure AD Connect használata lehetőséggel rendelkezik, tekintse meg a kapcsolódó oktatóanyagokat a [felügyelt](hybrid-azuread-join-managed-domains.md) vagy [összevont](hybrid-azuread-join-federated-domains.md) tartományokhoz. A Azure AD Connect használatával jelentősen egyszerűsítheti a hibrid Azure AD-csatlakozás konfigurációját.

Ha helyszíni Active Directory-környezettel rendelkezik, és csatlakoztatni szeretné a tartományokhoz csatlakoztatott eszközeit az Azure AD-hoz, ezt hibrid Azure AD-csatlakoztatott eszközök konfigurálásával teheti meg. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hibrid Azure AD-csatlakozás manuális konfigurálása
> * Szolgáltatáskapcsolódási pont konfigurálása
> * Jogcímek kiállításának beállítása
> * A korábbi verziójú Windows-eszközök engedélyezése
> * Csatlakoztatott eszközök ellenőrzése
> * A megvalósítás hibaelhárítása

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már ismeri a következőket:

* [Az Azure Active Directory eszközkezelésének alapjai](./overview.md)
* [A hibrid Azure Active Directory-csatlakozás megvalósításának megtervezése](hybrid-azuread-join-plan.md)
* [Az eszközök hibrid Azure AD-csatlakozásának vezérlése](hybrid-azuread-join-control.md)

Mielőtt elkezdené a hibrid Azure AD-hez csatlakoztatott eszközök engedélyezését a szervezetben, győződjön meg a következőket:

* A Azure AD Connect naprakész verzióját futtatja.
* Azure AD Connect szinkronizálta azon eszközök számítógép-objektumait, amelyeket hibrid Azure AD-hez csatlakoztatni szeretne az Azure AD-hez. Ha a számítógép-objektumok adott szervezeti egységekhez (OU) tartoznak, ezeket a szervezeti egységeket Azure AD Connect is be kell állítani a szinkronizáláshoz.

Azure AD Connect:

* Megtartja a számítógép fiókja közötti társítást a helyszíni Active Directory-példányban, valamint az eszköz objektumát az Azure AD-ben.
* Más eszközökhöz kapcsolódó funkciók (például a vállalati Windows Hello) engedélyezése.

Győződjön meg arról, hogy az alábbi URL-címek elérhetők a szervezet hálózatán belüli számítógépekről a számítógépek Azure AD-be való regisztrálásához:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com`
* A szervezet STS-je (összevont tartományok esetében), amelyet a felhasználó helyi intranetes beállításaiban kell szerepeltetni

> [!WARNING]
> Ha a szervezet olyan proxykiszolgálót használ, amelyek az SSL-forgalmat az adatveszteség-megelőzési vagy az Azure AD-bérlői korlátozások miatt észlelik, ügyeljen arra, hogy a (z) "" forgalom ne legyen https://device.login.microsoftonline.com kizárva a TLS-megszakítás és-vizsgálat alól. A (z) "" kizárása az https://device.login.microsoftonline.com ügyféltanúsítvány-alapú hitelesítés zavarásával járhat, ami problémákat okoz az eszközök regisztrációja és az eszközön alapuló feltételes hozzáférés miatt.

Ha a szervezete a zökkenőmentes SSO használatát tervezi, a következő URL-címnek elérhetőnek kell lennie a szervezeten belüli számítógépekről. Emellett hozzá kell adni a felhasználó helyi intranet zónához is.

* `https://autologon.microsoftazuread-sso.com`

Emellett a következő beállításokat kell engedélyezni a felhasználó intranetes zónájában: „Az állapotsor szkriptekkel való frissítése.”

Ha a szervezet felügyelt (nem összevont) beállítást használ a helyszíni Active Directory, és nem használja a Active Directory összevonási szolgáltatások (AD FS) (AD FS) szolgáltatást az Azure AD-vel való összevonása, akkor a hibrid Azure AD-csatlakozás a Windows 10-es verzióban az Azure AD-vel való szinkronizáláshoz a Active Directory számítógép-objektumaira támaszkodik. Győződjön meg arról, hogy minden olyan szervezeti egység, amely a hibrid Azure AD-hez szükséges számítógép-objektumokat tartalmazza, szinkronizálásra van engedélyezve a Azure AD Connect szinkronizálási konfigurációjában.

A 1703-es vagy korábbi verziójú Windows 10-es eszközök esetén, ha a szervezet kimenő proxyn keresztül kíván hozzáférni az internethez, a Windows 10-es számítógépek Azure AD-ba való regisztrálásának engedélyezéséhez be kell vezetnie a webproxy automatikus felderítése (WPAD) szolgáltatást.

A Windows 10 1803-től kezdve, még akkor is, ha egy összevont tartományban található eszköz hibrid Azure AD-csatlakoztatási kísérlete AD FS meghiúsul, és ha Azure AD Connect úgy van konfigurálva, hogy a számítógép/eszköz objektumait az Azure AD-be szinkronizálja, az eszköz a szinkronizált számítógép/eszköz használatával próbálja befejezni a hibrid Azure AD-csatlakozást.

> [!NOTE]
> Ahhoz, hogy az eszköz regisztrációjának szinkronizálása sikeres legyen, az eszköz regisztrációs konfigurációjának részeként ne zárja ki az alapértelmezett eszköz attribútumokat a Azure AD Connect Sync konfigurációból. Ha többet szeretne megtudni az Azure AD-vel szinkronizált alapértelmezett eszközök attribútumairól, tekintse meg az [Azure ad Connect által szinkronizált attribútumokat](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized#windows-10).

Annak ellenőrzéséhez, hogy az eszköz képes-e hozzáférni a fenti Microsoft-erőforrásokhoz a rendszerfiókban, használhatja a [teszt eszköz regisztrációjának kapcsolati](/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/) parancsfájlját.

## <a name="verify-configuration-steps"></a>Konfigurációs lépések ellenőrzése

A hibrid Azure AD-csatlakoztatott eszközöket számos típusú Windows-alapú eszközplatformhoz konfigurálhatja. Ez a témakör az összes jellemző konfigurációs forgatókönyv szükséges lépéseit tartalmazza:  

Az alábbi táblázatban áttekintheti a forgatókönyvéhez szükséges lépéseket:  

| Lépések | Jelenlegi Windows és a jelszókivonat szinkronizálása | Jelenlegi Windows és összevonás | Korábbi verziójú Windows |
| :--- | :---: | :---: | :---: |
| A szolgáltatáskapcsolódási pont konfigurálása | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] |
| Jogcímek kiállításának beállítása |     | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] |
| Nem Windows 10 operációs rendszerű eszközök engedélyezése |       |        | ![Jelölőnégyzet][1] |
| Csatlakoztatott eszközök ellenőrzése | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] |

## <a name="configure-a-service-connection-point"></a>Szolgáltatáskapcsolódási pont konfigurálása

Az eszközök a regisztráció során szolgáltatáskapcsolódási pont (SCP) objektumot használnak az Azure AD-bérlői információk felderítéséhez. A helyszíni Active Directory-példányban a hibrid Azure AD-hez csatlakoztatott eszközök SZOLGÁLTATÁSKAPCSOLÓDÁSI pont objektumának léteznie kell a számítógép erdőjának konfigurációs névhasználati környezet partíciójában. Erdőnként egy konfigurációs névhasználati környezet létezik. A többerdős Active Directory konfigurációjában a szolgáltatás kapcsolódási pontjának minden olyan erdőben léteznie kell, amely tartományhoz csatlakoztatott számítógépeket tartalmaz.

Használja a [**Get-ADRootDSE**](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee617246(v=technet.10)) parancsmagot az erdő konfigurációs névhasználati környezetének lekéréséhez.  

A *fabrikam.com* Active Directory-tartománynevű erdő konfigurációs névhasználati környezete a következő:

`CN=Configuration,DC=fabrikam,DC=com`

A tartományokhoz csatlakoztatott eszközök automatikus regisztrációjának SCP-objektuma a következő helyen található a saját erdőjében:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

A Azure AD Connect telepítésének módjától függően előfordulhat, hogy az SCP-objektum már konfigurálva van.
A következő Windows PowerShell-parancsfájllal ellenőrizheti az objektum létezését, és beolvashatja a felderítési értékeket:

   ```PowerShell
   $scp = New-Object System.DirectoryServices.DirectoryEntry;

   $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

   $scp.Keywords;
   ```

A **$SCP. A kulcsszavak** kimenete az Azure ad-bérlő információit jeleníti meg. Bemutatunk egy példát:

   ```
   azureADName:microsoft.com
   azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47
   ```

Ha a szolgáltatáskapcsolódási pont nem létezik, a létrehozásához futtassa az `Initialize-ADSyncDomainJoinedComputerSync` parancsmagot az Azure AD Connect-kiszolgálóján. A parancsmag futtatásához vállalati rendszergazdai hitelesítő adatok szükségesek.  

A parancsmag:

* Létrehozza a szolgáltatási kapcsolódási pontot abban a Active Directory erdőben, amelyhez Azure AD Connect csatlakozik.
* Az `AdConnectorAccount` paraméter megadása szükséges hozzá. Ez a fiók a Azure AD Connect Active Directory-összekötő fiókként van konfigurálva.


Az alábbi szkriptben egy példa látható a parancsmag használatára. A szkript `$aadAdminCred = Get-Credential` eleme esetében egy felhasználónév megadása szükséges. A felhasználónevet egyszerű felhasználónév (UPN) formátumban kell megadni (`user@example.com`).

   ```PowerShell
   Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

   $aadAdminCred = Get-Credential;

   Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;
   ```

Az `Initialize-ADSyncDomainJoinedComputerSync` parancsmag:

* A Active Directory PowerShell-modult és Azure Active Directory Domain Services (Azure AD DS) eszközöket használja. Ezek az eszközök a tartományvezérlőn futó Active Directory webszolgáltatásokra támaszkodnak. Az Active Directory webszolgáltatások Windows Server 2008 R2 vagy újabb rendszerű tartományvezérlőkön támogatott.
* Kizárólag az MSOnline PowerShell modul 1.1.166.0-s verziója támogatja. A modul letöltéséhez használja [ezt a hivatkozást](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0).
* Ha a AD DS-eszközök nincsenek telepítve, a `Initialize-ADSyncDomainJoinedComputerSync` művelet sikertelen lesz. A AD DS eszközök a Kiszolgálókezelő segítségével telepíthetők a **szolgáltatások**  >  **Távoli kiszolgálófelügyelet eszközei**  >  **szerepkör-felügyeleti eszközök** lehetőségre.

A Windows Server 2008-es vagy korábbi verzióit futtató tartományvezérlők esetén használja a következő parancsfájlt a szolgáltatás kapcsolódási pontjának létrehozásához. Többerdős konfiguráció esetén a következő parancsfájl használatával hozza létre a szolgáltatási kapcsolódási pontot minden olyan erdőben, ahol a számítógépek léteznek.

   ```PowerShell
   $verifiedDomain = "contoso.com" # Replace this with any of your verified domain names in Azure AD
   $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47" # Replace this with you tenant ID
   $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com" # Replace this with your Active Directory configuration naming context

   $de = New-Object System.DirectoryServices.DirectoryEntry
   $de.Path = "LDAP://CN=Services," + $configNC
   $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
   $deDRC.CommitChanges()

   $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
   $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
   $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

   $deSCP.CommitChanges()
   ```

Az előző szkriptben `$verifiedDomain = "contoso.com"` helyőrző. Cserélje le az egyik ellenőrzött tartománynevet az Azure AD-ben. Ahhoz, hogy használni tudja a tartományt, rendelkeznie kell a tartományhoz.

További információ az ellenőrzött tartománynevek használatáról: [Egyéni tartománynév hozzáadása Azure Active Directoryhoz](../fundamentals/add-custom-domain.md).

Az ellenőrzött vállalati tartományok listáját a [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain) parancsmaggal kaphatja meg.

![Vállalati tartományok listája](./media/hybrid-azuread-join-manual/01.png)

## <a name="set-up-issuance-of-claims"></a>Jogcímek kiállításának beállítása

Az összevont Azure AD-konfigurációban az eszközök AD FS vagy egy Microsoft-partner helyszíni összevonási szolgáltatásán alapulnak az Azure AD-ben való hitelesítéshez. Az eszközök a hitelesítés révén kapnak hozzáférési jogkivonatokat, amelyekkel regisztrálhatnak az Azure Active Directory eszközregisztrációs szolgáltatásába (Azure DRS).

A Windows jelenlegi eszközei a helyi összevonási szolgáltatás által üzemeltetett aktív WS-Trust végpontra (1,3 vagy 2005 verzió) integrált Windows-hitelesítéssel végzik a hitelesítést.

AD FS használatakor engedélyeznie kell a következő WS-Trust-végpontokat
- `/adfs/services/trust/2005/windowstransport`
- `/adfs/services/trust/13/windowstransport`
- `/adfs/services/trust/2005/usernamemixed`
- `/adfs/services/trust/13/usernamemixed`
- `/adfs/services/trust/2005/certificatemixed`
- `/adfs/services/trust/13/certificatemixed`

> [!WARNING]
> Az **ADFS/Services/Trust/2005/windowstransport** és az **ADFS/Services/Trust/13/windowstransport** egyaránt engedélyezve kell lennie csak intranetes végpontok számára, és a webalkalmazás-proxyn keresztül nem szabad az extranetes végpontok számára elérhetővé tenni. Ha többet szeretne megtudni a WS-Trust Windows-végpontok letiltásáról, tekintse meg [a WS-Trust Windows-végpontok letiltása a proxyn](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)című témakört. Láthatja, hogy mely végpontok vannak engedélyezve a AD FS felügyeleti konzolon a **szolgáltatási**  >  **végpontok** alatt.

> [!NOTE]
>Ha nem rendelkezik AD FS a helyszíni összevonási szolgáltatásként, kövesse a gyártó utasításait, és győződjön meg arról, hogy támogatják WS-Trust 1,3 vagy 2005 végpontot, és hogy ezek a metaadatok Exchange-fájlján (MEX) keresztül lesznek közzétéve.

Az eszközök regisztrációjának befejezéséhez a következő jogcímeknek léteznie kell az Azure DRS által fogadott jogkivonatban. Az Azure DRS egy eszköz-objektumot hoz létre az Azure AD-ben néhány ilyen információval. Azure AD Connect ezt az információt használja az újonnan létrehozott eszköz objektum és a helyszíni számítógépfiók hozzárendeléséhez.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Ha több ellenőrzött tartománynévvel rendelkezik, a következő jogcímeket kell megadnia a számítógépekhez:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Ha már kiadott egy ImmutableID-jogcímet (például a `mS-DS-ConsistencyGuid` vagy más attribútumot használja a ImmutableID), meg kell adnia egy megfelelő jogcímet a számítógépeknek:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

A következő szakaszokban az alábbiakról talál információt:

* Az egyes jogcímek értékeit.
* A definíciók megjelenése AD FS.

A meghatározás segít ellenőrizni, hogy az értékek léteznek-e már, vagy még létre kell őket hozni.

> [!NOTE]
> Ha helyszíni összevonási kiszolgálóként nem az AD FS-t használja, kövesse a szállítója utasításait a jogcímek kiállításához megfelelő konfiguráció létrehozásához.

### <a name="issue-account-type-claim"></a>Fióktípusra vonatkozó jogcím kiállítása

A `http://schemas.microsoft.com/ws/2012/01/accounttype` jogcímnek a **DJ** értéket kell tartalmaznia, amely az eszközt tartományhoz csatlakoztatott számítógépként azonosítja. Az AD FS-ben hozzáadhat egy kiállítási átalakítószabályt, amely a következőképpen néz ki:

   ```
   @RuleName = "Issue account type for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "DJ"
   );
   ```

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>A helyszíni számítógépfiók objectGUID értékének kiállítása

A `http://schemas.microsoft.com/identity/claims/onpremobjectguid` jogcímnek tartalmaznia kell a helyszíni számítógépfiók **ObjectGUID** értékét. Az AD FS-ben hozzáadhat egy kiállítási átalakítószabályt, amely a következőképpen néz ki:

   ```
   @RuleName = "Issue object GUID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$", 
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"),
      query = ";objectguid;{0}",
      param = c2.Value
   );
   ```

### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>A helyszíni számítógépfiók objectSID értékének kiállítása

A `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` jogcímnek tartalmaznia kell a helyszíni számítógépfiók **objectSid** értékét. Az AD FS-ben hozzáadhat egy kiállítási átalakítószabályt, amely a következőképpen néz ki:

   ```
   @RuleName = "Issue objectSID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(claim = c2);
   ```

### <a name="issue-issuerid-for-the-computer-when-multiple-verified-domain-names-are-in-azure-ad"></a>A számítógép issuerID kiadása, ha több ellenőrzött tartománynév van az Azure AD-ben

A `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` jogcímnek tartalmaznia kell a jogkivonatot kiállító helyszíni összevonási szolgáltatással (AD FS vagy partnerrel) csatlakozó ellenőrzött tartománynevek Uniform Resource Identifier (URI) nevét. AD FS az előzőek után az alábbihoz hasonló kiállítási átalakítási szabályokat adhat hozzá az adott sorrendben: Vegye figyelembe, hogy az egyik szabály a felhasználókra vonatkozó szabály explicit kikibocsátására van szükség. A következő szabályokban a felhasználó és számítógép hitelesítését azonosító első szabály lesz hozzáadva.

   ```
   @RuleName = "Issue account type with the value User when its not a computer"
   NOT EXISTS(
   [
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "DJ"
   ]
   )
   => add(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "User"
   );

   @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
   c1:[
      Type == "http://schemas.xmlsoap.org/claims/UPN"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "User"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = regexreplace(
      c1.Value,
      ".+@(?<domain>.+)",
      "http://${domain}/adfs/services/trust/"
      )
   );

   @RuleName = "Issue issuerID for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = "http://<verified-domain-name>/adfs/services/trust/"
   );
   ```

Az előző jogcímben `<verified-domain-name>` helyőrző. Cserélje le az egyik ellenőrzött tartománynevet az Azure AD-ben. Használja például a következőt: `Value = "http://contoso.com/adfs/services/trust/"` .

További információ az ellenőrzött tartománynevek használatáról: [Egyéni tartománynév hozzáadása Azure Active Directoryhoz](../fundamentals/add-custom-domain.md).  

Az ellenőrzött vállalati tartományok listáját a [Get-MsolDomain](/powershell/module/msonline/get-msoldomain) parancsmaggal kaphatja meg.

![Vállalati tartományok listája](./media/hybrid-azuread-join-manual/01.png)

### <a name="issue-immutableid-for-the-computer-when-one-for-users-exists-for-example-using-ms-ds-consistencyguid-as-the-source-for-immutableid"></a>Probléma ImmutableID a számítógépen, ha az egyik a felhasználók számára létezik (például mS-DS-ConsistencyGuid használata a ImmutableID forrásaként)

A `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID` jogcímnek érvényes értéket kell tartalmaznia a számítógépek számára. Az AD FS-ben a következő módon hozhat létre kiállítási átalakítószabályt:

   ```
   @RuleName = "Issue ImmutableID for computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"),
      query = ";objectguid;{0}",
      param = c2.Value
   );
   ```

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Az AD FS kiállítási átalakítószabályainak létrehozását segítő szkript

A következő szkript segítséget nyújt a korábban ismertetett kiadási átalakítási szabályok létrehozásában.

   ```
   $multipleVerifiedDomainNames = $false
   $immutableIDAlreadyIssuedforUsers = $false
   $oneOfVerifiedDomainNames = 'example.com'   # Replace example.com with one of your verified domains

   $rule1 = '@RuleName = "Issue account type for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "DJ"
   );'

   $rule2 = '@RuleName = "Issue object GUID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"),
      query = ";objectguid;{0}",
      param = c2.Value
   );'

   $rule3 = '@RuleName = "Issue objectSID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(claim = c2);'

   $rule4 = ''
   if ($multipleVerifiedDomainNames -eq $true) {
   $rule4 = '@RuleName = "Issue account type with the value User when it is not a computer"
   NOT EXISTS(
   [
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "DJ"
   ]
   )
   => add(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "User"
   );

   @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
   c1:[
      Type == "http://schemas.xmlsoap.org/claims/UPN"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "User"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = regexreplace(
      c1.Value,
      ".+@(?<domain>.+)",
      "http://${domain}/adfs/services/trust/"
      )
   );

   @RuleName = "Issue issuerID for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = "http://' + $oneOfVerifiedDomainNames + '/adfs/services/trust/"
   );'
   }

   $rule5 = ''
   if ($immutableIDAlreadyIssuedforUsers -eq $true) {
   $rule5 = '@RuleName = "Issue ImmutableID for computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"),
      query = ";objectguid;{0}",
      param = c2.Value
   );'
   }

   $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

   $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5

   $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

   Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString
   ```

#### <a name="remarks"></a>Megjegyzések

* A szkript hozzáfűzi a szabályokat a meglévő szabályokhoz. Ne futtassa kétszer a parancsfájlt, mert a szabályok halmaza kétszer lesz hozzáadva. A szkript újbóli futtatása előtt ellenőrizze, hogy a jogcímekre nem vonatkoznak kapcsolódó szabályok (a kapcsolódó feltételek mellett).
* Ha több ellenőrzött tartományneve van (ahogy az az Azure AD-portálon vagy a **Get-MsolDomain** parancsmagon keresztül is látható), a parancsfájlban állítsa be a **$multipleVerifiedDomainNames** értékét **$truere**. Győződjön meg arról is, hogy minden olyan meglévő **issuerid** -jogcímet eltávolít, amelyet Azure ad Connect vagy más módon hoztak létre. Példa erre a szabályra:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
   => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 
   ```

Ha a felhasználói fiókokhoz már kiállított egy **ImmutableID** jogcímet, állítsa az **$immutableIDAlreadyIssuedforUsers** értékét a **$true** értékre a szkriptben.

## <a name="enable-windows-down-level-devices"></a>A korábbi verziójú Windows-eszközök engedélyezése

Ha a tartományhoz csatlakoztatott eszközök között korábbi verziójú Windows-eszközök is vannak, végezze el a következőket:

* Állítson be egy szabályzatot az Azure AD-ben, amellyel a felhasználók eszközeinek regisztrációját engedélyezheti.
* Konfigurálja a helyszíni összevonási szolgáltatást arra, hogy jogcímek kiállításával támogassa az integrált Windows-hitelesítést (IWA) az eszközök regisztrációjához.
* Adja hozzá az Azure AD-alapú hitelesítési végpontot a helyi intranetes zónákhoz, hogy elkerülje a tanúsítvány kérését az eszköz hitelesítése során.
* A Windows leállási szintű eszközeinek szabályozása.

### <a name="set-a-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Házirend beállítása az Azure AD-ben, amely lehetővé teszi a felhasználók számára az eszközök regisztrálását

A Windows Down-szintű eszközök regisztrálásához engedélyezze a felhasználók számára az eszközök regisztrálását az Azure AD-ben beállítás engedélyezését. A Azure Portal a beállítás **Azure Active Directory**  >  **felhasználók és csoportok**  >  **eszköz beállításai** területen található.

Az alábbi szabályzatot **minden** értékre kell állítani: a **felhasználók regisztrálhatják eszközeiket az Azure ad**-ben.

![Az összes gomb, amely lehetővé teszi a felhasználók számára az eszközök regisztrálását](./media/hybrid-azuread-join-manual/23.png)

### <a name="configure-the-on-premises-federation-service"></a>A helyszíni összevonási szolgáltatás konfigurálása

A helyszíni összevonási szolgáltatásnak támogatnia kell a **AuthenticationMethod** és a **wiaormultiauthn** jogcímek kiállítását, amikor hitelesítési kérést kap az Azure ad függő entitásnak, amely a következő kódolt értékkel rendelkező resource_params paramétert tartja:

   ```
   eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

   which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}
   ```

Ha ilyen kérés érkezik, a helyszíni összevonási szolgáltatásnak integrált Windows-hitelesítéssel kell hitelesítenie a felhasználót. Ha a hitelesítés sikeres, az összevonási szolgáltatásnak a következő két jogcímet kell kiállítania:

   `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows` `http://schemas.microsoft.com/claims/wiaormultiauthn`

AD FSban hozzá kell adnia egy kiállítási átalakítási szabályt, amely áthalad a hitelesítési módszeren. A szabály hozzáadásához:

1. A AD FS felügyeleti konzolon lépjen a **AD FS**  >  **megbízhatósági kapcsolatok**  >  **függő entitások megbízhatóságai** elemre.
1. Kattintson a jobb gombbal a Microsoft Office 365 Identity Platform megbízható függő entitás elemre, és válassza a **Jogcímszabályok szerkesztése** lehetőséget.
1. A **Kiállítási átalakítószabályok** lapon válassza a **Szabály hozzáadása** lehetőséget.
1. A **Jogcímszabály** sablonlistában válassza a **Jogcímek küldése egyéni szabállyal** lehetőséget.
1. Kattintson a **Tovább** gombra.
1. A **jogcím-szabály neve** mezőbe írja be az **Auth Method jogcím szabályt**.
1. A **jogcím szabály** mezőjébe írja be a következő szabályt:

   `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

1. Az összevonási kiszolgálón adja meg a következő PowerShell-parancsot. Cserélje le az értékét **\<RPObjectName\>** Az Azure ad függő entitás megbízhatósági objektumának függő entitás nevű objektumára. Ezen objektum neve általában **Microsoft Office 365 Identity Platform**.

   `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-endpoint-to-the-local-intranet-zones"></a>Adja hozzá az Azure AD-alapú hitelesítési végpontot a helyi intranetes zónákhoz

Ha el szeretné kerülni, hogy a rendszer a regisztrált eszközök felhasználóinak hitelesítse magát az Azure AD-ben, leküldheti a szabályzatot a tartományhoz csatlakoztatott eszközökre, hogy hozzáadja a következő URL-címet a helyi intranet zónához az Internet Explorerben:

`https://device.login.microsoftonline.com`

### <a name="control-windows-down-level-devices"></a>A korábbi verziójú Windows-eszközök vezérlése

A korábbi verziójú Windows-eszközök regisztrálásához a Letöltőközpontból le kell töltenie, majd telepítenie kell egy Windows Installer-csomagot (.msi). További információkért lásd: [hibrid Azure ad-csatlakozás ellenőrzése a Windows Down-szintű eszközökön](hybrid-azuread-join-control.md#controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices).

## <a name="verify-joined-devices"></a>Csatlakoztatott eszközök ellenőrzése

Az eszköz állapotának megállapításához és ellenőrzéséhez az alábbi 3 módszer használható:

### <a name="locally-on-the-device"></a>Helyileg az eszközön

1. Nyissa meg a Windows PowerShellt.
2. Írja be a következő szöveget: `dsregcmd /status`.
3. Ellenőrizze, hogy a **AzureAdJoined** és a **DomainJoined** is **Igen** értékre van-e állítva.
4. Használhatja a **DeviceID** eszközt, és összehasonlíthatja a szolgáltatás állapotát a Azure Portal vagy a PowerShell használatával.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

1. Nyissa meg az eszközök lapot a [közvetlen hivatkozás](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices)használatával.
2. Az eszközök megkeresésének módjáról [az eszköz identitásának kezelése a Azure Portal segítségével](./device-management-azure-portal.md#manage-devices)című témakörben talál információt.
3. Ha a **regisztrált** oszlop **függőben** van, akkor a hibrid Azure ad-csatlakozás nem fejeződött be. Összevont környezetekben ez csak akkor fordulhat elő, ha a regisztráció sikertelen volt, és a HRE-kapcsolat az eszközök szinkronizálására van konfigurálva.
4. Ha a **regisztrált** oszlop egy **dátumot és időpontot** tartalmaz, akkor a hibrid Azure ad JOIN befejeződött.

### <a name="using-powershell"></a>A PowerShell használata

A **[Get-MsolDevice](/powershell/module/msonline/get-msoldevice)** használatával ellenőrizze az eszköz regisztrációs állapotát az Azure-bérlőben. Ez a parancsmag a [Azure Active Directory PowerShell-modulban](/powershell/azure/active-directory/install-msonlinev1)található.

Ha a **Get-MSolDevice** parancsmagot használja a szolgáltatás részleteinek megtekintéséhez:

- Léteznie kell egy olyan objektumnak **, amely megfelel a Windows** -ügyfél azonosítójának.
- A **DeviceTrustType** értéke **tartományhoz van csatlakoztatva**. Ez a beállítás megegyezik a **hibrid Azure ad-hez csatlakoztatott** állapottal az Azure ad-portál **eszközök** lapján.
- A feltételes hozzáférésben használt eszközök esetében az **enabled** érték **true (igaz** ), a **DeviceTrustLevel** pedig **felügyelt**.

1. Nyissa meg a Windows PowerShellt rendszergazdaként.
2. Az `Connect-MsolService` Azure-bérlőhöz való kapcsolódáshoz adja meg a következőt:.

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>Az összes hibrid Azure AD-hez csatlakoztatott eszköz száma (a **függő** állapot kizárása nélkül)

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Az összes **függő** állapotú, hibrid Azure ad-hez csatlakoztatott eszköz száma

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>Az összes hibrid Azure AD-hez csatlakoztatott eszköz listázása

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Az összes **függő** állapotú, hibrid Azure ad-hez csatlakoztatott eszköz listázása

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>Egyetlen eszköz részleteinek listázása:

1. ENTER `get-msoldevice -deviceId <deviceId>` (ez az eszközön helyileg beszerzett **DeviceID** ).
2. Ellenőrizze, hogy az **Engedélyezve** beállításhoz az **Igaz** érték van-e megadva.

## <a name="troubleshoot-your-implementation"></a>A megvalósítás hibaelhárítása

Ha a tartományhoz csatlakoztatott Windows-eszközök hibrid Azure AD-csatlakozásának kitöltésével kapcsolatos problémákat tapasztal, tekintse meg a következőt:

- [Eszközök hibaelhárítása a dsregcmd paranccsal](./troubleshoot-device-dsregcmd.md)
- [Az Azure Active Directoryhoz csatlakoztatott hibrid eszközök hibaelhárítása](troubleshoot-hybrid-join-windows-current.md)
- [A hibrid Azure Active Directory csatlakoztatása a régebbi verziójú eszközökhöz](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Következő lépések

* [Az Azure Active Directory eszközkezelésének alapjai](overview.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual/12.png

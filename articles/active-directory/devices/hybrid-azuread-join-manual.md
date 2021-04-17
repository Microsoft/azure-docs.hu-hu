---
title: Hibrid Azure Active Directory-csatlakoztatott eszközök manuális konfigurálása | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja manuálisan a hibrid Azure Active Directory eszközöket.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 04/16/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1aafcd8ace846d7da65d95d4148872d5a6eddeee
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587850"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Oktatóanyag: Az Azure Active Directoryhoz csatlakoztatott hibrid eszközök manuális konfigurálása

A Azure Active Directory (Azure AD) eszközkezelésével biztosíthatja, hogy a felhasználók olyan eszközökről férnek hozzá az ön erőforrásaihoz, amelyek megfelelnek a biztonsági és megfelelőségi szabványoknak. További információ: Bevezetés az [eszközkezelésbe a Azure Active Directory.](overview.md)

> [!TIP]
> Ha a Azure AD Connect a lehetőség, tekintse meg a [](hybrid-azuread-join-managed-domains.md) felügyelt vagy összevont tartományokra [vonatkozó oktatóanyagokat.](hybrid-azuread-join-federated-domains.md) A Azure AD Connect jelentősen leegyszerűsítheti a hibrid Azure AD-csatlakozás konfigurációját.

Ha helyszíni Active Directory-környezettel rendelkezik, és csatlakoztatni szeretné a tartományokhoz csatlakoztatott eszközeit az Azure AD-hoz, ezt hibrid Azure AD-csatlakoztatott eszközök konfigurálásával teheti meg. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hibrid Azure AD-csatlakozás manuális konfigurálása
> * Szolgáltatáskapcsolódási pont konfigurálása
> * Jogcímek kiállításának beállítása
> * A korábbi verziójú Windows-eszközök engedélyezése
> * Csatlakoztatott eszközök ellenőrzése
> * A megvalósítás hibaelhárítása

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy Ön ismeri a következőt:

* [Az Azure Active Directory eszközkezelésének alapjai](./overview.md)
* [A hibrid csatlakozás implementáció Azure Active Directory megterve](hybrid-azuread-join-plan.md)
* [Az eszközök hibrid Azure AD-csatlakozásának vezérlése](hybrid-azuread-join-control.md)

Mielőtt elkezdené engedélyezni a hibrid Azure AD-hez csatlakozott eszközöket a szervezetben, győződjön meg a következő szolgáltatásokról:

* Az alkalmazás egy naprakész verzióját Azure AD Connect.
* Azure AD Connect szinkronizálta az Azure AD-hez hibriden csatlakozni kívánt eszközök számítógép-objektumát. Ha a számítógép-objektumok adott szervezeti egységekhez (OM-khoz) tartoznak, ezeket a szervezeti egységeket is konfigurálni kell a szinkronizáláshoz a Azure AD Connect is.

Azure AD Connect:

* Megtartja a társítást a számítógépfiók között a helyi Active Directory-példányban és az eszközobjektum között az Azure AD-ban.
* Engedélyezi az eszközhöz kapcsolódó egyéb funkciókat, például a Vállalati Windows Hello.

Győződjön meg arról, hogy a következő URL-címek elérhetők a szervezet hálózatán belüli számítógépekről a számítógépek Azure AD-be való regisztrálásához:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com`
* A szervezet STS-ét (összevont tartományok esetén), amelynek szerepelnie kell a felhasználó helyi intranetbeállításai között

> [!WARNING]
> Ha a szervezet olyan proxykiszolgálókat használ, amelyek elfogják az SSL-forgalmat olyan helyzetekben, mint az adatveszteség-megelőzés vagy az Azure AD-bérlőkorlátozások, győződjön meg arról, hogy a () szolgáltatásba irányuló forgalom ki van zárva a https://device.login.microsoftonline.com TLS-törésből és -vizsgálatból. A kizárásának elmulasztása interferenciát okozhat az ügyfél-tanúsítvány hitelesítésében, ami az eszközregisztrációval és az eszközalapú feltételes https://device.login.microsoftonline.com hozzáféréssel kapcsolatos problémákat okozhat.

Ha a szervezet közvetlen egyszeri bejelentkezést tervez használni, az alábbi URL-címnek elérhetőnek kell lennie a szervezetben lévő számítógépekről. A felhasználó helyi intranetes zónájában is hozzá kell adni.

* `https://autologon.microsoftazuread-sso.com`

Emellett a következő beállításokat kell engedélyezni a felhasználó intranetes zónájában: „Az állapotsor szkriptekkel való frissítése.”

Ha a szervezet felügyelt (nem összevont) telepítést használ az helyi Active Directory-val, és nem használja az Active Directory összevonási szolgáltatások (AD FS) (AD FS) az Azure AD-val való összecsukását, akkor az Windows 10-hoz való hibrid Azure AD-csatlakozás az Active Directory számítógép-objektumaitól függ az Azure AD-val való szinkronizáláshoz. Győződjön meg arról, hogy az Azure AD-hez hibriden összekapcsolni szükséges számítógép-objektumokat tartalmazó minden OUs esetében engedélyezve van a szinkronizálás a Azure AD Connect konfigurációjában.

Az 1703-as vagy korábbi verziójú Windows 10-eszközök esetében, ha a szervezet kimenő proxyn keresztüli internet-hozzáférést igényel, a webproxy automatikus felderítését (WPAD) kell megvalósítania, hogy az Windows 10 számítógépek regisztrálva tudjanak az Azure AD-be.

Az 1803.Windows 10-től kezdődően, még ha egy összevont tartományban lévő eszköz hibrid Azure AD-beléptatási kísérlete sem sikerül az AD FS-ban, és a Azure AD Connect úgy van konfigurálva, hogy szinkronizálja a számítógépet/eszközobjektumokat az Azure AD-val, az eszköz megpróbálja befejezni a hibrid Azure AD-csatlakozást a szinkronizált számítógép/eszköz használatával.

> [!NOTE]
> Ahhoz, hogy az eszközregisztráció szinkronizálási csatlakozása sikeres legyen, az eszközregisztrációs konfiguráció részeként ne zárja ki az alapértelmezett eszközattribútumokat a szinkronizálási Azure AD Connect konfigurációból. Az Azure AD-be szinkronizált alapértelmezett eszközattribútumokkal kapcsolatos további információkért lásd a következővel szinkronizált [Azure AD Connect.](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized#windows-10)

Annak ellenőrzéséhez, hogy az eszköz hozzáfér-e a fenti Microsoft-erőforrásokhoz a rendszerfiók alatt, használhatja az Eszközregisztrációs kapcsolat [tesztelése szkriptet.](/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/)

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

Az eszközök szolgáltatáskapcsolati pont (SCP) objektumot használnak a regisztráció során az Azure AD-bérlő információinak felderítéséhez. A helyi Active Directory-példányban a hibrid Azure AD-hez csatlakozott eszközök SCP-objektumának léteznie kell a számítógép erdőjéhez a konfigurációs névhasználati környezet partíciójában. Erdőnként egy konfigurációs névhasználati környezet létezik. Többerdős konfigurációban Active Directory szolgáltatáskapcsolati pontnak minden olyan erdőben léteznie kell, amely tartományhoz csatlakozó számítógépeket tartalmaz.

Használja a [**Get-ADRootDSE**](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee617246(v=technet.10)) parancsmagot az erdő konfigurációs névhasználati környezetének lekéréséhez.  

A *fabrikam.com* Active Directory-tartománynevű erdő konfigurációs névhasználati környezete a következő:

`CN=Configuration,DC=fabrikam,DC=com`

A tartományokhoz csatlakoztatott eszközök automatikus regisztrációjának SCP-objektuma a következő helyen található a saját erdőjében:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Attól függően, hogy hogyan telepítette Azure AD Connect, előfordulhat, hogy az SCP-objektum már konfigurálva van.
Az objektum meglétének ellenőrzéséhez és a felderítési értékek lekéréséhez használja a következő Windows PowerShell szkriptet:

   ```PowerShell
   $scp = New-Object System.DirectoryServices.DirectoryEntry;

   $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

   $scp.Keywords;
   ```

A **$scp. A kulcsszavak** kimenete az Azure AD-bérlő adatait jeleníti meg. Bemutatunk egy példát:

   ```
   azureADName:microsoft.com
   azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47
   ```

Ha a szolgáltatáskapcsolódási pont nem létezik, a létrehozásához futtassa az `Initialize-ADSyncDomainJoinedComputerSync` parancsmagot az Azure AD Connect-kiszolgálóján. A parancsmag futtatásához vállalati rendszergazdai hitelesítő adatok szükségesek.  

A parancsmag:

* Létrehozza a szolgáltatáskapcsolati pontot abban Active Directory erdőben, Azure AD Connect csatlakozik.
* Az `AdConnectorAccount` paraméter megadása szükséges hozzá. Ez a fiók lesz az összekötő Active Directory a Azure AD Connect.


Az alábbi szkriptben egy példa látható a parancsmag használatára. A szkript `$aadAdminCred = Get-Credential` eleme esetében egy felhasználónév megadása szükséges. A felhasználónevet egyszerű felhasználónév (UPN) formátumban kell megadni (`user@example.com`).

   ```PowerShell
   Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

   $aadAdminCred = Get-Credential;

   Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;
   ```

Az `Initialize-ADSyncDomainJoinedComputerSync` parancsmag:

* Az Active Directory PowerShell-modult és Active Directory Domain Services (AD DS) eszközöket használja. Ezek az eszközök Active Directory tartományvezérlőn futó webszolgáltatásokra támaszkodnak. Az Active Directory webszolgáltatások Windows Server 2008 R2 vagy újabb rendszerű tartományvezérlőkön támogatott.
* Kizárólag az MSOnline PowerShell modul 1.1.166.0-s verziója támogatja. A modul letöltéséhez használja ezt [a hivatkozást.](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)
* Ha a AD DS eszközök nincsenek telepítve, `Initialize-ADSyncDomainJoinedComputerSync` a sikertelen lesz. A kiszolgálókezelő AD DS a Szerepkör-felügyeleti eszközök szolgáltatások Távoli kiszolgálófelügyelet eszközei  >    >  **keresztül telepítheti.**

Windows Server 2008 vagy korábbi verziójú tartományvezérlők esetén használja a következő szkriptet a szolgáltatáskapcsolati pont létrehozásához. Többerdős konfigurációban a következő szkript használatával hozza létre a szolgáltatáskapcsolati pontot minden olyan erdőben, ahol számítógépek vannak.

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

Az előző szkriptben `$verifiedDomain = "contoso.com"` a egy helyőrző. Cserélje le az egyik ellenőrzött tartománynévre az Azure AD-ban. Használat előtt a tartománynak a tulajdonában kell lennie.

Az ellenőrzött tartománynevekkel kapcsolatos további információkért lásd: Egyéni tartománynév hozzáadása [a Azure Active Directory.](../fundamentals/add-custom-domain.md)

Az ellenőrzött vállalati tartományok listáját a [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain) parancsmaggal kaphatja meg.

![Vállalati tartományok listája](./media/hybrid-azuread-join-manual/01.png)

## <a name="set-up-issuance-of-claims"></a>Jogcímek kiállításának beállítása

Összevont Azure AD-konfigurációkban az eszközök egy Microsoft AD FS partnertől származó virtuális vagy helyszíni összevonási szolgáltatásra támaszkodnak az Azure AD-hitelesítéshez. Az eszközök a hitelesítés révén kapnak hozzáférési jogkivonatokat, amelyekkel regisztrálhatnak az Azure Active Directory eszközregisztrációs szolgáltatásába (Azure DRS).

A jelenlegi Windows-eszközök hitelesítése integrált Windows-hitelesítés helyszíni összevonási szolgáltatás által üzemeltetett aktív WS-Trust-végponttal (1.3-as vagy 2005-ös verzió) történik.

A végpontok AD FS engedélyeznie kell a következő WS-Trust végpontokat
- `/adfs/services/trust/2005/windowstransport`
- `/adfs/services/trust/13/windowstransport`
- `/adfs/services/trust/2005/usernamemixed`
- `/adfs/services/trust/13/usernamemixed`
- `/adfs/services/trust/2005/certificatemixed`
- `/adfs/services/trust/13/certificatemixed`

> [!WARNING]
> Az **adfs/services/trust/2005/windowstransport** és az **adfs/services/trust/13/windowstransport** csak intranetes végpontként kell engedélyezni, és NEM szabad extranetes végpontként elérhetővé tenni a Web alkalmazásproxy. További információ a Windows-végpontok WS-Trust letiltásához: [WindowsWS-Trust végpont letiltása a proxyn.](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet) A szolgáltatásvégpontokkal kapcsolatos AD FS konzolján **láthatja,** hogy milyen  >  **végpontok vannak engedélyezve.**

> [!NOTE]
>Ha nem használja AD FS-t helyszíni összevonási szolgáltatásként, kövesse a szállító utasításait, és győződjön meg arról, hogy támogatják az WS-Trust 1.3-as vagy 2005-ös végpontokat, és hogy ezeket a metaadat-cserélő fájlon (MEX) keresztül teszi közzé.

Az eszközregisztráció befejezéséhez a következő jogcímnek kell lennie az Azure DRS által kapott jogkivonatban. Az Azure DRS létrehoz egy eszközobjektumot az Azure AD-ban ezen információk egy része alapján. Azure AD Connect ezt az információt felhasználva társítja az újonnan létrehozott eszközobjektumot a helyszíni számítógépfiókkal.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Ha több ellenőrzött tartománynévvel rendelkezik, a következő jogcímeket kell megadnia a számítógépekhez:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Ha már egy ImmutableID jogcímet ad ki (például a vagy egy másik attribútumot az ImmutableID forrásértékeként), meg kell adnia egy megfelelő jogcímet a számítógépek `mS-DS-ConsistencyGuid` számára:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

A következő szakaszokban az alábbiakról talál információt:

* Az egyes jogcímek által tartalmazott értékek.
* Hogyan nézne ki egy definíció a AD FS.

A meghatározás segít ellenőrizni, hogy az értékek léteznek-e már, vagy még létre kell őket hozni.

> [!NOTE]
> Ha helyszíni összevonási kiszolgálóként nem az AD FS-t használja, kövesse a szállítója utasításait a jogcímek kiállításához megfelelő konfiguráció létrehozásához.

### <a name="issue-account-type-claim"></a>Fióktípusra vonatkozó jogcím kiállítása

A `http://schemas.microsoft.com/ws/2012/01/accounttype` jogcímnek tartalmaznia kell a **DJ értéket,** amely tartományhoz csatlakozott számítógépként azonosítja az eszközt. Az AD FS-ben hozzáadhat egy kiállítási átalakítószabályt, amely a következőképpen néz ki:

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

A jogcímnek tartalmaznia kell a helyszíni `http://schemas.microsoft.com/identity/claims/onpremobjectguid` számítógépfiók **objectGUID** értékét. Az AD FS-ben hozzáadhat egy kiállítási átalakítószabályt, amely a következőképpen néz ki:

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

A jogcímnek tartalmaznia kell a helyszíni `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` számítógépfiók **objectSid** értékét. Az AD FS-ben hozzáadhat egy kiállítási átalakítószabályt, amely a következőképpen néz ki:

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

### <a name="issue-issuerid-for-the-computer-when-multiple-verified-domain-names-are-in-azure-ad"></a>A számítógép kiállítóazonosítójának kiállítása, ha több ellenőrzött tartománynév található az Azure AD-ban

A jogcímnek tartalmaznia kell Uniform Resource Identifier jogkivonatot kiállító helyszíni összevonási szolgáltatáshoz (AD FS vagy partnerhez) kapcsoló ellenőrzött tartománynevek `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` URI-ját. Ebben AD FS az előzőek után hozzáadhat olyan kiállítási átalakítási szabályokat, amelyek az alábbihoz hasonlók az adott sorrendben. Vegye figyelembe, hogy a szabály felhasználók számára való explicit kiadására vonatkozó egyik szabályra van szükség. A következő szabályokban egy, a felhasználó és a számítógép hitelesítését azonosító első szabály lesz hozzáadva.

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

Az előző jogcímben `<verified-domain-name>` a egy helyőrző. Cserélje le egy ellenőrzött tartománynévre az Azure AD-ban. Használja például a `Value = "http://contoso.com/adfs/services/trust/"` következőt: .

Az ellenőrzött tartománynevekkel kapcsolatos további információkért lásd: Egyéni tartománynév hozzáadása [a Azure Active Directory.](../fundamentals/add-custom-domain.md)  

Az ellenőrzött vállalati tartományok listáját a [Get-MsolDomain](/powershell/module/msonline/get-msoldomain) parancsmaggal kaphatja meg.

![Vállalati tartományok listája](./media/hybrid-azuread-join-manual/01.png)

### <a name="issue-immutableid-for-the-computer-when-one-for-users-exists-for-example-using-ms-ds-consistencyguid-as-the-source-for-immutableid"></a>A számítógép ImmutableID-jának kiállítása, ha létezik egy a felhasználók számára (például az mS-DS-ConsistencyGuid használata az ImmutableID forrásaként)

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

A következő szkript segít a korábban ismertetett kiállítási átalakítási szabályok létrehozásában.

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

* A szkript hozzáfűzi a szabályokat a meglévő szabályokhoz. Ne futtassa kétszer a szkriptet, mert a szabálykészlet kétszer lesz hozzáadva. A szkript újbóli futtatása előtt ellenőrizze, hogy a jogcímekre nem vonatkoznak kapcsolódó szabályok (a kapcsolódó feltételek mellett).
* Ha több ellenőrzött tartománynévvel is (az Azure AD portálon vagy a **Get-MsolDomain** parancsmagon keresztül) van, állítsa a **szkriptben** a $multipleVerifiedDomainNames értékét a következőre: **$true.** Győződjön meg arról is, hogy eltávolít minden meglévő **issuerid** jogcímet, amelyet a Azure AD Connect más módon hozott létre. Példa erre a szabályra:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
   => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 
   ```

Ha a felhasználói fiókokhoz már kiállított egy **ImmutableID** jogcímet, állítsa az **$immutableIDAlreadyIssuedforUsers** értékét a **$true** értékre a szkriptben.

## <a name="enable-windows-down-level-devices"></a>A korábbi verziójú Windows-eszközök engedélyezése

Ha a tartományhoz csatlakoztatott eszközök között korábbi verziójú Windows-eszközök is vannak, végezze el a következőket:

* Állítson be egy szabályzatot az Azure AD-ben, amellyel a felhasználók eszközeinek regisztrációját engedélyezheti.
* Konfigurálja a helyszíni összevonási szolgáltatást arra, hogy jogcímek kiállításával támogassa az integrált Windows-hitelesítést (IWA) az eszközök regisztrációjához.
* Adja hozzá az Azure AD-eszközhitelesítési végpontot a helyi intranetes zónákhoz, hogy elkerülje a tanúsítványkérelmeket az eszköz hitelesítése során.
* A windowsos eszközök szabályozása.

### <a name="set-a-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Szabályzat beállítása az Azure AD-ban annak engedélyezéséhez, hogy a felhasználók regisztrálják az eszközöket

A windowsos eszközök regisztrálásához győződjön meg arról, hogy engedélyezve van a beállítás, amely lehetővé teszi a felhasználók számára az eszközök regisztrálását az Azure AD-ban. A Azure Portal ezt a beállítást a Felhasználók **és** csoportok eszközbeállítások Azure Active Directory  >    >  **alatt találja.**

A következő szabályzatot a Minden: A felhasználók regisztrálhatja eszközeiket az **Azure AD-ban beállításra kell állítani.**

![A Minden gomb, amely lehetővé teszi a felhasználók számára az eszközök regisztrálását](./media/hybrid-azuread-join-manual/23.png)

### <a name="configure-the-on-premises-federation-service"></a>A helyszíni összevonási szolgáltatás konfigurálása

A helyszíni összevonási szolgáltatásnak támogatnia kell az **authenticationmethod** és **a wiaormultiauthn** jogcímek kiadását, amikor hitelesítési kérést kap egy resource_params-paramétert tároló Azure AD-függő félhez az alábbi kódolt értékkel:

   ```
   eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

   which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}
   ```

Ilyen kérés esetén a helyszíni összevonási szolgáltatásnak hitelesítenie kell a felhasználót a integrált Windows-hitelesítés. Ha a hitelesítés sikeres, az összevonási szolgáltatásnak ki kell adnunk a következő két jogcímet:

   `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows` `http://schemas.microsoft.com/claims/wiaormultiauthn`

A AD FS hozzá kell adni egy kiállítási átalakítási szabályt, amely áthalad a hitelesítési módszeren. A szabály hozzáadásához:

1. Az AD FS felügyeleti konzolon a **Megbízhatósági kapcsolatok AD FS** megbízható függőben  >    >  **van.**
1. Kattintson a jobb gombbal a Microsoft Office 365 Identity Platform megbízható függő entitás elemre, és válassza a **Jogcímszabályok szerkesztése** lehetőséget.
1. A **Kiállítási átalakítószabályok** lapon válassza a **Szabály hozzáadása** lehetőséget.
1. A **Jogcímszabály** sablonlistában válassza a **Jogcímek küldése egyéni szabállyal** lehetőséget.
1. Kattintson a **Tovább** gombra.
1. A **Jogcímszabály neve mezőbe írja** be az **Auth metódus jogcímszabálya nevet.**
1. A **Jogcímszabály mezőbe** írja be a következő szabályt:

   `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

1. Az összevonási kiszolgálón írja be a következő PowerShell-parancsot. Cserélje le a helyére az Azure AD függő függő fél **\<RPObjectName\>** megbízhatósági objektumának függő fél objektumának nevét. Ezen objektum neve általában **Microsoft Office 365 Identity Platform**.

   `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-endpoint-to-the-local-intranet-zones"></a>Az Azure AD-eszköz hitelesítési végpontjának hozzáadása a helyi intranetes zónákhoz

Ha el szeretne kerülni a tanúsítványkérelmeket, amikor a regisztrált eszközök felhasználói hitelesítik magukat az Azure AD-ben, lekkulálhat egy házirendet a tartományhoz csatlakozott eszközökre, hogy a következő URL-címet adja hozzá a helyi intranetes zónához a Internet Explorer:

`https://device.login.microsoftonline.com`

### <a name="control-windows-down-level-devices"></a>A korábbi verziójú Windows-eszközök vezérlése

A korábbi verziójú Windows-eszközök regisztrálásához a Letöltőközpontból le kell töltenie, majd telepítenie kell egy Windows Installer-csomagot (.msi). További információkért lásd a hibrid Azure AD-csatlakozás szabályozott érvényesítését a windowsos, alacsony szintű [eszközökön című szakaszt.](hybrid-azuread-join-control.md#controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices)

## <a name="verify-joined-devices"></a>Csatlakoztatott eszközök ellenőrzése

3 módszer az eszköz állapotának megkeresére és ellenőrzésére:

### <a name="locally-on-the-device"></a>Helyileg az eszközön

1. Nyissa meg a Windows PowerShellt.
2. Írja be a következő szöveget: `dsregcmd /status`.
3. Ellenőrizze, hogy **az AzureAdJoined** és **a DomainJoined** is **YES (IGEN) beállításra van-e állítva.**
4. A **DeviceId** használatával összehasonlíthatja a szolgáltatás állapotát a Azure Portal PowerShell használatával.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

1. A közvetlen hivatkozással kattintson az Eszközök [lapra.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices)
2. Az eszközök keresésével kapcsolatos információkért keresse meg az Eszköz identitásának kezelése [a](./device-management-azure-portal.md#manage-devices)Azure Portal.
3. Ha a **Regisztrált oszlopban** **a Függőben érték látható,** akkor a hibrid Azure AD-csatlakozás még nem fejeződött be. Összevont környezetekben ez csak akkor fordulhat elő, ha nem sikerült regisztrálnia, és az AAD Connect konfigurálva van az eszközök szinkronizálásához.
4. Ha a **Regisztrált oszlop** **dátum/idő** értékeket tartalmaz, akkor a hibrid Azure AD-csatlakozás befejeződött.

### <a name="using-powershell"></a>A PowerShell használata

Ellenőrizze az eszközregisztráció állapotát az Azure-bérlőben a **[Get-MsolDevice használatával.](/powershell/module/msonline/get-msoldevice)** Ez a parancsmag a [Azure Active Directory PowerShell-modulban található.](/powershell/azure/active-directory/install-msonlinev1)

Ha a **Get-MSolDevice** parancsmagot használja a szolgáltatás részleteinek ellenőrzéshez:

- Léteznie kell egy **objektumnak,** amely a Windows-ügyfélen található azonosítóval egyezik az eszközazonosítóval.
- A **DeviceTrustType értéke** **Tartományhoz csatlakozik.** Ez a beállítás megegyezik az Azure AD portál Eszközök lapján található Hibrid **Azure AD-hez** csatlakozott állapotával. 
- A feltételes hozzáférésben használt eszközök esetében az **Engedélyezve** értéke **Igaz,** a **DeviceTrustLevel** pedig **Felügyelt.**

1. Nyissa meg a Windows PowerShellt rendszergazdaként.
2. Az `Connect-MsolService` Azure-bérlőhöz való csatlakozáshoz írja be a(a) et.

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>Az összes hibrid Azure AD-hez csatlakozott eszköz száma (a **függőben lévő állapot kivételével)**

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Az összes Függő állapotban lévő hibrid Azure **AD-hez csatlakozott eszköz száma**

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>Az összes hibrid Azure AD-hez csatlakozott eszköz felsorolása

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Az összes Függő állapotban lévő hibrid Azure AD-hez **csatlakozott eszköz felsorolása**

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>Egyetlen eszköz részleteinek felsorolása:

1. Írja be a következőt: (Ez az eszközön `get-msoldevice -deviceId <deviceId>` helyileg lekért **DeviceId).**
2. Ellenőrizze, hogy az **Engedélyezve** beállításhoz az **Igaz** érték van-e megadva.

## <a name="troubleshoot-your-implementation"></a>A megvalósítás hibaelhárítása

Ha problémákat tapasztal a tartományhoz csatlakozott Windows-eszközök hibrid Azure AD-csatlakozása során, tekintse meg a következőt:

- [Eszközök hibaelhárítása a dsregcmd paranccsal](./troubleshoot-device-dsregcmd.md)
- [Az Azure Active Directoryhoz csatlakoztatott hibrid eszközök hibaelhárítása](troubleshoot-hybrid-join-windows-current.md)
- [Hibrid hálózati Azure Active Directory, lefelé csatlakozott eszközök hibaelhárítása](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Következő lépések

* [Az Azure Active Directory eszközkezelésének alapjai](overview.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual/12.png

---
title: Egyéni házirendek kezelése a PowerShell-lel
titleSuffix: Azure AD B2C
description: Használja az Azure Active Directory (Azure AD) PowerShell-parancsmagot a Azure AD B2C egyéni szabályzatok programozott felügyeletéhez. Egyéni szabályzatok létrehozása, olvasása, frissítése és törlése a PowerShell-lel.
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fcc482e215e646fec20516f35641bd05398d2f2d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96928715"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Azure AD B2C egyéni házirendek kezelése a Azure PowerShell

A Azure PowerShell számos parancsmagot biztosít a parancssori és parancsfájl-alapú egyéni házirendek kezeléséhez a Azure AD B2C-bérlőben. Ismerje meg, hogyan használhatja az Azure AD PowerShell-modult a következőhöz:

* Egyéni szabályzatok listázása egy Azure AD B2C-bérlőben
* Szabályzat letöltése a bérlőről
* Meglévő szabályzat frissítése a tartalom felülírásával
* Új szabályzat feltöltése a Azure AD B2C-bérlőbe
* Egyéni szabályzat törlése a bérlőből

## <a name="prerequisites"></a>Előfeltételek

* [Azure ad B2C bérlő](tutorial-create-tenant.md)és a címtárbeli felhasználó hitelesítő adatai a [B2C IEF házirend-rendszergazdai](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) szerepkörrel
* A bérlőre feltöltött [Egyéni szabályzatok](custom-policy-get-started.md)
* [Azure AD PowerShell a Graph **Preview modulhoz**](/powershell/azure/active-directory/install-adv2)

## <a name="connect-powershell-session-to-b2c-tenant"></a>PowerShell-munkamenet összekötése a B2C-Bérlővel

Ha egyéni szabályzatokkal szeretne dolgozni a Azure AD B2C-bérlőben, először össze kell kapcsolni a PowerShell-munkamenetet a bérlőhöz a [AzureAD][Connect-AzureAD] parancs használatával.

Hajtsa végre a következő parancsot a `{b2c-tenant-name}` Azure ad B2C bérlő nevével való helyettesítéssel. Jelentkezzen be egy olyan fiókkal, amely a [B2C IEF házirend-rendszergazdai](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) szerepkörhöz van rendelve a címtárban.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

Példa a sikeres bejelentkezést bemutató parancs kimenetére:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>A bérlő összes egyéni szabályzatának listázása

Az egyéni házirendek felfedése lehetővé teszi, hogy egy Azure AD B2C rendszergazda áttekintse, kezelje és adja hozzá az üzleti logikát a műveleteik végrehajtásához. A [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] parancs használatával visszaállíthatja egy Azure ad B2C-bérlőben lévő egyéni házirendek azonosítóinak listáját.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

Példa a parancs kimenetére:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>Szabályzat letöltése

A szabályzat-azonosítók listájának áttekintése után megcélozhat egy adott szabályzatot a [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] használatával a tartalom letöltéséhez.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

Ebben a példában a *B2C_1A_SIGNUP_SIGNIN* azonosítójú házirend le van töltve:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

A szabályzat tartalmának helyi szerkesztéséhez a parancs kimenetét egy argumentummal rendelkező fájlba kell átadnia, `-OutputFilePath` majd meg kell nyitnia a fájlt a kedvenc szerkesztőjében.

Példa a kimenet fájlba küldésére:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>Meglévő szabályzat frissítése

A létrehozott vagy letöltött házirendfájl szerkesztése után a [set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] parancs használatával közzéteheti a frissített szabályzatot a Azure ad B2C.

Ha a `Set-AzureADMSTrustFrameworkPolicy` parancsot egy olyan házirend azonosítójával állítja ki, amely már szerepel a Azure ad B2C-bérlőben, a rendszer felülírja a házirend tartalmát.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Példa parancs:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

További példákat a [set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] parancs hivatkozása tartalmaz.

## <a name="upload-a-new-policy"></a>Új szabályzat feltöltése

Ha olyan egyéni házirendet módosít, amely éles környezetben fut, előfordulhat, hogy a szabályzat több verzióját is közzé szeretné tenni tartalék vagy A/B tesztelési helyzetekben. Vagy előfordulhat, hogy másolatot szeretne készíteni egy meglévő szabályzatról, néhány kis módosítással módosítja azt, majd feltölti egy másik alkalmazás által használt új szabályzatként.

Új szabályzat feltöltéséhez használja a [New-AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] parancsot:

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Példa parancs:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>Egyéni szabályzat törlése

A tiszta működés életciklusának fenntartása érdekében javasoljuk, hogy rendszeresen távolítsa el a nem használt egyéni házirendeket. Előfordulhat például, hogy el szeretné távolítani a régi házirend-verziókat, miután elvégezte az áttelepítést a házirendek új készletére, és ellenőrzi az új szabályzatok funkcióit. Emellett, ha egyéni szabályzatok készletének közzétételét kísérli meg, és hibaüzenetet kap, érdemes lehet eltávolítani a sikertelen kiadás részeként létrehozott házirendeket.

A [Remove-AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] paranccsal törölhet egy szabályzatot a bérlőből.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

Példa parancs:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>Házirend feltöltésével kapcsolatos hibák

Amikor új egyéni házirendet próbál közzétenni, vagy egy meglévő házirendet frissít, a szabályzatok öröklődési láncában nem megfelelő XML-formázás és hibák érvényesítési hibákat okozhatnak.

Például egy olyan szabályzat frissítésére történt kísérlet, amely helytelen formátumú XML-fájlt tartalmaz (a kimenet rövidítve van):

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

További információ az egyéni házirendek hibaelhárításáról: [Azure ad B2C egyéni szabályzatok és az identitások felhasználói felületének](./troubleshoot-custom-policies.md)hibaelhárítása.

## <a name="next-steps"></a>Következő lépések

További információ arról, hogyan használható a PowerShell az egyéni házirendek folyamatos integrációs/folyamatos teljesítési (CI/CD) folyamat részeként történő telepítéséhez: [Egyéni szabályzatok telepítése Azure DevOps-folyamatból](deploy-custom-policies-devops.md).

<!-- LINKS - External -->
[Connect-AzureAD]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/set-azureadmstrustframeworkpolicy

---
title: Azure Active Directory beállítása az ügyfél-hitelesítéshez
description: Megtudhatja, hogyan állíthatja be Azure Active Directory (Azure AD) a Service Fabric-fürtökhöz tartozó ügyfelek hitelesítéséhez.
ms.topic: conceptual
ms.date: 6/28/2019
ms.openlocfilehash: a1f89e144f9cef12c5bff87befb00a88bad8d7d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102215969"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Azure Active Directory beállítása az ügyfél-hitelesítéshez

Az Azure-on futó fürtök esetében a Azure Active Directory (Azure AD) használata ajánlott a felügyeleti végpontokhoz való hozzáférés biztosításához. Ez a cikk azt ismerteti, hogyan telepítheti az Azure AD-t egy Service Fabric-fürt ügyfeleinek hitelesítésére.

Ebben a cikkben az "alkalmazás" kifejezést fogjuk használni [Azure Active Directory alkalmazásokra](../active-directory/develop/developer-glossary.md#client-application), nem Service Fabric alkalmazásokra; a különbséget szükség esetén a rendszer elvégzi. Az Azure AD lehetővé teszi, hogy a szervezetek (más néven bérlők) az alkalmazásokhoz való felhasználói hozzáférést kezeljék.

A Service Fabric-fürtök több belépési pontot biztosítanak a felügyeleti funkcióihoz, beleértve a webalapú [Service Fabric Explorer][service-fabric-visualizing-your-cluster] és a [Visual studiót][service-fabric-manage-application-in-visual-studio]is. Ennek eredményeképpen két Azure AD-alkalmazást fog létrehozni a fürt elérésének vezérléséhez: egy webalkalmazást és egy natív alkalmazást. Az alkalmazások létrehozása után a felhasználókat csak olvasási és rendszergazdai szerepkörökhöz rendelheti hozzá.

> [!NOTE]
> Linux rendszeren a fürt létrehozása előtt végre kell hajtania a következő lépéseket. Windows rendszeren lehetősége van az [Azure ad-hitelesítés konfigurálására is egy meglévő fürthöz](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/Configure%20Azure%20Active%20Directory%20Authentication%20for%20Existing%20Cluster.md).

> [!NOTE]
> [Ismert probléma](https://github.com/microsoft/service-fabric/issues/399) , hogy a Linux HRE-kompatibilis fürtökön futó alkalmazások és csomópontok nem tekinthetők meg az Azure Portalon.



## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy már létrehozott egy bérlőt. Ha nem, először olvassa el [Azure Active Directory bérlő beszerzését ismertető témakört][active-directory-howto-tenant].

Az Azure AD Service Fabric-fürttel való konfigurálásának néhány lépésének leegyszerűsítése érdekében létrehoztunk egy Windows PowerShell-szkriptet.

1. [A tárház klónozása](https://github.com/Azure-Samples/service-fabric-aad-helpers) a számítógépre.
2. [Győződjön meg arról, hogy rendelkezik](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) a telepített parancsfájlokra vonatkozó összes előfeltételsel.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Azure AD-alkalmazások létrehozása és felhasználók szerepkörökhöz rendelése

A szkriptek segítségével két Azure AD-alkalmazást hozhat létre a fürthöz való hozzáférés vezérléséhez: egy webalkalmazást és egy natív alkalmazást. Miután létrehozta az alkalmazásokat a fürt képviseletére, a [Service Fabric által támogatott szerepkörökhöz](service-fabric-cluster-security-roles.md)hozza létre a felhasználókat: csak olvasható és rendszergazda.

Futtassa `SetupApplications.ps1` a parancsot, és adja meg a bérlő azonosítóját, a fürt nevét és a webalkalmazás válaszának URL-címét paraméterként.  Felhasználóneveket és jelszavakat is meg kell adnia a felhasználók számára. Például:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> A nemzeti felhők (például Azure Government, Azure China, Azure Germany) esetében meg kell adnia a `-Location` paramétert is.

A *TenantId* a PowerShell-parancs végrehajtásával találhatja meg `Get-AzureSubscription` . A parancs végrehajtása az összes előfizetés TenantId jeleníti meg.

A *ClusterName* a parancsfájl által létrehozott Azure ad-alkalmazások előtagját használja. Nem kell pontosan megegyeznie a fürt tényleges nevével. Kizárólag arra szolgál, hogy megkönnyítse az Azure AD-összetevők leképezését arra a Service Fabric-fürtre, amelyen a használatban van.

A *WebApplicationReplyUrl* az az alapértelmezett végpont, amelyet az Azure ad visszaküld a felhasználóknak a bejelentkezés befejezését követően. Állítsa ezt a végpontot a fürt Service Fabric Explorer végpontjának. Ha egy meglévő fürtöt jelölő Azure AD-alkalmazásokat hoz létre, győződjön meg arról, hogy az URL-cím megfelel a meglévő fürt végpontjának. Ha új fürthöz hoz létre alkalmazásokat, tervezze meg a fürt által használt végpontot, és győződjön meg arról, hogy nem használja a meglévő fürt végpontját. Alapértelmezés szerint a Service Fabric Explorer végpont:

https:// &lt; cluster_domain &gt; : 19080/Explorer

A rendszer felszólítja, hogy jelentkezzen be egy olyan fiókba, amely rendszergazdai jogosultságokkal rendelkezik az Azure AD-bérlőhöz. A bejelentkezést követően a parancsfájl létrehozza a webes és natív alkalmazásokat, hogy az Service Fabric-fürtöt képviseljék. Ha megtekinti a bérlő alkalmazásait a [Azure Portalban][azure-portal], két új bejegyzést kell látnia:

   * *ClusterName* \_ Fürt
   * *ClusterName* \_ Ügyfél

A parancsfájl kinyomtatja a Azure Resource Manager sablon által a HRE- [kompatibilis fürt létrehozásakor](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access)szükséges JSON-t, ezért érdemes megnyitnia a PowerShell ablakát.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Hibaelhárítási súgó az Azure Active Directory konfigurálásához
Az Azure AD beállítása és használata kihívást jelenthet, így a probléma megoldásához szükséges néhány mutatót láthat.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer kéri a tanúsítvány kiválasztását
#### <a name="problem"></a>Probléma
Miután Service Fabric Explorer sikeresen bejelentkezett az Azure AD-be, a böngésző visszatér a kezdőlapra, de egy üzenet arra kéri, hogy válasszon ki egy tanúsítványt.

![SFX tanúsítvány párbeszédpanel][sfx-select-certificate-dialog]

#### <a name="reason"></a>Ok
A felhasználó nem rendel hozzá szerepkört az Azure AD-fürt alkalmazásban. Így az Azure AD-hitelesítés meghiúsul Service Fabric fürtön. Service Fabric Explorer visszatér a tanúsítványalapú hitelesítéshez.

#### <a name="solution"></a>Megoldás
Kövesse az Azure AD beállításával és a felhasználói szerepkörök hozzárendelésével kapcsolatos utasításokat. Azt is javasoljuk, hogy kapcsolja be a "felhasználói hozzárendelés szükséges az alkalmazás eléréséhez" beállítást `SetupApplications.ps1` .

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>A PowerShell-lel való kapcsolat hibával meghiúsul: "a megadott hitelesítő adatok érvénytelenek"
#### <a name="problem"></a>Probléma
Ha a PowerShell használatával csatlakozik a fürthöz a "AzureActiveDirectory" biztonsági mód használatával, miután sikeresen bejelentkezett az Azure AD-be, a kapcsolat meghiúsul a következő hibával: "a megadott hitelesítő adatok érvénytelenek."

#### <a name="solution"></a>Megoldás
Ez a megoldás megegyezik az előzővel.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>A Service Fabric Explorer a bejelentkezéskor hibát ad vissza: "AADSTS50011"
#### <a name="problem"></a>Probléma
Amikor Service Fabric Explorerban próbál bejelentkezni az Azure AD-be, a lap egy hibát ad vissza: "AADSTS50011: a Válaszcím &lt; URL-címe &gt; nem egyezik meg az alkalmazáshoz konfigurált válasz címével: &lt; GUID &gt; ."

![Az SFX-válasz címe nem egyezik][sfx-reply-address-not-match]

#### <a name="reason"></a>Ok
A fürt (web) alkalmazás, amely Service Fabric Explorer az Azure AD-vel való hitelesítésre irányuló kísérleteket jelképezi, és a kérelem részeként az átirányítási visszaküldési URL-címet adja meg. Az URL-cím azonban nem szerepel az Azure AD **-alkalmazás válaszának URL-címe** listán.

#### <a name="solution"></a>Megoldás
A fürthöz tartozó Azure AD-alkalmazás regisztrálása lapon válassza a **hitelesítés** lehetőséget, majd az URI-k **átirányítása** szakaszban adja meg a lista Service Fabric Explorer URL-címét. Mentse a változást.

![Webalkalmazás válaszának URL-címe][web-application-reply-url]

### <a name="connecting-to-the-cluster-using-azure-ad-authentication-via-powershell-gives-an-error-when-you-sign-in-aadsts50011"></a>Ha az Azure AD-hitelesítéssel a PowerShell használatával csatlakozik a fürthöz, hibaüzenetet ad a bejelentkezéskor: "AADSTS50011"
#### <a name="problem"></a>Probléma
Amikor egy Service Fabric-fürthöz próbál csatlakozni az Azure AD-vel a PowerShell használatával, a bejelentkezési oldal hibát jelez: "AADSTS50011: a kérelemben megadott válasz URL-cím nem egyezik az alkalmazáshoz konfigurált válasz URL-címekkel: &lt; GUID &gt; ."

#### <a name="reason"></a>Ok
Az előző hibához hasonlóan a PowerShell megpróbálja hitelesíteni az Azure AD-t, amely egy átirányítási URL-címet ad meg, amely nem szerepel az Azure AD **-alkalmazás válaszának URL-címei** listán.  

#### <a name="solution"></a>Megoldás
Ugyanazt a folyamatot használja, mint az előző probléma, de az URL-címet a `urn:ietf:wg:oauth:2.0:oob` következőre kell beállítani: speciális átirányítás a parancssori hitelesítéshez.

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>A fürt összekötése az Azure AD-hitelesítéssel a PowerShell segítségével
A Service Fabric-fürt összekapcsolásához használja a következő PowerShell-parancsot:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

További információ: a [ServiceFabricCluster parancsmag](/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Felhasználhatom ugyanazt az Azure AD-bérlőt több fürtben?
Igen. Azonban ne felejtse el felvenni a Service Fabric Explorer URL-címét a fürt (webes) alkalmazásba. Ellenkező esetben a Service Fabric Explorer nem működik.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Miért van szükség a kiszolgálói tanúsítványra, amíg az Azure AD engedélyezve van?
A FabricClient és a FabricGateway kölcsönös hitelesítést hajt végre. Az Azure AD-hitelesítés során az Azure AD-integráció ügyfél-identitást biztosít a kiszolgálónak, és a kiszolgáló tanúsítványát az ügyfél használja a kiszolgáló identitásának ellenőrzéséhez. További információ a Service Fabric tanúsítványokról: [X. 509 tanúsítványok és Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Következő lépések
Azure Active Directory alkalmazások beállítása és szerepkörök beállítása a felhasználók számára, [a fürt konfigurálása és üzembe helyezése](service-fabric-cluster-creation-via-arm.md).


<!-- Links -->
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png

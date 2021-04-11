---
title: Enterprise Security Package-fürtök létrehozása, konfigurálása – Azure
description: Ismerje meg, hogyan hozhat létre és konfigurálhat Enterprise Security Package-fürtöket az Azure HDInsight
services: hdinsight
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/10/2019
ms.openlocfilehash: 3e6a5742d2dc8079c36f2d9f39a827f8db99cd67
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867168"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Enterprise Security Package-fürtök létrehozása és konfigurálása az Azure HDInsight

Az Azure HDInsight Enterprise Security Package (ESP) hozzáférést biztosít Active Directory-alapú hitelesítéshez, többfelhasználós támogatáshoz és szerepköralapú hozzáférés-vezérléshez az Azure-beli Apache Hadoop-fürtökhöz. A HDInsight ESP-fürtök lehetővé teszik a szigorú vállalati biztonsági szabályzatoknak megfelelő szervezetek számára a bizalmas adatok biztonságos feldolgozását.

Ez az útmutató bemutatja, hogyan hozhat létre ESP-kompatibilis Azure HDInsight-fürtöt. Azt is bemutatja, hogyan hozhat létre olyan Windows IaaS virtuális gépet, amelyen engedélyezve van a Active Directory és a tartománynévrendszer (DNS). Ezzel az útmutatóval konfigurálhatók a szükséges erőforrások, amelyek lehetővé teszik a helyszíni felhasználók számára, hogy bejelentkezzenek egy ESP-kompatibilis HDInsight-fürtbe.

Az Ön által létrehozott kiszolgáló helyettesíti a *tényleges* helyszíni környezetet. Ezt fogja használni a telepítési és konfigurálási lépésekhez. Később megismételheti a lépéseket a saját környezetében.

Ez az útmutató a hibrid identitás-környezetek létrehozásához is segítséget nyújt a jelszó-kivonatoló szinkronizálással Azure Active Directory (Azure AD) használatával. Az útmutató kiegészíti az [ESP használatát a HDInsight-ben](apache-domain-joined-architecture.md).

Mielőtt a folyamatot a saját környezetében használja:

* A Active Directory és a DNS beállítása.
* Engedélyezze az Azure AD-t.
* Helyszíni felhasználói fiókok szinkronizálása az Azure AD-vel.

:::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png" alt-text="Azure AD architektúra diagram" border="false":::

## <a name="create-an-on-premises-environment"></a>Helyszíni környezet létrehozása

Ebben a szakaszban egy Azure rövid útmutató üzembe helyezési sablonját fogja használni új virtuális gépek létrehozásához, a DNS konfigurálásához és egy új Active Directory erdő hozzáadásához.

1. Nyissa meg a gyors üzembe helyezési sablont, és [hozzon létre egy új Active Directory erdővel rendelkező Azure-beli virtuális gépet](https://azure.microsoft.com/resources/templates/active-directory-new-domain/).

1. Válassza **az üzembe helyezés az Azure-** ban lehetőséget.
1. Jelentkezzen be az Azure-előfizetésbe.
1. Az Azure-beli **virtuális gép létrehozása új ad-erdővel** lapon adja meg a következő információkat:

    |Tulajdonság | Érték |
    |---|---|
    |Előfizetés|Válassza ki azt az előfizetést, amelyre telepíteni szeretné az erőforrásokat.|
    |Erőforráscsoport|Válassza az **új létrehozása** lehetőséget, és adja meg a nevet `OnPremADVRG`|
    |Hely|Válasszon ki egy helyet.|
    |Rendszergazdai Felhasználónév|`HDIFabrikamAdmin`|
    |Rendszergazdai jelszó|Adjon meg egy jelszót.|
    |Tartománynév|`HDIFabrikam.com`|
    |DNS-előtag|`hdifabrikam`|

    Hagyja meg a többi alapértelmezett értéket.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png" alt-text="Sablon új Azure AD-erdővel rendelkező Azure-beli virtuális gép létrehozásához" border="true":::

1. Tekintse át a **használati** feltételeket, majd válassza **az Elfogadom a fenti feltételeket és** kikötéseket lehetőséget.
1. Válassza a **vásárlás** lehetőséget, és figyelje meg a telepítést, és várjon, amíg befejeződik. A telepítés körülbelül 30 percet vesz igénybe.

## <a name="configure-users-and-groups-for-cluster-access"></a>Felhasználók és csoportok konfigurálása a fürthöz való hozzáféréshez

Ebben a szakaszban azokat a felhasználókat fogja létrehozni, akik hozzáférhetnek a HDInsight-fürthöz az útmutató végére.

1. Kapcsolódjon a tartományvezérlőhöz Távoli asztal használatával.
    1. A Azure Portalban navigáljon az   >  **OnPremADVRG**  >  **adVM**  >  **kapcsolódási** csoporthoz.
    1. Az **IP-cím** legördülő listából válassza ki a nyilvános IP-címet.
    1. Válassza az **RDP-fájl letöltése** lehetőséget, majd nyissa meg a fájlt.
    1. Használja `HDIFabrikam\HDIFabrikamAdmin` a nevet felhasználónévként.
    1. Adja meg a rendszergazdai fiókhoz választott jelszót.
    1. Válassza az **OK** lehetőséget.

1. A tartományvezérlő **Kiszolgálókezelő** irányítópultján navigáljon az **eszközök**  >  **Active Directory felhasználók és számítógépek** elemre.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png" alt-text="A Kiszolgálókezelő irányítópultján nyissa meg Active Directory felügyelet" border="true":::

1. Hozzon létre két új felhasználót: **HDIAdmin** és **HDIUser**. Ez a két felhasználó bejelentkezik a HDInsight-fürtökbe.

    1. A **Active Directory felhasználók és számítógépek** lapon kattintson a jobb gombbal a `HDIFabrikam.com` elemre, majd keresse meg az **új**  >  **felhasználót**.

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png" alt-text="Új Active Directory-felhasználó létrehozása" border="true":::

    1. Az **új objektum – felhasználó** lapon adja meg `HDIUser` a **nevet** és a **felhasználói bejelentkezési nevet**. A többi mező automatikusan kitöltődik. Ezután kattintson a **Tovább** gombra.

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png" alt-text="Az első rendszergazdai felhasználói objektum létrehozása" border="true":::

    1. A megjelenő előugró ablakban adjon meg egy jelszót az új fiókhoz. Válassza a **jelszó soha nem jár le** lehetőséget, majd az előugró üzenetben kattintson az **OK gombra** .
    1. Kattintson a **tovább**, majd a **Befejezés** gombra az új fiók létrehozásához.
    1. A felhasználó létrehozásához ismételje meg a fenti lépéseket `HDIAdmin` .

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png" alt-text="Második rendszergazdai felhasználói objektum létrehozása" border="true":::

1. Hozzon létre egy globális biztonsági csoportot.

    1. **Active Directory felhasználók és számítógépek** területen kattintson a jobb gombbal a elemre `HDIFabrikam.com` , majd keresse meg az **új**  >  **csoportot**.

    1. Írja be `HDIUserGroup` a **nevet a csoport neve** szövegmezőbe.

    1. Válassza az **OK** lehetőséget.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png" alt-text="Új Active Directory csoport létrehozása" border="true":::

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png" alt-text="Új objektum létrehozása" border="true":::

1. Tagok hozzáadása a **HDIUserGroup**.

    1. Kattintson a jobb gombbal a **HDIUser** elemre, és válassza **a Hozzáadás egy csoporthoz...** lehetőséget.
    1. Az **írja be a kijelölendő objektumok nevét** szövegmezőbe írja be a következőt: `HDIUserGroup` . Ezután kattintson az **OK gombra**, majd az előugró ablakban **az OK gombra** .
    1. Ismételje meg az előző lépéseket a **HDIAdmin** -fiókhoz.

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png" alt-text="A tag HDIUser hozzáadása a HDIUserGroup csoporthoz" border="true":::

Ezzel létrehozta Active Directory-környezetét. Két felhasználót és egy felhasználói csoportot adott hozzá, amely hozzáférhet a HDInsight-fürthöz.

A felhasználók szinkronizálása az Azure AD-vel történik.

### <a name="create-an-azure-ad-directory"></a>Azure AD-címtár létrehozása

1. Jelentkezzen be az Azure Portalra.
1. Válassza **az erőforrás létrehozása és a** típus lehetőséget `directory` . Válassza a **Azure Active Directory**  >  **Létrehozás** elemet.
1. A **szervezet neve** mezőben adja meg a értéket `HDIFabrikam` .
1. A **kezdeti tartománynév** mezőben adja meg a nevet `HDIFabrikamoutlook` .
1. Válassza a **Létrehozás** lehetőséget.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png" alt-text="Azure AD-címtár létrehozása" border="true":::

### <a name="create-a-custom-domain"></a>Egyéni tartomány létrehozása

1. Az új **Azure Active Directory** **kezelés** területén válassza az **Egyéni tartománynevek** lehetőséget.
1. Válassza az **+ Egyéni tartomány hozzáadása** lehetőséget.
1. Az **Egyéni tartománynév** területen adja meg `HDIFabrikam.com` a nevet, majd válassza a **tartomány hozzáadása** elemet.
1. Ezután fejezze be a [DNS-adatok hozzáadását a](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar)tartományregisztrálónál.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png" alt-text="Egyéni tartomány létrehozása" border="true":::

### <a name="create-a-group"></a>Csoport létrehozása

1. Az új **Azure Active Directory** **kezelés** területén válassza a **csoportok** lehetőséget.
1. Válassza az **+ új csoport** lehetőséget.
1. A **Csoportnév** szövegmezőbe írja be a nevet `AAD DC Administrators` .
1. Válassza a **Létrehozás** lehetőséget.

## <a name="configure-your-azure-ad-tenant"></a>Az Azure AD-bérlő konfigurálása

Most konfigurálja az Azure AD-bérlőt, hogy szinkronizálja a felhasználókat és a csoportokat a helyszíni Active Directory-példányról a felhőbe.

Hozzon létre egy Active Directory bérlői rendszergazdát.

1. Jelentkezzen be a Azure Portalba, és válassza ki az Azure AD-bérlőt ( **HDIFabrikam**).

1. Navigáljon a  >  **felhasználók**  >  **új felhasználóinak** kezeléséhez.

1. Adja meg az új felhasználó következő adatait:

   **Identitás**

   |Tulajdonság |Leírás |
   |---|---|
   |Felhasználónév|Írja be `fabrikamazureadmin` a szöveget a szövegmezőbe. A tartomány neve legördülő listából válassza a `hdifabrikam.com`|
   |Name| Írja be a következő szöveget: `fabrikamazureadmin`.|

   **Jelszó**
   1. Válassza **a jelszó létrehozása** lehetőséget.
   1. Adjon meg egy tetszőleges biztonságos jelszót.

   **Csoportok és szerepkörök**
   1. Válassza ki a **kiválasztott 0 csoportot**.
   1. Válassza az **HRE DC-rendszergazdák** lehetőséget, majd **válassza a lehetőséget**.

      :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png" alt-text="Az Azure AD-csoportok párbeszédpanel" border="true":::

   1. Válassza a **felhasználó** lehetőséget.
   1. Válassza a **globális rendszergazda** lehetőséget, majd **válassza a lehetőséget**.

      :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png" alt-text="Az Azure AD-szerepkör párbeszédpanel" border="true":::

1. Válassza a **Létrehozás** lehetőséget.

1. Ezután jelentkezzen be az új felhasználóval a Azure Portalba, ahol a rendszer kérni fogja a jelszó módosítására. Ezt a Microsoft Azure Active Directory Connect konfigurálása előtt kell elvégezni.

## <a name="sync-on-premises-users-to-azure-ad"></a>Helyszíni felhasználók szinkronizálása az Azure AD-vel

### <a name="configure-microsoft-azure-active-directory-connect"></a>Microsoft Azure Active Directory Connect konfigurálása

1. A tartományvezérlőről töltse le [Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Nyissa meg a letöltött végrehajtható fájlt, és fogadja el a licencfeltételeket. Válassza a **Folytatás** lehetőséget.

1. Válassza **az expressz beállítások használata** lehetőséget.

1. A **Kapcsolódás az Azure ad-hoz** lapon adja meg az Azure ad globális rendszergazdájának felhasználónevét és jelszavát. Használja a `fabrikamazureadmin@hdifabrikam.com` Active Directory bérlő konfigurálásakor létrehozott felhasználónevet. Ezután kattintson a **Tovább** gombra.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png" alt-text="Kapcsolódás az Azure A D-hez" border="true":::

1. A **kapcsolódás Active Directory Domain Services** lapon adja meg egy vállalati rendszergazdai fiók felhasználónevét és jelszavát. Használja a `HDIFabrikam\HDIFabrikamAdmin` korábban létrehozott felhasználónevet és jelszót. Ezután kattintson a **Tovább** gombra.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png" alt-text="Kapcsolódjon D D S oldalhoz." border="true":::

1. Az **Azure ad bejelentkezési konfigurációja** lapon válassza a **tovább** lehetőséget.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png" alt-text="Az Azure AD bejelentkezési konfigurációjának lapja" border="true":::

1. A **konfigurálásra kész** lapon válassza a **telepítés** lehetőséget.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png" alt-text="Konfigurálásra kész lap" border="true":::

1. A **konfiguráció kész** lapon válassza a **Kilépés** lehetőséget.
   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png" alt-text="Konfiguráció befejezése lap" border="true":::

1. A szinkronizálás befejeződése után ellenőrizze, hogy a IaaS könyvtárban létrehozott felhasználók szinkronizálva vannak-e az Azure AD-vel.
   1. Jelentkezzen be az Azure Portalra.
   1. Válassza **Azure Active Directory**  >  **HDIFabrikam**-  >  **felhasználók** lehetőséget.

### <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást, amely a Azure AD Domain Services (Azure AD DS) konfigurálására használható. További információ: [Létrehozás, Listázás, törlés vagy a szerepkör hozzárendelése egy felhasználó által hozzárendelt felügyelt identitáshoz a Azure Portal használatával](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Jelentkezzen be az Azure Portalra.
1. Válassza **az erőforrás létrehozása és a** típus lehetőséget `managed identity` . Válassza a **felhasználóhoz rendelt felügyelt identitás**  >  **létrehozása** elemet.
1. Az **Erőforrás neve** mezőbe írja be a következőt: `HDIFabrikamManagedIdentity` .
1. Válassza ki előfizetését.
1. Az **erőforráscsoport** területen válassza az **új létrehozása** elemet, és adja meg az értéket `HDIFabrikam-CentralUS` .
1. A hely területen válassza ki az **USA középső** **régióját**.
1. Válassza a **Létrehozás** lehetőséget.

:::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png" alt-text="Új, felhasználó által hozzárendelt felügyelt identitás létrehozása" border="true":::

### <a name="enable-azure-ad-ds"></a>Az Azure AD tartományi szolgáltatások engedélyezése

Az Azure AD DS engedélyezéséhez kövesse az alábbi lépéseket. További információ: [Az Azure AD DS engedélyezése a Azure Portal használatával](../../active-directory-domain-services/tutorial-create-instance.md).

1. Hozzon létre egy virtuális hálózatot az Azure AD DS üzemeltetéséhez. Futtassa a következő PowerShell-kódot.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }

    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Jelentkezzen be az Azure Portalra.
1. Válassza az **erőforrás létrehozása**, `Domain services` majd a **Azure ad Domain Services** a  >  **Létrehozás** lehetőséget.
1. Az **alapok** oldalon:
   1. A **címtár neve** területen válassza ki a létrehozott Azure ad-könyvtárat: **HDIFabrikam**.
   1. A **DNS-tartománynév** mezőben adja meg a *HDIFabrikam.com* nevet.
   1. Válassza ki előfizetését.
   1. Határozza meg az **HDIFabrikam-CentralUS** erőforráscsoportot. A hely mezőben válassza az **USA középső** **régiója** lehetőséget.

      :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png" alt-text="Azure AD DS – alapszintű részletek" border="true":::

1. A **hálózat** lapon válassza ki a hálózatot (**HDIFabrikam-VNET**) és az alhálózatot (**AADDS-alhálózat**), amelyet a PowerShell-parancsfájl használatával hozott létre. Vagy válassza az **új létrehozása** lehetőséget egy virtuális hálózat létrehozásához.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png" alt-text="Virtuális hálózat létrehozása lépés" border="true":::

1. A **Rendszergazda csoport** lapon értesítést kap arról, hogy egy **HRE DC-rendszergazdák** nevű csoport már létrejött a Csoport felügyeletéhez. Ha szeretné, módosíthatja a csoport tagságát, de ebben az esetben nem kell módosítania. Válassza az **OK** lehetőséget.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png" alt-text="Az Azure AD-rendszergazda csoport megtekintése" border="true":::

1. A **szinkronizálás** lapon engedélyezze a teljes szinkronizálást az   >  **OK gombra** kattintva.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png" alt-text="Az Azure AD DS szinkronizálásának engedélyezése" border="true":::

1. Az **Összefoglalás** lapon ellenőrizze az Azure-AD DS adatait, majd kattintson **az OK gombra**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png" alt-text="Az Azure AD tartományi szolgáltatások engedélyezése" border="true":::

Az Azure AD DS engedélyezése után a helyi DNS-kiszolgáló fut az Azure AD-beli virtuális gépeken.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Az Azure AD DS virtuális hálózat konfigurálása

Az alábbi lépésekkel konfigurálhatja Azure AD DS Virtual Network (**HDIFabrikam-AADDSVNET**) használatát az egyéni DNS-kiszolgálók használatára.

1. Keresse meg az egyéni DNS-kiszolgálók IP-címeit.
   1. Válassza ki az `HDIFabrikam.com` Azure AD DS erőforrást.
   1. A **Kezelés** területen válassza a **Tulajdonságok** elemet.
   1. Keresse **meg a virtuális hálózat IP-címe** alatt található IP-címeket.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png" alt-text="Az Azure AD DS egyéni DNS IP-címeinek megkeresése" border="true":::

1. Konfigurálja a **HDIFabrikam-AADDSVNET** egyéni IP-címek 10.0.0.4 és 10.0.0.5 használatára.

   1. A **Beállítások** területen válassza a **DNS-kiszolgálók** elemet.
   1. Válassza az **Egyéni** lehetőséget.
   1. A szövegmezőbe írja be az első IP-címet (*10.0.0.4*).
   1. Kattintson a **Mentés** gombra.
   1. Ismételje meg a lépéseket a másik IP-cím (*10.0.0.5*) hozzáadásához.

A példában az Azure AD DS IP-címek 10.0.0.4 és 10.0.0.5 használatára konfiguráltuk, az Azure AD DS virtuális hálózatban ugyanazt az IP-címet állítja be:

:::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png" alt-text="Az egyéni DNS-kiszolgálók lap" border="true":::

## <a name="securing-ldap-traffic"></a>Az LDAP-forgalom biztonságossá tétele

A Lightweight Directory Access Protocol (LDAP) a Azure Active Directory olvasására és írására szolgál. Az LDAP-forgalmat a SSL (SSL) vagy a Transport Layer Security (TLS) technológia segítségével bizalmasan és biztonságossá teheti. Az LDAP-t SSL (LDAPs) protokollon keresztül is engedélyezheti egy megfelelően formázott tanúsítvány telepítésével.

További információ a biztonságos LDAP-ről: [LDAPS konfigurálása Azure AD DS felügyelt tartományhoz](../../active-directory-domain-services/tutorial-configure-ldaps.md).

Ebben a szakaszban létrehoz egy önaláírt tanúsítványt, letölti a tanúsítványt, és konfigurálja az **HDIFabrikam** Azure AD DS felügyelt tartományhoz tartozó LDAPS-t.

A következő szkript létrehoz egy tanúsítványt a **HDIFabrikam** számára. A rendszer menti a tanúsítványt a *LocalMachine* útvonalon.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> A TLS-/SSL-tanúsítványkérelem létrehozásához bármely olyan segédprogram vagy alkalmazás használható, amely egy érvényes nyilvános kulcsú titkosítási szabvány (PKCS) \# 10 kérelmet hoz létre.

Ellenőrizze, hogy a tanúsítvány telepítve van-e a számítógép **személyes** tárolójába:

1. Indítsa el a Microsoft Management Console (MMC) programot.
1. Adja hozzá a Tanúsítványok beépülő modult, amely a helyi **számítógépen lévő** tanúsítványokat kezeli.
1. Bontsa ki a **tanúsítványok (helyi számítógép)**  >  **személyes**  >  **tanúsítványok** csomópontot. A **személyes** tárolóban léteznie kell egy új tanúsítványnak. Ezt a tanúsítványt a rendszer a teljes állomásnév számára adja ki.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png" alt-text="Helyi tanúsítvány létrehozásának ellenőrzése" border="true":::

1. A jobb oldali ablaktáblán kattintson a jobb gombbal a létrehozott tanúsítványra. Mutasson a **minden feladat** elemre, majd válassza az **Exportálás** lehetőséget.

1. A **titkos kulcs exportálása** lapon válassza **az igen, majd a titkos kulcs exportálása** lehetőséget. A számítógépnek, amelyre a kulcsot importálni kívánja, szüksége lesz a titkos kulcsra a titkosított üzenetek olvasásához.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png" alt-text="A tanúsítvány exportálása varázsló titkos kulcs exportálása lapja" border="true":::

1. Az **Exportálás fájlformátuma** lapon hagyja meg az alapértelmezett beállításokat, majd kattintson a **tovább** gombra.
1. A **jelszó** lapon adja meg a titkos kulcs jelszavát. A **titkosításhoz** válassza a **TripleDES-SHA1** elemet. Ezután kattintson a **Tovább** gombra.
1. Az exportálandó **fájl** lapon adja meg az exportált tanúsítványfájl elérési útját és nevét, majd kattintson a **tovább** gombra. A fájl nevének. pfx kiterjesztéssel kell rendelkeznie. Ezt a fájlt a Azure Portal konfigurálja biztonságos kapcsolat létesítéséhez.
1. Azure AD DS felügyelt tartományhoz tartozó LDAP-szolgáltatás engedélyezése.
   1. A Azure Portal válassza ki a tartományt `HDIFabrikam.com` .
   1. A **kezelés** területen válassza a **Secure LDAP** lehetőséget.
   1. A **Secure LDAP** lapon a **Secure LDAP** területen válassza az **Engedélyezés** lehetőséget.
   1. Tallózással keresse meg a számítógépen exportált. pfx tanúsítványfájl-fájlt.
   1. Adja meg a tanúsítvány jelszavát.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png" alt-text="Biztonságos LDAP engedélyezése" border="true":::

1. Most, hogy engedélyezte az LDAPs szolgáltatást, ellenőrizze, hogy elérhető-e a 636-es port engedélyezésével.
   1. A **HDIFabrikam-CentralUS** erőforráscsoport területen válassza a **AADDS-HDIFabrikam.com-NSG** hálózati biztonsági csoportot.
   1. A **Beállítások** területen válassza a **bejövő biztonsági szabályok**  >  **Hozzáadás** lehetőséget.
   1. A **bejövő biztonsági szabály hozzáadása** lapon adja meg a következő tulajdonságokat, majd válassza a **Hozzáadás** lehetőséget:

      | Tulajdonság | Érték |
      |---|---|
      | Forrás | Bármely |
      | Forrásporttartományok | * |
      | Cél | Bármely |
      | Célporttartomány | 636 |
      | Protokoll | Bármely |
      | Művelet | Engedélyezés |
      | Prioritás | \<Desired number> |
      | Name | Port_LDAP_636 |

      :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png" alt-text="A bejövő biztonsági szabály hozzáadása párbeszédpanel" border="true":::

A **HDIFabrikamManagedIdentity** a felhasználó által hozzárendelt felügyelt identitás. A HDInsight tartományi szolgáltatások közreműködői szerepköre engedélyezve van a felügyelt identitás számára, amely lehetővé teszi, hogy ez az identitás beolvassa, létrehozza, módosítsa és törölje a tartományi szolgáltatások műveleteit.

:::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png" alt-text="Felhasználó által hozzárendelt felügyelt identitás létrehozása" border="true":::

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>ESP-kompatibilis HDInsight-fürt létrehozása

Ehhez a lépéshez a következő előfeltételek szükségesek:

1. Hozzon létre egy új erőforráscsoportot a *HDIFabrikam-WestUS* az **USA nyugati** régiójában.
1. Hozzon létre egy virtuális hálózatot, amely az ESP-kompatibilis HDInsight-fürtöt fogja tárolni.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Hozzon létre egy társ-kapcsolatot az Azure AD DS ( `HDIFabrikam-AADDSVNET` ) és az ESP-kompatibilis HDInsight-fürtöt () futtató virtuális hálózat között `HDIFabrikam-HDIVNet` . Használja a következő PowerShell-kódot a két virtuális hálózat társításához.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Hozzon létre egy új, **Hdigen2store** nevű Azure Data Lake Storage Gen2 fiókot. Konfigurálja a fiókot a felhasználó által felügyelt identitás **HDIFabrikamManagedIdentity**. További információ: [Azure Data Lake Storage Gen2 használata az Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Állítsa be az egyéni DNS-t a **HDIFabrikam-AADDSVNET** virtuális hálózaton.
    1. Nyissa meg a   >  **OnPremADVRG**  >  **HDIFabrikam-AADDSVNET**  >  **DNS-kiszolgálók** Azure Portal > erőforráscsoportot.
    1. Válassza az **Egyéni** lehetőséget, majd adja meg a *10.0.0.4* és a *10.0.0.5*.
    1. Kattintson a **Mentés** gombra.

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png" alt-text="Virtuális hálózat egyéni DNS-beállításainak mentése" border="true":::

1. Hozzon létre egy új ESP-kompatibilis HDInsight Spark-fürtöt.
    1. Válassza **az egyéni (méret, beállítások, alkalmazások)** lehetőséget.
    1. Adja meg az **alapismeretek** részleteit (1. szakasz). Győződjön meg arról, hogy a **fürt típusa** **Spark 2,3 (HDI 3,6)**. Győződjön meg arról, hogy az **erőforráscsoport** **HDIFabrikam-CentralUS**.

    1. A **Biztonság és hálózatkezelés** (2. szakasz) esetében adja meg a következő adatokat:
        * Az **Enterprise Security Package** területen válassza az **engedélyezve** lehetőséget.
        * Válassza ki a **fürt rendszergazdai felhasználóját** , és válassza ki a helyi rendszergazdaként létrehozott **HDIAdmin** -fiókot. Kattintson a **Kiválasztás** elemre.
        * Válassza ki a **fürt hozzáférési csoportjának**  >  **HDIUserGroup**. A jövőben a csoportba felvett összes felhasználó hozzáférhet a HDInsight-fürtökhöz.

            :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg" alt-text="Válassza ki a fürt hozzáférési csoportjának HDIUserGroup" border="true":::

    1. Fejezze be a fürtkonfiguráció további lépéseit, és ellenőrizze a **fürt összegzésének** részleteit. Válassza a **Létrehozás** lehetőséget.

1. Jelentkezzen be az újonnan létrehozott fürt Ambari felhasználói felületén `https://CLUSTERNAME.azurehdinsight.net` . Használja a rendszergazdai felhasználónevét `hdiadmin@hdifabrikam.com` és jelszavát.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg" alt-text="Az Apache Ambari felhasználói felületének bejelentkezési ablaka" border="true":::

1. A fürt irányítópultján válassza a **szerepkörök** lehetőséget.
1. A **szerepkörök** lapon a **szerepkörök társítása** a **fürt rendszergazdai** szerepköre mellett adja meg a *hdiusergroup* csoportot.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg" alt-text="A fürt rendszergazdai szerepkörének társítása a hdiusergroup-hez" border="true":::

1. Nyissa meg a Secure Shell-(SSH-) ügyfelet, és jelentkezzen be a fürtbe. Használja a helyszíni Active Directory-példányban létrehozott **hdiuser** .

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg" alt-text="Bejelentkezés a fürtbe az SSH-ügyfél használatával" border="true":::

Ha be tud jelentkezni ezzel a fiókkal, megfelelően konfigurálta az ESP-fürtöt a helyszíni Active Directory-példánnyal való szinkronizálásra.

## <a name="next-steps"></a>Következő lépések

[Bemutatjuk az ESP-vel való Apache Hadoop biztonság bevezetését](hdinsight-security-overview.md).

---
title: Az Azure Blockchain Workbench előzetes verziójának üzembe helyezése
description: Az Azure Blockchain Workbench előzetes verziójának üzembe helyezése
ms.date: 07/16/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: references_regions
ms.openlocfilehash: b46a35b45a51d0cc76942c4ca142c4c7792a28b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87077016"
---
# <a name="deploy-azure-blockchain-workbench-preview"></a>Az Azure Blockchain Workbench előzetes verziójának üzembe helyezése

Az Azure Blockchain Workbench előzetes verziója az Azure piactéren elérhető megoldási sablonnal van üzembe helyezve. A sablon leegyszerűsíti a blockchain-alkalmazások létrehozásához szükséges összetevők üzembe helyezését. Az üzembe helyezést követően a Blockchain Workbench hozzáférést biztosít az ügyfélalkalmazások számára felhasználók és Blockchain alkalmazások létrehozásához és kezeléséhez.

Az Blockchain Workbench összetevőivel kapcsolatos további információkért lásd: [Azure Blockchain Workbench-architektúra](architecture.md).

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="prepare-for-deployment"></a>Felkészülés az üzembe helyezésre

A Blockchain Workbench lehetővé teszi egy Blockchain-Főkönyv üzembe helyezését, valamint a kapcsolódó Azure-szolgáltatások egy készletét, amely leggyakrabban a Blockchain-alapú alkalmazások létrehozásához használatos. A Blockchain Workbench üzembe helyezése az Azure-előfizetésben található erőforráscsoport keretében kiépített alábbi Azure-szolgáltatásokban történik.

* App Service csomag (standard)
* Application Insights
* Event Grid
* Azure Key Vault
* Service Bus
* SQL Database (standard S0)
* Azure Storage-fiók (standard LRS)
* Virtuálisgép-méretezési csoport kapacitása 1
* Virtual Network erőforráscsoport (Load Balancer, hálózati biztonsági csoport, nyilvános IP-cím, Virtual Network)
* Azure Blockchain szolgáltatás. Ha előző Blockchain Workbench-telepítést használ, vegye fontolóra az Azure Blockchain Workbench újbóli üzembe helyezését az Azure Blockchain szolgáltatás használatához.

Az alábbi példa a **myblockchain** erőforráscsoporthoz létrehozott központi telepítést szemlélteti.

![Példa központi telepítésre](media/deploy/example-deployment.png)

A Blockchain Workbench díja a mögöttes Azure-szolgáltatások díját összegzi. Az Azure-szolgáltatásokra vonatkozó díjszabás a [díjszabási számológép](https://azure.microsoft.com/pricing/calculator/)használatával számítható ki.

## <a name="prerequisites"></a>Előfeltételek

Az Azure Blockchain Workbench az Azure AD konfigurálását és az alkalmazások regisztrációját igényli. Az Azure AD- [konfigurációkat manuálisan](#azure-ad-configuration) is elvégezheti az üzembe helyezés előtt, vagy futtasson egy szkriptet az üzembe helyezés után. Ha újratelepíti a Blockchain Workbench alkalmazást, tekintse meg az Azure ad [konfigurációját](#azure-ad-configuration) az Azure ad-konfiguráció ellenőrzéséhez.

> [!IMPORTANT]
> A Workbench nem helyezhető üzembe az Azure AD-alkalmazás regisztrálásához használt bérlőben. A Workbench-t olyan bérlőn kell telepíteni, ahol rendelkezik az erőforrások üzembe helyezéséhez szükséges engedélyekkel. További információ az Azure AD-bérlők használatáról: [Active Directory-bérlő beszerzése](../../active-directory/develop/quickstart-create-new-tenant.md) és [alkalmazások integrálása Azure Active Directoryokkal](../../active-directory/develop/quickstart-register-app.md).

## <a name="deploy-blockchain-workbench"></a>A Blockchain Workbench üzembe helyezése

Az előfeltételként szükséges lépések elvégzése után készen áll a Blockchain Workbench üzembe helyezésére. A következő fejezetei a keretrendszer központi telepítését ismertetik.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a fiókját a jobb felső sarokban, és váltson arra a kívánt Azure AD-bérlőre, ahol az Azure Blockchain Workbenchet telepíteni kívánja.
1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
1. Válassza az **Blockchain**  >  **Azure Blockchain Workbench (előzetes verzió)** lehetőséget.

    ![Azure Blockchain Workbench létrehozása](media/deploy/blockchain-workbench-settings-basic.png)

    | Beállítás | Leírás  |
    |---------|--------------|
    | Erőforrás-előtag | Az üzemelő példány rövid egyedi azonosítója. Ezt az értéket használja a rendszer az erőforrások elnevezésének alapjaként. |
    | Virtuális gép felhasználóneve | A felhasználónevet a rendszer az összes virtuális gép (VM) rendszergazdájaként használja. |
    | Hitelesítéstípus | Válassza ki, hogy szeretne-e jelszót vagy kulcsot használni a virtuális gépekhez való csatlakozáshoz. |
    | Jelszó | A jelszó a virtuális gépekhez való csatlakozáshoz használatos. |
    | SSH | Használjon egy RSA nyilvános kulcsot az egysoros formátumban az **SSH-RSA-** vel, vagy használja a többsoros PEM formátumot. Az SSH-kulcsokat `ssh-keygen` Linux és OS X rendszeren is létrehozhatja, vagy a Windows PuTTYGen használatával. További információ az SSH-kulcsokról: [ssh-kulcsok használata az Azure-ban Windowson](../../virtual-machines/linux/ssh-from-windows.md). |
    | Adatbázis-és Blockchain jelszava | A telepítés részeként létrehozott adatbázishoz való hozzáféréshez használt jelszó megadása. A jelszónak meg kell felelnie a következő négy követelmény közül háromnak: a hossznak 12 & 72 karakter, 1 kisbetűs karakter, 1 nagybetű, 1 szám és 1 speciális karakter közé kell esnie, amely nem szám típusú jel (#), százalék (%), vessző (,), csillag (*), vissza idézőjel ( \` ), idézőjel ("), szimpla idézőjel ('), kötőjel (-) és semicolumn (;) |
    | Üzembe helyezési régió | Itt adhatja meg, hogy hová szeretné telepíteni a Blockchain Workbench erőforrásait. A legjobb rendelkezésre állás érdekében ennek meg kell egyeznie a **régió** helyének beállításával. Nem minden régió érhető el az előzetes verzió alatt. Előfordulhat, hogy egyes régiókban nem érhetők el szolgáltatások. Az Azure Blockchain Data Manager a következő Azure-régiókban érhető el: USA keleti régiója és Nyugat-Európa.|
    | Előfizetés | Adja meg az üzemelő példányhoz használni kívánt Azure-előfizetést. |
    | Erőforráscsoportok | Hozzon létre egy új erőforráscsoportot az **új létrehozása** lehetőség kiválasztásával, és adjon meg egy egyedi erőforráscsoport-nevet. |
    | Hely | Válassza ki azt a régiót, amelyre telepíteni kívánja a keretrendszert. |

1. Kattintson az **OK** gombra az alapszintű beállítás konfigurálása szakasz befejezéséhez.

1. A **Speciális beállítások** területen válassza ki, hogy új blockchain-hálózatot kíván-e létrehozni, vagy egy meglévő, a szolgáltatótól származó blockchain hálózatot szeretne használni.

    **Új létrehozása**:

    Az *új létrehozása* lehetőség üzembe helyez egy Azure Blockchain-szolgáltatás kvórum főkönyvét az alapértelmezett alapszintű SKU-val.

    ![Speciális beállítások az új blockchain-hálózathoz](media/deploy/advanced-blockchain-settings-new.png)

    | Beállítás | Leírás  |
    |---------|--------------|
    | Az Azure Blockchain Service díjszabási szintje | Válassza az **alapszintű** vagy a **standard szintű** Azure Blockchain szolgáltatási szintet, amelyet a Blockchain Workbench használ |
    | Az Azure Active Directory beállításai | Válassza a **Hozzáadás később** lehetőséget.</br>Megjegyzés: Ha úgy döntött, hogy [előzetesen konfigurálja az Azure ad-](#azure-ad-configuration) t, vagy újratelepíti, válassza a *Hozzáadás* lehetőséget. |
    | Virtuális gép kiválasztása | Válassza ki az előnyben részesített tárolási teljesítményt és a virtuális gép méretét a blockchain-hálózat számára. Válasszon kisebb méretű virtuálisgép-méretet, például a *standard DS1 v2* -et, ha olyan előfizetéssel rendelkezik, amely alacsony szolgáltatási korláttal, például az Azure ingyenes szintjével rendelkezik. |

    **Meglévő használat** esetén:

    A *meglévő lehetőség használata* lehetővé teszi egy Ethereum-szolgáltatói (PoA) blockchain hálózat megadását. A végpontok a következő követelményekkel rendelkeznek.

   * A végpontnak egy Ethereum proof-of-Authority (PoA) blockchain hálózatnak kell lennie.
   * A végpontnak nyilvánosan elérhetőnek kell lennie a hálózaton keresztül.
   * A PoA blockchain hálózatot úgy kell konfigurálni, hogy a gázárak nullára legyenek beállítva.

     > [!NOTE]
     > A Blockchain Workbench-fiókok nem támogatottak. Ha pénzre van szükség, a tranzakciók sikertelenek lesznek.

     ![A meglévő blockchain-hálózat speciális beállításai](media/deploy/advanced-blockchain-settings-existing.png)

     | Beállítás | Leírás  |
     |---------|--------------|
     | Ethereum RPC-végpont | Adja meg egy meglévő PoA blockchain-hálózat RPC-végpontját. A végpont https://vagy http://-val kezdődik, és portszámmal végződik. Például: `http<s>://<network-url>:<port>` |
     | Az Azure Active Directory beállításai | Válassza a **Hozzáadás később** lehetőséget.</br>Megjegyzés: Ha úgy döntött, hogy [előzetesen konfigurálja az Azure ad-](#azure-ad-configuration) t, vagy újratelepíti, válassza a *Hozzáadás* lehetőséget. |
     | Virtuális gép kiválasztása | Válassza ki az előnyben részesített tárolási teljesítményt és a virtuális gép méretét a blockchain-hálózat számára. Válasszon kisebb méretű virtuálisgép-méretet, például a *standard DS1 v2* -et, ha olyan előfizetéssel rendelkezik, amely alacsony szolgáltatási korláttal, például az Azure ingyenes szintjével rendelkezik. |

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a speciális beállítások befejezéséhez.

1. Tekintse át az összegzést, és ellenőrizze, hogy a paraméterek pontosak-e.

    ![Összefoglalás](media/deploy/blockchain-workbench-summary.png)

1. Válassza a **Létrehozás** lehetőséget a feltételek elfogadásához és az Azure Blockchain Workbench üzembe helyezéséhez.

Az üzembe helyezés akár 90 percet is igénybe vehet. A Azure Portal a folyamat figyelésére használható. Az újonnan létrehozott erőforráscsoporthoz válassza a **központi telepítések > az Áttekintés** lehetőséget az üzembe helyezett összetevők állapotának megtekintéséhez.

> [!IMPORTANT]
> Az üzembe helyezés után végre kell hajtania Active Directory beállításokat. Ha később a **Hozzáadás** lehetőséget választotta, futtatnia kell az [Azure ad konfigurációs parancsfájlt](#azure-ad-configuration-script).  Ha most a **Hozzáadás** lehetőséget választotta, [konfigurálnia kell a válasz URL-címét](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>Blockchain Workbench webes URL-címe

Miután az Blockchain Workbench üzembe helyezése befejeződött, egy új erőforráscsoport tartalmazza a Blockchain Workbench erőforrásait. A Blockchain Workbench-szolgáltatások webes URL-címen keresztül érhetők el. A következő lépések bemutatják, hogyan kérhető le a telepített keretrendszer webes URL-címe.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza az **erőforráscsoportok** lehetőséget.
1. Válassza ki a Blockchain Workbench telepítésekor megadott erőforráscsoport-nevet.
1. A **típus** oszlop fejlécének kiválasztásával betűrendbe rendezheti a listát típus szerint.
1. Két erőforrás van **app Service** típussal. Válassza ki **app Service** típusú erőforrást a "-API" utótag *nélkül* .

    ![App Service-lista](media/deploy/resource-group-list.png)

1. A App Service **áttekintése** lapon másolja az **URL-címet** , amely a telepített Blockchain Workbench webes URL-címét jelöli.

    ![App Service Essentials](media/deploy/app-service.png)

Ha egyéni tartománynevet szeretne hozzárendelni a Blockchain Workbench-hez, tekintse meg az [Egyéni tartománynév konfigurálása webalkalmazáshoz Azure app Service a Traffic Manager használatával](../../app-service/configure-domain-traffic-manager.md)című témakört.

## <a name="azure-ad-configuration-script"></a>Azure AD-konfigurációs parancsfájl

Az Azure AD-t úgy kell konfigurálni, hogy elvégezze a Blockchain Workbench üzembe helyezését. A konfiguráció végrehajtásához PowerShell-parancsfájlt kell használni.

1. Egy böngészőben nyissa meg a [Blockchain Workbench webes URL-címét](#blockchain-workbench-web-url).
1. Az Azure AD Cloud Shell használatával történő beállításához szükséges utasításokat fogja látni. Másolja a parancsot, és indítsa el Cloud Shell.

    ![HRE-szkript elindítása](media/deploy/launch-aad-script.png)

1. Válassza ki azt az Azure AD-bérlőt, ahol a Blockchain Workbenchet telepítette.
1. Cloud Shell PowerShell-környezetben illessze be és futtassa a parancsot.
1. Ha a rendszer kéri, adja meg az Blockchain Workbenchhez használni kívánt Azure AD-bérlőt. Ez lesz az a bérlő, amely a Blockchain Workbench felhasználóit tartalmazza.

    > [!IMPORTANT]
    > A hitelesített felhasználónak engedélyekre van szüksége az Azure AD-alkalmazások regisztrációjának létrehozásához és a delegált alkalmazások engedélyének megadásához a bérlőben. Előfordulhat, hogy meg kell kérnie a bérlő rendszergazdáját, hogy futtassa az Azure AD konfigurációs parancsfájlját, vagy hozzon létre egy új bérlőt.

    ![Adja meg az Azure AD-bérlőt](media/deploy/choose-tenant.png)

1. A rendszer egy böngésző használatával kéri az Azure AD-bérlő hitelesítésére. Nyissa meg a webes URL-címet egy böngészőben, írja be a kódot, és végezze el a hitelesítést.

    ![Hitelesítés kóddal](media/deploy/authenticate.png)

1. A parancsfájl több állapotüzenetek kimenetét jeleníti meg. Ha sikeresen kiosztotta a bérlőt, **sikeres** állapotjelző üzenet jelenik meg.
1. Navigáljon a Blockchain Workbench URL-címére. A rendszer arra kéri, hogy adjon engedélyt a címtárhoz való olvasási engedélyek megadására. Ez lehetővé teszi, hogy a Blockchain Workbench webalkalmazás hozzáférhessen a bérlő felhasználóihoz. Ha Ön a bérlő rendszergazdája, akkor dönthet úgy, hogy beleegyezik a teljes szervezetbe. Ez a beállítás elfogadja a bérlő összes felhasználójának beleegyezikét. Ellenkező esetben az Blockchain Workbench webalkalmazás első használatakor minden felhasználónak meg kell adnia a bejelentést.
1. Válassza az **elfogadás** lehetőséget.

     ![Beleegyezik a felhasználói profilok olvasásához](media/deploy/graph-permission-consent.png)

1. Ha beleegyezik, a Blockchain Workbench webalkalmazás használható.

Végrehajtotta az Azure Blockchain Workbench üzembe helyezését. Az üzembe helyezés használatának első lépéseiért tekintse meg a [következő lépéseket](#next-steps) .

## <a name="azure-ad-configuration"></a>Azure AD-konfiguráció

Ha az üzembe helyezés előtt manuálisan konfigurálja vagy ellenőrzi az Azure AD-beállításokat, hajtsa végre az ebben a részben ismertetett lépéseket. Ha szeretné automatikusan konfigurálni az Azure AD-beállításokat, használja az [Azure ad konfigurációs parancsfájlt](#azure-ad-configuration-script) a Blockchain Workbench telepítése után.

### <a name="blockchain-workbench-api-app-registration"></a>Blockchain Workbench API-alkalmazás regisztrálása

Az Blockchain Workbench üzembe helyezéséhez Azure AD-alkalmazás regisztrálása szükséges. Az alkalmazás regisztrálásához Azure Active Directory (Azure AD) bérlőre van szükség. Használhat meglévő bérlőt, vagy létrehozhat egy új bérlőt. Ha meglévő Azure AD-bérlőt használ, megfelelő engedélyekkel kell rendelkeznie az alkalmazások regisztrálásához, Graph API engedélyek megadásához és a vendég hozzáférés engedélyezéséhez egy Azure AD-bérlőn belül. Ha nem rendelkezik megfelelő engedélyekkel egy meglévő Azure AD-bérlőben, hozzon létre egy új bérlőt.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a fiókját a jobb felső sarokban, és váltson a kívánt Azure AD-bérlőre. A bérlőnek az előfizetés-rendszergazda bérlője kell lennie annak az előfizetésnek, amelyen az Azure Blockchain Workbench telepítve van, és rendelkezik az alkalmazások regisztrálásához szükséges engedélyekkel.
1. Válassza ki az **Azure Active Directory** szolgáltatást a bal oldali navigációs panelen. Válassza **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.

    ![Alkalmazásregisztráció](media/deploy/app-registration.png)

1. Adja meg a megjelenítendő **nevet** , és válassza a **fiókok lehetőséget csak ebben a szervezeti könyvtárban**.

    ![Alkalmazásregisztráció létrehozása](media/deploy/app-registration-create.png)

1. Az Azure AD-alkalmazás regisztrálásához válassza a **regisztráció** lehetőséget.

### <a name="modify-manifest"></a>Jegyzékfájl módosítása

Ezt követően módosítania kell a jegyzékfájlt, hogy az Azure AD-ben az alkalmazási szerepköröket használják, hogy megadják a Blockchain Workbench-rendszergazdákat.  További információ az alkalmazási jegyzékekről: [Azure Active Directory Application manifest](../../active-directory/develop/reference-app-manifest.md).

1. A jegyzékfájlhoz GUID szükséges. GUID-t a PowerShell `[guid]::NewGuid()` -paranccsal vagy-parancsmaggal hozhatja meg `New-GUID` . Egy másik lehetőség a GUID Generator webhely használata.
1. A regisztrált alkalmazás esetében válassza a **jegyzékfájl** lehetőséget a **kezelés** szakaszban.
1. Ezután frissítse a jegyzékfájl **appRoles** szakaszát. Cserélje le `"appRoles": []` a fájlt a megadott JSON-ra. Ügyeljen arra, hogy a mező értékét cserélje le `id` a generált GUID azonosítóra.
    ![Jegyzékfájl szerkesztése](media/deploy/edit-manifest.png)

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    > [!IMPORTANT]
    > A Blockchain Workbench-rendszergazdák azonosításához az érték **adminisztrátora** szükséges.

1. A jegyzékfájlban a **Oauth2AllowImplicitFlow** értéket is **igaz** értékre kell állítani.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

1. Válassza a **Mentés** lehetőséget a jegyzékfájl módosításainak mentéséhez.

### <a name="add-graph-api-required-permissions"></a>Graph API szükséges engedélyek hozzáadása

Az API-alkalmazásnak engedélyt kell kérnie a felhasználótól a címtár eléréséhez. Állítsa be a következő szükséges engedélyeket az API-alkalmazáshoz:

1. A *BLOCKCHAIN API* -alkalmazás regisztrálása lapon válassza az **API-engedélyek** lehetőséget. Alapértelmezés szerint a Graph API **User. Read** engedély van hozzáadva.
1. A Workbench alkalmazásnak olvasási hozzáférésre van szüksége a felhasználók alapszintű profiljának adataihoz. A *konfigurált engedélyek* területen válassza az **engedély hozzáadása** elemet. A **Microsoft API**-kon válassza a **Microsoft Graph** lehetőséget.
1. Mivel a Workbench alkalmazás a hitelesített felhasználói hitelesítő adatokat használja, válassza a **delegált engedélyek** lehetőséget.
1. A *felhasználó* kategóriában válassza a **User. ReadBasic. All** engedélyt.

    ![Az Azure AD-alkalmazás regisztrációs konfigurációja, amely a Microsoft Graph user. ReadBasic. All delegált engedély hozzáadását mutatja be](media/deploy/add-graph-user-permission.png)

    Válassza az **engedélyek hozzáadása** lehetőséget.

1. A *konfigurált engedélyek* területen jelölje be a **rendszergazdai jóváhagyás megadása** a tartományhoz lehetőséget, majd válassza az **Igen** lehetőséget a hitelesítési kéréshez.

   ![Engedélyek megadása](media/deploy/client-app-grant-permissions.png)

   Az engedélyek megadása lehetővé teszi a Blockchain Workbench számára a címtárban lévő felhasználók elérését. Az olvasási engedély szükséges a tagok kereséséhez és a Blockchain Workbenchhez való hozzáadásához.

### <a name="get-application-id"></a>Alkalmazás AZONOSÍTÓjának beolvasása

Az alkalmazás-azonosító és a bérlő adatai szükségesek az üzembe helyezéshez. Összegyűjtheti és tárolhatja az üzembe helyezés során használatos adatokat.

1. A regisztrált alkalmazáshoz válassza az **Áttekintés** lehetőséget.
1. Másolja és tárolja az **alkalmazás-azonosító** értékét későbbi használatra az üzembe helyezés során.

    ![API-alkalmazás tulajdonságai](media/deploy/app-properties.png)

    | Tárolási beállítás  | Használat az üzembe helyezés során |
    |------------------|-------------------|
    | Alkalmazás (ügyfél) azonosítója | Azure Active Directory telepítő > alkalmazás azonosítója |

### <a name="get-tenant-domain-name"></a>Bérlői tartomány nevének lekérése

Gyűjtse össze és tárolja a Active Directory bérlői tartománynevet, ahol az alkalmazások regisztrálva vannak.

Válassza ki az **Azure Active Directory** szolgáltatást a bal oldali navigációs panelen. Válassza az **Egyéni tartománynevek** elemet. Másolja és tárolja a tartománynevet.

![Tartománynév](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Vendég felhasználói beállítások

Ha az Azure AD-bérlőben van vendég felhasználó, kövesse a további lépéseket annak biztosításához, hogy a Blockchain Workbench felhasználói hozzárendelése és kezelése megfelelően működjön.

1. Állítsa be az Azure AD-bérlőt, és válassza **Azure Active Directory > felhasználói beállítások > a külső együttműködési beállítások kezelése** lehetőséget.
1. A **vendég felhasználói engedélyeinek** beállítása a **nem** értékre van korlátozva.
    ![Külső együttműködési beállítások](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>A válasz URL-címének konfigurálása

Miután telepítette az Azure Blockchain Workbench alkalmazást, konfigurálnia kell az üzembe helyezett Blockchain Workbench webes URL-címének Azure Active Directory (Azure AD) ügyfélalkalmazások **válaszának URL-címét** .

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Ellenőrizze, hogy a bérlőn van-e regisztrálva az Azure AD ügyfélalkalmazás.
1. Válassza ki az **Azure Active Directory** szolgáltatást a bal oldali navigációs panelen. Válassza az **Alkalmazásregisztrációk** lehetőséget.
1. Válassza ki az előfeltételek szakaszban regisztrált Azure AD-ügyfélalkalmazás.
1. Válassza a **Hitelesítés** lehetőséget.
1. Adja meg az Azure Blockchain Workbench központi telepítésének fő webes URL-címét, amelyet a [Blockchain Workbench webes URL-címe](#blockchain-workbench-web-url) szakaszban kapott le. A válasz URL-címe előtaggal van ellátva `https://` . Például: `https://myblockchain2-7v75.azurewebsites.net`

    ![Hitelesítési válasz URL-címei](media/deploy/configure-reply-url.png)

1. A **speciális beállítás** szakaszban keresse meg a **hozzáférési jogkivonatok** és **azonosító jogkivonatok elemet**.

    ![Hitelesítés – speciális beállítások](media/deploy/authentication-advanced-settings.png)

1. A **Mentés** gombra kattintva frissítheti az ügyfél regisztrációját.

## <a name="remove-a-deployment"></a>Központi telepítés eltávolítása

Ha már nincs szükség központi telepítésre, a Blockchain Workbench erőforráscsoport törlésével eltávolíthatja a központi telepítést.

1. A Azure Portalban navigáljon az **erőforráscsoporthoz** a bal oldali navigációs ablaktáblán, és válassza ki a törölni kívánt erőforráscsoportot.
1. Válassza az **Erőforráscsoport törlése** elemet. A törlés ellenőrzéséhez írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

    ![Erőforráscsoport törlése](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Következő lépések

Ebben a útmutatóban az Azure Blockchain Workbench üzembe helyezését ismertetjük. Ha szeretné megtudni, hogyan hozhat létre blockchain-alkalmazásokat, folytassa a következő útmutatók beírásával.

> [!div class="nextstepaction"]
> [Blockchain-alkalmazás létrehozása az Azure Blockchain Workbenchben](create-app.md)

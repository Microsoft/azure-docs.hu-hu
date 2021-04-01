---
title: Azure Firewall Manager biztonsági partner szolgáltatójának üzembe helyezése
description: Megtudhatja, hogyan helyezhet üzembe egy Azure Firewall Manager biztonsági partner-szolgáltatót a Azure Portal használatával.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 12/01/2020
ms.author: victorh
ms.openlocfilehash: 906687e08c9f31890a9ecec9154079e704512832
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96485722"
---
# <a name="deploy-a-security-partner-provider"></a>Biztonságipartner-szolgáltató üzembe helyezése

A Azure Firewall Managerben található *biztonsági partner szolgáltatók* lehetővé teszik, hogy ismerős, legjobb, harmadik féltől származó biztonsági szolgáltatásokat (SECaaS) kínálja a felhasználók internet-hozzáférésének védelmére.

Ha többet szeretne megtudni a támogatott forgatókönyvekről és az ajánlott eljárásokról, tekintse meg a [Mi a biztonsági partner szolgáltatók?](trusted-security-partners.md) című témakört.


Az integrált, harmadik féltől származó biztonsági szolgáltatások (SECaaS) partnerei már elérhetők: 

- **Zscaler**
- **[Ellenőrzőpont](check-point-overview.md)**
- **iboss**

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Külső gyártótól származó biztonsági szolgáltató üzembe helyezése új központban

Ugorja át ezt a szakaszt, ha egy külső szolgáltatót egy meglévő hubhoz telepít.

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
2. A **Search (keresés**) mezőbe írja be a **Firewall Manager** kifejezést, majd válassza ki a **szolgáltatások** területen.
3. Navigáljon **első lépések**. Válassza a **biztonságos virtuális hubok megtekintése** lehetőséget.
4. Válassza az **új biztonságos virtuális központ létrehozása** lehetőséget.
5. Adja meg az előfizetést és az erőforráscsoportot, válasszon ki egy támogatott régiót, és adja hozzá a hub és a virtuális WAN-információkat. 
6. Válassza **a VPN Gateway belefoglalása a biztonsági partner-szolgáltatók engedélyezéséhez** lehetőséget.
7. Válassza ki az igényeinek megfelelő **átjáró-méretezési egységeket** .
8. Válassza a Next (tovább) lehetőséget **: Azure Firewall**
   > [!NOTE]
   > A biztonsági partner-szolgáltatók VPN Gateway alagutak használatával csatlakoznak a hubhoz. Ha törli a VPN Gateway, a biztonsági partner-szolgáltatók kapcsolatai megszakadnak.
9. Ha Azure Firewall szeretné központilag telepíteni a privát forgalmat, valamint a külső szolgáltatót az internetes forgalom szűrésére, válassza ki a szabályzatot a Azure Firewallhoz. Tekintse meg a [támogatott forgatókönyveket](trusted-security-partners.md#key-scenarios).
10. Ha csak egy külső gyártótól származó biztonsági szolgáltatót szeretne üzembe helyezni a központban, válassza a **Azure Firewall: engedélyezve/letiltva** lehetőséget a **Letiltva** beállításhoz. 
11. Válassza a Next (tovább) lehetőséget  **: biztonsági partner szolgáltatója**.
12. Állítsa be a **biztonsági partner szolgáltatóját** az **engedélyezéshez**. 
13. Válasszon partnert. 
14. Válassza a **Tovább: Ellenőrzés és létrehozás** lehetőségre. 
15. Tekintse át a tartalmat, majd válassza a **Létrehozás** lehetőséget.

A VPN-átjáró üzembe helyezése több mint 30 percet is igénybe vehet.

Annak ellenőrzéséhez, hogy létrejött-e a hub, navigáljon Azure Firewall Manager->biztonságos hubokhoz. Válassza ki a hub->áttekintő lapot a partner nevének és az állapotnak a **függőben lévő biztonsági kapcsolatként** való megjelenítéséhez.

Miután létrejött a hub, és a biztonsági partner be van állítva, folytassa a szolgáltatást a biztonsági szolgáltató hubhoz való összekapcsolásához.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Külső gyártótól származó biztonsági szolgáltató üzembe helyezése meglévő központban

Kiválaszthat egy meglévő hubot egy virtuális WAN-ban, és átalakíthatja azt egy *biztonságos virtuális hubhoz*.

1. A **első lépések** területen válassza a **biztonságos virtuális hubok megtekintése** lehetőséget.
2. Válassza a **meglévő hubok konvertálása** lehetőséget.
3. Válasszon egy előfizetést és egy meglévő hubot. A külső szolgáltatók új központban való üzembe helyezéséhez kövesse a további lépéseket.

Ne feledje, hogy a VPN-átjárót központilag kell telepíteni ahhoz, hogy egy meglévő hubot a külső szolgáltatóktól származó biztonságos hubhoz lehessen alakítani.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Külső gyártótól származó biztonsági szolgáltatók konfigurálása biztonságos hubhoz való kapcsolódáshoz

A virtuális központ VPN Gatewayhoz tartozó alagutak beállításához a harmadik féltől származó szolgáltatók hozzáférési jogokkal kell rendelkezniük a központhoz. Ehhez társítson egy egyszerű szolgáltatást az előfizetéséhez vagy az erőforráscsoporthoz, és adja meg a hozzáférési jogosultságokat. Ezeket a hitelesítő adatokat a portálon keresztül kell megadnia a harmadik féltől.

### <a name="create-and-authorize-a-service-principal"></a>Egyszerű szolgáltatás létrehozása és engedélyezése

1. Azure Active Directory (AD) egyszerű szolgáltatás létrehozása: kihagyhatja az átirányítási URL-címet. 

   [Útmutató: Az erőforrásokhoz hozzáférő Azure AD-alkalmazás és -szolgáltatásnév létrehozása a portálon](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. Adja meg az egyszerű szolgáltatásnév hozzáférési jogosultságait és hatókörét.
   [Útmutató: Az erőforrásokhoz hozzáférő Azure AD-alkalmazás és -szolgáltatásnév létrehozása a portálon](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)

   > [!NOTE]
   > A részletesebb szabályozás érdekében korlátozhatja a hozzáférést csak az erőforráscsoporthoz.

### <a name="visit-partner-portal"></a>A partneri portál meglátogatása

1. Kövesse a partner által megadott utasításokat a telepítés befejezéséhez. Ide tartozik a HRE-információk elküldése a hub észleléséhez és a csatlakozáshoz, a kimenő házirendek frissítése, valamint a kapcsolati állapot és naplók ellenőrzése.

   - [Zscaler: Microsoft Azure virtuális WAN-integráció konfigurálása](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration).
   - [Ellenőrzési pont: Microsoft Azure virtuális WAN-integráció konfigurálása](https://sc1.checkpoint.com/documents/Infinity_Portal/WebAdminGuides/EN/CloudGuard-Connect-Azure-Virtual-WAN/Default.htm).
   - [iboss: Microsoft Azure virtuális WAN-integráció konfigurálása](https://www.iboss.com/blog/securing-microsoft-azure-with-iboss-saas-network-security). 
   
2. Az Azure-beli Azure Virtual WAN-portálon megtekintheti az alagút létrehozási állapotát. Miután az alagutak az Azure-ban és a partner portálon **is megjelennek, folytassa a következő** lépésekkel, hogy beállítsa az útvonalakat annak kiválasztásához, hogy mely ágakat és virtuális hálózatok kell elküldeni az internetes forgalomnak a partnernek.

## <a name="configure-route-settings"></a>Útvonal-beállítások konfigurálása

1. Keresse meg a Azure Firewall Manager-> biztonságos hubokat. 
2. Válasszon egy hubot. A hub állapotának most már a **függőben lévő biztonsági kapcsolat** helyett **kiépítve** kell lennie.

   Győződjön meg arról, hogy a külső szolgáltató tud csatlakozni az elosztóhoz. A VPN-átjárón lévő alagutaknak **csatlakoztatott** állapotban kell lenniük. Ez az állapot jobban tükrözi a hub és a külső partner közötti kapcsolat állapotát az előző állapothoz képest.
3. Válassza ki a hubot, és navigáljon az **útvonal beállításai** elemre.

   Ha harmadik féltől származó szolgáltatót helyez üzembe a központba, az a hubot egy *biztonságos virtuális hubhoz* alakítja át. Ez biztosítja, hogy a harmadik féltől származó szolgáltató egy 0.0.0.0/0 (alapértelmezett) útvonalat Hirdessen a hubhoz. Az VNet-kapcsolatok és a központhoz csatlakoztatott helyek azonban nem kapják meg ezt az útvonalat, kivéve, ha nem engedélyezte, hogy mely kapcsolatokhoz kell ezt az alapértelmezett útvonalat választania.
4. Az **internetes forgalom** területen válassza a **VNet** vagy az **ág – Internet** lehetőséget, vagy mindkettőt, hogy az útvonalak konfigurálva legyenek a harmadik féltől.

   Ez csak azt jelzi, hogy milyen típusú forgalmat kell átirányítani a központba, de nem befolyásolja az útvonalakat a virtuális hálózatok vagy ágakon. Ezeket az útvonalakat a rendszer alapértelmezés szerint nem továbbítja a hubhoz csatolt összes virtuális hálózatok/ág számára.
5. Ki kell választania a **biztonságos kapcsolatokat** , és ki kell választania azokat a kapcsolatokat, amelyeken ezeket az útvonalakat be kell állítani. Ez azt jelzi, hogy mely virtuális hálózatok/ágak indíthatják el az internetes forgalmat a külső szolgáltató felé.
6. Az **útvonal beállításai** területen válassza a **biztonságos kapcsolatok** az internetes forgalom alatt lehetőséget, majd válassza ki a VNet vagy az ágakat (virtuális WAN-*helyeket* ) a biztonság érdekében. Válassza a **biztonságos internetes forgalom** lehetőséget.
   ![Biztonságos internetes forgalom](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Váltson vissza a hubok lapra. A központ **biztonsági partnere szolgáltatójának** állapota most már  **védett**.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Harmadik féltől származó szolgáltatással történő internetes forgalom ág-vagy VNet

Ezt követően ellenőrizheti, hogy a VNet virtuális gépek vagy a fiókirodák hozzáférhetnek-e az internethez, és ellenőrizhetik, hogy a forgalom a külső szolgáltatás felé áramlik-e.

Az útvonal-beállítás lépéseinek befejezése után a VNet virtuális gépek és a fiókirodák a 0/0-as vagy harmadik féltől származó szolgáltatási útvonalra kerülnek. Ezeket a virtuális gépeket nem lehet RDP-vagy SSH-kapcsolat. A bejelentkezéshez az [Azure Bastion](../bastion/bastion-overview.md) szolgáltatást egy társ VNet helyezheti üzembe.

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: a felhőalapú hálózat védelme a Azure Firewall Managerrel a Azure Portal használatával](secure-cloud-network.md)
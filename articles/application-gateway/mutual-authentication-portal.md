---
title: Kölcsönös hitelesítés konfigurálása az Azure Application Gateway portálon keresztül
description: Megtudhatja, hogyan konfigurálhat egy Application Gatewayt kölcsönös hitelesítésre a portálon keresztül
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: b2118a257f029a63445b09dfa618d6e3ceacddda
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231435"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-portal-preview"></a>Kölcsönös hitelesítés konfigurálása Application Gateway a portálon keresztül (előzetes verzió)

Ez a cikk azt ismerteti, hogyan használható a Azure Portal a kölcsönös hitelesítés konfigurálásához a Application Gateway. A kölcsönös hitelesítés azt jelenti, Application Gateway hitelesíti a kérést küldő ügyfelet a Application Gatewayra feltöltött ügyféltanúsítvány használatával. 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Előkészületek

A kölcsönös hitelesítés Application Gatewayhoz való konfigurálásához szükség van egy ügyféltanúsítványt az átjáróra való feltöltéshez. Az ügyféltanúsítványt a rendszer a tanúsítvány érvényesítésére fogja használni az ügyfél Application Gateway. Tesztelési célból önaláírt tanúsítványt is használhat. Ez azonban nem javasolt az éles számítási feladatokhoz, mivel azok nehezebben kezelhetők és nem teljesen biztonságosak. 

Ha többet szeretne megtudni, különösen arról, hogy milyen típusú Ügyféltanúsítványok tölthetők fel, tekintse meg [a kölcsönös hitelesítés áttekintését Application Gateway](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication).

## <a name="create-a-new-application-gateway"></a>Új Application Gateway létrehozása

Először hozzon létre egy új Application Gateway, mint általában a portálon keresztül – a kölcsönös hitelesítés engedélyezéséhez nincs szükség további lépésekre a létrehozásban. Application Gateway a portálon való létrehozásával kapcsolatos további információkért tekintse meg a [portál rövid útmutatóját](./quick-create-portal.md).

## <a name="configure-mutual-authentication"></a>Kölcsönös hitelesítés konfigurálása 

Ha egy meglévő Application Gateway kölcsönös hitelesítéssel szeretne konfigurálni, először nyissa meg az SSL- **beállítások (előzetes verzió)** lapot a portálon, és hozzon létre egy új SSL-profilt. Ha létrehoz egy SSL-profilt, két lap jelenik meg: **ügyfél-hitelesítés** és **SSL-házirend**. Az **ügyfél-hitelesítés** lapon töltheti fel az ügyféltanúsítvány (oka) t. Az **SSL-szabályzat** lapon egy figyelő-specifikus SSL-házirendet konfigurálhat – további tudnivalókért tekintse meg [a figyelőre vonatkozó SSL-szabályzat konfigurálását](./application-gateway-configure-listener-specific-ssl-policy.md)ismertető témakört.

> [!IMPORTANT]
> Győződjön meg arról, hogy a teljes ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványlánc egy fájlban van feltöltve, és a fájlnak csak egy lánca van.

1. Keresse meg **Application Gateway** a portálon, válassza az **Application Gateways** elemet, majd kattintson a meglévő Application Gatewayre.

2. Válassza az **SSL-beállítások (előzetes verzió)** lehetőséget a bal oldali menüben.

3. Az új SSL-profil létrehozásához kattintson a felül található **SSL-profilok** melletti plusz jelre.

4. Adjon meg egy nevet az **SSL-profil neve** alatt. Ebben a példában az SSL-profil *applicationGatewaySSLProfile* nevezzük. 

5. Maradjon az **ügyfél-hitelesítés** lapon. Töltse fel az ügyfél és a Application Gateway közötti kölcsönös hitelesítéshez használni kívánt PEM-tanúsítványt az **új tanúsítvány feltöltése** gomb használatával. 

    Ha további információt szeretne arról, hogyan kinyerheti a megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványait a feltöltéshez, tekintse meg a [megbízható ügyfél hitelesítésszolgáltatói tanúsítványainak kinyerésével](./mutual-authentication-certificate-management.md)foglalkozó témakört.

   > [!NOTE]
   > Ha ez nem az első SSL-profilja, és más ügyféltanúsítványt is feltöltött a Application Gatewayba, dönthet úgy, hogy az átjárón lévő meglévő tanúsítványt újra felhasználja a legördülő menüből. 

6. Jelölje be az **ügyféltanúsítvány-kibocsátó** megkülönböztető neve jelölőnégyzetet, ha azt szeretné, hogy a Application Gateway ellenőrizze az ügyféltanúsítvány azonnali kiállítójának megkülönböztető nevét. 

7. Vegye fontolóra egy figyelő-specifikus szabályzat hozzáadását. Tekintse meg a [figyelő-specifikus SSL-házirendek beállításával](./application-gateway-configure-listener-specific-ssl-policy.md)kapcsolatos utasításokat.

8. Válassza a **Hozzáadás** lehetőséget a mentéshez.
    > [!div class="mx-imgBorder"]
    > ![Ügyfél-hitelesítés hozzáadása az SSL-profilhoz](./media/mutual-authentication-portal/mutual-authentication-portal.png)

## <a name="associate-the-ssl-profile-with-a-listener"></a>Az SSL-profil hozzárendelése figyelőhöz

Most, hogy létrehozott egy SSL-profilt kölcsönös hitelesítéssel, az SSL-profilt hozzá kell rendelni a figyelőhöz a kölcsönös hitelesítés beállításának befejezéséhez. 

1. Navigáljon a meglévő Application Gateway. Ha éppen befejezte a fenti lépéseket, semmit nem kell tennie. 

2. Válassza a **figyelők** lehetőséget a bal oldali menüben. 

3. Kattintson a **figyelő hozzáadása** lehetőségre, ha még nincs BEÁLLÍTVA a https-figyelő. Ha már rendelkezik egy HTTPS-figyelővel, kattintson rá a listából. 

4. Adja meg a **figyelő nevét**, a előtér **-IP-címet**, a **portot**, a **protokollt** és az egyéb **https-beállításokat** , hogy azok megfeleljenek a követelményeinek.

5. Jelölje be az **SSL-profil engedélyezése** jelölőnégyzetet, így kiválaszthatja, hogy melyik SSL-profilt szeretné hozzárendelni a figyelőhöz. 

6. Válassza ki az imént létrehozott SSL-profilt a legördülő listából. Ebben a példában a korábbi lépésekből létrehozott SSL-profilt választjuk: *applicationGatewaySSLProfile*. 

7. Folytassa a figyelő hátralévő részének konfigurálását, hogy megfeleljen a követelményeinek. 

8. Kattintson a **Hozzáadás** gombra, hogy az új figyelőt a hozzá társított SSL-profilba mentse. 

    > [!div class="mx-imgBorder"]
    > ![SSL-profil hozzárendelése az új figyelőhöz](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)

## <a name="renew-expired-client-ca-certificates"></a>Lejárt ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványainak megújítása

Abban az esetben, ha az ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa lejárt, a következő lépésekkel frissítheti az átjáró tanúsítványát: 

1. Navigáljon a Application Gateway, és lépjen a bal oldali menüben az **SSL-beállítások (előnézet)** lapra. 
 
1. Válassza ki a meglévő SSL-profilt (ka) t a lejárt ügyféltanúsítvány használatával. 
 
1. Az **ügyfél-hitelesítés** lapon válassza az **új tanúsítvány feltöltése** lehetőséget, és töltse fel az új ügyféltanúsítványt. 
 
1. Válassza a Kuka ikont a lejárt tanúsítvány mellett. Ezzel törli a tanúsítvány társítását az SSL-profilból. 

1. Ismételje meg a fenti 2-4. lépést minden olyan SSL-profillal, amely ugyanazt a lejárt ügyféltanúsítványt használta. A 3. lépésben feltöltött új tanúsítványt más SSL-profilok legördülő menüjéből is kiválaszthatja.

## <a name="next-steps"></a>Következő lépések

- [Webes forgalom kezelése Application Gatewayjel az Azure CLI segítségével](./tutorial-manage-web-traffic-cli.md)
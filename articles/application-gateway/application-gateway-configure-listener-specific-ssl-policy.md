---
title: Figyelő-specifikus SSL-szabályzatok konfigurálása az Azure Application Gateway portálon keresztül
description: Megtudhatja, hogyan konfigurálhat figyelő-specifikus SSL-házirendeket Application Gateway a portálon keresztül
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/30/2021
ms.author: caya
ms.openlocfilehash: b0bda1921c63b4e353365c3b391e17f620740396
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231574"
---
# <a name="configure-listener-specific-ssl-policies-on-application-gateway-through-portal-preview"></a>Figyelő-specifikus SSL-szabályzatok konfigurálása Application Gateway a portálon keresztül (előzetes verzió)

Ez a cikk azt ismerteti, hogyan lehet a Azure Portal használatával konfigurálni a figyelőre vonatkozó SSL-házirendeket a Application Gateway. A figyelőhöz tartozó SSL-házirendek lehetővé teszik bizonyos figyelők konfigurálását, hogy különböző SSL-házirendeket használjanak egymástól. Továbbra is beállíthat egy alapértelmezett SSL-házirendet, amelyet az összes figyelő használni fog, kivéve, ha felülírja a figyelőre vonatkozó SSL-házirendet. 

> [!NOTE]
> Csak Standard_v2 és WAF_v2 SKU-támogatással rendelkező figyelő-specifikus házirendek, mivel a figyelő-specifikus házirendek az SSL-profilok részét képezik, és az SSL-profilok csak a v2-átjárók esetében támogatottak. 



Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-new-application-gateway"></a>Új Application Gateway létrehozása

Először hozzon létre egy új Application Gateway, amelyet általában a portálon keresztül használ – a figyelőhöz tartozó SSL-házirendek konfigurálásához nem szükséges további lépések a létrehozásához. Application Gateway a portálon való létrehozásával kapcsolatos további információkért tekintse meg a [portál rövid útmutatóját](./quick-create-portal.md).

## <a name="set-up-a-listener-specific-ssl-policy"></a>Figyelőhöz tartozó SSL-szabályzat beállítása

Egy figyelőhöz tartozó SSL-házirend beállításához először nyissa meg az **SSL-beállítások (előzetes verzió)** lapot a portálon, és hozzon létre egy új SSL-profilt. Ha létrehoz egy SSL-profilt, két lap jelenik meg: **ügyfél-hitelesítés** és **SSL-házirend**. Az **SSL-házirend** lapon egy figyelő-specifikus SSL-házirendet konfigurálhat. Az **ügyfél-hitelesítés** lapon a kölcsönös hitelesítés konfigurálását végző ügyfél-tanúsítvány (ok) feltöltésére van lehetőség. További információkért tekintse meg [a kölcsönös hitelesítés konfigurálását](./mutual-authentication-portal.md)ismertető témakört.

> [!NOTE]
> Javasoljuk, hogy a TLS 1,2-as TLS 1,2-et használja a jövőben. 

1. Keresse meg **Application Gateway** a portálon, válassza az **Application Gateways** elemet, majd kattintson a meglévő Application Gatewayre.

2. Válassza az **SSL-beállítások (előzetes verzió)** lehetőséget a bal oldali menüben.

3. Az új SSL-profil létrehozásához kattintson a felül található **SSL-profilok** melletti plusz jelre.

4. Adjon meg egy nevet az **SSL-profil neve** alatt. Ebben a példában az SSL-profil *applicationGatewaySSLProfile* nevezzük. 

5. Nyissa meg az **SSL-házirend** lapot, és jelölje be a **figyelőre vonatkozó SSL-házirend engedélyezése** jelölőnégyzetet. 

6. Állítsa be a figyelőre vonatkozó SSL-szabályzatot az igényeinek megfelelően. Az előre definiált SSL-házirendek közül választhat, és testre szabhatja saját SSL-házirendjét. Az SSL-szabályzatokkal kapcsolatos további információkért tekintse meg az [SSL-házirend áttekintése](./application-gateway-ssl-policy-overview.md)című témakört. A TLS 1,2 használatát javasoljuk

7. Válassza a **Hozzáadás** lehetőséget a mentéshez.

    > [!NOTE]
    > Nem kell konfigurálnia az ügyfél-hitelesítést egy SSL-profilban a figyelőhöz való hozzárendeléshez. Csak az ügyfél-hitelesítés konfigurálható, vagy csak a figyelőhöz megadott SSL-házirend van konfigurálva, vagy mindkettő konfigurálva van az SSL-profilban.  

    ![Figyelő-specifikus SSL-szabályzat hozzáadása az SSL-profilhoz](./media/application-gateway-configure-listener-specific-ssl-policy/listener-specific-ssl-policy-ssl-profile.png)
    
## <a name="associate-the-ssl-profile-with-a-listener"></a>Az SSL-profil hozzárendelése figyelőhöz

Most, hogy létrehozott egy SSL-profilt egy figyelő-specifikus SSL-házirenddel, az SSL-profilt társítani kell a figyelőhöz, hogy a figyelő-specifikus szabályzatot működésbe helyezze. 

1. Navigáljon a meglévő Application Gateway. Ha éppen befejezte a fenti lépéseket, semmit nem kell tennie. 

2. Válassza a **figyelők** lehetőséget a bal oldali menüben. 

3. Kattintson a **figyelő hozzáadása** lehetőségre, ha még nincs BEÁLLÍTVA a https-figyelő. Ha már rendelkezik egy HTTPS-figyelővel, kattintson rá a listából. 

4. Adja meg a **figyelő nevét**, a előtér **-IP-címet**, a **portot**, a **protokollt** és az egyéb **https-beállításokat** , hogy azok megfeleljenek a követelményeinek.

5. Jelölje be az **SSL-profil engedélyezése** jelölőnégyzetet, így kiválaszthatja, hogy melyik SSL-profilt szeretné hozzárendelni a figyelőhöz. 

6. Válassza ki a legördülő listából létrehozott SSL-profilt. Ebben a példában a korábbi lépésekből létrehozott SSL-profilt választjuk: *applicationGatewaySSLProfile*. 

7. Folytassa a figyelő hátralévő részének konfigurálását, hogy megfeleljen a követelményeinek. 

8. Kattintson a **Hozzáadás** gombra, hogy az új figyelőt a hozzá társított SSL-profilba mentse. 

    ![SSL-profil hozzárendelése az új figyelőhöz](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)        

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése Application Gatewayjel az Azure CLI segítségével](./tutorial-manage-web-traffic-cli.md)
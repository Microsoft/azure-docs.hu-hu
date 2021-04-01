---
title: FortiGate üzembe helyezési útmutató | Microsoft Docs
description: Állítsa be és működjön együtt a Fortinet FortiGate következő generációs tűzfallal.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: jeedes
ms.openlocfilehash: cdaa6a9601452100ab90ef8b0f2191002f256b74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95025465"
---
# <a name="fortigate-azure-virtual-machine-deployment-guide"></a>FortiGate Azure-beli virtuális gépek üzembe helyezési útmutatója

Ennek a telepítési útmutatónak a segítségével megtudhatja, hogyan állíthatja be és használhatja az Azure-beli virtuális gépekként üzembe helyezett Fortinet-FortiGate. Emellett a FortiGate SSL VPN Azure AD Gallery alkalmazást is konfigurálja a VPN-hitelesítés Azure Active Directoryon keresztüli biztosításához.

## <a name="redeem-the-fortigate-license"></a>A FortiGate-licenc beváltása

A következő generációs Fortinet FortiGate virtuális gépként érhető el az Azure-infrastruktúra szolgáltatásként (IaaS). Ehhez a virtuális géphez két licencelési mód van: utólagos elszámolású és saját licenc (BYOL).

Ha FortiGate-licencet vásárolt a Fortinet-ből a BYOL virtuális gép üzembe helyezési beállítással való használatra, akkor a Fortinet termékaktiválás oldaláról válthatja be https://support.fortinet.com . Az eredményül kapott licencfájl. lic kiterjesztéssel fog rendelkezni.

## <a name="download-firmware"></a>Belső vezérlőprogram letöltése

A Fortinet FortiGate Azure-beli virtuális gép az írás időpontjában nem az SAML-hitelesítéshez szükséges belső vezérlőprogram-verziót szállítja. A legújabb verziót a Fortinet-ből kell beszerezni.

1. Jelentkezzen be a következő címen: https://support.fortinet.com/ .
2. Válassza a   >  **belső vezérlőprogram lemezképének** letöltése lehetőséget.
3. A **kibocsátási megjegyzések** jobb oldalán válassza a **Letöltés** lehetőséget.
4. Válassza a **v 6,00**  >  **6,4**  >  **6.4.2** elemet.
5. Töltse le **FGT_VM64_AZURE-v6-build1723-Fortinet. out** -t úgy, hogy kijelöli a **https** -hivatkozást ugyanazon a sorban.
6. Mentse a fájlt később.

## <a name="deploy-the-fortigate-vm"></a>A FortiGate virtuális gép üzembe helyezése

1. Lépjen a Azure Portalra, és jelentkezzen be az előfizetésbe, amelybe telepíteni fogja a FortiGate virtuális gépet.
2. Hozzon létre egy új erőforráscsoportot, vagy nyissa meg azt az erőforráscsoportot, amelybe telepíteni fogja a FortiGate virtuális gépet.
3. Válassza a **Hozzáadás** lehetőséget.
4. A **Keresés a piactéren** mezőbe írja be a *Forti*. Válassza a **Fortinet FortiGate következő generációs tűzfal** lehetőséget.
5. Válassza ki a szoftvercsomagot (bring-your-own-License, ha rendelkezik licenccel vagy utólagos elszámolással). Válassza a **Létrehozás** lehetőséget.
6. Töltse fel a virtuális gép konfigurációját.

    ![Képernyőkép a virtuális gép létrehozásáról.](virtual-machine.png)

7. Állítsa be a **hitelesítési típust** a **jelszó** értékre, és adja meg a virtuális gép rendszergazdai hitelesítő adatait.
8. Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget.
9. Várjon, amíg befejeződik a virtuális gép üzembe helyezése.


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Adjon meg egy statikus nyilvános IP-címet, és rendeljen hozzá egy teljesen minősített tartománynevet

Az egységes felhasználói élmény érdekében állítsa a FortiGate virtuális géphez hozzárendelt nyilvános IP-címet statikusan hozzárendelt értékre. Emellett a teljes tartománynevet (FQDN) is leképezheti.

1. Lépjen a Azure Portalra, és nyissa meg a FortiGate virtuális gép beállításait.
2. Az **Áttekintés** képernyőn válassza ki a nyilvános IP-címet.

    ![Képernyőkép a FortiGate SSL VPN-ről.](public-ip-address.png)

3. Válassza a **statikus**  >  **Mentés** lehetőséget.

Ha a FortiGate virtuális gép üzembe helyezéséhez nyilvánosan irányítható tartománynév tartozik a környezethez, hozzon létre egy állomás (A) rekordot a virtuális géphez. Ez a rekord az előző, statikusan hozzárendelt nyilvános IP-címhez van leképezve.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port-8443"></a>Új bejövő hálózati biztonsági csoportra vonatkozó szabály létrehozása a 8443-as TCP-porthoz

1. Lépjen a Azure Portalra, és nyissa meg a FortiGate virtuális gép beállításait.
2. A bal oldali menüben válassza a **hálózatkezelés** lehetőséget. Megjelenik a hálózati adapter a listáról, és megjelennek a bejövő portok szabályai.
3. Válassza a **bejövő Port hozzáadása szabály** lehetőséget.
4. Hozzon létre egy új bejövő portszabály-szabályt a 8443-es TCP-porthoz.

    ![Képernyőkép a bejövő biztonsági szabály hozzáadásáról.](port-rule.png)

5. Válassza a **Hozzáadás** lehetőséget.

## <a name="create-a-second-virtual-nic-for-the-vm"></a>Hozzon létre egy második virtuális NIC-t a virtuális GÉPHEZ

Ahhoz, hogy a felhasználók számára elérhetővé kell tenni a belső erőforrásokat, egy második virtuális hálózati adaptert kell hozzáadni a FortiGate virtuális géphez. Az Azure-beli Virtual Network, amelyen a virtuális hálózati adapter található, irányítható kapcsolatban kell állnia a belső erőforrásokkal.

1. Lépjen a Azure Portalra, és nyissa meg a FortiGate virtuális gép beállításait.
2. Ha a FortiGate virtuális gép még nincs leállítva, válassza a **Leállítás** lehetőséget, és várjon, amíg a virtuális gép leáll.
3. A bal oldali menüben válassza a **hálózatkezelés** lehetőséget.
4. Válassza a **hálózati adapter csatolása** lehetőséget.
5. Válassza **a létrehozás lehetőséget, és csatlakoztassa a hálózati adaptert**.
6. Konfigurálja az új hálózati adapter tulajdonságait, majd válassza a **Létrehozás** lehetőséget.

    ![Képernyőfelvétel a hálózati adapter létrehozásáról.](new-network-interface.png)

7. Indítsa el a FortiGate virtuális gépet.


## <a name="configure-the-fortigate-vm"></a>A FortiGate virtuális gép konfigurálása

Az alábbi szakaszokból megtudhatja, hogyan állíthatja be a FortiGate virtuális gépet.

### <a name="install-the-license"></a>A licenc telepítése

1. Nyissa meg a következőt: `https://<address>`. Itt a `<address>` FortiGate virtuális géphez hozzárendelt teljes tartománynév vagy nyilvános IP-cím.

2. Folytassa a tanúsítvány esetleges hibáit.
3. Jelentkezzen be a FortiGate virtuális gép telepítése során megadott rendszergazdai hitelesítő adatok használatával.
4. Ha a telepítés a saját licencet használó modellt használja, megjelenik egy, a licenc feltöltésére vonatkozó kérdés. Válassza ki a korábban létrehozott licencfájl, és töltse fel a fájlt. Kattintson **az OK gombra** , és indítsa újra a FortiGate virtuális gépet.

    ![Képernyőkép a FortiGate VM-licencről.](license.png)

5. Az újraindítás után jelentkezzen be újra a rendszergazdai hitelesítő adatokkal a licenc érvényesítéséhez.

### <a name="update-firmware"></a>Belső vezérlőprogram frissítése

1. Nyissa meg a következőt: `https://<address>`. Itt a `<address>` FortiGate virtuális géphez hozzárendelt teljes tartománynév vagy nyilvános IP-cím.

2. Folytassa a tanúsítvány esetleges hibáit.
3. Jelentkezzen be a FortiGate virtuális gép telepítése során megadott rendszergazdai hitelesítő adatok használatával.
4. A bal oldali **menüben válassza a**  >  **rendszerbelső vezérlőprogram** elemet.
5. A **belső vezérlőprogram felügyelete** területen válassza a **Tallózás** lehetőséget, majd válassza ki a korábban letöltött belső vezérlőprogram-fájlt.
6. Hagyja figyelmen kívül a figyelmeztetést, és válassza a **biztonsági mentés konfiguráció és frissítés** lehetőséget.

    ![Képernyőfelvétel a belső vezérlőprogram felügyeletéről.](backup-configure-upgrade.png)

7. Válassza a **Folytatás** lehetőséget.
8. Amikor a rendszer kéri, hogy mentse a FortiGate-konfigurációt (. conf-fájlként), válassza a **Mentés** lehetőséget.
9. Várja meg a belső vezérlőprogram feltöltését és alkalmazásának megvárniát. Várjon, amíg a FortiGate virtuális gép újraindul.
10. A FortiGate virtuális gép újraindítása után jelentkezzen be újra a rendszergazdai hitelesítő adatokkal.
11. Amikor a rendszer felszólítja az irányítópult beállítására, válassza a **később** lehetőséget.
12. Az oktatóanyag-videó elkezdése után válassza **az OK gombot**.

### <a name="change-the-management-port-to-tcp-8443"></a>A felügyeleti port módosítása a TCP 8443-re

1. Nyissa meg a következőt: `https://<address>`. Itt a `<address>` FortiGate virtuális géphez hozzárendelt teljes tartománynév vagy nyilvános IP-cím.

2. Folytassa a tanúsítvány esetleges hibáit.
3. Jelentkezzen be a FortiGate virtuális gép telepítése során megadott rendszergazdai hitelesítő adatok használatával.
4. A bal oldali menüben válassza a **System (rendszeren**) lehetőséget.
5. Az **Adminisztráció beállításai** területen módosítsa a https-portot a **8443** értékre, majd kattintson az **alkalmaz** gombra.
6. A módosítás után a böngésző megkísérli az adminisztrációs oldal újratöltését, de az sikertelen lesz. Mostantól a felügyeleti oldal címe a következő: `https://<address>:8443` .

    ![Képernyőkép a távoli tanúsítvány feltöltéséről.](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Az Azure AD SAML aláíró tanúsítványának feltöltése

1. Nyissa meg a következőt: `https://<address>:8443`. Itt a `<address>` FortiGate virtuális géphez hozzárendelt teljes tartománynév vagy nyilvános IP-cím.

2. Folytassa a tanúsítvány esetleges hibáit.
3. Jelentkezzen be a FortiGate virtuális gép telepítése során megadott rendszergazdai hitelesítő adatok használatával.
4. A bal oldali **menüben válassza a**  >  **rendszertanúsítványok** lehetőséget.
5. Válassza a  >  **távoli tanúsítvány** importálása lehetőséget.
6. Tallózással keresse meg a FortiGate egyéni alkalmazás telepítése az Azure-bérlőben letöltött tanúsítványát. Jelölje ki, majd kattintson **az OK gombra**.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Egyéni SSL-tanúsítvány feltöltése és konfigurálása

Előfordulhat, hogy a FortiGate virtuális gépet saját SSL-tanúsítvánnyal szeretné konfigurálni, amely támogatja az Ön által használt teljes tartománynevet. Ha olyan SSL-tanúsítványhoz fér hozzá, amely PFX formátumban van csomagolva a titkos kulccsal, akkor erre a célra használható.

1. Nyissa meg a következőt: `https://<address>:8443`. Itt a `<address>` FortiGate virtuális géphez hozzárendelt teljes tartománynév vagy nyilvános IP-cím.

2. Folytassa a tanúsítvány esetleges hibáit.
3. Jelentkezzen be a FortiGate virtuális gép telepítése során megadott rendszergazdai hitelesítő adatok használatával.
4. A bal oldali **menüben válassza a**  >  **rendszertanúsítványok** lehetőséget.
5. Válassza a  >  **helyi tanúsítvány**  >  **PKCS #12 tanúsítvány** importálása lehetőséget.
6. Tallózással keresse meg a következőt:. Az SSL-tanúsítványt és a titkos kulcsot tartalmazó PFX-fájl.
7. Adja meg a. PFX-jelszó és a tanúsítvány kifejező neve. Ez után válassza az **OK** gombot.
8. A bal oldali **menüben válassza a**  >  **Rendszerbeállítások** elemet.
9. Az **Adminisztráció beállításai** területen bontsa ki a **https-kiszolgálói tanúsítvány** melletti listát, és válassza ki a korábban importált SSL-tanúsítványt.
10. Kattintson az **Alkalmaz** gombra.
11. Zárjuk be a böngészőablakot, és nyissa meg a következőt: `https://<address>:8443` .
12. Jelentkezzen be a FortiGate rendszergazdai hitelesítő adataival. Ekkor látnia kell a megfelelő SSL-tanúsítványt a használatban.

### <a name="configure-authentication-timeout"></a>Hitelesítési időtúllépés konfigurálása

1. Lépjen a Azure Portalra, és nyissa meg a FortiGate virtuális gép beállításait.
2. A bal oldali menüben válassza a **soros konzol** lehetőséget.
3. Jelentkezzen be a soros konzolon a FortiGate VM-rendszergazda hitelesítő adataival.
4. A soros konzolon futtassa a következő parancsokat:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```

### <a name="ensure-network-interfaces-are-obtaining-ip-addresses"></a>Győződjön meg arról, hogy a hálózati adapterek IP-címeket szereznek be

1. Nyissa meg a következőt: `https://<address>:8443`. Itt a `<address>` FortiGate virtuális géphez hozzárendelt teljes tartománynév vagy nyilvános IP-cím.

2. Jelentkezzen be a FortiGate virtuális gép telepítése során megadott rendszergazdai hitelesítő adatok használatával.
3. A bal oldali menüben válassza a **hálózatkezelés** lehetőséget.
4. A hálózat területen válassza a **felületek** elemet.
5. Vizsgálja meg a port1 (külső felület) és a port2 (belső csatoló), hogy az IP-címet a megfelelő Azure-alhálózaton szerezheti be.
    a. Ha a port nem kap IP-címet az alhálózatról (DHCP protokollon keresztül), kattintson a jobb gombbal a portra, majd válassza a **Szerkesztés** lehetőséget.
    b. A címzési mód mellett győződjön meg arról, hogy a **DHCP** van kiválasztva.
    c. Seelct **OK**.

    ![Képernyőkép a hálózati adapter címzéséről.](addressing.png)

### <a name="ensure-fortigate-vm-has-correct-route-to-on-premises-corporate-resources"></a>Győződjön meg arról, hogy a FortiGate virtuális gép megfelelő útvonalat biztosít a helyszíni vállalati erőforrásokhoz

A többhelyes Azure-beli virtuális gépek mindegyike ugyanazon a virtuális hálózaton (de talán különálló alhálózatokon) található összes hálózati adapterrel rendelkezik. Ez gyakran azt jelenti, hogy mindkét hálózati adapternek kapcsolata van a FortiGate-on keresztül közzétett helyszíni vállalati erőforrásokkal. Ezért olyan egyéni útválasztási bejegyzéseket kell létrehoznia, amelyek biztosítják, hogy a forgalom a megfelelő felületen lépjen ki, amikor a helyszíni vállalati erőforrásokra vonatkozó kérések történnek.

1. Nyissa meg a következőt: `https://<address>:8443`. Itt a `<address>` FortiGate virtuális géphez hozzárendelt teljes tartománynév vagy nyilvános IP-cím.

2. Jelentkezzen be a FortiGate virtuális gép telepítése során megadott rendszergazdai hitelesítő adatok használatával.
3. A bal oldali menüben válassza a **hálózatkezelés** lehetőséget.
4. A hálózat területen válassza a **statikus útvonalak** elemet.
5. Válassza az **Új létrehozása** lehetőséget.
6. A cél mellett válassza az **alhálózat** lehetőséget.
7. Az alhálózat területen válassza ki azokat az alhálózati információkat, amelyeken a helyszíni vállalati erőforrások találhatók (például 10.1.0.0/255.255.255.0).
8. Az átjáró címe mellett határozza meg az átjárót az Azure-alhálózaton, ahol a port2 csatlakoztatva van (például ez általában 1 mint 10.6.1.1-ben végződik)
9. A felület mellett válassza ki a belső hálózati adaptert, a port2
10. Válassza az **OK** lehetőséget.

    ![Az útvonal konfigurálásának képernyőképe.](route.png)

## <a name="configure-fortigate-ssl-vpn"></a>FortiGate SSL VPN konfigurálása

Kövesse a következő témakörben ismertetett lépéseket: https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial

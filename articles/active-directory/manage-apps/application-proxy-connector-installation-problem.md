---
title: Probléma az alkalmazásproxy-ügynök összekötőjének telepítésekor
description: Az alkalmazásproxy-ügynök összekötő Azure Active Directory való telepítésekor esetlegesen felmerülő problémák elhárítása.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/28/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 0f76f03883746b6f4b87bb817f8adde850ed28b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99253664"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Probléma az alkalmazásproxy-ügynök összekötőjének telepítésekor

Microsoft Azure Active Directory alkalmazásproxy-összekötő egy belső tartomány-összetevő, amely kimenő kapcsolatokat használ a felhőben elérhető végpont és a belső tartomány közötti kapcsolat létrehozásához.

## <a name="general-problem-areas-with-connector-installation"></a>Általános problémás területek az összekötő telepítésével

Ha egy összekötő telepítése nem sikerül, a kiváltó ok általában az alábbi területek egyike. **A hibaelhárítás előfutáraként mindenképp indítsa újra az összekötőt.**

1.  **Kapcsolat** – a sikeres telepítés befejezéséhez az új összekötőnek regisztrálnia kell és meg kell teremtenie a jövőbeli megbízhatósági tulajdonságokat. Ezt úgy teheti meg, hogy csatlakozik a Azure Active Directory Application Proxy Cloud Service-hez.

2.  **Megbízhatósági kapcsolat létesítése** – az új összekötő létrehoz egy önaláírt tanúsítványt, és regisztrálja magát a Cloud Service-ben.

3.  **A rendszergazda hitelesítése** – a telepítés során a felhasználónak rendszergazdai hitelesítő adatokat kell megadnia az összekötő telepítésének befejezéséhez.

> [!NOTE]
> Az összekötő telepítési naplói megtalálhatók a% TEMP% mappában, és további információkat is megadhatnak a telepítési hibák okának megadásáról.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>A Cloud Application proxy szolgáltatás és a Microsoft bejelentkezési oldal kapcsolatának ellenőrzése

**Cél:** Ellenőrizze, hogy az összekötő számítógép tud-e csatlakozni az alkalmazásproxy regisztrációs végponthoz, valamint a Microsoft bejelentkezési lapjához.

1.  Az összekötő-kiszolgálón futtassa a port tesztet a [Telnet](/windows-server/administration/windows-commands/telnet) vagy más port tesztelési eszköz használatával annak ellenőrzéséhez, hogy a 443-es és a 80-es portok nyitva vannak-e.

2.  Ha a portok bármelyike nem sikeres, ellenőrizze, hogy a tűzfal vagy a háttér-proxy hozzáfér-e a szükséges tartományokhoz és portokhoz. lásd: a helyszíni [környezet előkészítése](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Nyisson meg egy böngészőt (külön lapon), és lépjen a következő weblapra:, és ellenőrizze, hogy be `https://login.microsoftonline.com` tud-e jelentkezni az adott oldalra.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-certificate"></a>Ellenőrizze, hogy a számítógép-és háttér-összetevők támogatják-e az alkalmazásproxy megbízhatósági tanúsítványát

**Cél:** Győződjön meg arról, hogy az összekötő gép, a háttér-proxy és a tűzfal támogatja az összekötő által a jövőbeli megbízhatósághoz létrehozott tanúsítványt, valamint a tanúsítvány érvényességét.

>[!NOTE]
>Az összekötő megpróbál létrehozni egy, a TLS 1.2 által támogatott SHA512-tanúsítványt. Ha a gép vagy a háttérrendszer tűzfala és proxyja nem támogatja a TLS 1.2-et, a telepítés sikertelen lesz.
>
>

**Tekintse át a szükséges előfeltételeket:**

1.  Ellenőrizze, hogy a gép támogatja-e a TLS 1.2-et – a 2012 R2 utáni összes Windows-verziónak támogatnia kell a TLS 1,2 Ha az összekötő-számítógép 2012 R2 vagy korábbi verziójú, akkor győződjön meg arról, hogy a következő Tudásbázis vannak telepítve a gépen: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Forduljon a hálózati rendszergazdához, és kérje meg, hogy ellenőrizze, hogy a háttér-proxy és a tűzfal nem blokkolja-e a kimenő forgalom SHA512.

**Az ügyféltanúsítvány ellenőrzése:**

Ellenőrizze az aktuális ügyféltanúsítvány ujjlenyomatát. A tanúsítványtároló a következő címen található: `%ProgramData%\microsoft\Microsoft AAD Application Proxy Connector\Config\TrustSettings.xml` .

```
<?xml version="1.0" encoding="utf-8"?>
<ConnectorTrustSettingsFile xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <CloudProxyTrust>
    <Thumbprint>4905CC64B2D81BBED60962ECC5DCF63F643CCD55</Thumbprint>
    <IsInUserStore>false</IsInUserStore>
  </CloudProxyTrust>
</ConnectorTrustSettingsFile>
```

A lehetséges **IsInUserStore** értékek értéke **igaz** és **hamis**. Az **igaz** érték azt jelenti, hogy az automatikusan megújított tanúsítványt a hálózati szolgáltatás felhasználói tanúsítványtárolójának személyes tárolójában tárolja a rendszer. A **false** érték azt jelenti, hogy az ügyféltanúsítvány a Register-AppProxyConnector parancs által kezdeményezett telepítés vagy regisztráció során lett létrehozva, és a rendszer a helyi gép tanúsítványtárolójának személyes tárolójában tárolja.

Ha az érték **true (igaz**), kövesse az alábbi lépéseket a tanúsítvány ellenőrzéséhez:
1. [PsTools.zip](/sysinternals/downloads/pstools) letöltése
2. Bontsa ki a [PsExec](/sysinternals/downloads/psexec) a csomagból, és futtassa a **PsExec-i-u "NT AUTHORITY \ szolgáltatás" cmd.exe** egy rendszergazda jogú parancssorból.
3. Futtassa a **certmgr. msc fájlt** az újonnan megjelenő parancssorban
4. A felügyeleti konzolon bontsa ki a személyes tárolót, és kattintson a tanúsítványok elemre.
5. A **connectorregistrationca.msappproxy.net** által kiállított tanúsítvány megkeresése

Ha az érték **hamis**, kövesse az alábbi lépéseket a tanúsítvány ellenőrzéséhez:
1. A **certlm. msc** futtatása
2. A felügyeleti konzolon bontsa ki a személyes tárolót, és kattintson a tanúsítványok elemre.
3. A **connectorregistrationca.msappproxy.net** által kiállított tanúsítvány megkeresése

**Az ügyféltanúsítvány megújítása:**

Ha egy összekötő több hónapig nem csatlakozik a szolgáltatáshoz, előfordulhat, hogy a tanúsítványa elavult. A tanúsítvány megújításának meghibásodása egy lejárt tanúsítványhoz vezet. Így az összekötő szolgáltatás leáll. Az 1000-es eseményt a rendszer az összekötő felügyeleti naplójába rögzíti:

"Az összekötő ismételt regisztrálása nem sikerült: az összekötő megbízhatósági tanúsítványa lejárt. Futtassa a PowerShell-parancsmag Register-AppProxyConnector azon a számítógépen, amelyen az összekötő fut, és regisztrálja újra az összekötőt. "

Ebben az esetben távolítsa el, majd telepítse újra az összekötőt a regisztráció elindításához, vagy futtassa a következő PowerShell-parancsokat:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

Az Register-AppProxyConnector paranccsal kapcsolatos további tudnivalókért tekintse meg [a felügyelet nélküli telepítési parancsfájl létrehozása az Azure ad Application proxy-összekötőhöz](./application-proxy-register-connector-powershell.md) című témakört.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Ellenőrizze, hogy a rendszergazda az összekötő telepítéséhez használt-e

**Cél:** Ellenőrizze, hogy az összekötőt telepítő felhasználó a megfelelő hitelesítő adatokkal rendelkező rendszergazda-e. A felhasználónak jelenleg legalább egy alkalmazás-rendszergazdának kell lennie ahhoz, hogy a telepítés sikeres legyen.

**A hitelesítő adatok helyességének ellenőrzése:**

Csatlakozhat, `https://login.microsoftonline.com` és ugyanazokat a hitelesítő adatokat használja. Győződjön meg arról, hogy a bejelentkezés sikeres. A felhasználói szerepkört úgy tekintheti meg, hogy **Azure Active Directory**  - &gt; **felhasználókat és csoportokat**  - &gt; **minden felhasználó számára**. 

Válassza ki a felhasználói fiókot, majd a "címtárbeli szerepkör" lehetőséget az eredményül kapott menüben. Győződjön meg arról, hogy a kiválasztott szerepkör az "Application Administrator". Ha nem fér hozzá a fenti lépésekhez tartozó lapokhoz, nem rendelkezik a szükséges szerepkörrel.

## <a name="next-steps"></a>Következő lépések
[Az Azure AD Application Proxy-összekötők ismertetése](application-proxy-connectors.md)

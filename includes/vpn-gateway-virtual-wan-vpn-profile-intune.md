---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a1fb1c1be8a0203d9f36712fda8e30f0f9354091
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576115"
---
A profilokat üzembe helyezheti az Azure VPN-ügyfelekhez (Windows 10) Microsoft Intune használatával. Ez a cikk segítséget nyújt az Intune-profilok egyéni beállításokkal történő létrehozásában.

## <a name="prerequisites"></a>Előfeltételek

* Az eszközök már regisztrálva vannak az Intune MDM.
* A Windows 10 rendszerhez készült Azure VPN-ügyfél már telepítve van az ügyfélszámítógépen.
* Csak a Windows 19H2 vagy újabb verziója támogatott.

## <a name="modify-xml"></a><a name="xml"></a>XML módosítása

A következő lépésekben egy minta XML-t használunk az Intune egyéni OMA-URI profiljához a következő beállításokkal:

* Automatikus bejelentkezés
* A megbízható hálózat észlelése engedélyezve.

Más támogatott lehetőségekért tekintse meg a [VPNV2 CSP](https://docs.microsoft.com/windows/client-management/mdm/vpnv2-csp) -t ismertető cikket.

1. Töltse le a VPN-profilt a Azure Portalból, és bontsa ki a *azurevpnconfig.xml* fájlt a csomagból.
1. Másolja és illessze be az alábbi szöveget egy új szövegszerkesztőbe.

   ```xml-interactive
    <VPNProfile>
      <!--<EdpModeId>corp.contoso.com</EdpModeId>-->
      <RememberCredentials>true</RememberCredentials>
      <AlwaysOn>true</AlwaysOn>
      <TrustedNetworkDetection>contoso.com,test.corp.contoso.com</TrustedNetworkDetection>
      <DeviceTunnel>false</DeviceTunnel>
      <RegisterDNS>false</RegisterDNS>
      <PluginProfile>
        <ServerUrlList>azuregateway-7cee0077-d553-4323-87df-069c331f58cb-053dd0f6af02.vpn.azure.com</ServerUrlList> 
        <CustomConfiguration>
        </CustomConfiguration>
        <PluginPackageFamilyName>Microsoft.AzureVpn_8wekyb3d8bbwe</PluginPackageFamilyName>
      </PluginProfile>
    </VPNProfile>
   ```
1. Módosítsa a és a közötti bejegyzést ```<ServerUrlList>``` ```</ServerUrlList>``` a letöltött profil (azurevpnconfig.xml) bejegyzéseivel. Módosítsa a "TrustedNetworkDetection" FQDN-t úgy, hogy az illeszkedjen a környezetéhez.
1. Nyissa meg az Azure letöltött profilt (azurevpnconfig.xml), és másolja a tartalmat a vágólapra a szöveg kiemelésével, és nyomja le a <ctrl> + C billentyűkombinációt. másolja a következő AzVpnProfile-sorok között, de ne másolja maguk a AzVpnProfile sorokat:

   ```
   <AzVpnProfile xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.datacontract.org/2004/07/">
     <any xmlns:d2p1="http://schemas.datacontract.org/2004/07/System.Xml"
       i:nil="true" />
   For example - copy the text in your xml that is located here.
   </AzVpnProfile>
   ```
1. Illessze be a másolt szöveget az előző lépésből a címkék között a 2. lépésben létrehozott fájlba ```<CustomConfiguration>  </CustomConfiguration>``` . Mentse a fájlt egy XML-bővítménnyel.
1. Jegyezze fel az értéket a ```<name>  </name>``` címkékben. Ez a profil neve. Erre a névre szüksége lesz, amikor létrehozza a profilt az Intune-ban. Zárjuk be a fájlt, és jegyezze fel a mentési helyét.

## <a name="create-intune-profile"></a>Intune-profil létrehozása

Ebben a szakaszban egy Microsoft Intune-profilt hoz létre egyéni beállításokkal.

1. Jelentkezzen be az Intune-ba, és navigáljon az **eszközök-> konfigurációs profilokhoz**. Válassza a **+ profil létrehozása** lehetőséget.

   :::image type="content" source="./media/vpn-gateway-virtual-wan-vpn-profile-intune/configuration-profile.png" alt-text="Konfigurációs profilok":::
1. A **platform** területen válassza a **Windows 10 és újabb** lehetőséget. A **profil** lapon válassza az **Egyéni** lehetőséget. Ezután kattintson a **Létrehozás** elemre.
1. Adja meg a profil nevét és leírását, majd kattintson a **tovább** gombra.
1. A **konfigurációs beállítások** lapon válassza a **Hozzáadás** lehetőséget.

    * **Név:** Adja meg a konfiguráció nevét.
    * **Leírás:** Nem kötelező leírás.
    * **OMA-URI:** ```./User/Vendor/MSFT/VPNv2/<name of your connection>/ProfileXML``` (ez az információ a <name></name> címke azurevpnconfig.xml fájljában található).
    * **Adattípus:** Karakterlánc (XML-fájl).

   Jelölje ki a mappa ikont, és válassza ki az [XML-](#xml) lépések 6. lépésében mentett fájlt. Válassza a **Hozzáadás** elemet.

   :::image type="content" source="./media/vpn-gateway-virtual-wan-vpn-profile-intune/configuration-settings.png" alt-text="Konfigurációs beállítások" lightbox="./media/vpn-gateway-virtual-wan-vpn-profile-intune/configuration-settings.png":::
1. Kattintson a **Tovább** gombra.
1. A **hozzárendelések** alatt válassza ki azt a csoportot, amelyhez el szeretné küldeni a konfigurációt. Ezután válassza a **tovább** lehetőséget.
1. Az alkalmazhatósági szabályok megadása nem kötelező. Szükség esetén adjon meg minden szabályt, majd kattintson a **tovább** gombra.
1. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="./media/vpn-gateway-virtual-wan-vpn-profile-intune/create-profile.png" alt-text="Profil létrehozása":::
1. Az egyéni profil létrehozása megtörtént. A profil üzembe helyezésének Microsoft Intune lépéseiért lásd: [felhasználói és eszköz profilok társítása](https://docs.microsoft.com/mem/intune/configuration/device-profile-assign).

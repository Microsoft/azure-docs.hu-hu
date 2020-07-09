---
title: Biztonságos Azure Service Fabric-fürt kapcsolatainak konfigurálása
description: Ismerje meg, hogyan konfigurálhatja a Visual studiót az Azure Service Fabric-fürt által támogatott biztonságos kapcsolatok konfigurálására.
author: cawaMS
ms.topic: conceptual
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: 11f76153726d3fc92118fb46cc61b4627ab6a1b2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75464093"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Biztonságos kapcsolatok konfigurálása egy Service Fabric-fürthöz a Visual studióból
Ismerje meg, hogyan férhet hozzá a Visual Studióval egy Azure Service Fabric-fürt biztonságos eléréséhez a konfigurált hozzáférés-vezérlési házirendekkel.

## <a name="cluster-connection-types"></a>Fürt kapcsolatainak típusai
Az Azure Service Fabric-fürt két típusú kapcsolatot támogat: **nem biztonságos** kapcsolatok és **x509 tanúsítványalapú** biztonságos kapcsolatok. (A helyszíni, **Windows** -és **dSTS** -alapú hitelesítések esetében is támogatottak a Service Fabric-fürtök.) Konfigurálnia kell a fürt kapcsolódási típusát a fürt létrehozásakor. Miután létrejött, a kapcsolattípus nem módosítható.

A Visual Studio Service Fabric eszközei támogatják a fürthöz való csatlakozáshoz szükséges összes hitelesítési típust a közzétételhez. A biztonságos Service Fabric fürt beállításával kapcsolatos útmutatásért lásd: [Service Fabric-fürt beállítása a Azure Portalból](service-fabric-cluster-creation-via-portal.md) .

## <a name="configure-cluster-connections-in-publish-profiles"></a>A fürt kapcsolatainak konfigurálása a közzétételi profilokban
Ha Service Fabric projektet tesz közzé a Visual studióból, a **Service Fabric alkalmazás közzététele** párbeszédpanelen választhatja ki az Azure Service Fabric-fürtöt. A **csatlakoztatási végpont**területen válasszon ki egy meglévő fürtöt az előfizetése alatt.

![A * * közzétételi Service Fabric alkalmazás * * párbeszédpanel egy Service Fabric-kapcsolatok konfigurálására szolgál.][publishdialog]

A **Service Fabric alkalmazás közzététele** párbeszédpanel automatikusan ellenőrzi a fürthöz való kapcsolatokat. Ha a rendszer kéri, jelentkezzen be az Azure-fiókjába. Ha az érvényesítés sikeres, az azt jelenti, hogy a rendszernek megfelelő tanúsítványok vannak telepítve a fürt biztonságos csatlakoztatásához, vagy a fürt nem biztonságos. Az érvényesítési hibákat hálózati problémák okozhatják, vagy ha a rendszer konfigurációja nem megfelelően van konfigurálva a biztonságos fürthöz való kapcsolódáshoz.

![A * * közzétételi Service Fabric alkalmazás * * párbeszédpanel érvényesít egy meglévő, megfelelően konfigurált Service Fabric-fürtöt.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Kapcsolódás biztonságos fürthöz
1. Győződjön meg arról, hogy a cél fürt által megbízhatónak ítélt Ügyféltanúsítványok egyikét is elérheti. A tanúsítvány általában személyes információcsere (. pfx) fájlként van megosztva. Lásd: [Service Fabric-fürt beállítása a Azure Portalból](service-fabric-cluster-creation-via-portal.md) , hogy miként konfigurálhatja a kiszolgálót egy ügyfélhez való hozzáférés biztosításához.
2. Telepítse a megbízható tanúsítványt. Ehhez kattintson duplán a. pfx fájlra, vagy használja az import-PfxCertificate PowerShell-parancsfájlt a tanúsítványok importálásához. Telepítse a tanúsítványt a következő tanúsítványra **: \ LocalMachine\My**. A tanúsítvány importálása során az összes alapértelmezett beállítást el kell fogadnia.
3. A projekt helyi menüjében válassza a **Publish...** parancsot az **Azure-alkalmazás közzététele** párbeszédpanel megnyitásához, majd válassza ki a célként megadott fürtöt. Az eszköz automatikusan feloldja a kapcsolatokat, és menti a biztonságos kapcsolatok paramétereit a közzétételi profilban.
4. Nem kötelező: a közzétételi profil szerkesztésével megadhat egy biztonságos fürtöt.
   
   Mivel manuálisan módosítja a közzétételi profil XML-fájlját a tanúsítvány adatainak megadásához, jegyezze fel a tanúsítványtároló nevét, az áruház helyét és a tanúsítvány ujjlenyomatát. Ezeket az értékeket meg kell adnia a tanúsítvány tárolójának és tárolási helyének. További információt a [tanúsítvány ujjlenyomatának beolvasása](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx) című témakörben talál.
   
   A *ClusterConnectionParameters* paraméterrel megadhatja a Service Fabric-fürthöz való csatlakozáskor használandó PowerShell-paramétereket. Az érvényes paraméterek tetszőlegesek, amelyeket a ServiceFabricCluster parancsmag fogad el. A rendelkezésre álló paraméterek listáját a [Csatlakozás – ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster) című részben tekintheti meg.
   
   Ha távoli fürtöt tesz közzé, meg kell adnia a megfelelő paramétereket az adott fürthöz. A következő példa egy nem biztonságos fürthöz való csatlakozásra mutat be:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Az alábbi példa egy x509 tanúsítvány alapú biztonságos fürthöz való csatlakozásra mutat:
   
   ```xml
   <ClusterConnectionParameters
   ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
   X509Credential="true"
   ServerCertThumbprint="0123456789012345678901234567890123456789"
   FindType="FindByThumbprint"
   FindValue="9876543210987654321098765432109876543210"
   StoreLocation="CurrentUser"
   StoreName="My" />
   ```
5. Szerkessze az egyéb szükséges beállításokat, például a frissítési paramétereket és az alkalmazás paramétereinek helyét, majd tegye közzé az alkalmazást a Visual Studióban a **közzététel Service Fabric alkalmazásban** párbeszédpanelen.

## <a name="next-steps"></a>További lépések
A Service Fabric-fürtök elérésével kapcsolatos további információkért lásd: [a fürt megjelenítése Service Fabric Explorer használatával](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png

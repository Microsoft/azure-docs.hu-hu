---
title: Szolgáltatás portszámának megadása paraméterek használatával
description: Bemutatja, hogyan használhatók paraméterek egy alkalmazás portjának megadásához Service Fabric
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: ba2fb459dc9c981ad168aca4d0edf969650ccf48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576706"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Szolgáltatás portszámának megadása a Service Fabric paramétereinek használatával

Ez a cikk bemutatja, hogyan határozhatja meg egy szolgáltatás portszámát a Visual Studio használatával Service Fabric paramétereket.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Eljárás a szolgáltatás portszámának megadásához paraméterek használatával

Ebben a példában a asp.net Core webes API portszámát paraméterrel állíthatja be.

1. Nyissa meg a Visual studiót, és hozzon létre egy új Service Fabric alkalmazást.
1. Válassza ki az állapot nélküli ASP.NET Core sablont.
1. Válassza a webes API lehetőséget.
1. Nyissa meg a ServiceManifest.xml fájlt.
1. Jegyezze fel a szolgáltatáshoz megadott végpont nevét. Az alapértelmezett szint a `ServiceEndpoint`.
1. A ApplicationManifest.xml fájl megnyitása
1. A `ServiceManifestImport` elemben adjon hozzá egy új `RessourceOverrides` elemet a ServiceManifest.xml fájlban található végpontra mutató hivatkozással.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. A `Endpoint` elemben mostantól felülbírálhat bármely attribútumot egy paraméter használatával. Ebben a példában a `Port` szögletes zárójelek használatával adja meg és állítja be a paraméter nevét – például: `[MyWebAPI_PortNumber]`

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint" Port="[MyWebAPI_PortNumber]"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. Továbbra is a ApplicationManifest.xml fájlban adja meg a paramétert a `Parameters` elemben.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. És Definiáljon egy `DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. Nyissa meg a ApplicationParameters mappát és a `Cloud.xml` fájlt
1. Ha másik portot szeretne megadni a távoli fürtre való közzétételkor, adja hozzá a paramétert a fájlhoz a portszámmal.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Ha az alkalmazást a Visual studióból a Cloud.xml publish profil használatával teszi közzé, a szolgáltatás a 80-es port használatára van konfigurálva. Ha az alkalmazást a MyWebAPI_PortNumber paraméter meghatározása nélkül helyezi üzembe, a szolgáltatás a 8080-es portot használja.

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni az ebben a cikkben tárgyalt alapfogalmakról, tekintse meg az [Alkalmazások kezelése több környezethez című cikket](service-fabric-manage-multiple-environment-app-configuration.md).

További információ a Visual Studióban elérhető egyéb alkalmazás-felügyeleti lehetőségekről: [Service Fabric alkalmazások kezelése a Visual Studióban](service-fabric-manage-application-in-visual-studio.md).

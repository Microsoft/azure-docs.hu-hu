---
title: Tutorial - Configure geographic traffic routing with Azure Traffic Manager
description: This tutorial explains how to configure the geographic traffic routing method using Azure Traffic Manager
services: traffic-manager
author: asudbring
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: allensu
ms.openlocfilehash: 9de1f0b0adc4d82b666adcd4bc9b26e31e7750d6
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422797"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Tutorial: Configure the geographic traffic routing method using Traffic Manager

The Geographic traffic routing method allows you to direct traffic to specific endpoints based on the geographic location where the requests originate. This tutorial shows you how to create a Traffic Manager profile with this routing method and configure the endpoints to receive traffic from specific geographies.

## <a name="create-a-traffic-manager-profile"></a>Create a Traffic Manager Profile

1. Egy böngészőben jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nincs fiókja, regisztrálhat egy [egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/).
2. Kattintson az **Erőforrás létrehozása** > **Hálózat** > **Traffic Manager-profil** > **Létrehozás** lehetőségre.
4. In the **Create Traffic Manager profile**:
    1. Provide a name for your profile. This name needs to be unique within the trafficmanager.net zone. To access your Traffic Manager profile, you use the DNS name `<profilename>.trafficmanager.net`.
    2. Select the **Geographic** routing method.
    3. Select the subscription you want to create this profile under.
    4. Use an existing resource group or create a new resource group to place this profile under. If you choose to create a new resource group, use the **Resource Group location** dropdown to specify the location of the resource group. This setting refers to the location of the resource group, and has no impact on the Traffic Manager profile that's deployed globally.
    5. After you click **Create**, your Traffic Manager profile is created and deployed globally.

![Traffic Manager-profil létrehozása](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Add endpoints

1. Search for the Traffic Manager profile name you created in the portal’s search bar and click on the result when it is shown.
2. Navigate to **Settings** -> **Endpoints** in Traffic Manager.
3. Click **Add** to show the **Add Endpoint**.
3. Click **Add** and in the **Add endpoint** that is displayed, complete as follows:
4. Select **Type** depending upon the type of endpoint you are adding. For geographic routing profiles used in production, we strongly recommend using nested endpoint types containing a child profile with more than one endpoint. For more details, see [FAQs about geographic traffic routing methods](traffic-manager-FAQs.md).
5. Adjon meg egy **Nevet**, amelyről felismeri majd a végpontot.
6. Certain fields on this page depend on the type of endpoint you are adding:
    1. If you are adding an Azure endpoint, select the **Target resource type** and the **Target** based on the resource you want to direct traffic to
    2. If you are adding an **External** endpoint, provide the **Fully-qualified domain name (FQDN)** for your endpoint.
    3. If you are adding a **Nested endpoint**, select the **Target resource** that corresponds to the child profile you want to use and specify the **Minimum child endpoints count**.
7. In the Geo-mapping section, use the drop down to add the regions from where you want traffic to be sent to this endpoint. You must add at least one region, and you can have multiple regions mapped.
8. Repeat this for all endpoints you want to add under this profile

![Traffic Manager-végpont hozzáadása](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Use the Traffic Manager profile
1.  In the portal’s search bar, search for the **Traffic Manager profile** name that you created in the preceding section and click on the traffic manager profile in the results that the displayed.
2. Kattintson az **Áttekintés** elemre.
3. A **Traffic Manager-profil** mezőben megjelenik az újonnan létrehozott Traffic Manager-profil DNS-neve. This can be used by any clients (for example, by navigating to it using a web browser) to get routed to the right endpoint as determined by the routing type.  In the case of geographic routing, Traffic Manager looks at the source IP of the incoming request and determines the region from which it is originating. If that region is mapped to an endpoint, traffic is routed to there. If this region is not mapped to an endpoint, then Traffic Manager returns a NODATA query response.

## <a name="next-steps"></a>Következő lépések

- Learn more about [Geographic traffic routing method](traffic-manager-routing-methods.md#geographic).
- Learn how to [test Traffic Manager settings](traffic-manager-testing-settings.md).

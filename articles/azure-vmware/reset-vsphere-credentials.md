---
title: VSphere hitelesítő adatok alaphelyzetbe állítása Azure VMware-megoldáshoz
description: Ismerje meg, hogyan állíthatja alaphelyzetbe az Azure VMware-megoldás vSphere hitelesítő adatait, és gondoskodjon arról, hogy a HCX-összekötő a legújabb vSphere hitelesítő adatokkal rendelkezik.
ms.topic: how-to
ms.date: 03/31/2021
ms.openlocfilehash: 793b79e42a0adbca54804d1b66102736aff22d7a
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109101"
---
# <a name="reset-vsphere-credentials-for-azure-vmware-solution"></a>VSphere hitelesítő adatok alaphelyzetbe állítása Azure VMware-megoldáshoz

Ebből a cikkből megtudhatja, hogyan állíthatja alaphelyzetbe a vCenter Server és a NSX-T Manager hitelesítő adatait az Azure VMware-megoldás privát felhője számára. Ezzel biztosíthatja, hogy a HCX-összekötő a legújabb vCenter Server hitelesítő adatokkal rendelkezik.

Ezen útmutató mellett megtekintheti a videót is a [vCenter CloudAdmin & NSX-T rendszergazdai jelszavának alaphelyzetbe állításához](https://youtu.be/cK1qY3knj88).

## <a name="reset-your-azure-vmware-solution-credentials"></a>Az Azure VMware-megoldás hitelesítő adatainak alaphelyzetbe állítása

 Először állítsa alaphelyzetbe az Azure VMare-megoldás összetevőinek hitelesítő adatait. A vCenter Server CloudAdmin és NSX-T rendszergazdai hitelesítő adatai nem járnak le; az alábbi lépéseket követve azonban új jelszavakat is létrehozhat ezekhez a fiókokhoz.

> [!NOTE]
> Ha a CloudAdmin hitelesítő adatait használja a csatlakoztatott szolgáltatások, például a HCX, a vRealize Orchestrator, a vRealizae Operations Manager vagy a VMware Horizon esetében, a kapcsolatok a jelszó frissítése után nem fognak működni.  Ezeket a szolgáltatásokat le kell állítani a jelszó elforgatásának megkezdése előtt.  Ennek elmulasztása miatt ideiglenes zárolást eredményezhet a vCenter-CloudAdmin és a NSX-T rendszergazdai fiókokkal, mivel ezek a szolgáltatások folyamatosan a régi hitelesítő adataik használatával fognak megszólítani.  A csatlakoztatott szolgáltatások különálló fiókjainak beállításával kapcsolatos további információkért lásd: [hozzáférés és identitás fogalmai](https://docs.microsoft.com/azure/azure-vmware/concepts-identity).

1. Nyisson meg egy Azure Cloud Shell-munkamenetet a Azure Portal.

2. Futtassa a következő parancsot a vCenter-CloudAdmin jelszavának frissítéséhez.  Le kell cserélnie a ({SubscriptionID}, {ResourceGroup} és {PrivateCloudName}) értéket azon privát felhő tényleges értékeivel, amelyhez a CloudAdmin-fiók tartozik.

```
az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```
          
3. Futtassa a következő parancsot a NSX-T rendszergazdai jelszavának frissítéséhez. Le kell cserélnie a ({SubscriptionID}, {ResourceGroup} és {PrivateCloudName}) értéket a saját felhő tényleges értékeivel, amelyek a NSX-T rendszergazdai fiókhoz tartoznak.

```
az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```

## <a name="ensure-the-hcx-connector-has-your-latest-vcenter-server-credentials"></a>Győződjön meg arról, hogy a HCX-összekötő rendelkezik a legújabb vCenter Server hitelesítő adataival

Most, hogy alaphelyzetbe állítja a hitelesítő adatait, kövesse az alábbi lépéseket annak biztosításához, hogy a HCX-összekötő rendelkezik a frissített hitelesítő adataival.

1. A jelszó módosítása után nyissa meg a helyszíni HCX-összekötő webes felületét az HCX-összekötő berendezés https://{IP-címének használatával}: 443. Ügyeljen arra, hogy a 443-es portot használja. Jelentkezzen be az új hitelesítő adataival.

2. A VMware HCX irányítópulton válassza a **hely párosítása** lehetőséget.
    
    :::image type="content" source="media/reset-vsphere-credentials/hcx-site-pairing.png" alt-text="Képernyőkép a VMware HCX-irányítópultról a hely párosításával kiemelve.":::
 
3. Válassza ki a megfelelő kapcsolódást az Azure VMware-megoldáshoz (ha van több), és válassza a **Kapcsolódás szerkesztése** lehetőséget.
 
4. Adja meg az új vCenter Server CloudAdmin felhasználói hitelesítő adatait, és válassza a **Szerkesztés** lehetőséget, amely menti a hitelesítő adatokat. A mentésnek sikeresnek kell lennie.

## <a name="next-steps"></a>Következő lépések

Most, hogy lefedette a vCenter Server és a NSX-T Manager hitelesítő adatait az Azure VMware megoldáshoz, érdemes megismernie az alábbiakat:

- [NSX hálózati összetevők konfigurálása az Azure VMware megoldásban](configure-nsx-network-components-azure-portal.md).
- [Azure VMware-megoldás virtuális gépek életciklus-kezelése](lifecycle-management-of-azure-vmware-solution-vms.md).
- [Virtuális gépek vész-helyreállításának üzembe helyezése az Azure VMware megoldással](disaster-recovery-for-virtual-machines.md).

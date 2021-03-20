---
title: Windows Update-beállítások konfigurálása az Azure Automation frissítéskezelési szolgáltatásához
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja a Windows Update beállításait Azure Automation Update Management való működéshez.
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: a1f95ca856223628974a9519b7c4811bde43965e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92222160"
---
# <a name="configure-windows-update-settings-for-azure-automation-update-management"></a>Windows Update-beállítások konfigurálása az Azure Automation frissítéskezelési szolgáltatásához

Azure Automation Update Management a [Windows Update ügyfélen](/windows/deployment/update/windows-update-overview) támaszkodik a Windows-frissítések letöltésére és telepítésére. A Windows Update-ügyfél az Windows Server Update Services (WSUS) vagy a Windows Updatehoz való csatlakozáskor adott beállításokat használ. Ezen beállítások közül sokat a következővel kezelhet:

- Helyicsoportházirend-szerkesztő
- Csoportházirend
- PowerShell
- A beállításjegyzék közvetlen szerkesztése

A Update Management a Windows Update-ügyfél vezérléséhez megadott beállítások többségét tiszteletben tartja. Ha a nem Windows rendszerű frissítéseket engedélyező beállításokat használ, akkor a Update Management a frissítéseket is kezeli. Ha szeretné engedélyezni a frissítések letöltését a frissítés központi telepítése előtt, akkor a frissítés központi telepítése gyorsabb, hatékonyabb és kevésbé valószínű, hogy túllépi a karbantartási időszakot.

A WSUS Azure-előfizetésben való beállításával és a Windows rendszerű virtuális gépek naprakészen tartásával kapcsolatos további javaslatokért tekintse át az Azure-beli [Windows rendszerű virtuális gépek WSUS használatával történő frissítésének megtervezése](/azure/architecture/example-scenario/wsus/)című részt.

## <a name="pre-download-updates"></a>Frissítések előzetes letöltése

Ha a frissítések automatikus letöltését szeretné konfigurálni anélkül, hogy automatikusan telepítené őket, a Csoportházirend használatával [állíthatja be az automatikus frissítések beállítást](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) 3 értékre. Ez a beállítás engedélyezi a szükséges frissítések letöltését a háttérben, és értesíti arról, hogy a frissítések készen állnak a telepítésre. Így Update Management továbbra is az ütemtervek felügyelete alatt marad, de lehetővé teszi a frissítések Update Management karbantartási időszakon kívüli letöltését. Ez a viselkedés megakadályozza a `Maintenance window exceeded` Update Management hibáit.

Ezt a beállítást a PowerShellben engedélyezheti:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Újraindítási beállítások konfigurálása

Az automatikus frissítések konfigurálása az [újraindításhoz használt](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) beállításjegyzék és beállításkulcsok [szerkesztésével című témakörben](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) felsorolt beállításkulcsokat a gépek újraindítását okozhatja, még akkor is, ha a **frissítés központi telepítési** beállításai között nem ad meg **újraindítást** . Konfigurálja ezeket a beállításkulcsokat úgy, hogy az megfeleljen a környezetének.

## <a name="enable-updates-for-other-microsoft-products"></a>Egyéb Microsoft-termékek frissítéseinek engedélyezése

Alapértelmezés szerint a Windows Update ügyfél úgy van konfigurálva, hogy csak Windows rendszerű frissítéseket biztosítson. Ha engedélyezi a **Windows-termékek frissítéseinek más Microsoft-termékekhez való frissítését** , akkor más termékek frissítései is megjelenhetnek, beleértve a Microsoft SQL Server és más Microsoft-szoftverek biztonsági javításait is. Ezt a beállítást akkor állíthatja be, ha letöltötte és átmásolta a Windows 2016 és újabb rendszerekhez elérhető legújabb [felügyeleti sablonfájlokat](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) .

Ha a Windows Server 2012 R2 rendszert futtató gépekkel rendelkezik, a beállítás nem konfigurálható Csoportházirend használatával. Futtassa a következő PowerShell-parancsot ezeken a gépeken:

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="make-wsus-configuration-settings"></a>A WSUS konfigurációs beállításainak elkészítése

Update Management támogatja a WSUS beállításait. A frissítések kereséséhez és letöltéséhez szükséges forrásokat az [intranetes Microsoft Update szolgáltatás helyének megadása](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)című témakör útmutatása alapján adhatja meg. Alapértelmezés szerint a Windows Update ügyfél úgy van konfigurálva, hogy letöltse a frissítéseket a Windows Updateról. Ha a WSUS-kiszolgálót a számítógépek forrásaként adja meg, ha a frissítések nem hagyták jóvá a WSUS-ben, a frissítés telepítése sikertelen lesz. 

Ha a gépeket a belső frissítési szolgáltatásra szeretné korlátozni, állítsa a ne [kapcsolódjon egyetlen Windows Update internetes helyhez](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations).

## <a name="next-steps"></a>Következő lépések

A frissítés telepítésének ütemezett lépései a [virtuális gépek frissítéseinek és javításának kezelése](manage-updates-for-vm.md)című témakörben olvashatók.

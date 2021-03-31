---
title: Eszköz-átirányítások konfigurálása – Azure
description: Eszközök átirányításának konfigurálása a Windows rendszerű virtuális asztali számítógépekhez.
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b60a6e53e8fecd71885204690231776ff69fc08f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95018375"
---
# <a name="configure-device-redirections"></a>Eszközátirányítások konfigurálása

A Windows rendszerű virtuális asztali környezethez tartozó eszközök átirányításának konfigurálása lehetővé teszi, hogy a távoli munkamenetben nyomtatókat, USB-eszközöket, mikrofonokat és más perifériákat használjon. Egyes eszközök átirányításához a RDP protokoll (RDP) tulajdonságok és a Csoportházirend beállítások módosítása szükséges.

## <a name="supported-device-redirections"></a>Támogatott eszközök átirányítása

Minden ügyfél támogatja a különböző eszközök átirányítását. Tekintse meg az [ügyfelek összevetését](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare) az egyes ügyfelek támogatott eszköz-átirányításának teljes listájához.

## <a name="customizing-rdp-properties-for-a-host-pool"></a>A gazdagépek RDP-tulajdonságainak testreszabása

Ha többet szeretne megtudni a gazdagépek RDP-tulajdonságainak testreszabásáról a PowerShell vagy a Azure Portal használatával, tekintse meg az [RDP-tulajdonságokat](customize-rdp-properties.md). A támogatott RDP-tulajdonságok teljes listájáért lásd: [támogatott RDP-fájl beállításai](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext).

## <a name="setup-device-redirections"></a>Eszköz átirányításának beállítása

Az eszköz átirányításának konfigurálásához a következő RDP-tulajdonságokat és Csoportházirend beállításokat használhatja.

### <a name="audio-input-microphone-redirection"></a>Hangbemenet (mikrofon) átirányítása

Állítsa be a következő RDP-tulajdonságot a hangbemenetek átirányításának konfigurálásához:

- `audiocapturemode:i:1` engedélyezi a hangbemenet átirányítását.
- `audiocapturemode:i:0` letiltja a hangbemenetek átirányítását.

### <a name="audio-output-speaker-redirection"></a>Hangkimenet (hangsugárzó) átirányítása

Állítsa be a következő RDP-tulajdonságot a hangkimeneti átirányítás konfigurálásához:

- `audiomode:i:0` a hangkimenet átirányításának engedélyezése.
- `audiomode:i:1` vagy `audiomode:i:2` Tiltsa le a hangkimenet átirányítását.

### <a name="camera-redirection"></a>Kamera átirányítása

Állítsa be a következő RDP-tulajdonságot a kamera átirányításának konfigurálásához:

- `camerastoredirect:s:*` átirányítja az összes kamerát.
- `camerastoredirect:s:` letiltja a kamera átirányítását.

>[!NOTE]
>Annak ellenére, hogy a `camerastoredirect:s:` tulajdonság le van tiltva, a helyi kamerák átirányíthatók a `devicestoredirect:s:` tulajdonságon keresztül. A kamera-átirányítási készlet teljes letiltásához `camerastoredirect:s:` , illetve `devicestoredirect:s:` a Plug and Play eszközök olyan részhalmazának beállításához vagy definiálásához, amely nem tartalmaz kamerát.

Az egyes kamerákat a KSCATEGORY_VIDEO_CAMERA felületek pontosvesszővel tagolt listája alapján is átirányíthatja, például: `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi` . 

### <a name="clipboard-redirection"></a>Vágólap-átirányítás

A vágólap átirányításának konfigurálásához állítsa be a következő RDP-tulajdonságot:

- `redirectclipboard:i:1` engedélyezi a vágólap átirányítását.
- `redirectclipboard:i:0` letiltja a vágólap átirányítását.

### <a name="com-port-redirections"></a>COM-portok átirányítása

Állítsa be a következő RDP-tulajdonságot a COM-port átirányításának konfigurálásához:

- `redirectcomports:i:1` engedélyezi a COM-portok átirányítását.
- `redirectcomports:i:0` letiltja a COM-portok átirányítását.

### <a name="usb-redirection"></a>USB-átirányítás

Először állítsa be a következő RDP-tulajdonságot az USB-eszközök átirányításának engedélyezéséhez:

- `usbdevicestoredirect:s:*` engedélyezi az USB-eszközök átirányítását.
- `usbdevicestoredirect:s:` letiltja az USB-eszközök átirányítását.

Másodszor állítsa be a következő Csoportházirend a felhasználó helyi eszközén:

- Navigáljon a **számítógép-konfigurációs**  >  **házirendek** >  **Felügyeleti sablonok**  >  **Windows-összetevők**  >  **Távoli asztali szolgáltatások**  >  **Távoli asztali kapcsolat ügyfél** távoli elérési útjának  >  **USB-eszközök átirányítása**.
- Válassza a **más támogatott távoli USB-eszközök RDP-átirányítása a számítógépről lehetőséget**.
- Válassza az **engedélyezve** lehetőséget, majd válassza ki a **rendszergazdákat és a felhasználókat a távoli USB-átirányítás hozzáférési jogosultságok** mezőben.
- Válassza az **OK** lehetőséget.

### <a name="plug-and-play-device-redirection"></a>Plug and Play eszközök átirányítása

A Plug and Play eszközök átirányításának konfigurálásához állítsa be a következő RDP-tulajdonságot:

- `devicestoredirect:s:*` engedélyezi az összes plug and Play eszköz átirányítását.
- `devicestoredirect:s:` letiltja a Plug and Play eszközök átirányítását.

Az adott plug and Play eszközöket pontosvesszővel tagolt listával is kiválaszthatja, például: `devicestoredirect:s:root\*PNP0F08` .

### <a name="local-drive-redirection"></a>Helyi meghajtó átirányítása

Állítsa be a következő RDP-tulajdonságot a helyi meghajtó átirányításának konfigurálásához:

- `drivestoredirect:s:*` engedélyezi az összes lemezmeghajtó átirányítását.
- `drivestoredirect:s:` letiltja a helyi meghajtó átirányítását.

Az egyes meghajtókat pontosvesszővel tagolt listával is kiválaszthatja, például: `drivestoredirect:s:C:;E:;` .

### <a name="printer-redirection"></a>Nyomtató átirányítása

Állítsa be a következő RDP-tulajdonságot a nyomtató átirányításának konfigurálásához:

- `redirectprinters:i:1` engedélyezi a nyomtatók átirányítását.
- `redirectprinters:i:0` letiltja a nyomtató átirányítását.

### <a name="smart-card-redirection"></a>Intelligens kártya átirányítása

Állítsa be a következő RDP-tulajdonságot az intelligens kártya átirányításának konfigurálásához:

- `redirectsmartcards:i:1` engedélyezi az intelligens kártya átirányítását.
- `redirectsmartcards:i:0` letiltja az intelligens kártyák átirányítását.
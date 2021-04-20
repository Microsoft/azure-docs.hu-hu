---
title: Eszközátirányítások konfigurálása – Azure
description: Eszközátirányítások konfigurálása Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 37ecd06c4e3e71234e8fb1b6bad0cd05482dd31b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727846"
---
# <a name="configure-device-redirections"></a>Eszközátirányítások konfigurálása

Ha az eszközátirányítást a Windows Virtual Desktop konfigurálja, akkor a távoli munkamenetben nyomtatókat, USB-eszközöket, mikrofonokat és egyéb perifériaeszközöket használhat. Egyes eszközátirányítások megkövetelik a két RDP protokoll (RDP) és a Csoportházirend módosításait.

## <a name="supported-device-redirections"></a>Támogatott eszközátirányítások

Minden ügyfél különböző eszközátirányításokat támogat. Az egyes [ügyfelek támogatott](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare) eszközátirányításait az Ügyfelek összehasonlítása cikk tartalmazza.

## <a name="customizing-rdp-properties-for-a-host-pool"></a>Gazdagépkészlet RDP-tulajdonságainak testreszabása

Ha többet szeretne megtudni a gazdagépkészlet RDP-tulajdonságainak a PowerShell vagy a Azure Portal való testreszabásáról, tekintse meg az [RDP-tulajdonságokat.](customize-rdp-properties.md) A támogatott RDP-tulajdonságok teljes listáját lásd: Supported RDP file settings (Támogatott [RDP-fájlbeállítások).](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext)

## <a name="setup-device-redirections"></a>Eszközátirányítások beállítása

Az alábbi RDP-tulajdonságok és -beállítások Csoportházirend konfigurálhatja az eszközátirányítást.

### <a name="audio-input-microphone-redirection"></a>Hangbemenet (mikrofon) átirányítása

Állítsa be a következő RDP-tulajdonságot a hangbemenet átirányításának konfiguráléhez:

- `audiocapturemode:i:1` A engedélyezi a hangbemenet átirányítását.
- `audiocapturemode:i:0` letiltja a hangbemenet átirányítását.

### <a name="audio-output-speaker-redirection"></a>Hangkimenet (beszélő) átirányítása

Állítsa be a következő RDP-tulajdonságot a hangkimenet átirányításának konfigurálához:

- `audiomode:i:0` A engedélyezi a hangkimenet átirányítását.
- `audiomode:i:1` vagy `audiomode:i:2` tiltsa le a hangkimenet átirányítását.

### <a name="camera-redirection"></a>Kamera átirányítása

Állítsa be a következő RDP-tulajdonságot a kameraátirányítás konfigurálhoz:

- `camerastoredirect:s:*` átirányítja az összes kamerát.
- `camerastoredirect:s:` letiltja a kameraátirányítást.

>[!NOTE]
>A helyi kamerák akkor is átirányíthatók a tulajdonságon keresztül, ha a tulajdonság le van `camerastoredirect:s:` `devicestoredirect:s:` tiltva. A kameraátirányítási készlet teljes letiltásához, valamint a plug and play eszközök bizonyos olyan részhalmazának beállításához vagy definiálásához, amely nem tartalmaz `camerastoredirect:s:` `devicestoredirect:s:` kamerát.

Adott kamerákat pontosvesszővel tagolt listában is átirányíthat KSCATEGORY_VIDEO_CAMERA, például a következővel: `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi` .

### <a name="clipboard-redirection"></a>Vágólap átirányítása

Állítsa be a következő RDP-tulajdonságot a vágólap átirányításának konfigurálhoz:

- `redirectclipboard:i:1` A engedélyezi a vágólap átirányítását.
- `redirectclipboard:i:0` letiltja a vágólap átirányítását.

### <a name="com-port-redirections"></a>COM-portátirányítások

Állítsa be a következő RDP-tulajdonságot a COM-port átirányításának konfiguráljához:

- `redirectcomports:i:1` A engedélyezi a COM-port átirányítását.
- `redirectcomports:i:0` letiltja a COM-port átirányítását.

### <a name="usb-redirection"></a>USB-átirányítás

Először állítsa be a következő RDP-tulajdonságot az USB-eszköz átirányításának engedélyezéséhez:

- `usbdevicestoredirect:s:*` A engedélyezi az USB-eszközök átirányítását.
- `usbdevicestoredirect:s:` letiltja az USB-eszköz átirányítását.

Másodszor állítsa be a következő Csoportházirend a felhasználó helyi eszközén:

- Lépjen a **Számítógép-konfigurációs**  >  **házirendek** >  **lapra, Felügyeleti sablonok**  >  **Windows-összetevők között**  >  **Távoli asztali szolgáltatások**  >  **Távoli asztali kapcsolat**  >  **RemoteFX USB-eszközátirányítást.**
- Jelölje **be Az egyéb támogatott RemoteFX USB-eszközök RDP-átirányításának** lehetővé teszi ezt a lehetőséget erről a számítógépről.
- Válassza az **Engedélyezve** lehetőséget, majd jelölje be a Rendszergazdák és felhasználók a **RemoteFX USB-átirányítás hozzáférési jogosultságai között jelölőnégyzetet.**
- Válassza az **OK** lehetőséget.

### <a name="plug-and-play-device-redirection"></a>Plug and Play-eszközök átirányítása

Állítsa be a következő RDP-tulajdonságot a plug and play eszköz átirányításának konfiguráléhez:

- `devicestoredirect:s:*` lehetővé teszi az összes Plug and Play-eszköz átirányítását.
- `devicestoredirect:s:` letiltja a plug and play eszközök átirányítását.

Pontosvesszővel tagolt listával is kiválaszthat adott plug and play eszközöket, `devicestoredirect:s:root\*PNP0F08` például: .

### <a name="local-drive-redirection"></a>Helyi meghajtó átirányítása

Állítsa be a következő RDP-tulajdonságot a helyi meghajtó átirányításának konfigurálhoz:

- `drivestoredirect:s:*` lehetővé teszi az összes lemezmeghajtó átirányítását.
- `drivestoredirect:s:` letiltja a helyi meghajtó átirányítását.

Pontosvesszővel tagolt listával is kiválaszthat adott meghajtókat, `drivestoredirect:s:C:;E:;` például: .

A webes ügyfél fájlátvitelének konfigurálhoz állítsa be a következőt: `drivestoredirect:s:*` .

### <a name="printer-redirection"></a>Nyomtatóátirányítás

Állítsa be a következő RDP-tulajdonságot a nyomtatóátirányítás konfigurálához:

- `redirectprinters:i:1` A engedélyezi a nyomtatóátirányítást.
- `redirectprinters:i:0` letiltja a nyomtatóátirányítást.

### <a name="smart-card-redirection"></a>Intelligens kártya átirányítása

Állítsa be a következő RDP-tulajdonságot az intelligens kártya átirányításának konfigurálhoz:

- `redirectsmartcards:i:1` az intelligens kártya átirányítását teszi lehetővé.
- `redirectsmartcards:i:0` letiltja az intelligens kártya átirányítását.

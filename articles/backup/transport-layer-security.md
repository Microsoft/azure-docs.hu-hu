---
title: Transport Layer Security a Azure Backup
description: Megtudhatja, hogyan engedélyezheti a Azure Backup számára a titkosítási protokoll Transport Layer Security (TLS) használatát, ha a hálózaton keresztüli adatátvitel közben szeretné megőrizni az adatvédelmet.
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: ba9c9d91f562f54695a0739908c8a409d14d5852
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96327117"
---
# <a name="transport-layer-security-in-azure-backup"></a>Transport Layer Security a Azure Backup

A Transport Layer Security (TLS) egy titkosítási protokoll, amely a hálózaton keresztüli adatátvitel közben gondoskodik az adatbiztonságról. A Azure Backup a Transport Layer Security használatával gondoskodik az átvitt biztonsági mentési adatok védelméről. Ez a cikk a TLS 1,2 protokoll engedélyezésének lépéseit ismerteti, amely nagyobb biztonságot nyújt a korábbi verziókhoz képest.

## <a name="earlier-versions-of-windows"></a>A Windows korábbi verziói

Ha a gép a Windows korábbi verzióit futtatja, akkor az alább felsorolt megfelelő frissítéseket telepíteni kell, és a TUDÁSBÁZIS cikkeiben ismertetett beállításjegyzékbeli módosításokat is alkalmazni kell.

|Operációs rendszer  |TUDÁSBÁZISCIKK |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2, Windows 7, Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>A frissítés a szükséges protokoll-összetevőket fogja telepíteni. A telepítést követően a fenti TUDÁSBÁZIS cikkeiben felsorolt beállításkulcs-módosításokat kell végrehajtani a szükséges protokollok megfelelő engedélyezéséhez.

## <a name="verify-windows-registry"></a>A Windows beállításjegyzék ellenőrzése

### <a name="configuring-schannel-protocols"></a>SChannel-protokollok konfigurálása

A következő beállításkulcsok biztosítják, hogy a TLS 1,2 protokoll engedélyezve legyen a SChannel-összetevő szintjén:

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>A megjelenített értékek alapértelmezés szerint a Windows Server 2012 R2 és újabb verziókban vannak beállítva. A Windows ezen verziói esetében, ha a beállításkulcsok hiányoznak, nem szükséges létrehozni.

### <a name="configuring-net-framework"></a>A .NET-keretrendszer konfigurálása

A következő beállításkulcsokat konfigurálja a .NET-keretrendszert az erős titkosítás támogatásához. A [.NET-keretrendszer konfigurálásával kapcsolatban itt](/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry)talál további információt.

```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="why-enable-tls-12"></a>Miért engedélyezzük a TLS 1,2-et?

A TLS 1,2 biztonságosabb, mint a korábbi titkosítási protokollok, például az SSL 2,0, az SSL 3,0, a TLS 1,0 és a TLS 1,1. Azure Backup szolgáltatások már teljes mértékben támogatják a TLS 1,2-t.

### <a name="what-determines-the-encryption-protocol-used"></a>Mi határozza meg a használt titkosítási protokollt?

Az ügyfél és a kiszolgáló által támogatott legmagasabb protokoll verziója egyeztetve van a titkosított beszélgetés létrehozásához. A TLS-kézfogási protokollról további információt a [biztonságos munkamenetek TLS használatával történő létrehozásával](/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls)foglalkozó témakörben talál.

### <a name="what-is-the-impact-of-not-enabling-tls-12"></a>Mi a hatása, ha nem engedélyezi a TLS 1,2-et?

A protokoll-visszalépési támadások jobb biztonsága érdekében a Azure Backup fokozatosan letiltja a 1,2-nál régebbi TLS-verziókat. Ez egy hosszú távú váltás része a szolgáltatások között az örökölt protokoll-és titkosító csomagok kapcsolatainak letiltásához. Azure Backup szolgáltatások és összetevők teljes mértékben támogatják a TLS 1,2-t. A szükséges frissítéseket vagy bizonyos testreszabott konfigurációkat nem igénylő Windows-verziók azonban továbbra is megakadályozhatják a TLS 1,2 protokollokat. Ez olyan hibákhoz vezethet, mint például a következők közül egy vagy több:

- A biztonsági mentési és visszaállítási műveletek sikertelenek lehetnek.
- A biztonsági mentési összetevők kapcsolódási hibái a 10054-as hibával (a meglévő kapcsolatot a távoli gazdagép kényszerített lezárta).
- Azure Backup kapcsolódó szolgáltatások nem állnak le, vagy a szokásos módon indulnak el.

## <a name="additional-resources"></a>További források

- [Transport Layer Biztonsági Protokoll](/windows/win32/secauthn/transport-layer-security-protocol)
- [A TLS 1,2 támogatásának biztosítása az üzembe helyezett operációs rendszerek között](/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- [A Transport Layer Security (TLS) ajánlott eljárásai a .NET-keretrendszerrel](/dotnet/framework/network-programming/tls)
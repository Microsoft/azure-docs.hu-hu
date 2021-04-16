---
title: Az Azure Arc-ügynök csatlakozási problémáinak elhárítása
description: Ez a cikk azt mutatja be, hogyan háríthatja el és háríthatja el a csatlakoztatottgép-ügynökkel kapcsolatos problémákat, amelyek az engedélyezett Azure Arc a szolgáltatáshoz való csatlakozáskor merülnek fel.
ms.date: 04/12/2021
ms.topic: conceptual
ms.openlocfilehash: ae26b599a72129b5ed7f47d76d10353be5c0e8ac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497999"
---
# <a name="troubleshoot-azure-arc-enabled-servers-agent-connection-issues"></a>Az Azure Arc-ügynök csatlakozási problémáinak elhárítása

Ez a cikk a windowsos vagy linuxos csatlakoztatottgép-Azure Arc konfigurálás során előforduló hibák elhárításáról és megoldásáról nyújt tájékoztatást. A szolgáltatáshoz való csatlakozás konfigurálásakor az interaktív és a nagy léptékű telepítési módszerek is megjelennek. Általános információkért lásd az [Arc-kompatibilis kiszolgálók áttekintését.](./overview.md)

## <a name="agent-error-codes"></a>Ügynök hibakódok

Ha hibaüzenetet kap az Azure Arc-kompatibilis kiszolgálóügynök konfigurálásakor, az alábbi táblázat segíthet azonosítani a probléma lehetséges okát és javasolt lépéseit. A folytatáshoz szüksége lesz a konzol- vagy szkriptkimenetre nyomtatott `AZCM0000` ("0000" bármilyen 4 számjegyű szám lehet) hibakódra.

| Hibakód | Valószínű ok | Javasolt szervizelés |
|------------|----------------|-----------------------|
| AZCM0000 | A művelet sikeres volt | N/A |
| AZCM0001 | Ismeretlen hiba történt | További Microsoft ügyfélszolgálata forduljon a Microsoft ügyfélszolgálata. |
| AZCM0011 | A felhasználó megszakította a műveletet (CTRL+C) | Próbálja újra az előző parancsot |
| AZCM0012 | A megadott hozzáférési jogkivonat érvénytelen | Szerezzen be egy új hozzáférési jogkivonatot, és próbálkozzon újra |
| AZCM0013 | A megadott címkék érvénytelenek | Ellenőrizze, hogy a címkék idézőjelek közé vannak-e vesszővel elválasztva, és hogy a szóközt is tartalmazó nevek vagy értékek a következő idézőjelek közé vannak-e ékezve: `--tags "SingleName='Value with spaces',Location=Redmond"`
| AZCM0014 | A felhő érvénytelen | Támogatott felhő megadása: `AzureCloud` vagy `AzureUSGovernment` |
| AZCM0015 | A megadott korrelációs azonosító nem érvényes GUID | Adjon meg egy érvényes GUID-azonosítót a `--correlation-id` |
| AZCM0016 | Hiányzik egy kötelező paraméter | Tekintse át a kimenetet a hiányzó paraméterek azonosításához |
| AZCM0017 | Az erőforrás neve érvénytelen | Olyan nevet adjon meg, amely csak alfanumerikus karaktereket, kötőjeleket és/vagy aláhúzásjeleket használ. A név nem végződhet kötőjellel vagy aláhúzásjellel. |
| AZCM0018 | A parancs végrehajtása rendszergazdai jogosultságok nélkül történt | Rendszergazdai vagy rendszergazdai jogosultságokkal próbálja megismételni a parancsot emelt szintű parancssorban vagy konzol-munkamenetben. |
| AZCM0041 | A megadott hitelesítő adatok érvénytelenek | Az eszközbe való bejelentkezések esetén ellenőrizze, hogy a megadott felhasználói fiók rendelkezik-e hozzáféréssel ahhoz a bérlőhöz és előfizetéshez, ahol a kiszolgálói erőforrás létre lesz hozva. Szolgáltatásnév-bejelentkezések esetén ellenőrizze az ügyfél-azonosítót és a titkos adatokat a helyesség, a titkos kulcs lejárati dátuma, valamint hogy a szolgáltatásnév ugyanattól a bérlőtől van-e, ahol a kiszolgálói erőforrás létre lesz hozva. |
| AZCM0042 | Az Arc-kompatibilis kiszolgálói erőforrás létrehozása sikertelen volt | Ellenőrizze, hogy a megadott felhasználó/szolgáltatásnév rendelkezik-e hozzáféréssel arc-kompatibilis kiszolgálói erőforrások létrehozásához a megadott erőforráscsoportban. |
| AZCM0043 | Az Arc-kompatibilis kiszolgálói erőforrás törlése sikertelen volt | Ellenőrizze, hogy a megadott felhasználó/szolgáltatásnév rendelkezik-e hozzáféréssel az Arc-kompatibilis kiszolgálói erőforrások törléséhez a megadott erőforráscsoportban. Ha az erőforrás már nem létezik az Azure-ban, használja a `--force-local-only` jelzőt a folytatáshoz. |
| AZCM0044 | Már létezik ilyen nevű erőforrás | Adjon egy másik nevet a paraméternek, vagy törölje a meglévő `--resource-name` Arc-kompatibilis kiszolgálót az Azure-ban, és próbálkozzon újra. |
| AZCM0061 | Nem sikerült elérni az ügynökszolgáltatást | Ellenőrizze, hogy emelt szintű felhasználói környezetben (rendszergazda/gyökér) futtatja-e a parancsot, és hogy a HIMDS szolgáltatás fut-e a kiszolgálón. |
| AZCM0062 | Hiba történt a kiszolgáló csatlakoztatásakor | További információért tekintse át a kimenetben található egyéb hibakódokat. Ha a hiba az Azure-erőforrás létrehozása után történt, az újrapróbálkozás előtt törölnie kell az Arc-kiszolgálót az erőforráscsoportból. |
| AZCM0063 | Hiba történt a kiszolgáló leválasztása során | További információért tekintse át a kimenetben található egyéb hibakódokat. Ha továbbra is ezt a hibát fogja látni, törölheti az erőforrást az Azure-ban, majd a kiszolgálón futtatva leválaszthatja `azcmagent disconnect --force-local-only` az ügynököt. |
| AZCM0064 | Az ügynökszolgáltatás nem válaszol | Ellenőrizze a szolgáltatás `himds` állapotát, és ellenőrizze, hogy fut-e. Indítsa el a szolgáltatást, ha az nem fut. Ha fut, várjon egy percet, majd próbálkozzon újra. |
| AZCM0065 | Belső ügynökkel kapcsolatos kommunikációs hiba történt | Segítségért Microsoft ügyfélszolgálata forduljon a Microsoft ügyfélszolgálata. |
| AZCM0066 | Az ügynök webszolgáltatása nem válaszol vagy nem érhető el | Segítségért Microsoft ügyfélszolgálata forduljon a Microsoft ügyfélszolgálata. |
| AZCM0067 | Az ügynök már csatlakoztatva van az Azure-hoz | Először kövesse az ügynök [leválasztása lépéseit,](manage-agent.md#unregister-machine) majd próbálkozzon újra. |
| AZCM0068 | Belső hiba történt a kiszolgáló Azure-ral való leválasztása során | Segítség Microsoft ügyfélszolgálata a Microsoft ügyfélszolgálata |
| AZCM0081 | Hiba történt a felügyelt identitás tanúsítványának Azure Active Directory letöltése során | Ha ez az üzenet akkor jelenik meg, amikor megpróbálja csatlakoztatni a kiszolgálót az Azure-hoz, az ügynök nem fog tudni kommunikálni a Azure Arc szolgáltatással. Törölje az erőforrást az Azure-ban, és próbálkozzon újra a csatlakozással. |
| AZCM0101 | A parancs nem lett megfelelően elemezve | Futtassa `azcmagent <command> --help` a parancsot a megfelelő parancsszintaxis áttekintéshez |
| AZCM0102 | Nem sikerült lekérni a számítógép állomásnevét | A `hostname` futtatásával keressen rendszerszintű hibaüzeneteket, majd lépjen kapcsolatba a Microsoft ügyfélszolgálata. |
| AZCM0103 | Hiba történt az RSA-kulcsok létrehozása során | Segítségért Microsoft ügyfélszolgálata forduljon a Microsoft ügyfélszolgálata. |
| AZCM0104 | Nem sikerült beolvasni a rendszeradatokat | Ellenőrizze, hogy a futtatáshoz használt identitás `azcmagent` rendelkezik-e rendszergazdai/rendszergazdai jogosultságokkal a rendszeren, majd próbálkozzon újra. |

## <a name="agent-verbose-log"></a>Ügynök részletes naplója

A cikk későbbi lépéseit követő hibaelhárítási lépések előtt a minimálisan szükséges információ a részletes napló. Az **azcmagent** eszközparancsok kimenetét tartalmazza a verbose (-v) argumentum használata esetén. A naplófájlok a fájlba vannak `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` írva Windows rendszeren, a Linuxon pedig a fájlba. `/var/opt/azcmagent/log/azcmagent.log`

### <a name="windows"></a>Windows

Az alábbi példa egy parancsot mutat be, amely lehetővé teszi a részletes naplózást a Windows connected machine ügynökével egy interaktív telepítés végrehajtásakor.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Az alábbi példa egy parancsot mutat be, amely engedélyezi a windowsos Csatlakoztatott gép ügynökkel történő részletes naplózást, amikor nagy léptékű telepítést végez szolgáltatásnév használatával.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

Az alábbi példában az parancs egy interaktív telepítés végrehajtásakor engedélyezi a Részletes naplózást a Linuxhoz csatlakoztatott gép ügynökével.

>[!NOTE]
>Az **azcmagent** futtatásához gyökér szintű hozzáférési engedélyekkel kell rendelkeznie Linux rendszerű gépeken. 

```bash
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Az alábbi példa egy parancsot mutat be, amely engedélyezi a linuxos Csatlakoztatott gép ügynökkel való részletes naplózást, amikor nagy léptékű telepítést végez szolgáltatásnév használatával.

```bash
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>Ügynök szolgáltatáskapcsolati problémái

Az alábbi táblázat néhány ismert hibát és javaslatot tartalmaz a hibaelhárításra és azok megoldására.

|Üzenet |Hiba |Valószínű ok |Megoldás |
|--------|------|---------------|---------|
|Nem sikerült be szerezni az engedélyezési jogkivonat-eszközfolyamot |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |Nem lehet elérni a `login.windows.net` végpontot | Ellenőrizze a végponthoz való csatlakozást. |
|Nem sikerült be szerezni az engedélyezési jogkivonat-eszközfolyamot |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |A proxy vagy a tűzfal blokkolja a végponthoz való `login.windows.net` hozzáférést. | Ellenőrizze a végponttal való kapcsolatot, és azt nem blokkolja egy tűzfal vagy proxykiszolgáló. |
|Nem sikerült be szerezni az engedélyezési jogkivonat-eszközfolyamot  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | Csoportházirend az Object *Computer Configuration\ Felügyeleti sablonok\ System\ User Profiles\ Delete* user profiles older than a specified number of days on system restart is enabled. | Ellenőrizze, hogy a GPO engedélyezve van-e, és hogy az érintett gépet célozza-e meg. További részleteket <sup>[az 1.](#footnote1)</sup> lábjegyzetben talál. |
|Nem sikerült az engedélyezési jogkivonat beszerzése az SPN-ről |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |A proxy vagy a tűzfal blokkolja a végponthoz való `login.windows.net` hozzáférést. |Ellenőrizze a végponttal való kapcsolatot, és azt nem blokkolja tűzfal vagy proxykiszolgáló. |
|Nem sikerült az engedélyezési jogkivonat beszerzése az SPN-ről |`Invalid client secret is provided` |Helytelen vagy érvénytelen szolgáltatásnév-titkos adatokat tartalmaz. |Ellenőrizze az egyszerű szolgáltatás titkos ját. |
| Nem sikerült az engedélyezési jogkivonat beszerzése az SPN-ről |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |Helytelen szolgáltatásnév és/vagy bérlőazonosító. |Ellenőrizze a szolgáltatásnév és/vagy a bérlőazonosítót.|
|ARM-erőforrás válaszának lekérte |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |Helytelen hitelesítő adatok és/vagy engedélyek |Ellenőrizze, hogy Ön vagy a szolgáltatásnév tagja-e az **Azure Connected Machine-bevezető szerepkörnek.** |
|Nem sikerült az AzcmagentConnect ARM-erőforrás |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Az Azure-erőforrásszolgáltatók nincsenek regisztrálva. |Regisztrálja az [erőforrás-szolgáltatókat.](./agent-overview.md#register-azure-resource-providers) |
|Nem sikerült az AzcmagentConnect ARM-erőforrás |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |A proxykiszolgáló vagy a tűzfal blokkolja a végponthoz való `management.azure.com` hozzáférést. |Ellenőrizze a végponttal való kapcsolatot, és azt nem blokkolja tűzfal vagy proxykiszolgáló. |

<a name="footnote1"></a><sup>1</sup> Ha ez a GPO engedélyezve van, és a Connected Machine-ügynökkel rendelkező gépekre vonatkozik, törli a *himds* szolgáltatáshoz megadott beépített fiókhoz társított felhasználói profilt. Ennek eredményeképpen törli a helyi tanúsítványtárolóban 30 napig gyorsítótárazott szolgáltatással való kommunikációhoz használt hitelesítési tanúsítványt is. A 30 napos korlát előtt a rendszer megkísérli megújítani a tanúsítványt. A probléma megoldásához kövesse [](manage-agent.md#unregister-machine) a gép regisztrációjának feloldásához szükséges lépéseket, majd regisztrálja újra a-t futtató `azcmagent connect` szolgáltatásban.

## <a name="next-steps"></a>Következő lépések

Ha itt nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért próbálkozzon az alábbi csatornák egyikével:

* Válaszokat kaphat Azure-szakértőktől a [Microsoft Q&A segítségével.](/answers/topics/azure-arc.html)

* Csatlakozzon [@AzureSupport](https://twitter.com/azuresupport) a -hez, Microsoft Azure a felhasználói élmény javítása érdekében. Azure ügyfélszolgálata összekapcsolja az Azure-közösséget válaszokkal, támogatással és szakértőkkel.

* Be kell Azure-támogatás incidenst. A webhelyre [Azure-támogatás,](https://azure.microsoft.com/support/options/)és válassza a **Támogatás et.**

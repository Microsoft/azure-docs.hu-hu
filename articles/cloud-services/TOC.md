# [A Cloud Services dokumentációja](index.md)

# Áttekintés
## [Mi az a Cloud Services?](cloud-services-choose-me.md)
## [Felhőszolgáltatás konfigurációs fájljai és csomagja](cloud-services-model-and-package.md)

# Első lépések
## [.NET Cloud Service minta](cloud-services-dotnet-get-started.md)
## [Python for Visual Studio Cloud Service minta](cloud-services-python-ptvs.md)
## [Hibrid HPC-fürt beállítása Microsoft HPC Packkel](cloud-services-setup-hybrid-hpcpack-cluster.md)

# Útmutató
## Felkészülés
### [Virtuálisgép-méretek](cloud-services-sizes-specs.md)
### [Frissítések](cloud-services-update-azure-service.md)

## Fejlesztés
### [Webes és feldolgozói PHP-szerepkörök létrehozása](../cloud-services-php-create-web-role.md)
### [Node.js-alkalmazás fejlesztése és üzembe helyezése](cloud-services-nodejs-develop-deploy-app.md)
### [Node.js-webalkalmazás fejlesztése az Express használatával](cloud-services-nodejs-develop-deploy-express-app.md)
### Tárolás és a Visual Studio
#### [A Blob Storage és a kapcsolódó szolgáltatások](../visual-studio/vs-storage-cloud-services-getting-started-blobs.md)
#### [A Queue Storage és a kapcsolódó szolgáltatások](../visual-studio/vs-storage-cloud-services-getting-started-queues.md)
#### [A Table Storage és a kapcsolódó szolgáltatások](../visual-studio/vs-storage-cloud-services-getting-started-tables.md)
### [Szerepkör adatforgalmi szabályainak konfigurálása](cloud-services-enable-communication-role-instances.md)
### [Cloud Service-életciklusesemények kezelése](cloud-services-role-lifecycle-dotnet.md)
### [Socket.io (Node.js)](cloud-services-nodejs-chat-app-socketio.md)
### [Telefonhívás indítása Twilióról (.NET)](../partner-twilio-cloud-services-dotnet-phone-call-web-role.md)

### Indítási feladatok konfigurálása
#### [Indítási feladatok létrehozása](cloud-services-startup-tasks.md)
#### [Gyakori indítási feladatok](cloud-services-startup-tasks-common.md)
#### [A .NET telepítése egy Cloud Service szerepkörre feladat használatával](cloud-services-dotnet-install-dotnet.md)

### A Távoli asztal konfigurálása
#### [Portál](cloud-services-role-enable-remote-desktop-new-portal.md)
#### [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
#### [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

## Üzembe helyezés
### [Felhőszolgáltatás létrehozása és üzembe helyezése a portálon](cloud-services-how-to-create-deploy-portal.md)
### [Üres felhőszolgáltatás-tároló létrehozása PowerShellben](cloud-services-powershell-create-cloud-container.md)
### [Egyéni tartománynév konfigurálása](cloud-services-custom-domain-name-portal.md)
### [Csatlakoztatás egyéni tartományvezérlőhöz](cloud-services-connect-to-custom-domain.md)

## Szolgáltatás kezelése
### [Gyakori felügyeleti feladatok](cloud-services-how-to-manage-portal.md)
### [Felhőszolgáltatás konfigurálása](cloud-services-how-to-configure-portal.md)
### [Egy Cloud Service felügyelete az Azure Automationnel](automation-manage-cloud-services.md)
### [Az automatikus skálázás konfigurálása](cloud-services-how-to-scale-portal.md)
### [Azure-erőforrások felügyelete a Pythonnal](cloud-services-python-how-to-use-service-management.md)
### [Spekulatív végrehajtás kockázatának csökkentése](mitigate-se.md)

### [Vendég operációs rendszer javításai](cloud-services-guestos-msrc-releases.md)
### Vendég operációs rendszer kivezetése
#### [Kivezetési szabályzat](cloud-services-guestos-retirement-policy.md)
#### [1. család kivezetéséről szóló értesítés](cloud-services-guestos-family1-retirement.md)
### [A vendég operációs rendszer kiadási hírei](cloud-services-guestos-update-matrix.md)
### [Cloud Services-szerepkör konfigurálása, XPath-adatlap](cloud-services-role-config-xpath.md)

## Tanúsítványok kezelése
### [Cloud Services és felügyeleti tanúsítványok](cloud-services-certs-create.md)
### [Az SSL konfigurálása](cloud-services-configure-ssl-certificate-portal.md)

## Figyelés
### [Felhőszolgáltatás monitorozása](cloud-services-how-to-monitor.md)
### [Teljesítményszámlálók használata](diagnostics-performance-counters.md)
### [Teljesítmény tesztelése](../vs-azure-tools-performance-profiling-cloud-services.md)
#### [Tesztelés a Visual Studio Profilerével](cloud-services-performance-testing-visual-studio-profiler.md)
### Diagnosztika engedélyezése
#### [Azure PowerShell](cloud-services-diagnostics-powershell.md)
#### [.NET](cloud-services-dotnet-diagnostics.md)
#### [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
### [Diagnosztikai adatok tárolása és megtekintése az Azure Storage-ban](../monitoring-and-diagnostics/azure-diagnostics-storage.md?toc=%2fazure%2fcloud-services%2ftoc.json )
### [Felhőszolgáltatás követése a Diagnostics használatával](cloud-services-dotnet-diagnostics-trace-flow.md)

## Hibaelhárítás
### Hibakeresés 
#### [Felhőszolgáltatás beállításai](../vs-azure-tools-debugging-cloud-services-overview.md)
#### [Helyi felhőszolgáltatás a Visual Studióban](../vs-azure-tools-debug-cloud-services-virtual-machines.md)
#### [Közzétett felhőszolgáltatás a Visual Studióban](../vs-azure-tools-intellitrace-debug-published-cloud-services.md)
### [Cloud Service-foglalási hiba](cloud-services-allocation-failures.md)
### [A Cloud Service-szerepkörök újrahasznosításának gyakori okai](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)
### [Az alapértelmezett TEMP mappa mérete túl kicsi a szerepkörhöz](cloud-services-troubleshoot-default-temp-folder-size-too-small-web-worker-role.md)
### [Gyakori üzembe helyezési problémák](cloud-services-troubleshoot-deployment-problems.md)
### [A szerepkör nem indult el](cloud-services-troubleshoot-roles-that-fail-start.md)
### [Helyreállítási útmutató](cloud-services-disaster-recovery-guidance.md)
### Cloud Services – gyakori kérdések
#### [Az alkalmazások és szolgáltatások rendelkezésre állásával kapcsolatos gyakori kérdések](cloud-services-application-and-service-availability-faq.md)
#### [Konfigurálással és felügyelettel kapcsolatos gyakori kérdések](cloud-services-configuration-and-management-faq.md)
#### [Kapcsolatokkal és hálózatkezeléssel kapcsolatos gyakori kérdések](cloud-services-connectivity-and-networking-faq.md)
#### [Üzembe helyezéssel kapcsolatos gyakori kérdések](cloud-services-deployment-faq.md)

# Referencia
## [Kódminták](https://azure.microsoft.com/resources/samples/?service=cloud-services)
## [.csdef XML-séma](schema-csdef-file.md)
### [LoadBalancerProbe séma](schema-csdef-loadbalancerprobe.md)
### [WebRole séma](schema-csdef-webrole.md)
### [WorkerRole séma](schema-csdef-workerrole.md)
### [NetworkTrafficRules séma](schema-csdef-networktrafficrules.md)
## [.cscfg XML-séma](schema-cscfg-file.md)
### [Szerepkörséma](schema-cscfg-role.md)
### [NetworkConfiguration séma](schema-cscfg-networkconfiguration.md)
## [REST](/rest/api/compute/cloudservices/)

# További források
## [Azure-ütemterv](https://azure.microsoft.com/roadmap/?category=compute)
## [Képzési terv](https://azure.microsoft.com/documentation/learning-paths/cloud-services/)
## [MSDN-fórum](https://social.msdn.microsoft.com/Forums/en-us/home?forum=windowsazuredevelopment)
## [Díjszabás](https://azure.microsoft.com/pricing/details/cloud-services/)
## [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)
## [Szolgáltatási hírek](https://azure.microsoft.com/updates/?product=cloud-services&updatetype=&platform=)
## [Videók](https://azure.microsoft.com/documentation/videos/index/?services=cloud-services)

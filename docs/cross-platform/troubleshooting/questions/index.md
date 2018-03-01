---
title: Domande frequenti generali
ms.topic: article
ms.prod: xamarin
ms.assetid: C7E6E54D-3957-407D-BB87-22B095148C6B
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 0c01bfb08d525fd336b29f405304efaeaf749f35
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="general-frequently-asked-questions"></a>Domande frequenti generali

## <a name="visual-studio-2017-release-candidate"></a>Visual Studio 2017 Release Candidate
### <a name="can-i-use-visual-studio-2017-release-candidate-with-xamarinvisualstudio-2017-rcmd"></a>[È possibile utilizzare Visual Studio 2017 versione finale candidata con Xamarin?](visualstudio-2017-rc.md)
Per una descrizione delle implicazioni dell'utilizzo di Xamarin con Visual Studio 2017 Release Candidate (RC) nonché per informazioni su come installare Xamarin in Visual Studio2017 RC corrente.

## <a name="portable-class-libraries"></a>Librerie di classi portabili
### <a name="how-can-i-view-what-libraries-are-supported-in-a-pclpcl-support-librariesmd"></a>[Come è possibile visualizzare le librerie sono supportate in una PCL?](pcl-support-libraries.md)
Questa guida vengono elencati i metodi per determinare se la raccolta esistente è supportato da varie piattaforme di destinazione PCL, o può essere convertito in un profilo della libreria di classi Portabile e risorse.

### <a name="pcl-reflection-apipcl-reflectionmd"></a>[API di Reflection di libreria di classi Portabile](pcl-reflection.md)
Microsoft ha sviluppato una nuova API di Reflection per l'utilizzo nelle librerie di classi portabile. Se si dispone di codice esistente che si desidera spostare in una PCL Reflection, potrebbe non funzionare.

### <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-packagepcl-case-studymd"></a>[Case study di libreria di classi Portabile: come è possibile risolvere i problemi correlati alla System.Diagnostics.Tracing per il pacchetto NuGet di flussi di dati TPL Microsoft?](pcl-case-study.md)
Xamarin. IOS e xamarin non implementano 100% di tutti i profili che consentono come riferimenti PCL. Per motivi di praticità pratiche in Visual Studio per Mac, Visual Studio e gestione pacchetti NuGet, progetti di Xamarin consentono l'utilizzo di diversi profili che hanno solo le implementazioni incomplete. Ad esempio, xamarin né xamarin attualmente include un'implementazione completa dei tipi di `System.Diagnostics.Tracing` PCL dello spazio dei nomi. È possibile risolvere il problema modificando il progetto di app per fare riferimento il portatile net45 win8 wp8 + wpa81 versione della libreria del flusso di dati TPL.

## <a name="nuget-packages--xamarin-components"></a>Pacchetti NuGet e componenti di Xamarin
### <a name="how-can-i-update-nugetnuget-updatemd"></a>[Come posso aggiornare NuGet?](nuget-update.md)
Componenti aggiuntivi, estensioni e aggiornamenti di NuGet è reperibile nella **aggiornamenti** nella scheda il **Gestione pacchetti NuGet**. Navigazione dettagliate per trovare gli aggiornamenti in Visual Studio per Mac e Visual Studio è in questa Guida.

### <a name="how-do-i-downgrade-a-nuget-packagenuget-package-downgrademd"></a>[Come il downgrade un pacchetto NuGet?](nuget-package-downgrade.md)
Visual Studio per Mac e Visual Studio sono disponibili funzionalità per la selezione di versioni precedenti di pacchetti e installarli automaticamente. simile alla modalità di aggiornamento pacchetti works.

### <a name="missing-packages-error-after-updating-nuget-packagesnuget-packages-missingmd"></a>[Errore di pacchetti mancante dopo l'aggiornamento di pacchetti Nuget](nuget-packages-missing.md)
Questo problema è stato segnalato principalmente sulle soluzioni di app di esempio xamarin. Forms, ma il potenziale per questo problema può verificarsi in qualsiasi progetto che utilizza i pacchetti NuGet.

### <a name="unifying-google-play-services-components-and-nugetgps-components-nugetmd"></a>[Componenti e NuGet, unificazione Google Play Services](gps-components-nuget.md)
Sono utilizzati da diversi componenti di Google Play Services e pacchetti NuGet, ma per semplificare le operazioni per gli sviluppatori, è stato ora i componenti e unificati NuGet pacchetti in due. In quasi tutti i casi, Google Play Services deve essere utilizzato. L'unico motivo per utilizzare il pacchetto (Froyo) è se la destinazione attivamente Froyo.

### <a name="where-are-the-components-stored-on-my-machinecomponent-storagemd"></a>[In cui sono archiviati i componenti nel computer?](component-storage.md)
Quando si installa un componente Xamarin in un progetto di App, si ottiene inserito nei due percorsi elencati in questa Guida.


## <a name="troubleshooting"></a>Risoluzione dei problemi
### <a name="where-can-i-find-my-version-information-and-logsversion-logsmd"></a>[Dove posso trovare le informazioni sulla versione e i registri?](version-logs.md)
Questa guida illustra in dettaglio la posizione in cui trovare la maggior parte delle informazioni di diagnostica che possono essere usate per risolvere i problemi di Xamarin.

### <a name="when-and-how-should-i-file-a-bug-reporthowto-file-bugmd"></a>[Quando e come devo file di un report sui bug?](howto-file-bug.md)
Questa guida vengono forniti suggerimenti per l'invio di segnalazioni di bug di alta qualità, in modo che i tecnici sono in grado di determinare la causa (e le potenziali correzioni) per un problema in modo più efficiente.

### <a name="why-isnt-jenkins-supported-by-xamarinxamarin-jenkinsmd"></a>[Motivo per cui non è supportato Jenkins da Xamarin?](xamarin-jenkins.md)
Jenkins è una suite di elemento di configurazione open source; a causa di problemi di questo numero direttamente causati da di Jenkins *stesso* dovrà essere archiviato come problemi di base in cui è stato ottenuto il codice; ad esempio il [repository principale di Jenkins](https://github.com/jenkinsci/jenkins), o il repository per [ Jenkins.app](https://github.com/stisti/jenkins-app).

### <a name="what-project-settings-are-required-for-the-debuggerdebugger-settingsmd"></a>[Le impostazioni del progetto sono necessari per il debugger?](debugger-settings.md)
Affinché il debugger a funzionare come previsto (passaggi dei punti di interruzione, visualizzare i registri di debug e così via), visualizzazione di informazioni di debug e di strumentazione per sviluppatori deve entrambi essere abilitata. Questa guida illustra in dettaglio come trovare e attivare queste impostazioni.


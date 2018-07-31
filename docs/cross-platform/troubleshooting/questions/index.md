---
title: Domande frequenti generali
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C7E6E54D-3957-407D-BB87-22B095148C6B
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 06aa6569301d1bfdbf9f6fd1e7397a38a9beb6f6
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350820"
---
# <a name="general-frequently-asked-questions"></a>Domande frequenti generali

## <a name="portable-class-libraries"></a>Librerie di classi portabili

### <a name="how-can-i-view-what-libraries-are-supported-in-a-pclpcl-support-librariesmd"></a>[Come è possibile visualizzare le librerie supportate in una libreria di classi portabile (PCL)?](pcl-support-libraries.md)
Questa guida vengono elencati i metodi per determinare se la libreria esistente è supportata da varie piattaforme di destinazione di libreria di classi Portabile, oppure può essere convertita in un profilo di libreria di classi Portabile e le risorse.

### <a name="pcl-reflection-apipcl-reflectionmd"></a>[API Reflection per librerie PCL](pcl-reflection.md)
Microsoft ha sviluppato una nuova API di Reflection per l'uso nelle librerie di classi portabile. Se hai un codice di Reflection esistente che si desidera spostare in una libreria di classi Portabile, potrebbe non funzionare.

### <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-packagepcl-case-studymd"></a>[Case study per librerie PCL: come è possibile risolvere i problemi correlati a System.Diagnostics.Tracing per il pacchetto NuGet Microsoft TPL Dataflow?](pcl-case-study.md)
Xamarin. IOS e xamarin. Android non implementano 100% di tutti i profili PCL che consentono come riferimenti. Per motivi di praticità pratiche in Visual Studio per Mac, Visual Studio e la gestione pacchetti NuGet, progetti Xamarin consentono l'uso dei diversi profili che hanno solo le implementazioni incomplete. Ad esempio, xamarin. IOS né xamarin. Android attualmente include un'implementazione completa dei tipi nel `System.Diagnostics.Tracing` dello spazio dei nomi di libreria di classi Portabile. È possibile risolvere il problema passando il progetto dell'app per fare riferimento il formato portabile TargetFramework="net45 win8 wp8 + wpa81 versione della libreria del flusso di dati TPL.

## <a name="nuget-packages--xamarin-components"></a>I pacchetti NuGet e componenti di Xamarin
### <a name="how-can-i-update-nugetnuget-updatemd"></a>[Come si aggiorna NuGet?](nuget-update.md)
Aggiornamenti di NuGet, estensioni e componenti aggiuntivi sono disponibili nel **aggiornamenti** scheda le **Gestione pacchetti NuGet**. Navigazione dettagliati per trovare gli aggiornamenti in Visual Studio per Mac e Visual Studio è in questa Guida.

### <a name="how-do-i-downgrade-a-nuget-packagenuget-package-downgrademd"></a>[Come si effettua il downgrade di un pacchetto NuGet?](nuget-package-downgrade.md)
Visual Studio per Mac e Visual Studio hanno caratteristiche per la selezione di versioni meno recenti dei pacchetti e installarli automaticamente. simile al modo in cui l'aggiornamento dei pacchetti works.

### <a name="missing-packages-error-after-updating-nuget-packagesnuget-packages-missingmd"></a>[Errore di pacchetti mancanti dopo l'aggiornamento di pacchetti Nuget](nuget-packages-missing.md)
Questo problema è stato segnalato principalmente sulle soluzioni di app di esempio xamarin. Forms, ma il potenziale per risolvere questo problema può verificarsi in qualsiasi progetto che usa i pacchetti NuGet.

### <a name="unifying-google-play-services-components-and-nugetgps-components-nugetmd"></a>[Unificazione dei componenti di Google Play Services e NuGet](gps-components-nuget.md)
Sono utilizzati da diversi componenti di Google Play Services e pacchetti NuGet, ma per semplificare le cose per gli sviluppatori, abbiamo abbiamo ora unificata i componenti e NuGet pacchetti in due parti. In quasi ogni caso, usare Google Play Services. L'unico motivo per usare il pacchetto (Froyo) è se si usa attivamente Froyo.

### <a name="where-are-the-components-stored-on-my-machinecomponent-storagemd"></a>[In quale posizione sono archiviati i componenti nel computer?](component-storage.md)
Quando si installa un componente Xamarin in un progetto di App, si ottiene inserito nei due percorsi elencati in questa Guida.


## <a name="troubleshooting"></a>Risoluzione dei problemi
### <a name="where-can-i-find-my-version-information-and-logsversion-logsmd"></a>[Dove si possono trovare informazioni sulla versione e i log?](version-logs.md)
Questa guida viene illustrato dove trovare la maggior parte delle informazioni di diagnostica che possono essere utilizzate per risolvere i problemi di Xamarin.

### <a name="when-and-how-should-i-file-a-bug-reporthowto-file-bugmd"></a>[Quando e come è opportuno registrare un report sui bug?](howto-file-bug.md)
Questa guida vengono forniti suggerimenti per l'invio di segnalazioni di bug di alta qualità, in modo che i tecnici sono in grado di determinare la causa (e le potenziali correzioni) per un problema in modo più efficiente.

### <a name="why-isnt-jenkins-supported-by-xamarinxamarin-jenkinsmd"></a>[Perché Jenkins non è supportato da Xamarin?](xamarin-jenkins.md)
Jenkins è una suite di integrazione continua open source; a causa di questo molti problemi sono causati direttamente dagli Jenkins *sé* dovrà essere presentata come problemi di in cui è stato ottenuto il codice, ad esempio il [repository Jenkins principale](https://github.com/jenkinsci/jenkins), o il repository per [ Jenkins.app](https://github.com/stisti/jenkins-app).

### <a name="what-project-settings-are-required-for-the-debuggerdebugger-settingsmd"></a>[Quali impostazioni di progetto sono richieste per il debugger?](debugger-settings.md)
Affinché il debugger a funzionare come previsto (passaggi dei punti di interruzione, visualizzare i log di debug e così via), visualizzazione di informazioni di debug e strumentazione per sviluppatori deve essere entrambi abilitata. Questa guida illustra come trovare e attivare queste impostazioni.


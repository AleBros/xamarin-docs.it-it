---
title: Domande frequenti generali
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C7E6E54D-3957-407D-BB87-22B095148C6B
author: conceptdev
ms.author: crdun
ms.date: 05/08/2018
ms.openlocfilehash: de70eda2898f29a1e7afed9440d5f5fae496e069
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765107"
---
# <a name="general-frequently-asked-questions"></a>Domande frequenti generali

## <a name="portable-class-libraries"></a>Librerie di classi portabili

### <a name="how-can-i-view-what-libraries-are-supported-in-a-pclpcl-support-librariesmd"></a>[Come è possibile visualizzare le librerie supportate in una libreria di classi portabile (PCL)?](pcl-support-libraries.md)
Questa guida elenca le risorse e i metodi per determinare se la libreria esistente è supportata dalle diverse piattaforme di destinazione PCL oppure può essere convertita in un profilo PCL.

### <a name="pcl-reflection-apipcl-reflectionmd"></a>[API Reflection per librerie PCL](pcl-reflection.md)
Microsoft ha sviluppato una nuova API di reflection da usare nelle librerie di classi portabili. Se si dispone di un codice di Reflection esistente che si desidera spostare in una libreria di classi portabile, potrebbe non funzionare.

### <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-packagepcl-case-studymd"></a>[Case study PCL: Come è possibile risolvere i problemi correlati a System. Diagnostics. Tracing per il pacchetto NuGet Microsoft TPL Dataflow?](pcl-case-study.md)
Novell. iOS e Novell. Android non implementano il 100% di ogni profilo PCL che consentono come riferimenti. Per praticità in Visual Studio per Mac, Visual Studio e gestione pacchetti NuGet, i progetti Novell consentono di usare diversi profili che hanno solo implementazioni incomplete. Ad esempio, né Novell. iOS né Novell. Android includono attualmente un'implementazione completa dei tipi nello `System.Diagnostics.Tracing` spazio dei nomi PCL. Per risolvere il problema, cambiare il progetto dell'app in modo che faccia riferimento alla versione portabile-Net45 + Win8 + WP8 + wpa81 della libreria del flusso di flussi di dispositivi TPL.

## <a name="nuget-packages--xamarin-components"></a>Pacchetti NuGet & componenti Novell
### <a name="how-can-i-update-nugetnuget-updatemd"></a>[Come si aggiorna NuGet?](nuget-update.md)
Gli aggiornamenti, le estensioni e i componenti aggiuntivi di NuGet sono disponibili nella scheda **aggiornamenti** di **Gestione pacchetti NuGet**. La navigazione dettagliata per trovare gli aggiornamenti in Visual Studio per Mac & Visual Studio è illustrata in questa guida.

### <a name="how-do-i-downgrade-a-nuget-packagenuget-package-downgrademd"></a>[Come si effettua il downgrade di un pacchetto NuGet?](nuget-package-downgrade.md)
Visual Studio per Mac & Visual Studio includono funzionalità per la selezione di versioni precedenti dei pacchetti e per l'installazione automatica. simile a come funziona l'aggiornamento dei pacchetti.

### <a name="missing-packages-error-after-updating-nuget-packagesnuget-packages-missingmd"></a>[Errore di pacchetti mancanti dopo l'aggiornamento di pacchetti Nuget](nuget-packages-missing.md)
Questo problema è stato segnalato principalmente per le soluzioni di esempio di Novell. Forms, ma la possibilità di questo problema può verificarsi in qualsiasi progetto che usa pacchetti NuGet.

### <a name="unifying-google-play-services-components-and-nugetgps-components-nugetmd"></a>[Unificazione dei componenti di Google Play Services e NuGet](gps-components-nuget.md)
Sono stati usati diversi componenti Google Play Services e pacchetti NuGet, ma per semplificare le operazioni per gli sviluppatori, abbiamo ora unificato i nostri componenti e pacchetti NuGet in due. In quasi tutti i casi, è necessario usare Google Play Services. L'unico motivo per usare il pacchetto (Froyo) è la destinazione attiva di Froyo.

### <a name="where-are-the-components-stored-on-my-machinecomponent-storagemd"></a>[In quale posizione sono archiviati i componenti nel computer?](component-storage.md)
Ogni volta che si installa un componente Novell in un progetto di app, questo viene inserito nelle due posizioni elencate in questa guida.

## <a name="troubleshooting"></a>Risoluzione dei problemi
### <a name="where-can-i-find-my-version-information-and-logsversion-logsmd"></a>[Dove si possono trovare informazioni sulla versione e i log?](version-logs.md)
Questa guida descrive in dettaglio dove trovare le informazioni di diagnostica che possono essere usate per risolvere i problemi relativi a Novell.

### <a name="when-and-how-should-i-file-a-bug-reporthowto-file-bugmd"></a>[Quando e come è opportuno registrare un report sui bug?](howto-file-bug.md)
Questa guida fornisce suggerimenti per la creazione di report di bug di alta qualità, in modo che i tecnici siano in grado di determinare la causa (e le eventuali correzioni potenziali) per un problema in modo più efficiente.

### <a name="why-isnt-jenkins-supported-by-xamarinxamarin-jenkinsmd"></a>[Perché Jenkins non è supportato da Xamarin?](xamarin-jenkins.md)
Jenkins è una suite CI open source, a causa di questo problema, i problemi causati direttamente *da Jenkins dovranno* essere archiviati come problemi rispetto alla posizione in cui è stato ottenuto il codice; come il [repository Jenkins principale](https://github.com/jenkinsci/jenkins)o il repository per [Jenkins. app](https://github.com/stisti/jenkins-app).

### <a name="what-project-settings-are-required-for-the-debuggerdebugger-settingsmd"></a>[Quali impostazioni di progetto sono richieste per il debugger?](debugger-settings.md)
Affinché il debugger funzioni come previsto (hit punti di interruzione, Visualizza log di debug e così via), è necessario abilitare entrambi gli strumenti per la visualizzazione di informazioni di debug e strumentazione per sviluppatori. Questa guida illustra come trovare e attivare queste impostazioni.

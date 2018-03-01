---
title: Nozioni fondamentali sulle applicazioni
description: Concetti di base dell'applicazione
ms.topic: article
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: 9e4e7705e1ca29b6abf716a48ae3fa0e7c1a19ec
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="application-fundamentals"></a>Nozioni fondamentali sulle applicazioni

In questa sezione viene fornita una Guida su alcune delle più comuni attività di cose o concetti che gli sviluppatori devono tenere conto durante lo sviluppo di applicazioni per dispositivi mobili.

##  <a name="building-cross-platform-applicationscross-platformapp-fundamentalsbuilding-cross-platform-applicationsindexmd"></a>[Compilazione incrociata piattaforma applicazioni](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)

Scegliendo Xamarin e tenendo in considerazione alcuni aspetti quando si progettano e sviluppano applicazioni per dispositivi mobili, è possibile realizzare notevole codice condiviso tra le piattaforme per dispositivi mobili, ridurre il tempo di immissione sul mercato, sfruttare le competenze esistenti, soddisfare le esigenze per l'accesso mobile, e ridurre la complessità e multipiattaforma. &nbsp;Questo documento descrive linee guida fondamentali per comprendere i vantaggi per le applicazioni di utilità e produttività.

## <a name="code-sharing-optionscode-sharingmd"></a>[Opzioni di condivisione del codice](code-sharing.md)

Ulteriori informazioni sul codice diverso opzioni disponibili per progetti di Xamarin, incluse le librerie di classi portabili (PCLs), progetti condivisi e le librerie Standard di .NET di condivisione.


## <a name="accessibilityaccessibilitymd"></a>[Accessibilità](accessibility.md)

Suggerimenti per la compilazione di applicazioni accessibili.


## <a name="localizationlocalizationmd"></a>[Localizzazione](localization.md)

Linee guida per la creazione di applicazioni basate sulle impostazioni internazionali che possono essere convertite in più lingue.


##  <a name="portable-class-librariescross-platformapp-fundamentalspclmd"></a>[Librerie di classi portabili](~/cross-platform/app-fundamentals/pcl.md)

Progetti libreria di classi portabili consentono di creare e distribuire gli assembly contenenti codice condiviso per l'esecuzione su più piattaforme. Per creare una libreria di classi portabile (o "PCL") prima selezionare le piattaforme di destinazione, quindi scrivere codice per un sottoinsieme di .NET Framework è disponibile nel profilo definito per tali piattaforme. Questo documento viene descritto come creare e usare PCLs con Xamarin.

##  <a name="shared-projectscross-platformapp-fundamentalsshared-projectsmd"></a>[Progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md)

Progetti condivisi consentono di scrivere codice comune a cui fa riferimento un numero di progetti di applicazione diverso. Il codice viene compilato come parte di ogni progetto di riferimento e può includere le direttive del compilatore per incorporare le funzionalità specifiche della piattaforma di base di codice condiviso. Questo articolo illustra il funzionamento progetti condivisi e come crearli e utilizzarli con i progetti di Xamarin.

##  <a name="net-standardcross-platformapp-fundamentalsnet-standardmd"></a>[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)

.NET standard è una nuova opzione per la condivisione del codice tra le piattaforme. Funziona in modo simile alle librerie di classi portabile; codice viene compilato con una versione specifica (attualmente 1.0 tramite 1.6) e può quindi essere utilizzato da altri progetti che supportano tale livello o superiore. Progetti .NET standard sono supportati in 6.2 di Xamarin Studio, Visual Studio per Windows e Visual Studio per Mac.

##  <a name="nuget-projects-multiplatform-libraries-for-code-sharingcross-platformapp-fundamentalsnuget-multiplatform-librariesindexmd"></a>[Progetti di NuGet: Librerie multipiattaforma per la condivisione del codice](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)

Pacchetti NuGet possono essere generati automaticamente da progetti libreria di classi Portabile o .NET standard. e i progetti condivisi possono essere inseriti in pacchetti NuGet "tecnica dello" utilizzando il tipo di progetto NuGet separato. In questa sezione viene illustrato come creare pacchetti di NuGet per ogni scenario di condivisione del codice.

##  <a name="manually-creating-nuget-packages-for-xamarincross-platformapp-fundamentalsnuget-manualmd"></a>[Creazione manuale di pacchetti NuGet di Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)

Suggerimenti per la creazione di pacchetti NuGet che funzionano con la piattaforma di Xamarin.

##  <a name="cross-platform-data-accessxamarin-formsdata-cloudindexmd"></a>[Accesso a dati piattaforma](~/xamarin-forms/data-cloud/index.md)

La maggior parte delle applicazioni presentano un requisito per salvare i dati nel dispositivo locale. A meno che la quantità di dati sia in modo semplice di piccole dimensioni, è in genere necessario un database e un livello di dati nell'applicazione per gestire l'accesso al database. iOS e Android installato il motore di database SQLite "incorporato" e viene semplificato l'accesso per archiviare e recuperare dati dalla piattaforma di Xamarin. Il [accesso ai dati Android](~/android/data-cloud/data-access/index.md), [accesso ai dati di iOS](~/ios/data-cloud/data/index.md), e [accesso ai dati di xamarin. Forms](~/xamarin-forms/data-cloud/index.md) guide forniscono esempi relativi all'accesso SQLite in ciascuna piattaforma.


##  <a name="transport-layer-securitytransport-layer-securitymd"></a>[Transport Layer Security](transport-layer-security.md)

Informazioni sulla selectingthe corretta implementazione di SSL/TLS per proteggere la connettività di rete dell'applicazione.


##  <a name="notificationsxamarin-formsdata-cloudpush-notificationsindexmd"></a>[Notifiche](~/xamarin-forms/data-cloud/push-notifications/index.md)

Applicazioni per dispositivi mobili di usare le notifiche come un modo per informare l'utente che ha si è verificato un evento specifico di applicazione non intrusivo. Le notifiche vengono in genere usate per informare l'utente dello stato di un processo dell'applicazione in cui è in esecuzione in background. Un esempio potrebbe essere download di un file di grandi dimensioni. Questo file potrebbe richiedere molto tempo per il download, in modo da questa attività deve essere eseguita in background. Una volta completato il download, l'utente viene informato del fatto da una notifica.
Inoltre, notifica are non riguarda solo per le applicazioni locali. È inoltre possibile per le applicazioni server pubblicare notifiche a applicazioni per dispositivi mobili. Questo articolo viene illustrato come usare le notifiche in Android e iOS.

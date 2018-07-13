---
title: Nozioni fondamentali dell'applicazione xamarin. Forms
description: Esplorare i concetti fondamentali dello sviluppo di applicazioni xamarin. Forms, inclusi tutti i concetti principali necessari, tramite per ultimi ritocchi, ad esempio accessibilità e localizzazione.
ms.prod: xamarin
ms.assetid: 7B516BBC-F7E1-4387-9779-7754E2E69723
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: 515dbd2683619cfcfb7a6c8ecac6bc147265ef7d
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995612"
---
# <a name="xamarinforms-application-fundamentals"></a>Nozioni fondamentali dell'applicazione xamarin. Forms

## <a name="accessibilityaccessibilityindexmd"></a>[Accessibilità](accessibility/index.md)

Suggerimenti su come per incorporare funzionalità accessibili (ad esempio, supporto di strumenti di lettura dello schermo) con xamarin. Forms.

## <a name="app-classapplication-classmd"></a>[Classe App](application-class.md)

Il `Application` classe è il punto di partenza per xamarin. Forms – ogni app deve implementare una sottoclasse `App` per impostare la pagina iniziale. Fornisce inoltre il `Properties` raccolta per archiviazione di dati semplici. Può essere definita in c# o XAML.

## <a name="app-lifecycleapp-lifecyclemd"></a>[Ciclo di vita dell'app](app-lifecycle.md)

Il `Application` classe `OnStart`, `OnSleep`, e `OnResume` metodi, nonché gli eventi di navigazione modale, consentono di gestire gli eventi del ciclo di vita dell'applicazione con codice personalizzato.

## <a name="behaviorsbehaviorsindexmd"></a>[Comportamenti](behaviors/index.md)

Controlli dell'interfaccia utente possono essere facilmente estesa senza la creazione di sottoclassi usando i comportamenti per aggiungere funzionalità.

## <a name="custom-rendererscustom-rendererindexmd"></a>[Renderer personalizzati](custom-renderer/index.md)

Esegue il rendering personalizzato consentono agli sviluppatori 'override' il rendering predefinito dei controlli di xamarin. Forms per personalizzare l'aspetto e il comportamento in ogni piattaforma (se necessario, usando SDK nativi).

## <a name="data-bindingdata-bindingindexmd"></a>[Data binding](data-binding/index.md)

Associazione dati collega le proprietà di due oggetti, sono consentite modifiche in una proprietà saranno apportate automaticamente l'altra proprietà. Associazione dati è parte integrante di Model-View-ViewModel ([MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md)) architettura dell'applicazione.

## <a name="dependency-servicedependency-serviceindexmd"></a>[Servizio di dipendenza](dependency-service/index.md)

Il `DependencyService` fornisce un localizzatore semplice in modo che sia possibile di codice alle interfacce nel codice condiviso e fornire implementazioni specifiche della piattaforma che vengono risolte automaticamente, rendendo più semplice fare riferimento a funzionalità specifiche della piattaforma in xamarin. Forms.

## <a name="effectseffectsindexmd"></a>[Effetti](effects/index.md)

Effetti consentono controlli nativi in ogni piattaforma per la personalizzazione e vengono in genere usati per lo stile piccole modifiche.

## <a name="filesfilesmd"></a>[File](files.md)

File di gestione delle eccezioni con xamarin. Forms può essere ottenuto tramite codice in una libreria .NET Standard, o utilizzando le risorse incorporate.

## <a name="gesturesgesturesindexmd"></a>[Movimenti](gestures/index.md)

Xamarin. Forms [ `GestureRecognizer` ](xref:Xamarin.Forms.GestureRecognizer) classe supporta tocco zoom indietro e i movimenti di panoramica sui controlli dell'interfaccia utente.

## <a name="localizationlocalizationindexmd"></a>[Localizzazione](localization/index.md)

Il framework di localizzazione .NET incorporato è utilizzabile per compilare applicazioni multilingue multipiattaforma con xamarin. Forms.

## <a name="local-databasesdatabasesmd"></a>[Database locali](databases.md)

Xamarin. Forms supporta applicazioni basate su database con il motore di database SQLite, che consente di caricare e salvare gli oggetti nel codice condiviso.

## <a name="messaging-centermessaging-centermd"></a>[Centro di messaggistica](messaging-center.md)

Xamarin. Forms `MessagingCenter` consente di visualizzare i modelli e altri componenti per comunicare con senza dover conoscere tra loro oltre a un semplice contratto di messaggio.

## <a name="navigationnavigationindexmd"></a>[Navigazione](navigation/index.md)

Xamarin. Forms fornisce una serie di esperienze di navigazione pagina diversa, a seconda di `Page` digitare in uso.

## <a name="templatestemplatesindexmd"></a>[Modelli](templates/index.md)

Modelli di controllo offrono la possibilità di tema facilmente e re-theme le pagine dell'applicazione in fase di esecuzione, mentre i modelli di dati offrono la possibilità di definire la presentazione dei dati nei controlli supportati.

## <a name="triggerstriggersmd"></a>[Trigger](triggers.md)

Aggiornare i controlli in risposta alle modifiche di proprietà e gli eventi in XAML.


## <a name="related-links"></a>Collegamenti correlati

- [Introduction to Xamarin.Forms (Introduzione a Xamarin.Forms)](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)

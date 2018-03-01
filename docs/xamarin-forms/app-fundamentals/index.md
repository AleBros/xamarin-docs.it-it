---
title: Nozioni fondamentali sulle applicazioni
description: Esplorare le nozioni di base dello sviluppo di xamarin. Forms
ms.topic: article
ms.prod: xamarin
ms.assetid: 7B516BBC-F7E1-4387-9779-7754E2E69723
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: afa3bf25b1448d98c49c95a66bd0f4dc55bde39e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="application-fundamentals"></a>Nozioni fondamentali sulle applicazioni

## <a name="accessibilityaccessibilityindexmd"></a>[Accessibilità](accessibility/index.md)

Suggerimenti per incorporare funzionalità accessibili (ad esempio strumenti di lettura dello schermo di supporto) con xamarin. Forms.

## <a name="app-classapplication-classmd"></a>[Classe App](application-class.md)

Il `Application` classe è il punto di partenza per xamarin. Forms – ogni app deve implementare una sottoclasse `App` per impostare la pagina iniziale. Fornisce inoltre il `Properties` raccolta per archiviazione di dati semplice. Può essere definito in c# o XAML.

## <a name="app-lifecycleapp-lifecyclemd"></a>[Ciclo di vita App](app-lifecycle.md)

Il `Application` classe `OnStart`, `OnSleep`, e `OnResume` metodi, nonché degli eventi di navigazione modale, consentono di gestire gli eventi del ciclo di vita dell'applicazione con codice personalizzato.

## <a name="behaviorsbehaviorsindexmd"></a>[Comportamenti](behaviors/index.md)

Controlli dell'interfaccia utente possono essere esteso facilmente senza la creazione di sottoclassi utilizzando i comportamenti per aggiungere funzionalità.

## <a name="custom-rendererscustom-rendererindexmd"></a>[Renderer personalizzato](custom-renderer/index.md)

Esegue il rendering personalizzato consentono agli sviluppatori 'override', il rendering predefinito dei controlli di xamarin. Forms per personalizzare l'aspetto e comportamento in ciascuna piattaforma (tramite il SDK nativo se lo si desidera).

## <a name="data-bindingdata-bindingindexmd"></a>[Data binding](data-binding/index.md)

Associazione dati consente di collegare le proprietà di due oggetti, sono consentite modifiche in una proprietà per riflettere automaticamente le altre proprietà. Data binding è parte integrante del modello-View-ViewModel ([MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md)) architettura dell'applicazione.

## <a name="dependency-servicedependency-serviceindexmd"></a>[Servizio di dipendenza](dependency-service/index.md)

Il `DependencyService` fornisce un localizzatore semplice in modo che sia possibile di codice alle interfacce nel codice condiviso e fornire implementazioni specifiche della piattaforma che vengono risolte automaticamente, semplificando così fa riferimento a funzionalità specifiche della piattaforma in xamarin. Forms.

## <a name="effectseffectsindexmd"></a>[Effetti](effects/index.md)

Effetti consentono controlli nativi in ciascuna piattaforma da personalizzare e vengono in genere utilizzati per lo stile piccole modifiche.

## <a name="gesturesgesturesindexmd"></a>[movimenti](gestures/index.md)

Di xamarin. Forms [ `GestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/) classe supporta tap, zoom indietro e i movimenti di panoramica sui controlli dell'interfaccia utente.

## <a name="localizationlocalizationmd"></a>[Localizzazione](localization.md)

Il framework di localizzazione .NET incorporato consente di compilare applicazioni multilingue multipiattaforma con xamarin. Forms.

## <a name="local-databasesdatabasesmd"></a>[Database locali](databases.md)

Xamarin. Forms supporta le applicazioni basate sul database utilizzando il motore di database SQLite, che consente di caricare e salvare gli oggetti nel codice condiviso.

## <a name="messaging-centermessaging-centermd"></a>[Messaggistica Center](messaging-center.md)

Xamarin. Forms `MessagingCenter` consente di visualizzare modelli e altri componenti per comunicare con senza dover conoscere tra loro oltre a un semplice contratto di messaggio.

## <a name="navigationnavigationindexmd"></a>[Navigazione](navigation/index.md)

Xamarin. Forms fornisce una serie di esperienze di navigazione pagina diversa, a seconda di `Page` digitare in uso.

## <a name="templatestemplatesindexmd"></a>[Modelli](templates/index.md)

Modelli di controllo consentono di tema facilmente e il tema di ambiente ripristino pagine dell'applicazione in fase di esecuzione, mentre i modelli di dati consentono di definire la presentazione dei dati in controlli supportati.

## <a name="triggerstriggersmd"></a>[Trigger](triggers.md)

Aggiornare i controlli in risposta a modifiche di proprietà ed eventi in XAML.


## <a name="related-links"></a>Collegamenti correlati

- [Introduction to Xamarin.Forms (Introduzione a Xamarin.Forms)](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)

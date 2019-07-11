---
title: Nozioni di base sulle applicazioni Xamarin.Forms
description: Esplorare le nozioni di base dello sviluppo di applicazioni Xamarin.Forms, inclusi tutti i concetti principali necessari, fino agli ultimi ritocchi come l'accessibilità e la localizzazione.
ms.prod: xamarin
ms.assetid: 7B516BBC-F7E1-4387-9779-7754E2E69723
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/08/2018
ms.openlocfilehash: 61d9c5b1c7b81bfc8a20932ce1d188cd0cb0f980
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650437"
---
# <a name="xamarinforms-application-fundamentals"></a>Nozioni di base sulle applicazioni Xamarin.Forms

## <a name="accessibilityaccessibilityindexmd"></a>[Accessibilità](accessibility/index.md)

Suggerimenti su come incorporare funzionalità per l'accessibilità (ad esempio, il supporto di strumenti di lettura dello schermo) con Xamarin.Forms.

## <a name="app-classapplication-classmd"></a>[Classe App](application-class.md)

La classe `Application` è il punto di partenza per Xamarin.Forms: ogni app deve implementare una sottoclasse `App` per impostare la pagina iniziale. Include anche la raccolta `Properties` per l'archiviazione semplice dei dati. Può essere definita in C# o XAML.

## <a name="app-lifecycleapp-lifecyclemd"></a>[Ciclo di vita dell'app](app-lifecycle.md)

I metodi `OnStart`, `OnSleep` e `OnResume` della classe `Application` nonché gli eventi di navigazione modale, consentono di gestire gli eventi del ciclo di vita dell'applicazione con codice personalizzato.

## <a name="application-indexing-and-deep-linkingdeep-linkingmd"></a>[Indicizzazione e deep linking delle applicazioni](deep-linking.md)

L'indicizzazione delle applicazioni consente di mantenere rilevanti, visualizzandole nei risultati della ricerca, le applicazioni che verrebbero altrimenti dimenticate dopo pochi usi. Il deep linking consente alle applicazioni di rispondere a un risultato di ricerca che contiene dati dell'applicazione, in genere passando a una pagina a cui fa riferimento un collegamento diretto.

## <a name="behaviorsbehaviorsindexmd"></a>[Comportamenti](behaviors/index.md)

I controlli dell'interfaccia utente possono essere facilmente estesi senza la creazione di sottoclassi usando i comportamenti per aggiungere funzionalità.

## <a name="custom-rendererscustom-rendererindexmd"></a>[Renderer personalizzati](custom-renderer/index.md)

I renderer personalizzati consentono agli sviluppatori di eseguire l'ovverride del rendering predefinito dei controlli di Xamarin.Forms per personalizzare l'aspetto e il comportamento in ogni piattaforma (se necessario, usando SDK nativi).

## <a name="data-bindingdata-bindingindexmd"></a>[Data binding](data-binding/index.md)

Il data binding consente di collegare le proprietà di due oggetti in modo che le modifiche apportate a una proprietà vengano automaticamente riflesse nell'altra proprietà. Il data binding è parte integrante dell'architettura dell'applicazione Model-View-ViewModel ([MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md)).

## <a name="dependency-servicedependency-serviceindexmd"></a>[Servizio di dipendenza](dependency-service/index.md)

`DependencyService` offre un localizzatore semplice che consente di scrivere codice sulle interfacce nel codice condiviso e offrire implementazioni specifiche della piattaforma che vengono risolte automaticamente, rendendo semplice il riferimento a funzionalità specifiche della piattaforma in Xamarin.Forms.

## <a name="effectseffectsindexmd"></a>[Effetti](effects/index.md)

Gli effetti consentono di personalizzare i controlli nativi di ogni piattaforma e in genere vengono usati per apportare piccole modifiche allo stile.

## <a name="gesturesgesturesindexmd"></a>[Movimenti](gestures/index.md)

La classe [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) di Xamarin.Forms supporta i movimenti tocco, avvicinamento delle dita e panoramica sui controlli dell'interfaccia utente.

## <a name="localizationlocalizationindexmd"></a>[Localizzazione](localization/index.md)

Il framework di localizzazione .NET incorporato può essere usato per compilare applicazioni multilingue e multipiattaforma con Xamarin.Forms.

## <a name="messaging-centermessaging-centermd"></a>[Centro di messaggistica](messaging-center.md)

`MessagingCenter` di Xamarin.Forms abilita modelli di visualizzazione e altri componenti con cui comunicare senza la necessità di alcuna conoscenza reciproca, se non un semplice contratto di messaggio.

## <a name="navigationnavigationindexmd"></a>[Navigazione](navigation/index.md)

Xamarin.Forms offre diverse esperienze di navigazione tra le pagine, a seconda del tipo `Page` in uso.

## <a name="shellshellindexmd"></a>[Shell](shell/index.md)

La shell Xamarin.Forms riduce la complessità dello sviluppo di applicazioni per dispositivi mobili offrendo le principali funzionalità richieste dalla maggior parte delle applicazioni per dispositivi mobili. Queste funzionalità includono un'esperienza utente di navigazione comune, uno schema di navigazione basato su URI e un gestore di ricerca integrato.

## <a name="templatestemplatesindexmd"></a>[Modelli](templates/index.md)

I modelli del controllo offrono la possibilità di applicare e riapplicare facilmente un tema alle pagine dell'applicazione in fase di esecuzione, mentre i modelli di dati consentono di definire la presentazione dei dati nei controlli supportati.

## <a name="triggerstriggersmd"></a>[Trigger](triggers.md)

Aggiornare i controlli in risposta alle modifiche di proprietà e gli eventi in XAML.

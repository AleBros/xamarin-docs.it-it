---
title: Introduzione ai modelli del controllo Xamarin.Forms
description: I modelli del controllo Xamarin.Forms consentono di applicare e riapplicare facilmente un tema alle pagine dell'applicazione in fase di esecuzione. Questo articolo offre un'introduzione ai modelli del controllo.
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 70646999154297592137c6966626b318fb73897c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771260"
---
# <a name="introduction-to-xamarinforms-control-templates"></a>Introduzione ai modelli del controllo Xamarin.Forms

_I modelli del controllo Xamarin.Forms consentono di applicare e riapplicare facilmente un tema alle pagine dell'applicazione in fase di esecuzione. Questo articolo offre un'introduzione ai modelli del controllo._

I controlli presentano proprietà diverse, ad esempio `BackgroundColor` e `TextColor`, che possono definire le caratteristiche dell'aspetto del controllo. Queste proprietà possono essere impostate usando gli [stili](~/xamarin-forms/user-interface/styles/index.md), che possono essere modificati in fase di esecuzione per implementare i temi di base. Gli stili non mantengono tuttavia una netta separazione tra l'aspetto di una pagina e il relativo contenuto e le modifiche che possono essere apportate tramite l'impostazione di tali proprietà sono limitate.

I modelli del controllo offrono una separazione netta tra l'aspetto di una pagina e il relativo contenuto, consentendo pertanto la creazione di pagine facilmente associabili a temi. Un'applicazione può ad esempio contenere i modelli del controllo a livello di applicazione che offrono un tema chiaro e un tema scuro. È possibile associare il tema a ogni [`ContentPage`](xref:Xamarin.Forms.ContentPage) nell'applicazione applicando uno dei modelli del controllo senza modificare il contenuto visualizzato da ogni pagina. I temi offerti dai modelli del controllo non sono limitati alla modifica delle proprietà dei controlli. Possono anche modificare i controlli usati per implementare il tema.

## <a name="creating-a-controltemplate"></a>Creazione di un ControlTemplate

Un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) specifica l'aspetto di una pagina o vista, contiene un layout radice e, all'interno del layout, i controlli che implementano il modello. Un `ControlTemplate` userà in genere un [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) per indicare il punto in cui sarà inserito il contenuto che deve essere visualizzato nella pagina o vista. La pagina o vista che usa il `ControlTemplate` definirà quindi il contenuto da visualizzare tramite il `ContentPresenter`. Il diagramma seguente illustra un `ControlTemplate` per una pagina che contiene alcuni controlli, tra cui un `ContentPresenter` contrassegnato da un rettangolo blu:

![](introduction-images/control-template.png "Modello del controllo per una pagina")

Un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) può essere applicato ai tipi seguenti impostando le relative proprietà `ControlTemplate`:

- [`ContentPage`](xref:Xamarin.Forms.ContentPage)
- [`ContentView`](xref:Xamarin.Forms.ContentView)
- [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)
- [`TemplatedView`](xref:Xamarin.Forms.TemplatedView)

Quando un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) viene creato e assegnato a questi tipi, qualsiasi aspetto esistente viene sostituito con l'aspetto definito nel `ControlTemplate`. Oltre a impostare l'aspetto tramite la proprietà `ControlTemplate`, i modelli del controllo possono essere applicati anche usando gli stili per espandere ulteriormente la capacità di associazione dei temi.

> [!NOTE]
> *Che cosa sono i tipi `TemplatedPage` e `TemplatedView`?* `TemplatedPage` è la classe di base per `ContentPage` ed è il tipo di pagina di base offerto da Xamarin.Forms. A differenza di `ContentPage`, `TemplatedPage` non prevede una proprietà `Content`. Il contenuto non può quindi essere aggiunto direttamente a un'istanza `TemplatedPage`. Viene invece aggiunto impostando il modello del controllo per l'istanza `TemplatedPage`. Analogamente, `TemplatedView` è la classe base per `ContentView`. A differenza di `ContentView`, `TemplatedView` non prevede una proprietà `Content`. Il contenuto non può quindi essere aggiunto direttamente a un'istanza `TemplatedView`. Viene invece aggiunto impostando il modello del controllo per l'istanza `TemplatedView`.

I modelli del controllo possono essere creati in XAML e in C#:

- I modelli del controllo creati in XAML sono definiti in un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) che viene assegnato alla raccolta [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) di una pagina o,più frequentemente, alla raccolta [`Resources`](xref:Xamarin.Forms.Application.Resources) dell'applicazione.
- I modelli del controllo creati in C# vengono in genere definiti nella classe della pagina o in una classe alla quale si può accedere a livello globale.

La scelta della posizione in cui definire un'istanza [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) ha effetto sulla posizione in cui può essere usato:

- Le istanze [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) definite a livello di pagina possono essere applicate solo alla pagina.
- Le istanze [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) definite a livello di applicazione possono essere applicate alle pagine in tutta l'applicazione.

I modelli del controllo inferiori nella gerarchia di visualizzazione hanno la precedenza rispetto a quelli definiti a livello superiore. Ad esempio, un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) denominato `DarkTheme` definito a livello di pagina avrà la precedenza su un modello con nome identico definito a livello di applicazione. Un modello del controllo che definisce un tema da applicare a ogni pagina in un'applicazione deve perciò essere definito a livello di applicazione.

## <a name="related-links"></a>Collegamenti correlati

- [Stili](~/xamarin-forms/user-interface/styles/index.md)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)

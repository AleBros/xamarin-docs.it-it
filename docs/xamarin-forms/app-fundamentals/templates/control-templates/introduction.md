---
title: Introduzione ai modelli di controllo di xamarin. Forms
description: Modelli di controllo di xamarin. Forms offrono la possibilità di tema facilmente e re-theme le pagine dell'applicazione in fase di esecuzione. Questo articolo fornisce un'introduzione ai modelli di controllo.
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 6b7a6c6d9c9c541e1d5e821fc2dac202e98bec62
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994425"
---
# <a name="introduction-to-xamarinforms-control-templates"></a>Introduzione ai modelli di controllo di xamarin. Forms

_Modelli di controllo di xamarin. Forms offrono la possibilità di tema facilmente e re-theme le pagine dell'applicazione in fase di esecuzione. Questo articolo fornisce un'introduzione ai modelli di controllo._

Controlli presentano proprietà diverse, ad esempio `BackgroundColor` e `TextColor`, che puoi definire alcuni aspetti dell'aspetto del controllo. Queste proprietà possono essere impostate usando [stili](~/xamarin-forms/user-interface/styles/index.md), che può essere modificato in fase di esecuzione per implementare i temi di base. Tuttavia, gli stili non mantengono una netta separazione tra l'aspetto di una pagina e il relativo contenuto e le modifiche che possono essere apportate tramite l'impostazione di tali proprietà sono limitate.

Modelli di controllo forniscono una netta separazione tra l'aspetto di una pagina e il relativo contenuto, consentendo pertanto la creazione di pagine che possono essere facilmente con tema. Ad esempio, un'applicazione può contenere i modelli di controllo a livello di applicazione che forniscono un tema chiaro e un tema scuro. Ciascuna [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) nell'applicazione possono essere con temi applicando uno dei modelli del controllo senza modificarne il contenuto visualizzato da ogni pagina. Inoltre, i temi forniti dai modelli di controllo non sono limitati a modificare le proprietà dei controlli. È inoltre possibile modificare i controlli usati per implementare il tema.

## <a name="creating-a-controltemplate"></a>Creazione di un ControlTemplate

Oggetto [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) specifica l'aspetto di una pagina o una vista e contiene un layout di primo livello e all'interno del layout, i controlli che implementano il modello. In genere, un `ControlTemplate` utilizzerà un [ `ContentPresenter` ](xref:Xamarin.Forms.ContentPresenter) per contrassegnare in cui verrà visualizzato il contenuto da visualizzare tramite la pagina o la vista. La pagina o la vista che utilizza il `ControlTemplate` definirà quindi il contenuto da visualizzare tramite il `ContentPresenter`. Il diagramma seguente illustra un `ControlTemplate` per una pagina che contiene un numero di controlli, tra cui un `ContentPresenter` contrassegnato da un rettangolo blu:

![](introduction-images/control-template.png "Modello di controllo per una pagina")

Oggetto [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) può essere applicato per i tipi seguenti impostando i `ControlTemplate` proprietà:

- [`ContentPage`](xref:Xamarin.Forms.ContentPage)
- [`ContentView`](xref:Xamarin.Forms.ContentView)
- [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)
- [`TemplatedView`](xref:Xamarin.Forms.TemplatedView)

Quando un [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) viene creato e assegnato a questi tipi qualsiasi aspetto esistente viene sostituito con l'aspetto definito nel `ControlTemplate`. Inoltre, oltre a impostare l'aspetto usando i `ControlTemplate` controllo modelli possono essere applicati anche utilizzando gli stili per la proprietà espandere la capacità di tema.

> [!NOTE]
>  *Quali sono le `TemplatedPage` e `TemplatedView` tipi?* `TemplatedPage` è la classe base per `ContentPage`ed è il tipo di pagina di base fornito da xamarin. Forms. A differenza `ContentPage`, `TemplatedPage` non ha un `Content` proprietà. Pertanto, non è possibile aggiungere direttamente il contenuto a un `TemplatedPage` istanza. Al contrario, il contenuto viene aggiunto, impostare il modello di controllo per il `TemplatedPage` istanza. Analogamente, `TemplatedView` è la classe base per `ContentView`. A differenza `ContentView`, `TemplatedView` non ha un `Content` proprietà. Pertanto, non è possibile aggiungere direttamente il contenuto a un `TemplatedView` istanza. Al contrario, il contenuto viene aggiunto, impostare il modello di controllo per il `TemplatedView` istanza.

È possibile creare modelli di controllo in XAML e c#:

- I modelli di controllo creati in XAML sono definiti un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) che viene assegnato al [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) raccolta di una pagina o, in genere, per il [ `Resources` ](xref:Xamarin.Forms.Application.Resources) raccolta dell'applicazione.
- Modelli di controllo creati in c# sono in genere definiti nella classe della pagina, o in una classe che è possibile accedere a livello globale.

Scegliere dove definire un [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) istanza impatti dove può essere utilizzato:

- [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) le istanze definite a livello di pagina è applicabile solo alla pagina.
- [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) istanze definite a livello di applicazione possono essere applicate alle pagine in tutta l'applicazione.

Modelli di livello inferiore nella gerarchia di visualizzazione del controllo hanno la precedenza rispetto a quelli definiti più backup. Ad esempio, un [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) denominato `DarkTheme` definito a livello di pagina avrà la precedenza su un modello con nome identico definito a livello di applicazione. Pertanto, un modello di controllo che definisce un tema da applicare a ogni pagina in un'applicazione deve essere definito a livello di applicazione.


## <a name="related-links"></a>Collegamenti correlati

- [Stili](~/xamarin-forms/user-interface/styles/index.md)
- [Oggetto ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)

---
title: Introduzione ai modelli di controllo di xamarin. Forms
description: Modelli di controllo di xamarin. Forms consentono di tema facilmente e il tema di ambiente ripristino pagine dell'applicazione in fase di esecuzione. In questo articolo viene fornita un'introduzione ai modelli di controllo.
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: a8e5c84bfa2525a28e9af5343be0ee156564bdd6
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242518"
---
# <a name="introduction-to-xamarinforms-control-templates"></a>Introduzione ai modelli di controllo di xamarin. Forms

_Modelli di controllo di xamarin. Forms consentono di tema facilmente e il tema di ambiente ripristino pagine dell'applicazione in fase di esecuzione. In questo articolo viene fornita un'introduzione ai modelli di controllo._

Controlli presentano diverse proprietà, ad esempio `BackgroundColor` e `TextColor`, che puoi definire alcuni aspetti dell'aspetto del controllo. Queste proprietà possono essere impostate utilizzando [stili](~/xamarin-forms/user-interface/styles/index.md), che può essere modificato in fase di esecuzione per implementare i temi di base. Tuttavia, gli stili di non mantengono una netta separazione tra l'aspetto di una pagina e il relativo contenuto e le modifiche apportate mediante l'impostazione di tali proprietà sono limitate.

Modelli di controllo forniscono una netta separazione tra l'aspetto di una pagina e il relativo contenuto, consentendo pertanto la creazione di pagine che possono essere facilmente con temi. Ad esempio, un'applicazione può contenere i modelli di controllo a livello di applicazione che forniscono un tema chiaro e un tema scuro. Ogni [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) nell'applicazione è possibile applicare un tema applicando uno dei modelli di controllo senza modificarne il contenuto visualizzato da ogni pagina. Inoltre, i temi forniti dai modelli di controllo non sono limitati alle proprietà dei controlli. Possono inoltre modificare i controlli utilizzati per implementare il tema.

## <a name="creating-a-controltemplate"></a>Creazione di un ControlTemplate

Oggetto [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) specifica l'aspetto di una pagina o una vista e contiene un layout di primo livello e all'interno del layout, i controlli che implementano il modello. In genere, un `ControlTemplate` utilizzerà un [ `ContentPresenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/) per contrassegnare in cui verrà visualizzato il contenuto da visualizzare tramite la pagina o la vista. La pagina o la vista che utilizza il `ControlTemplate` verrà quindi definire il contenuto da visualizzare tramite il `ContentPresenter`. Il diagramma seguente illustra un `ControlTemplate` per una pagina che contiene un numero di controlli, ad esempio, un `ContentPresenter` contrassegnato da un rettangolo blu:

![](introduction-images/control-template.png "Modello di controllo per una pagina")

Oggetto [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) può essere applicato ai seguenti tipi impostando i relativi `ControlTemplate` proprietà:

- [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)
- [`ContentView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)
- [`TemplatedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/)
- [`TemplatedView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/)

Quando un [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) viene creato e assegnato a questi tipi, qualsiasi aspetto esistente viene sostituito con l'aspetto definito nel `ControlTemplate`. Inoltre, nonché l'impostazione di aspetto tramite il `ControlTemplate` controllo modelli possono essere applicati anche utilizzando gli stili di un'ulteriore proprietà espandere il possibilità di tema.

> [!NOTE]
>  *Quali sono le `TemplatedPage` e `TemplatedView` tipi?* `TemplatedPage` è la classe base per `ContentPage`ed è il tipo di pagina di base fornito da xamarin. Forms. A differenza di `ContentPage`, `TemplatedPage` non esiste un `Content` proprietà. Pertanto, il contenuto non possa essere aggiunti direttamente a un `TemplatedPage` istanza. Al contrario, il contenuto viene aggiunto impostando il modello di controllo per il `TemplatedPage` istanza. Analogamente, `TemplatedView` è la classe base per `ContentView`. A differenza di `ContentView`, `TemplatedView` non esiste un `Content` proprietà. Pertanto, il contenuto non possa essere aggiunti direttamente a un `TemplatedView` istanza. Al contrario, il contenuto viene aggiunto impostando il modello di controllo per il `TemplatedView` istanza.

Modelli di controllo possono essere creati in XAML e in c#:

- Modelli di controllo creati in XAML sono definiti un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) assegnato al [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) raccolta di una pagina o a più di frequente per il [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Resources/) raccolta dell'applicazione.
- Modelli di controllo creati in c# sono in genere definiti nella classe della pagina o in una classe che è possibile accedere a livello globale.

Scelta di come definire un [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) istanza impatti dove possono essere usato:

- [`ControlTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) solo istanze definite a livello di pagina possono essere applicate alla pagina.
- [`ControlTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) le istanze definite a livello di applicazione possono essere applicate alle pagine in tutta l'applicazione.

Nei modelli di controllo di livello inferiore nella gerarchia di visualizzazione hanno la precedenza su quelli definiti più backup. Ad esempio, un [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) denominato `DarkTheme` definito a livello di pagina ha la precedenza su un modello per lo stesso nome a livello di applicazione. Pertanto, un modello di controllo che definisce un tema da applicare a ogni pagina in un'applicazione debba essere definito a livello di applicazione.


## <a name="related-links"></a>Collegamenti correlati

- [Stili](~/xamarin-forms/user-interface/styles/index.md)
- [ControlTemplate](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)
- [ContentPresenter](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/)

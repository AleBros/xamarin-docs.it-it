---
title: Temi di xamarin. Forms
description: Questo articolo presenta i temi di xamarin. Forms, che definiscono specifico aspetto visivo per le visualizzazioni standard.
ms.prod: xamarin
ms.assetid: 3DFB7C55-69F6-4980-A501-588719143482
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: 1c5b2635dca6aa74fd0dfb92d7e62e6da3140538
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051108"
---
# <a name="xamarinforms-themes"></a>Temi di xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/Themes/ThemesDemo)

![](~/media/shared/preview.png "Questa API è attualmente in anteprima")

I temi di xamarin. Forms sono stati annunciati in occasione di variare delle 2016 e sono disponibili come anteprima ai clienti di provare e inviare commenti e suggerimenti.

Un tema viene aggiunto a un'applicazione xamarin. Forms, includendo il **Xamarin.Forms.Theme.Base** di pacchetti Nuget, oltre a un pacchetto aggiuntivo che definisce un tema specifico (ad es. Xamarin.Forms.Theme.Light) o altrimenti un tema locale può essere definito per l'applicazione.

Fare riferimento al [tema chiaro](light.md) e [tema scuro](dark.md) pagine per istruzioni su come aggiungerli a un'app o consultare la [tema personalizzato di esempio](custom.md).

**Importante:** è anche consigliabile seguire i passaggi necessari per [caricare gli assembly di tema (sotto)](#loadtheme) aggiungendo codice boilerplate per iOS `AppDelegate` e Android `MainActivity`. Questo verrà migliorato in una versione futura versione di anteprima.


## <a name="control-appearance"></a>Controllare l'aspetto

Il [Light](light.md) e [scuro](dark.md) entrambi i temi definiscono un aspetto visivo specifico per i controlli standard. Dopo aver aggiunto un tema a dizionario risorse dell'applicazione, verrà modificato l'aspetto dei controlli standard.

Il markup XAML seguente mostra alcuni controlli comuni:

```xaml
<StackLayout Padding="40">
    <Label Text="Regular label" />
    <Entry Placeholder="type here" />
    <Button Text="OK" />
    <BoxView Color="Yellow" />
    <Switch />
</StackLayout>
```

Questi screenshot mostrano questi controlli con:

* Nessuna tema applicato
* Tema chiaro (solo differenze minime non con alcun tema)
* Tema scuro

![](images/standard-none-sml.png "Controlli senza temi") ![](images/standard-light-sml.png "controlli con tema chiaro") ![](images/standard-dark-sml.png "controlli con tema scuro")

<a name="styleclass" />

## <a name="styleclass"></a>StyleClass

Il `StyleClass` proprietà consente l'aspetto della visualizzazione da modificare in base a una definizione fornita da un tema.

Il [Light](light.md) e [scuro](dark.md) entrambi i temi definiscono tre aspetti diversi per una `BoxView`: `HorizontalRule`, `Circle`, e `Rounded`. Questo markup illustra tre diversi `BoxView`s con classi di stile diverse applicate:

```xaml
<StackLayout Padding="40">
    <BoxView StyleClass="HorizontalRule" />
    <BoxView StyleClass="Circle" />
    <BoxView StyleClass="Rounded" />
</StackLayout>
```

Ciò viene eseguito il rendering con chiaro e scuro come indicato di seguito:

![](images/boxview-light-sml.png "BoxView con StyleClass un tema chiaro") ![](images/boxview-dark-sml.png "BoxView con StyleClass un tema scuro")

<a name="builtin" />

## <a name="built-in-classes"></a>Classi incorporate

Oltre a automaticamente lo stile comune controlla la luce e i temi scuri supportano attualmente le classi seguenti che possono essere applicate tramite l'impostazione di `StyleClass` ai controlli:

**BoxView**

* HorizontalRule
* Cerchio
* Arrotondato

**Immagine**

* Cerchio
* Arrotondato
* Anteprima

**Pulsante**

* Impostazione predefinita
* Primario
* Riuscito
* Info
* Avviso
* Pericolo
* Collegamento
* Small
* Large

**Etichetta**

* Intestazione
* Sottotitolo
* Body
* Collegamento
* Inverso


## <a name="troubleshooting"></a>Risoluzione dei problemi

<a name="loadtheme" />

### <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>Non è stato possibile caricare file o l'assembly 'Xamarin.Forms.Theme.Light' o una delle relative dipendenze

Nella versione di anteprima, i temi potrebbero non essere in grado di caricare in fase di esecuzione. Aggiungere il codice seguente nei progetti appropriati per correggere l'errore.

**iOS**

Nel **AppDelegate.cs** aggiungere le righe seguenti dopo `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

Nel **MainActivity.cs** aggiungere le righe seguenti dopo `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```


## <a name="related-links"></a>Collegamenti correlati

- [Esempio ThemesDemo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Themes/ThemesDemo)

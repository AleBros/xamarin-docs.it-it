---
title: Controlli XAML Standard (anteprima)
description: Questo articolo esamina i controlli standard XAML disponibili in xamarin. Forms.
ms.prod: xamarin
ms.assetid: 287E6631-D1C5-46C5-8905-AB53D34E365D
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: 1b01d0773f0c2150db575875b770957eb6452f41
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245569"
---
# <a name="xaml-standard-preview-controls"></a>Controlli XAML Standard (anteprima)

![Anteprima](~/media/shared/preview.png)

Questa pagina elenca i controlli XAML Standard disponibili in anteprima, insieme ai relativi equivalente controllo xamarin. Forms.

È inoltre disponibile un elenco di controlli che dispongono di nuovi nomi di proprietà e di enumerazione nella sintassi XAML per gli Standard.

## <a name="controls"></a>Controlli

|Xamarin.Forms|Sintassi XAML per gli Standard|
|--- |--- |
|Frame|Bordo|
|Selezione|ComboBox|
|ActivityIndicator|ProgressRing|
|StackLayout|StackPanel|
|Label|TextBlock|
|Voce|TextBox|
|Opzione|ToggleSwitch|
|ContentView|UserControl|


## <a name="properties-and-enumerations"></a>Proprietà ed enumerazioni

|Controlli di xamarin. Forms con proprietà aggiornate|Proprietà di xamarin. Forms o enum|Sintassi XAML per gli Standard equivalente|
|--- |--- |--- |
|Pulsante, voce, etichetta, DatePicker, Editor, SearchBar, TimePicker|TextColor|Primo piano|
|VisualElement|BackgroundColor|Sfondo *|
|Strumento di selezione, pulsante|BorderColor, OutlineColor|BorderBrush|
|Button|BorderWidth|BorderThickness|
|ProgressBar|Stato|Valore|
|Pulsante, voce, etichetta, Editor, SearchBar, intervallo, tipo di carattere|FontAttributesBold, corsivo, nessuno|FontStyleItalic, Normal|
|Pulsante, voce, etichetta, Editor, SearchBar, intervallo, tipo di carattere|FontAttributes|FontWeights * grassetto, Normal|
|InputView|KeyboardDefault, Url, numero, telefono, testo, Chat, inviare tramite posta elettronica|InputScopeNameValue * predefinito, Url, numero, TelephoneNumber, testo, Chat, EmailNameOrAddress|
|StackPanel|StackOrientation|Orientamento *|

> [!IMPORTANT]
> Le voci contrassegnate con * sono incompleti nell'anteprima corrente

## <a name="related-links"></a>Collegamenti correlati

- [Anteprima NuGet](https://aka.ms/xf-xamlstandard-nuget)

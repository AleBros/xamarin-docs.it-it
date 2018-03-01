---
title: Controlli XAML Standard (anteprima)
description: Come iniziare a esplorare l'anteprima Standard di XAML in xamarin. Forms
ms.topic: article
ms.prod: xamarin
ms.assetid: 287E6631-D1C5-46C5-8905-AB53D34E365D
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: 3f30a77975a9f42380ecf7efd73426763ec83ef0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="xaml-standard-preview-controls"></a>Controlli XAML Standard (anteprima)

![Anteprima](~/media/shared/preview.png)

Questa pagina elenca i controlli XAML Standard disponibili in anteprima, insieme ai relativi equivalente controllo xamarin. Forms.

È inoltre disponibile un elenco di controlli che dispongono di nuovi nomi di proprietà e di enumerazione nella sintassi XAML per gli Standard.

## <a name="controls"></a>Controlli

<table style="width:300px">
  <tr><th>Xamarin.Forms</th><th>Sintassi XAML per gli Standard</th></tr>
  <tr><td>Frame</td><td>Bordo</td></tr>
  <tr><td>Selezione</td><td>ComboBox</td></tr>
  <tr><td>ActivityIndicator</td><td>ProgressRing</td></tr>
  <tr><td>StackLayout</td><td>StackPanel</td></tr>
  <tr><td>Label</td><td>TextBlock</td></tr>
  <tr><td>Voce</td><td>TextBox</td></tr>
  <tr><td>Opzione</td><td>ToggleSwitch</td></tr>
  <tr><td>ContentView</td><td>UserControl</td></tr>
</table>

## <a name="properties-and-enumerations"></a>Proprietà ed enumerazioni

<table>
  <tr><th>Xamarin.Forms<br/>Controlli con le proprietà aggiornate</th><th>Xamarin.Forms<br/>Proprietà o Enum</th><th>Sintassi XAML per gli Standard<br/>Equivalente</th></tr>
  <tr><td>Pulsante, voce, etichetta, DatePicker, Editor, SearchBar, TimePicker</td><td>TextColor</td><td>Primo piano</td></tr>
  <tr><td>VisualElement</td><td>BackgroundColor</td><td><i>Sfondo *</i></td></tr>
  <tr><td>Strumento di selezione, pulsante</td><td>BorderColor, OutlineColor</td><td>BorderBrush</td></tr>
  <tr><td>Button</td><td>BorderWidth</td><td>BorderThickness</td></tr>
  <tr><td>ProgressBar</td><td>Stato</td><td>Valore</td></tr>
  <tr><td>Pulsante, voce, etichetta, Editor, SearchBar, intervallo, tipo di carattere</td><td>FontAttributes<br/>Grassetto, corsivo, nessuno</td><td>FontStyle<br/>Corsivo, normale</td></tr>
  <tr><td>Pulsante, voce, etichetta, Editor, SearchBar, intervallo, tipo di carattere</td><td>FontAttributes</td><td><i>FontWeights *</i><br/>Normale, grassetto</td></tr>
  <tr><td>InputView</td><td>Tastiera<br/>Impostazione predefinita, l'Url, numero, telefono, testo, Chat, posta elettronica</td><td><i>InputScopeNameValue *</i><br/>Impostazione predefinita, l'Url, numero, TelephoneNumber, testo, Chat, EmailNameOrAddress</td></tr>
  <tr><td>StackPanel</td><td>StackOrientation</td><td><i>Orientamento *</i></td></tr>
</table>

> [!IMPORTANT]
> Le voci contrassegnate con * sono incompleti nell'anteprima corrente


## <a name="related-links"></a>Collegamenti correlati

- [Anteprima NuGet](https://aka.ms/xf-xamlstandard-nuget)

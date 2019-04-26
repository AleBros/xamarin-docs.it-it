---
title: Tema chiaro di xamarin. Forms
description: Questo articolo illustra come usare il tema chiaro di xamarin. Forms in un'app.
ms.prod: xamarin
ms.assetid: D5D16AE3-F51F-4359-B37A-E1087ECE512B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: 7f40e375d653acec60f8848627234ab46fcce8de
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896271"
---
# <a name="xamarinforms-light-theme"></a>Tema chiaro di xamarin. Forms

![](~/media/shared/preview.png "Questa API è attualmente in anteprima")

> [!NOTE]
> I temi richiedono la versione di anteprima di xamarin. Forms 2.3. Verificare i [suggerimenti](~/xamarin-forms/user-interface/themes/index.md) se si verificano errori.

Per utilizzare il tema chiaro:

## <a name="1-add-nuget-packages"></a>1. Aggiungere i pacchetti Nuget

* Xamarin.Forms.Theme.Base
* Xamarin.Forms.Theme.Light

## <a name="2-add-to-the-resource-dictionary"></a>2. Aggiungere al dizionario risorse

Nel **app. XAML** file aggiungere una nuova `xmlns` per il tema, quindi verificare che le risorse del tema vengono unite con dizionario risorse dell'applicazione.
Seguito è riportato un esempio di file XAML:

```xaml
<?xml version="1.0" encoding="utf-8"?>
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="EvolveApp.App"
             xmlns:light="clr-namespace:Xamarin.Forms.Themes;assembly=Xamarin.Forms.Theme.Light">
    <Application.Resources>
        <ResourceDictionary MergedWith="light:LightThemeResources" />
    </Application.Resources>
</Application>
```

## <a name="3-load-theme-classes"></a>3. Caricare le classi di temi

Seguire questo [risoluzione dei problemi di passaggio](~/xamarin-forms/user-interface/themes/index.md) e aggiungere il codice necessario in iOS e i progetti applicazione Android.

## <a name="4-use-styleclass"></a>4. Usare StyleClass

Ecco un esempio di pulsanti ed etichette il tema chiaro, con il markup che li produce.

[![](light-images/light-theme-sml.png "Pulsanti ed etichette nel tema chiaro")](light-images/light-theme.png#lightbox "pulsanti ed etichette nel tema chiaro")

```xaml
<StackLayout Padding="20">
    <Button Text="Button Default" />
    <Button Text="Button Class Default" StyleClass="Default" />
    <Button Text="Button Class Primary" StyleClass="Primary" />
    <Button Text="Button Class Success" StyleClass="Success" />
    <Button Text="Button Class Info" StyleClass="Info" />
    <Button Text="Button Class Warning" StyleClass="Warning" />
    <Button Text="Button Class Danger" StyleClass="Danger" />
    <Button Text="Button Class Link" StyleClass="Link" />
    <Button Text="Button Class Default Small" StyleClass="Small" />
    <Button Text="Button Class Default Large" StyleClass="Large" />
</StackLayout>
```

Il [elenco completo delle classi incorporate](~/xamarin-forms/user-interface/themes/index.md) Mostra gli stili disponibili per alcuni controlli comuni.

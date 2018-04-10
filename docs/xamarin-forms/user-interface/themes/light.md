---
title: Tema chiaro
ms.prod: xamarin
ms.assetid: D5D16AE3-F51F-4359-B37A-E1087ECE512B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: d6d898bad2ac2107c4819815a441b89128eac04e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="light-theme"></a>Tema chiaro

![](~/media/shared/preview.png "Questa API è attualmente in anteprima")

> [!NOTE]
> Temi richiedono la versione di anteprima di xamarin. Forms 2.3. Controllare il [suggerimenti sulla risoluzione dei](~/xamarin-forms/user-interface/themes/index.md) se si verificano errori.

Per utilizzare il tema chiaro:

## <a name="1-add-nuget-packages"></a>1. Aggiungere pacchetti Nuget

* Xamarin.Forms.Theme.Base
* Xamarin.Forms.Theme.Light

## <a name="2-add-to-the-resource-dictionary"></a>2. Aggiungere al dizionario risorse

Nel **app** file aggiunge un nuovo oggetto personalizzato `xmlns` per il tema, quindi verificare che il dizionario di risorse dell'applicazione vengono unite le risorse del tema.
Un file XAML di esempio è illustrato di seguito:

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

Seguire questo [risoluzione dei problemi di passaggio](~/xamarin-forms/user-interface/themes/index.md) e aggiungere il codice necessario nella progetti di applicazione Android e iOS.

## <a name="4-use-styleclass"></a>4. Utilizzare StyleClass

Di seguito è riportato un esempio di pulsanti ed etichette tema chiaro, con il markup che li produce.

[![](light-images/light-theme-sml.png "I pulsanti ed etichette in tema chiaro")](light-images/light-theme.png#lightbox "pulsanti ed etichette in tema chiaro")

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

Il [elenco completo delle classi incorporate](~/xamarin-forms/user-interface/themes/index.md) presenta gli stili disponibili per alcuni controlli comuni.

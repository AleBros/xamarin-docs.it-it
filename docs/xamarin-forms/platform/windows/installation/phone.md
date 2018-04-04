---
title: Aggiunta di un'App di Windows Phone
ms.prod: xamarin
ms.assetid: B598FA9D-6818-4CC9-8191-838C156DB9DA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/16/2016
ms.openlocfilehash: 55bd4bdcfde4c91ad5c9b94bef486207466e135d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="adding-a-windows-phone-app"></a>Aggiunta di un'App di Windows Phone


In primo luogo, se è stato utilizzato il modello, libreria di classi Portabile xamarin. Forms [aggiornare il profilo](~/xamarin-forms/platform/windows/installation/index.md), quindi seguire le istruzioni seguenti:

1. **pulsante destro del mouse sulla soluzione > Aggiungi > Nuovo progetto...**  e aggiungere un **applicazione vuota (Windows Phone)**

  ![](phone-images/add-wp81.png "Aggiungere una finestra di dialogo Nuovo progetto")

2. **pulsante destro del mouse sul progetto appena creato > Gestisci pacchetti NuGet...**  e aggiungere il **xamarin. Forms** pacchetto.

3. **Fare clic su progetto > Aggiungi > riferimento** e creare un riferimento al progetto al progetto di applicazione condiviso xamarin. Forms.

  ![](phone-images/addref.png "Finestra di dialogo Gestione riferimenti")

4. Modifica **App.xaml.cs** per includere il `Init()` chiamata al metodo, nel `OnLaunched` metodo alla riga 67:

```csharp
// add this line
Xamarin.Forms.Forms.Init (e); // requires LaunchActivatedEventArgs
// above this existing line
if (e.PreviousExecutionState == ApplicationExecutionState.Terminated) {}
```

 5 . Modifica **MainPage. XAML** -modificare l'elemento radice `<Page` a `<forms:WindowsPhonePage` *e* definiscono il `xmlns:forms` che utilizza:

```xaml
<forms:WindowsPhonePage
...
   xmlns:forms="using:Xamarin.Forms.Platform.WinRT"
...
</forms:WindowsPhonePage>
```

 6 . Modifica **MainPage.xaml.cs** per rimuovere il `: PhonePage` identificatore di ereditarietà per il nome della classe.

```csharp
public sealed partial class MainPage  // REMOVE ": PhonePage"
```

 7 . Ancora in **MainPage.xaml.cs**, aggiungere il `LoadApplication` chiamare il `MainPage` costruttore (attorno alla riga 28) per avviare l'app xamarin. Forms:

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

8 . Fare doppio clic su **package. appxmanifest** per impostare queste funzionalità che sono spesso necessarie:

  * Internet (client e server)

9 . Infine, aggiungere risorse locali (ad es. file di immagine) nei progetti di piattaforma esistenti che sono necessari.


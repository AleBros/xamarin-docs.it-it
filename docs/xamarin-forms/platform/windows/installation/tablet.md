---
title: Aggiunta di un'App di Windows
ms.prod: xamarin
ms.assetid: ADF99B78-F1BC-48DF-9128-01B93C4411C1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/16/2016
ms.openlocfilehash: 0d2ef44896c9352776443c2fec318d40d27d7539
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2018
---
# <a name="adding-a-windows-app"></a>Aggiunta di un'App di Windows


1. **pulsante destro del mouse sulla soluzione > Aggiungi > Nuovo progetto...**  e aggiungere un **App vuota (Windows)**

 ![](tablet-images/add-wu.png "Aggiungere una finestra di dialogo Nuovo progetto")

2. **Fare clic su progetto > Gestisci pacchetti NuGet...**  e aggiungere il **xamarin. Forms** pacchetto.

3. **Fare clic su progetto > Aggiungi > riferimento** e creare un riferimento al progetto al progetto di applicazione condiviso xamarin. Forms.

  ![](tablet-images/addref.png "Finestra di dialogo Gestione riferimenti")

4. Modifica **App.xaml.cs** per includere il `Init()` chiamata al metodo all'interno di `OnLaunched` alla riga 65 (metodo)

```csharp
// add this line
Xamarin.Forms.Forms.Init (e); // requires LaunchActivatedEventArgs
// above this existing line
if (e.PreviousExecutionState == ApplicationExecutionState.Terminated) {}
```

 5 . Modifica **MainPage. XAML** -modificare l'elemento radice `<Page` a `<forms:WindowsPage` *e* definiscono il `xmlns:forms` che utilizza:

```xaml
<forms:WindowsPage
...
   xmlns:forms="using:Xamarin.Forms.Platform.WinRT"
...
</forms:WindowsPage>
```


 6 . Modifica **MainPage.xaml.cs** per rimuovere il `: Page` identificatore di ereditarietà per il nome della classe.

```csharp
public sealed partial class MainPage  // REMOVE ": Page"
```

 7 . Ancora in **MainPage.xaml.cs**, aggiungere il `LoadApplication` chiamare il `MainPage` costruttore (attorno alla riga 28) per avviare l'app xamarin. Forms:

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

8 . Fare doppio clic su **package. appxmanifest** per impostare queste funzionalità che sono spesso necessarie:

  Set di funzionalità:

  * Internet (client)
  * Percorso

9 . Infine, aggiungere risorse locali (ad es. file di immagine) nei progetti di piattaforma esistenti che sono necessari.


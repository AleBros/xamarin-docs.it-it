---
title: Utilizzo delle impostazioni
ms.topic: article
ms.prod: xamarin
ms.assetid: 4B2EB192-F0A2-4010-B141-0431520594C0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 3e699fdc2092d17834c348c07f2440e40441ad86
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-settings"></a>Utilizzo delle impostazioni

App di Apple Watch è possibile utilizzare la stessa funzionalità di impostazioni come le app iOS: l'interfaccia utente di impostazioni viene visualizzata nel **Apple Watch** app iPhone, ma i valori sono accessibili sia l'app iPhone, e anche l'estensione di espressioni di controllo.

![](settings-images/intro.png "App di Apple Watch è possibile utilizzare la stessa funzionalità di impostazioni come le app iOS")

Le impostazioni verranno archiviate in un percorso condiviso accessibile sia l'app iOS che l'estensione dell'app espressioni di controllo, definito da un **gruppo App**. È necessario [configurare un gruppo di App](~/ios/watchos/app-fundamentals/app-groups.md) prima di aggiungere le impostazioni utilizzando le istruzioni seguenti.

## <a name="add-settings-in-a-watch-solution"></a>Aggiungere le impostazioni in una soluzione di espressioni di controllo

Nel **app iPhone** nella soluzione (*non* app espressioni di controllo o estensione):

1. Fare doppio clic su **Aggiungi > Nuovo File...**  e scegliere **Settings** (non è possibile modificare il nome di **nuovo File** finestra di dialogo):

   [![](settings-images/settings-add-sml.png "Aggiungere una nuova aggregazione impostazioni")](settings-images/settings-add.png#lightbox)

2. Modificare il nome in **impostazioni Watch.bundle** (selezionare e digitare **comando + R** rinominare):

   ![](settings-images/settings-rename.png "Rinominare il pacchetto")

3. Aggiungere una nuova chiave `ApplicationGroupContainerIdentifier` per il **plist** con il valore impostato per il gruppo dell'applicazione è configurata, (ad es. `group.com.xamarin.WatchSettings` Nell'esempio):

   [ ![](settings-images/settings-appgroup-sml.png "Aggiungere una chiave ApplicationGroupContainerIdentifier il plist")](settings-images/settings-appgroup.png#lightbox)

4. Modificare il **Settings-Watch.bundle/Root.plist** per contenere le opzioni che si desidera utilizzare - il file modello contiene un gruppo.
  TextField, interruttore e dispositivo di scorrimento per impostazione predefinita (che è possibile eliminare e sostituire con le proprie impostazioni):

  [![](settings-images/rootplist-sml.png "Modificare il Settings-Watch.bundle/Root.plist")](settings-images/rootplist.png#lightbox)


## <a name="use-settings-in-the-watch-app"></a>Utilizza le impostazioni nell'applicazione di espressioni di controllo

Per accedere ai valori selezionati dall'utente, creare un `NSUserDefaults` istanza usando il gruppo dell'applicazione e indicando `NSUserDefaultsType.SuiteName`:

```csharp
NSUserDefaults shared = new NSUserDefaults(
    "group.com.xamarin.WatchSettings",
    NSUserDefaultsType.SuiteName);

var isEnabled = shared.BoolForKey ("enabled_preference");
var userName = shared.StringForKey ("name_preference");
```

## <a name="apple-watch-app"></a>App di Apple Watch

[![](settings-images/settings-app-sml.png "La nuova app di Apple Watch su iPhone")](settings-images/settings-app.png#lightbox)

Gli utenti interagiscono con le impostazioni tramite la nuova **Apple Watch** app sul loro iPhone. Questa applicazione consente all'utente mostrare/nascondere le App in espressioni di controllo, nonché modificare le impostazioni esposte tramite il **impostazioni Watch.bundle**.

![](settings-images/applewatch-1.png "Ad esempio le impostazioni dell'app") ![ ] (settings-images/applewatch-2.png "esempio delle impostazioni di app")



## <a name="related-links"></a>Collegamenti correlati

- [Documento di impostazioni di Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Settings.html#//apple_ref/doc/uid/TP40014969-CH22-SW1)

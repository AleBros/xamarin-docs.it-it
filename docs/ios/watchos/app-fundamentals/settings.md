---
title: Utilizzo di watchOS impostazioni in Xamarin
description: Questo documento viene descritto come utilizzare le impostazioni di watchOS in Xamarin. Illustra le impostazioni di aggiunta a una soluzione di app watch, utilizzando queste impostazioni nell'app e l'app Apple Watch su iPhone.
ms.prod: xamarin
ms.assetid: 4B2EB192-F0A2-4010-B141-0431520594C0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: a8fe2c2765676db52c23fd7c475f218f14697caf
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675234"
---
# <a name="working-with-watchos-settings-in-xamarin"></a>Utilizzo di watchOS impostazioni in Xamarin

Le app Apple Watch è possono usare la stessa funzionalità di impostazioni come le app iOS: l'interfaccia utente delle impostazioni viene visualizzata nel **Apple Watch** app iPhone, ma i valori sono accessibili in sia l'app iPhone e anche l'estensione di espressioni di controllo.

![](settings-images/intro.png "Le app Apple Watch possono usare la stessa funzionalità di impostazioni come le app iOS")

Le impostazioni verranno archiviate in un percorso condiviso accessibile per l'app iOS e l'estensione dell'app watch, definito da un **gruppo di App**. È consigliabile [configurare un gruppo di App](~/ios/watchos/app-fundamentals/app-groups.md) prima di aggiungere le impostazioni usando le istruzioni seguenti.

## <a name="add-settings-in-a-watch-solution"></a>Aggiungere le impostazioni in una soluzione di espressioni di controllo

Nel **app iPhone** all'interno della soluzione (*non* le app watch o estensione):

1. Fare doppio clic su **Aggiungi > Nuovo File...**  e scegliere **Settings. bundle** (non è possibile modificare il nome nel **nuovo File** finestra di dialogo):

   [![](settings-images/settings-add-sml.png "Aggiungere un nuovo Bundle di impostazioni")](settings-images/settings-add.png#lightbox)

2. Modificare il nome in **impostazioni Watch.bundle** (selezionare e digitare **comando + R** rinominare):

   ![](settings-images/settings-rename.png "Rinominare il pacchetto")

3. Aggiungere una nuova chiave `ApplicationGroupContainerIdentifier` per il **root. plist** con il valore impostato per il gruppo di app è stato configurato, (ad es. `group.com.xamarin.WatchSettings` Nell'esempio):

   [![](settings-images/settings-appgroup-sml.png "Aggiungere una chiave ApplicationGroupContainerIdentifier il root. plist")](settings-images/settings-appgroup.png#lightbox)

4. Modificare il **Settings-Watch.bundle/Root.plist** per contenere le opzioni da usare: il file del modello contiene un gruppo.
  TextField, interruttore e dispositivo di scorrimento per impostazione predefinita (che è possibile eliminare e sostituire con le proprie impostazioni):

  [![](settings-images/rootplist-sml.png "Modificare il Settings-Watch.bundle/Root.plist")](settings-images/rootplist.png#lightbox)


## <a name="use-settings-in-the-watch-app"></a>Usare le impostazioni nell'App Watch

Per accedere ai valori selezionati dall'utente, creare un `NSUserDefaults` dell'istanza usando il gruppo di app e specificando `NSUserDefaultsType.SuiteName`:

```csharp
NSUserDefaults shared = new NSUserDefaults(
    "group.com.xamarin.WatchSettings",
    NSUserDefaultsType.SuiteName);

var isEnabled = shared.BoolForKey ("enabled_preference");
var userName = shared.StringForKey ("name_preference");
```

## <a name="apple-watch-app"></a>Apple Watch App

[![](settings-images/settings-app-sml.png "La nuova app Apple Watch nell'iPhone")](settings-images/settings-app.png#lightbox)

Gli utenti interagiranno con le impostazioni tramite il nuovo **Apple Watch** app sul proprio iPhone. Questa app consente all'utente mostrare/nascondere le app nelle espressioni di controllo e inoltre modifica le impostazioni esposte tramite il **impostazioni Watch.bundle**.

![](settings-images/applewatch-1.png "Ad esempio le impostazioni dell'app") ![](settings-images/applewatch-2.png "esempio delle impostazioni di app")



## <a name="related-links"></a>Collegamenti correlati

- [Documentazione di impostazioni di Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Settings.html#//apple_ref/doc/uid/TP40014969-CH22-SW1)

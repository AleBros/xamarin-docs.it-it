---
title: Uso delle impostazioni di watchos in Novell
description: Questo documento descrive come usare le impostazioni di watchos in Novell. Viene illustrato come aggiungere impostazioni a una soluzione di controllo app, usando queste impostazioni nell'app e l'app Apple Watch sull'iPhone.
ms.prod: xamarin
ms.assetid: 4B2EB192-F0A2-4010-B141-0431520594C0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 1a59979b164c5200a96343caa1a44e05992763d0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028376"
---
# <a name="working-with-watchos-settings-in-xamarin"></a>Uso delle impostazioni di watchos in Novell

Apple Watch app possono usare le stesse funzionalità delle impostazioni delle app iOS. l'interfaccia utente delle impostazioni viene visualizzata nell'app **Apple Watch** iPhone, ma i valori sono accessibili sia nell'app iPhone sia nell'estensione Watch.

![](settings-images/intro.png "Apple Watch apps can use the same Settings functionality as iOS apps")

Le impostazioni verranno archiviate in un percorso di file condiviso accessibile sia per l'app iOS che per l'estensione Watch app, definita da un **gruppo di app**. È necessario [configurare un gruppo di app](~/ios/watchos/app-fundamentals/app-groups.md) prima di aggiungere le impostazioni seguendo le istruzioni riportate di seguito.

## <a name="add-settings-in-a-watch-solution"></a>Aggiungere le impostazioni in una soluzione Watch

Nell' **app iPhone** nella soluzione (*non* nell'app Watch o nell'estensione):

1. Fare clic con il pulsante destro del mouse su **aggiungi > nuovo file..** . e scegliere **Settings. bundle** (non è possibile modificare il nome nella finestra di dialogo **nuovo file** ):

   [![](settings-images/settings-add-sml.png "Add a new Settings Bundle")](settings-images/settings-add.png#lightbox)

2. Modificare il nome in **Settings-Watch. bundle** (Select e digitare **Command + R** per rinominare):

   ![](settings-images/settings-rename.png "Rename the bundle")

3. Aggiungere una nuova chiave `ApplicationGroupContainerIdentifier` al file **root. plist** con il valore impostato sul gruppo di app configurato, ad esempio `group.com.xamarin.WatchSettings` nell'esempio):

   [![](settings-images/settings-appgroup-sml.png "Add a ApplicationGroupContainerIdentifier key to the Root.plist")](settings-images/settings-appgroup.png#lightbox)

4. Modificare **Settings-Watch. bundle/root. plist** in modo che contenga le opzioni da usare. il file modello contiene un gruppo.
  TextField, interruttore e dispositivo di scorrimento per impostazione predefinita (che è possibile eliminare e sostituire con le proprie impostazioni):

  [![](settings-images/rootplist-sml.png "Edit the Settings-Watch.bundle/Root.plist")](settings-images/rootplist.png#lightbox)

## <a name="use-settings-in-the-watch-app"></a>Usare le impostazioni nell'app Watch

Per accedere ai valori selezionati dall'utente, creare un'istanza di `NSUserDefaults` usando il gruppo di app e specificando `NSUserDefaultsType.SuiteName`:

```csharp
NSUserDefaults shared = new NSUserDefaults(
    "group.com.xamarin.WatchSettings",
    NSUserDefaultsType.SuiteName);

var isEnabled = shared.BoolForKey ("enabled_preference");
var userName = shared.StringForKey ("name_preference");
```

## <a name="apple-watch-app"></a>App Apple Watch

[![](settings-images/settings-app-sml.png "The new Apple Watch app on the iPhone")](settings-images/settings-app.png#lightbox)

Gli utenti interagiranno con le impostazioni tramite la nuova app **Apple Watch** sul proprio iPhone. Questa app consente all'utente di visualizzare/nascondere le app nell'orologio e di modificare anche le impostazioni esposte usando il **controllo Settings-Watch. bundle**.

![](settings-images/applewatch-1.png "Esempio di impostazioni dell'app") ![](settings-images/applewatch-2.png "Esempio di impostazioni dell'app")

## <a name="related-links"></a>Collegamenti correlati

- [DOC delle impostazioni di Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Settings.html#//apple_ref/doc/uid/TP40014969-CH22-SW1)

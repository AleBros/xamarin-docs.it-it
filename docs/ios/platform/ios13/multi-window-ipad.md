---
title: Più finestre per iPad in Novell. iOS
description: Aggiunta del supporto per più finestre alle applicazioni iPad.
ms.prod: xamarin
ms.assetid: 524b6f2e-dbdf-11e9-8a34-2a2ae2dbcce4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/20/2019
ms.openlocfilehash: ce7df59d41efdd2d151fd2ea73cf26b40ee7fa10
ms.sourcegitcommit: 834466c9d9cf35e9659e467ce0123e5f5ade6138
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2020
ms.locfileid: "85129909"
---
# <a name="multiple-windows-for-ipad"></a>Più finestre per iPad

iOS 13 supporta ora Windows affiancato per la stessa app in iPad. In questo modo vengono abilitate le nuove esperienze e le interazioni con trascinamento della selezione tra le finestre. In questo documento viene illustrato come configurare l'applicazione per supportare questa funzionalità e vengono introdotte le nuove funzionalità. 

## <a name="project-configuration"></a>Configurazione progetto

Per configurare il progetto per più finestre, modificare il `info.plist` per dichiarare la dichiarazione di `NSUserActivityTypes` iOS l'app gestirà le attività di questo tipo e `UIApplicationSceneManifest` per abilitare `UIApplicationSupportsMultipleScenes` per più finestre e `UISceneConfigurations` per associare la scena a uno storyboard.

```xml
<key>NSUserActivityTypes</key>
<array>
    <string>com.xamarin.Gallery.openDetail</string>
</array>
<key>UIApplicationSceneManifest</key>
<dict>
    <key>UIApplicationSupportsMultipleScenes</key>
    <true/>
    <key>UISceneConfigurations</key>
    <dict>
        <key>UIWindowSceneSessionRoleApplication</key>
        <array>
            <dict>
                <key>UISceneConfigurationName</key>
                <string>Default Configuration</string>
                <key>UISceneDelegateClassName</key>
                <string>SceneDelegate</string>
                <key>UISceneStoryboardFile</key>
                <string>Main</string>
            </dict>
        </array>
    </dict>
</dict>
```

## <a name="opening-multiple-windows"></a>Apertura di più finestre

Con l'app aperta e in esecuzione su un iPad, esistono diversi modi per aprire più finestre dell'app che iOS gestisce per impostazione predefinita.

- **Esporre l'app** : toccare l'icona dell'app nel Dock per accedere a questa modalità mentre l'app è aperta.
- **Scorrere** il mouse sull'icona dell'app dal dock nella parte superiore dell'app in esecuzione per avere una finestra mobile.
- **Split Screen** : trascinare l'icona dell'app dal dock al bordo della schermata iPad per creare una nuova finestra affiancata.

All'interno dell'applicazione sono disponibili interazioni aggiuntive per l'immissione di una modalità a più finestre.

**Trascinare dall'app** : usare un'interazione di trascinamento all'interno dell'app per avviare un nuovo `NSUserActivity` proprio come il trascinamento dell'icona dell'app negli esempi precedenti.

Quando si usa un' [interazione con trascinamento della selezione][0], si crea un oggetto e si `NSUserActivity` associano i dati da passare alla nuova finestra richiesta da iOS per l'apertura.

```csharp
public UIDragItem [] GetItemsForBeginningDragSession (UICollectionView collectionView, IUIDragSession session, NSIndexPath indexPath)
{
    var selectedPhoto = GetPhoto (indexPath);

    var userActivity = selectedPhoto.OpenDetailUserActivity ();
    var itemProvider = new NSItemProvider (UIImage.FromFile (selectedPhoto.Name));
    itemProvider.RegisterObject (userActivity, NSItemProviderRepresentationVisibility.All);

    var dragItem = new UIDragItem (itemProvider) {
        LocalObject = selectedPhoto
    };

    return new [] { dragItem };
}
```

Nel codice precedente, l' `selectedPhoto` oggetto modello dispone di un metodo per restituire un oggetto `NSUserActivity` denominato `OpenDetailUserActivity()` . Quando il gesto di trascinamento è completo, l'oggetto `UIDragItem` con il presente l'oggetto `userActivity` tramite l'oggetto `NSItemProvider` .

**Azioni esplicite** : i movimenti utente sui pulsanti o sui collegamenti offrono la possibilità di aprire una nuova finestra.

Dalla `UIApplication` è possibile avviare un nuovo chiamando `UISceneSession` `RequestSceneSessionActivation` . Se esiste già una scena esistente, è consigliabile usarla. Per impostazione predefinita, viene creata una nuova scena.

```csharp
public void ItemSelected(UICollectionView collectionView, NSIndexPath indexPath)
{
    var userActivity = selectedPhoto.OpenDetailUserActivity ();

    UIApplication.SharedApplication.RequestSceneSessionActivation(
        sceneSession: null,
        userActivity: userActivity,
        options: null,
        errorHandler: null
    );
}
```

In questo esempio, `userActivity` è l'unico valore passato al metodo per `RequestSceneSessionActivation` aprire una nuova finestra dell'applicazione in base a un'azione utente esplicita, in questo caso un `ItemSelected` gestore di un oggetto `UICollectionView` .

## <a name="related-links"></a>Collegamenti correlati

- [Trascinamento della selezione in Novell. iOS][0]

[0]: ~/ios/platform/introduction-to-ios11/drag-and-drop.md

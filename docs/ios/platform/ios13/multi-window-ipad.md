---
title: Più finestre per iPad in Xamarin.iOS
description: Aggiunta del supporto per più finestre alle applicazioni iPad.
ms.prod: xamarin
ms.assetid: 524b6f2e-dbdf-11e9-8a34-2a2ae2dbcce4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/20/2019
ms.openlocfilehash: b3f96f342679d8be6d2f8fbc0ad5962ca675d2a5
ms.sourcegitcommit: 09bc69d7119a04684c9e804c5cb113b8b1bb7dfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213793"
---
# <a name="multiple-windows-for-ipad"></a>Più finestre per iPad

iOS 13 supporta ora Windows affiancato per la stessa app in iPad. In questo modo vengono abilitate le nuove esperienze e le interazioni con trascinamento della selezione tra le finestre. In questo documento viene illustrato come configurare l'applicazione per supportare questa funzionalità e vengono introdotte le nuove funzionalità. 

## <a name="project-configuration"></a>Configurazione progetto

Per configurare il progetto per più finestre, modificare il `info.plist` per dichiarare `NSUserActivityTypes` la dichiarazione di iOS l'app gestirà le attività di questo `UIApplicationSceneManifest` tipo e `UIApplicationSupportsMultipleScenes` per abilitare per più `UISceneConfigurations` finestre e per associare il scena con uno storyboard.

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

**Trascinare dall'app** : usare un'interazione di trascinamento all'interno dell'app `NSUserActivity` per avviare un nuovo proprio come il trascinamento dell'icona dell'app negli esempi precedenti.

Quando si usa un' [interazione con trascinamento della selezione][0], si `NSUserActivity` crea un oggetto e si associano i dati da passare alla nuova finestra richiesta da iOS per l'apertura.

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

Nel codice precedente, l' `selectedPhoto` oggetto modello dispone di un metodo per restituire un `NSUserActivity` oggetto denominato. `OpenDetailUserActivity()` Quando il gesto di trascinamento è `UIDragItem` completo, l' `userActivity` oggetto con `NSItemProvider`il presente l'oggetto tramite l'oggetto.

**Azioni esplicite** : i movimenti utente sui pulsanti o sui collegamenti offrono la possibilità di aprire una nuova finestra.

Dalla è possibile avviare un nuovo `UISceneSession` `RequestSceneSessionActivation`chiamando. `UIApplication` Se esiste già una scena esistente, è consigliabile usarla. Per impostazione predefinita, viene creata una nuova scena.

```csharp
pubic void ItemSelected(UICollectionView collectionView, NSIndexPath indexPath)
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

In `userActivity` questo esempio, è l'unico valore passato `RequestSceneSessionActivation` al metodo per aprire una nuova finestra dell'applicazione in base a un'azione utente esplicita, in questo caso un `ItemSelected` gestore di un oggetto `UICollectionView`.

## <a name="related-links"></a>Collegamenti correlati

- [Trascinamento della selezione in Xamarin.iOS][0]

[0]: ~/ios/platform/introduction-to-ios11/drag-and-drop.md

---
title: "Errore IBTool: Non è stato possibile completare l'operazione."
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: c2f727b55b21dc3bd976f0b41c71b794841cfca4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421996"
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>Errore IBTool: Non è stato possibile completare l'operazione.

## <a name="fixed-in-xcode-611"></a>Risolto in Xcode 6.1.1

Apple [fissa](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1) ciò `ibtool` bug in Xcode 6.1.1, pertanto, l'aggiornamento a Xcode 6.1.1 o versione successiva è la soluzione più semplice.

* * *

## <a name="description-of-the-problem"></a>Descrizione del problema

Il `ibtool` comando Xcode 6.0 presenta un bug in OS X 10.10 Yosemite. Xamarin. IOS Usa Xcode `ibtool` per compilare gli storyboard e `XIB` file.

Altre informazioni sui bug in relazione a Xcode sono reperibili nel seguente post di Stack Overflow: [https://stackoverflow.com/questions/25754763/cant-open-storyboard](https://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>Messaggio di errore

> Impossibile aprire il documento "Mainstoryboard". Non è stato possibile completare l'operazione. (errore com.apple.InterfaceBuilder -1).

## <a name="workarounds-for-xcode-60"></a>Soluzioni alternative (per Xcode 6.0)

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>Opzione 1: Gestire tutti `UIImageView.Image` proprietà nel codice

Anziché impostare il `Image` proprietà di un `UIImageView` dello storyboard o `.xib` file, è possibile impostare la proprietà in uno della vista del ciclo di vita nei metodi di override nel controller di visualizzazione (ad esempio, in `ViewDidLoad()`). Vedere anche [utilizzo delle immagini](~/ios/app-fundamentals/images-icons/index.md) per suggerimenti sull'utilizzo `UIImage.FromBundle()` confronto `UIImage.FromFile()`.

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>Opzione 2: Spostare tutte le risorse immagine per il primo livello `Resources` cartella

Dopo aver spostato le immagini di primo livello `Resources` cartella, sarà necessario aggiornare lo storyboard e `.xib` file da utilizzare i nuovi percorsi delle immagini.

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>Opzione 3: Impostare il `LogicalName` per tutti gli asset delle immagini problematica in modo che vengano copiati nel livello superiore del`.app` bundle

Ad esempio supponga originale `.csproj` file contiene la voce seguente:

`<BundleResource Include="Resources\Images\image.png" />`

È possibile modificare questo elemento e aggiungere una `LogicalName` in modo che l'immagine verrà copiata invece nel livello superiore del `.app `bundle:

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

In Visual Studio per Mac la `LogicalName` possono essere impostate anche usando il `Resource ID` campo per l'immagine in **visualizzazione > riquadri > proprietà**. (Vedere anche: [ https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545 ](https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545))

Dopo questa modifica, sarà necessario aggiornare lo storyboard e `.xib` file da utilizzare i nuovi percorsi di primo livello immagine. Visual Studio per Mac verrà automaticamente aggiornato l'elenco di autocompletions per il `Image` proprietà in iOS Designer. In Visual Studio, è necessario modificare manualmente il percorso. IOS Designer, verrà visualizzato ciò come un'immagine manca, ma il progetto viene compilato ed eseguito correttamente.

### <a name="next-steps"></a>Passaggi successivi

Per ulteriore assistenza, contattare il supporto tecnico o se il problema rimane anche dopo che usano le informazioni sopra riportate, vedi [le opzioni di supporto sono disponibili per Xamarin?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, suggerimenti, nonché come archiviare un nuovo bug se necessario. 


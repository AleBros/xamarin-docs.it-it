---
title: Icone personalizzate del documento in Xamarin.iOS
description: Questo articolo illustra l'inclusione e la gestione di un asset immagine in un'app Xamarin.iOS da usare come icona di tipo di documento personalizzato.
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/23/2017
ms.openlocfilehash: ac8ee96d6183f9a62233d217c75b03da15605bd2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73004228"
---
# <a name="custom-document-icons-in-xamarinios"></a>Icone personalizzate del documento in Xamarin.iOS

_Questo articolo illustra l'inclusione e la gestione di un asset immagine in un'app Xamarin.iOS da usare come icona di tipo di documento personalizzato._

Se un'app Xamarin.iOS supporta il caricamento di un tipo di documento specifico, lo sviluppatore può fornire icone che il sistema userà quando rileva il tipo di documento, ad esempio quando un utente mantiene un allegato nell'applicazione di *posta elettronica* , come illustrato di seguito:

 [![](custom-document-types-images/17.png "An example of document type icons")](custom-document-types-images/17.png#lightbox)

Lo sviluppatore può aggiungere informazioni sul tipo di documento per un formato di file che l'app è in grado di aprire includendo le voci del dizionario per la stringa `CFBundleTypeName` e la matrice di `LSItemContentTypes` nel `Info.plist`dell'app. Le icone per il tipo di documento vengono inserite nella matrice `CFBundleTypeIconFiles`. Se non viene fornita un'icona di documento, iOS ne deriverà uno dall'icona dell'app.
Le icone possono essere fornite per diverse dimensioni, ottimizzate per le varie risoluzioni del dispositivo. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Per assegnare questi valori in Visual Studio per Mac, usare la sezione **tipi di documento** nella scheda **avanzate** dell'editor `Info.plist` per aggiungere il tipo di documento e assegnarvi le icone di immagine. Ecco ad esempio una schermata che mostra la registrazione per il supporto PDF:

 [![](custom-document-types-images/18.png "The Document Types section under the Advanced tab on the `Info.plist` editor")](custom-document-types-images/18.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per assegnare questi valori in Visual Studio, usare la sezione **tipi di documento** nella scheda **Avanzate** della `Info.plist`:

 ![](custom-document-types-images/doc01w.png "Open the Document Types section under the Advanced tab")

Fare clic sul pulsante **Aggiungi tipo di documento** e compilare i campi obbligatori:

![](custom-document-types-images/doc02w.png "The Add Document Type form")

-----

Per altre informazioni sui tipi di documento, vedere gli argomenti di [riferimento sugli identificatori di tipo uniforme](https://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html) di Apple e [la programmazione dell'interazione tra documenti per iOS](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html).

## <a name="related-links"></a>Collegamenti correlati

- [Uso delle immagini (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Linee guida per la creazione di icone e immagini personalizzate](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)

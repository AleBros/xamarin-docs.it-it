---
title: Icone personalizzate del documento in Novell. iOS
description: Questo articolo illustra l'inclusione e la gestione di un asset immagine in un'app Novell. iOS da usare come icona di tipo di documento personalizzato.
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/23/2017
ms.openlocfilehash: 1125b0c420d6dbd23c5539a2183d531bc0d6089f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643010"
---
# <a name="custom-document-icons-in-xamarinios"></a>Icone personalizzate del documento in Novell. iOS

_Questo articolo illustra l'inclusione e la gestione di un asset immagine in un'app Novell. iOS da usare come icona di tipo di documento personalizzato._

Se un'app Novell. iOS supporta il caricamento di un tipo di documento specifico, lo sviluppatore può fornire icone che il sistema userà quando rileva il tipo di documento, ad esempio quando un utente mantiene un allegato nell'applicazione di *posta elettronica* , come illustrato di seguito:

 [![](custom-document-types-images/17.png "Esempio di icone del tipo di documento")](custom-document-types-images/17.png#lightbox)

Lo sviluppatore può aggiungere informazioni sul tipo di documento per un formato di file che l'app è in grado di aprire includendo le `LSItemContentTypes` voci del dizionario per la `Info.plist`stringa e la `CFBundleTypeName` matrice nell'app. Le icone per il tipo di documento vengono inserite `CFBundleTypeIconFiles` nella matrice. Se non viene fornita un'icona di documento, iOS ne deriverà uno dall'icona dell'app.
Le icone possono essere fornite per diverse dimensioni, ottimizzate per le varie risoluzioni del dispositivo. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Per assegnare questi valori in Visual Studio per Mac, usare la sezione **tipi di documento** nella `Info.plist` scheda avanzate dell'editor per aggiungere il tipo di documento e assegnarvi le icone di immagine. Ecco ad esempio una schermata che mostra la registrazione per il supporto PDF:

 [![](custom-document-types-images/18.png "La sezione relativa ai tipi di documento nella scheda avanzate dell'Editor ' info. plist '")](custom-document-types-images/18.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per assegnare questi valori in Visual Studio, usare la sezione **tipi di documento** nella `Info.plist`scheda Avanzate della:

 ![](custom-document-types-images/doc01w.png "Aprire la sezione tipi di documento nella scheda Avanzate")

Fare clic sul pulsante **Aggiungi tipo di documento** e compilare i campi obbligatori:

![](custom-document-types-images/doc02w.png "Modulo Aggiungi tipo di documento")

-----


Per altre informazioni sui tipi di documento, vedere gli argomenti di riferimento sugli identificatori di [tipo uniforme](https://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html) di Apple e [la programmazione dell'interazione tra documenti per iOS](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html).


## <a name="related-links"></a>Collegamenti correlati

- [Uso delle immagini (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Linee guida per la creazione di icone e immagini personalizzate](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)

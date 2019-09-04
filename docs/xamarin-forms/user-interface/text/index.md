---
title: Testo in xamarin. Forms
description: Xamarin. Forms ha tre visualizzazioni principali per l'utilizzo di testo e questo articolo illustra come usarle per immettere e visualizzare il testo nelle applicazioni xamarin. Forms.
ms.prod: xamarin
ms.assetid: 4DBA7689-E5C8-4583-8FB4-02AB208B4416
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2018
ms.openlocfilehash: 7b7cd9445ec9f28211037608ab04ffc45bec705c
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227968"
---
# <a name="text-in-xamarinforms"></a>Testo in xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Con xamarin. Forms è possibile immettere o visualizzare il testo._

Xamarin. Forms ha tre visualizzazioni principali per l'utilizzo di testo:

- **[Etichetta](#Label)**  &mdash; per la presentazione di testo su una o più righe. È possibile visualizzare il testo con più opzioni di formattazione nella stessa riga.
- **[Immissione](#Entry)**  &mdash; per l'immissione di testo che occupa una sola riga. Immissione include una modalità password.
- **[Editor](#Editor)**  &mdash; per l'immissione di testo che potrebbe richiedere più di una riga.

L'aspetto del testo può essere modificato usando [stili](#Styles) predefiniti o personalizzati; alcuni controlli supportano [tipi di carattere](#Fonts) personalizzati.

<a name="Label" />

## <a name="labellabelmd"></a>[Etichetta](label.md)

La vista `Label` viene utilizzata per visualizzare il testo. È possibile visualizzare più righe di testo o una singola riga di testo. `Label` può presentare testo con più opzioni di formattazione usate inline. La visualizzazione etichetta può eseguire il wrapping o troncare il testo quando non è sufficiente un'unica riga.

![Esempio di etichetta](images/label.png)

Vedere l'articolo [Etichetta](label.md) per informazioni più dettagliate.

Per informazioni sulla personalizzazione del carattere usato in un'etichetta, vedere [tipi di carattere](fonts.md).

<a name="Entry" />

## <a name="entryentrymd"></a>[Immissione](entry.md)

`Entry` consente di accettare l'input di testo a riga singola. `Entry` offre il controllo sui tipi di carattere e sui colori. `Entry` include una modalità password e può visualizzare il testo segnaposto durante l'immissione del testo.

![Esempio di voce](images/entry.png)

Vedere l'articolo [Immissione](entry.md) per altre informazioni.

Si noti che, a differenza di `Label`, `Entry` non può avere impostazioni personalizzate del tipo di carattere.

<a name="Editor" />

## <a name="editoreditormd"></a>[Editor](editor.md)

`Editor` consente di accettare input di testo su più righe. `Editor` offre il controllo sui tipi di carattere e colori.

![Esempio di editor](images/editor.png)

Vedere l'articolo [Editor](editor.md) per altre informazioni.

<a name="Fonts" />

## <a name="fontsfontsmd"></a>[Tipi di carattere](fonts.md)

Molti controlli supportano impostazioni del tipo di carattere diverse con i tipi di carattere incorporati in ogni piattaforma o tipi di carattere personalizzati inclusi con l'app. Vedere l'articolo [Tipi di carattere](fonts.md) per informazioni più dettagliate.

<a name="Styles" />

## <a name="stylesstylesmd"></a>[Stili](styles.md)

Fare riferimento alle [operazioni con gli stili](~/xamarin-forms/user-interface/styles/index.md) per informazioni su come impostare il tipo di carattere, [colore](~/xamarin-forms/user-interface/colors.md) e altre proprietà di visualizzazione applicabili a più controlli.

## <a name="related-links"></a>Collegamenti correlati

- [Testo (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

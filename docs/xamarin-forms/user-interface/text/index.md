---
title: Testo in xamarin. Forms
description: Xamarin. Forms ha tre visualizzazioni principali per l'utilizzo di testo e questo articolo illustra come usarli per immettere e visualizzare il testo nelle applicazioni xamarin. Forms.
ms.prod: xamarin
ms.assetid: 4DBA7689-E5C8-4583-8FB4-02AB208B4416
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2018
---

# <a name="text-in-xamarinforms"></a>Testo in xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)

_Con xamarin. Forms consente di immettere o visualizzare il testo._

Xamarin. Forms ha tre visualizzazioni principali per l'utilizzo di testo:

- **[Etichetta](#Label)**  &mdash; per la presentazione di testo singolo o multilinea. Può visualizzare il testo con più opzioni di formattazione nella stessa riga.
- **[Voce](#Entry)**  &mdash; per l'immissione di testo che rappresenta solo una riga. Voce include una modalità di password.
- **[Editor](#Editor)**  &mdash; per l'immissione di testo che potrebbe richiedere più di una riga.

Aspetto del testo può essere modificata utilizzando predefiniti o personalizzati [stili](#Styles) e alcuni controlli supportano personalizzati [tipi di carattere](#Fonts).

<a name="Label" />

## <a name="labellabelmd"></a>[Etichetta](label.md)

Il `Label` vista viene utilizzata per visualizzare il testo. È possibile visualizzare più righe di testo o una singola riga di testo. `Label` può presentare testo con più opzioni di formattazione usate in linea. La visualizzazione etichetta può eseguire il wrapping o troncare il testo quando non è sufficiente in un'unica riga.

![](images/label.png "Esempio di etichetta")

Vedere le [etichetta](label.md) articolo per informazioni più dettagliate.

Per informazioni sulla personalizzazione di carattere utilizzato in un'etichetta, vedere [tipi di carattere](fonts.md).

<a name="Entry" />

## <a name="entryentrymd"></a>[Entry](entry.md)

`Entry` Consente di accettare l'input di testo a riga singola. `Entry` offerte di controllo sui tipi di carattere e colori. `Entry` include una modalità di password e può visualizzare il testo segnaposto fino a quando non si immette il testo.

![](images/entry.png "Esempio di voce")

Vedere le [voce](entry.md) per altre informazioni.

Si noti che, a differenza `Label`, `Entry` non possono avere impostazioni personalizzate del tipo di carattere.

<a name="Editor" />

## <a name="editoreditormd"></a>[Editor](editor.md)

`Editor` Consente di accettare input di testo su più righe. `Editor` offerte di controllo sui tipi di carattere e colori.

![](images/editor.png "Esempio di editor")

Vedere le [Editor](editor.md) per altre informazioni.

<a name="Fonts" />

## <a name="fontsfontsmd"></a>[Tipi di carattere](fonts.md)

Molti controlli supportano le impostazioni del tipo di carattere diversi con i tipi di carattere incorporati in ogni piattaforma o tipi di carattere personalizzati inclusi con l'app. Vedere le [tipi di carattere](fonts.md) articolo per informazioni più dettagliate.

<a name="Styles" />

## <a name="stylesstylesmd"></a>[Stili](styles.md)

Fare riferimento a [funziona con gli stili](~/xamarin-forms/user-interface/styles/index.md) per informazioni su come impostare il tipo di carattere [colore](~/xamarin-forms/user-interface/colors.md)e altre proprietà di visualizzazione applicabili a più controlli.

## <a name="related-links"></a>Collegamenti correlati

- [Testo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)

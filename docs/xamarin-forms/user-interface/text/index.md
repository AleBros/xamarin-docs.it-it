---
title: "testo in Xamarin.Forms " Descrizione: " Xamarin.Forms dispone di tre visualizzazioni principali per l'utilizzo del testo e in questo articolo viene illustrato come utilizzarle per inserire e visualizzare il testo nelle Xamarin.Forms applicazioni".
ms. prod: Novell MS. AssetID: 4DBA7689-E5C8-4583-8FB4-02AB208B4416 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/26/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="text-in-xamarinforms"></a>Testo inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Utilizzo Xamarin.Forms di per immettere o visualizzare il testo._

Xamarin.Formsdispone di tre visualizzazioni principali per l'utilizzo del testo:

- **[Etichetta](#label)** &mdash; di per la presentazione di testo a riga singola o a più righe. Consente di visualizzare il testo con più opzioni di formattazione nella stessa riga.
- **[Voce](#entry)** &mdash; di per l'immissione di testo che corrisponde a una sola riga. La voce ha una modalità password.
- **[Editor](#editor)** &mdash; di per l'immissione di testo che potrebbe richiedere più di una riga.

L'aspetto del testo può essere modificato usando [stili](#styles) predefiniti o personalizzati e alcuni controlli supportano i [tipi di carattere](#fonts)personalizzati.

## <a name="label"></a>[Label](label.md)

La visualizzazione `Label` viene utilizzata per visualizzare il testo. Può visualizzare più righe di testo o una singola riga di testo. `Label`può presentare testo con più opzioni di formattazione usate in inline. La visualizzazione etichetta può eseguire il wrapping o troncare il testo quando non può essere inserito in una sola riga.

![Esempio di etichetta](images/label.png)

Per informazioni più dettagliate, vedere l'articolo relativo alle [etichette](label.md) .

Per informazioni sulla personalizzazione del tipo di carattere usato in un'etichetta, vedere [tipi](fonts.md)di carattere.

## <a name="entry"></a>[Voce](entry.md)

`Entry`viene usato per accettare l'input di testo a riga singola. `Entry`offre il controllo dei colori e dei tipi di carattere. `Entry`ha una modalità password e può visualizzare il testo segnaposto fino a quando non viene immesso il testo.

![Esempio di voce](images/entry.png)

Per ulteriori informazioni, vedere l'articolo relativo alla [voce](entry.md) .

Si noti che, a differenza `Label` di, `Entry` non possono avere impostazioni del tipo di carattere personalizzate.

## <a name="editor"></a>[Editor](editor.md)

`Editor`viene usato per accettare l'input di testo su più righe. `Editor`offre il controllo dei colori e dei tipi di carattere.

![Esempio di editor](images/editor.png)

Per ulteriori informazioni, vedere l'articolo dell' [Editor](editor.md) .

## <a name="fonts"></a>[Fonts](fonts.md)

Molti controlli supportano diverse impostazioni del tipo di carattere usando i tipi di carattere incorporati in ogni piattaforma o i tipi di carattere personalizzati inclusi nell'app. Per informazioni più dettagliate, vedere l'articolo sui [tipi di carattere](fonts.md) .

## <a name="styles"></a>[Stili](styles.md)

Per informazioni su come impostare il tipo di carattere, il [colore](~/xamarin-forms/user-interface/colors.md)e altre proprietà di visualizzazione che si applicano a più controlli, vedere [utilizzo degli stili](~/xamarin-forms/user-interface/styles/index.md) .

## <a name="related-links"></a>Collegamenti correlati

- [Testo (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

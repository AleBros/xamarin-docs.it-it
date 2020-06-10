---
title: "prefissi dello spazio dei nomi XAML consigliati in Xamarin.Forms " Description: "la classe XmlnsPrefixAttribute può essere usata dagli autori del controllo per specificare un prefisso consigliato da associare a uno spazio dei nomi XAML, per l'utilizzo di XAML."
ms. prod: Novell MS. AssetID: 7B315BEC-7A35-48F4-A9C7-EF40255E95FF ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 02/28/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xaml-namespace-recommended-prefixes-in-xamarinforms"></a>Prefissi di spazio dei nomi XAML consigliati inXamarin.Forms

La `XmlnsPrefixAttribute` classe può essere utilizzata dagli autori del controllo per specificare un prefisso consigliato da associare a uno spazio dei nomi XAML, per l'utilizzo di XAML. Il prefisso è utile quando si supporta la serializzazione dell'albero degli oggetti in XAML o quando si interagisce con un ambiente di progettazione che dispone di funzionalità di modifica XAML. Ad esempio:

- Gli editor di testo XAML possono utilizzare `XmlnsPrefixAttribute` come hint per un mapping iniziale dello spazio dei nomi XAML `xmlns` .
- Gli ambienti di progettazione XAML possono utilizzare `XmlnsPrefixAttribute` per aggiungere mapping a XAML quando si trascinano oggetti da una casella degli strumenti e su un'area di progettazione visiva.

I prefissi degli spazi dei nomi consigliati devono essere definiti a livello di assembly con il `XmlnsPrefixAttribute` costruttore, che accetta due argomenti: una stringa che specifica l'identificatore di uno spazio dei nomi XAML e una stringa che specifica un prefisso consigliato:

```csharp
[assembly: XmlnsPrefix("http://xamarin.com/schemas/2014/forms", "xf")]
```

I prefissi devono usare stringhe brevi, perché il prefisso viene in genere applicato a tutti gli elementi serializzati che provengono dallo spazio dei nomi XAML. La lunghezza della stringa di prefisso può pertanto avere un effetto evidente sulle dimensioni dell'output XAML serializzato.

> [!NOTE]
> `XmlnsPrefixAttribute`È possibile applicare più di una a un assembly. Se, ad esempio, si dispone di un assembly che definisce i tipi per più di uno spazio dei nomi XAML, è possibile definire valori di prefisso diversi per ogni spazio dei nomi XAML.

## <a name="related-links"></a>Collegamenti correlati

- [Schemi spazio dei nomi personalizzati XAML](custom-namespace-schemas.md)
- [Spazi dei nomi XAML inXamarin.Forms](namespaces.md)

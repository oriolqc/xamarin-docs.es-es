---
title: Mediante el marco de lenguaje Natural con Xamarin.iOS
description: Este documento describe el marco de lenguaje Natural. Se introdujo en iOS 12, el marco de lenguaje Natural es la API de iOS preferido que se utilizará para el reconocimiento de idioma, la identificación de partes de la oración y reconocimiento de entidades con nombre.
ms.prod: xamarin
ms.assetid: 126C8764-F873-4EB9-98A3-D82AB5689111
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/20/2018
ms.openlocfilehash: 41f629739b06431a9b20548f61111bc31e911abb
ms.sourcegitcommit: 495680e74c72e7c570e68cde95d3d3643b1fcc8a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/02/2019
ms.locfileid: "58870045"
---
# <a name="using-the-natural-language-framework-with-xamarinios"></a>Mediante el marco de lenguaje Natural con Xamarin.iOS

Se introdujo en iOS 12, el marco de lenguaje Natural permite el procesamiento de lenguaje natural en el dispositivo. Es compatible con el reconocimiento de idioma, la tokenización y etiquetado. Tokenización divide el texto en su componente palabras, frases o párrafos etiquetado identifica las partes de la oración, personas, lugares y organizaciones.

El marco de lenguaje Natural también puede usar modelos de aprendizaje automático de Core personalizados para clasificar y etiquetar texto en contextos especializados.

El [NSLinguisticTagger](xref:Foundation.NSLinguisticTagger) clase sigue estando disponible. Sin embargo, el marco de lenguaje Natural es el mecanismo preferido para el procesamiento de lenguaje Natural.

## <a name="sample-app-xamarinnl"></a>Aplicación de ejemplo: XamarinNL

Para obtener información sobre cómo usar el marco de lenguaje Natural con Xamarin.iOS, eche un vistazo a la [aplicación de ejemplo XamarinNL](https://developer.xamarin.com/samples/monotouch/iOS12/XamarinNL).
Esta aplicación de ejemplo muestra cómo usar el marco de lenguaje Natural:

- [Reconocer idiomas](#recognizing-languages).
- [Dividir texto en palabras y frases](#tokenizing-text-into-words-sentences-and-paragraphs).
- [Etiqueta denominados entidades y partes de la oración](#tagging-named-entities-and-parts-of-speech).

## <a name="recognizing-languages"></a>Reconocimiento de idiomas

El **reconocedor** pestaña de la aplicación de ejemplo muestra cómo usar un [`NLLanguageRecognizer`](xref:NaturalLanguage.NLLanguageRecognizer)
para determinar el idioma de un bloque de texto.

> [!NOTE]
> Reconocimiento de lenguaje es un tipo específico de la clasificación de texto. El marco de lenguaje Natural también admite la clasificación de texto personalizado a través de los modelos de aprendizaje automático de Core proporcionados por desarrolladores. Para obtener más información, eche un vistazo a la [presentación de lenguaje Natural Framework](https://developer.apple.com/videos/play/wwdc2018/713/) sesión desde la sesión WWDC 2018.

### <a name="dominant-language"></a>Idioma principal

Pulse el **lenguaje** botón para identificar el idioma predominante en la entrada del usuario.

El `HandleDetermineLanguageButtonTap` método de la `LanguageRecognizerViewController` usa la [`GetDominantLanguage`](xref:NaturalLanguage.NLLanguageRecognizer.GetDominantLanguage*)
método de un `NLLanguageRecognizer` se debe recuperar el [`NLLanguage`](xref:NaturalLanguage.NLLanguage)
para el idioma principal que se encuentra en el texto:

```csharp
partial void HandleDetermineLanguageButtonTap(UIButton sender)
{
    UserInput.ResignFirstResponder();
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        NLLanguage lang = NLLanguageRecognizer.GetDominantLanguage(UserInput.Text);
        DominantLanguageLabel.Text = lang.ToString();
    }
}
```

### <a name="language-probabilities"></a>Probabilidades de lenguaje

Pulse el **probabilidades de lenguaje** botón para recuperar una lista de las hipótesis de lenguaje para la entrada del usuario.

El `HandleLanguageProbabilitiesButtonTap` método de la `LanguageRecognizerViewController` crea una instancia de la clase un `NLLanguageRecognizer` y le pide que [`Process`](xref:NaturalLanguage.NLLanguageRecognizer.Process*)
texto del usuario. A continuación, llama el reconocedor de lenguaje [`GetNativeLanguageHypotheses`](xref:NaturalLanguage.NLLanguageRecognizer.GetNativeLanguageHypotheses*)
método, que captura un diccionario de idiomas y las probabilidades asociadas. La `LanguageRecognizerTableViewController` clase, a continuación, procesa estos lenguajes y las probabilidades.

```csharp
partial void HandleLanguageProbabilitiesButtonTap(UIButton sender)
{
    UserInput.ResignFirstResponder();
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var recognizer = new NLLanguageRecognizer();
        recognizer.Process(UserInput.Text);
        NSDictionary<NSString, NSNumber> probabilities = recognizer.GetNativeLanguageHypotheses(10);
        PerformSegue(ShowLanguageProbabilitiesSegue, this);
    }
}
```

Posibles `NLLanguage` los valores incluyen:

- `Amharic`
- `Arabic`
- `Armenian`
- `Bengali`
- `Bulgarian`
- `Burmese`
- `Catalan`
- `Cherokee`
- `Croatian`
- `Czech`
- `Danish`
- `Dutch`
- `English`
- `Finnish`
- `French`
- `Georgian`
- `German`
- `Greek`
- `Gujarati`
- `Hebrew`
- `Hindi`
- `Hungarian`
- `Icelandic`
- `Indonesian`
- `Italian`
- `Japanese`
- `Kannada`
- `Khmer`
- `Korean`
- `Lao`
- `Malay`
- `Malayalam`
- `Marathi`
- `Mongolian`
- `Norwegian`
- `Oriya`
- `Persian`
- `Polish`
- `Portuguese`
- `Punjabi`
- `Romanian`
- `Russian`
- `SimplifiedChinese`
- `Sinhalese`
- `Slovak`
- `Spanish`
- `Swedish`
- `Tamil`
- `Telugu`
- `Thai`
- `Tibetan`
- `TraditionalChinese`
- `Turkish`
- `Ukrainian`
- `Undetermined`
- `Urdu`
- `Vietnamese`

Una lista completa de los idiomas admitidos está disponible como parte de la [`NLLanguage`](xref:NaturalLanguage.NLLanguage)
documentación de API de enumeración.

## <a name="tokenizing-text-into-words-sentences-and-paragraphs"></a>Dividir texto en tokens en palabras, frases y párrafos

El **Tokenizer** pestaña de la aplicación de ejemplo muestra cómo separar un bloque de texto en palabras de sus componentes o frases con un [ `NLTokenizer` ](xref:NaturalLanguage.NLTokenizer).

Pulse el **palabras** o **frases** botón para recuperar una lista de tokens. Cada token se asocia con una palabra o frase en el texto original.

`ShowTokens` divide la entrada del usuario en tokens mediante una llamada a la [`GetTokens`](xref:NaturalLanguage.NLTokenizer.GetTokens*)
método de un `NLTokenizer`. Este método devuelve una matriz de [`NSValue`](xref:Foundation.NSValue)
objetos, cada ajuste una `NSRange` valor que corresponde a un token en el texto original.

```csharp
void ShowTokens(NLTokenUnit unit)
{
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var tokenizer = new NLTokenizer(unit);
        tokenizer.String = UserInput.Text;
        var range = new NSRange(0, UserInput.Text.Length);
        NSValue[] tokens = tokenizer.GetTokens(range);
        PerformSegue(ShowTokensSegue, this);
    }
}
```

`LanguageTokenizerTableViewController` Representa un token único en cada celda de tabla. Extrae un `NSRange` desde un símbolo (token) `NSValue`, busca la cadena correspondiente en el texto original y establece una etiqueta en la celda de vista de tabla:

```csharp
public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
    var cell = TableView.DequeueReusableCell(TokenCell);
    NSRange range = Tokens[indexPath.Row].RangeValue;
    cell.TextLabel.Text = Text.Substring((int)range.Location, (int)range.Length);
    return cell;
}
```

## <a name="tagging-named-entities-and-parts-of-speech"></a>Etiquetado de entidades con nombre y partes de la oración

El **etiquetador** pestaña de la aplicación de ejemplo XamarinNL muestra cómo usar el [`NLTagger`](xref:NaturalLanguage.NLTagger)
clase para asociar los tokens de una cadena de entrada en categorías.
El marco de lenguaje Natural incluye compatibilidad integrada para reconocer personas, lugares, las organizaciones y partes de la oración.

> [!NOTE]
> El marco de lenguaje Natural también admite esquemas personalizados de etiquetado a través de los modelos de aprendizaje automático de Core proporcionados por desarrolladores. Para obtener más información, eche un vistazo a la [presentación de lenguaje Natural Framework](https://developer.apple.com/videos/play/wwdc2018/713/) sesión desde la sesión WWDC 2018.

Pulse el **entidades con nombre** o **partes de la oración** botón para capturar:

- Una matriz de `NSValue` objetos, cada ajuste un `NSRange` para un token en el texto original.
- Una matriz de [ `NLTag` ](xref:NaturalLanguage.NLTag) valores – categorías para el `NSValue` tokens en el mismo índice de matriz.

En `LanguageTaggerViewController`, `HandlePartsOfSpeechButtonTap` y `HandleNamedEntitiesButtonTap` cada llamada `ShowTags`, pasando a lo largo de un [ `NLTagScheme` ](xref:NaturalLanguage.NLTagScheme) : cualquier `NLTagScheme.LexicalClass` (para las partes de la oración) o `NLTagScheme.NameType` (para entidades con nombre).

`ShowTags` crea un `NLTagger`, cree una instancia con una matriz de `NLTagScheme` tipos para que se va a consultar (en este caso, solo pasa del `NLTagScheme` valor). A continuación, usa la [`GetTags`](xref:NaturalLanguage.NLTagger.GetTags*)
método en el `NLTagger` para determinar las etiquetas pertinentes para el texto de la entrada del usuario.

```csharp
void ShowTags(NLTagScheme tagScheme)
{
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var tagger = new NLTagger(new NLTagScheme[] { tagScheme });
        var range = new NSRange(0, UserInput.Text.Length);
        tagger.String = UserInput.Text;

        NLTag[] tags = tagger.GetTags(range, NLTokenUnit.Word, tagScheme, NLTaggerOptions.OmitWhitespace, out NSValue[] ranges);
        NSValue[] tokenRanges = ranges;
        detailViewTitle = tagScheme == NLTagScheme.NameType ? "Named Entities" : "Parts of Speech";

        PerformSegue(ShowEntitiesSegue, this);
    }
}
```

Las etiquetas se muestran a continuación, en una tabla por la `LanguageTaggerTableViewController`.

Posibles `NLTag` los valores incluyen:

- `Adjective`
- `Adverb`
- `Classifier`
- `CloseParenthesis`
- `CloseQuote`
- `Conjunction`
- `Dash`
- `Determiner`
- `Idiom`
- `Interjection`
- `Noun`
- `Number`
- `OpenParenthesis`
- `OpenQuote`
- `OrganizationName`
- `Other`
- `OtherPunctuation`
- `OtherWhitespace`
- `OtherWord`
- `ParagraphBreak`
- `Particle`
- `PersonalName`
- `PlaceName`
- `Preposition`
- `Pronoun`
- `Punctuation`
- `SentenceTerminator`
- `Verb`
- `Whitespace`
- `Word`
- `WordJoiner`

Una lista completa de etiquetas compatibles está disponible como parte de la [`NLTag`](xref:NaturalLanguage.NLTag)
documentación de API de enumeración.

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo: XamarinNL](https://developer.xamarin.com/samples/monotouch/iOS12/XamarinNL)
- [Introducción a la plataforma del lenguaje Natural](https://developer.apple.com/videos/play/wwdc2018/713/)
- [Lenguaje natural (Apple)](https://developer.apple.com/documentation/naturallanguage?language=objc)

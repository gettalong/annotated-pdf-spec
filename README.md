# Annotated PDF Specification

This repository contains information that is not available in the [PDF 1.7 specification][spec] but useful when creating a PDF library. I came across these irregularities one by one while implementing [HexaPDF][hp] and I hope to be able to help others by collecting them here.

Version 1.7 of the PDF specification has been chosen because it is freely available even though it is an ISO standard. Later versions like the PDF 2.0 specification can be bought from ISO and local standard institutes. Since those are not freely available it is harder to start a PDF project.

Most of things stated here are needed because many PDF writers don't create standard conform PDF files. And this led to PDF readers (e.g. Adobe Reader) being lenient in what they accept. So as a rule of thumb: *Be strict in what you write, lenient in what you accept*.

*Note*: If some information starts with 'Maybe', it might be worth doing it.


[spec]: http://wwwimages.adobe.com/content/dam/Adobe/en/devnet/pdf/pdfs/PDF32000_2008.pdf
[hp]: https://hexapdf.gettalong.org

# Annotations

## 7.3.8 Stream Objects

* Handle CR being used instead of LF or LF/CR after the `stream` keyword:

  ~~~
  1 0 obj
  <</Length 5>>
  stream<CR>
  Value
  endstream
  endobj
  ~~~

* Handle a single space being used instead of LF or LF/CR after the `stream` keyword:

  ~~~
  1 0 obj
  <</Length 5>>
  stream Value
  endstream
  endobj
  ~~~

* Write the `stream` and `endstream` keywords on separate lines to allow for cross-reference reconstruction:

  ~~~
  1 0 obj
  <</Length 5>>
  stream
  Value
  endstream
  endobj
  ~~~

* Maybe recover from an invalid `/Length` entry by scanning for `endstream` line by line:

  ~~~
  1 0 obj
  <</Length 100>>
  stream
  Value
  endstream
  endobj
  ~~~

## 7.3.10 Indirect Objects

* Handle no value between `obj` and `endobj`:

  ~~~
  1 0 obj
  endobj
  ~~~

* Handle missing `endobj`:

  ~~~
  1 0 obj
  /Value

  2 0 obj
  /Value
  endobj
  ~~~

* Handle an integer glued to `endobj`, i.e. without whitespace:

  ~~~
  1 0 obj
  56endobj
  ~~~

* Write indirect object like this, i.e. object identifier and `obj` on one line, then the value, then `endobj` on separate line, to allow for cross-reference table reconstruction:

  ~~~
  1 0 obj
  /Value
  endobj
  ~~~

* Maybe treat invalid values as `null`:

  ~~~
  1 0 obj
  (asdfasdf
  endobj
  ~~~

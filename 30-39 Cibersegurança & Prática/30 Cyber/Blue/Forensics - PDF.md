**Doc :** https://web.archive.org/web/20220113130243/https://www.adobe.com/content/dam/acom/en/devnet/pdf/pdfs/PDF32000_2008.pdf

#### PDF Structure
Podemos ver a estrutura de um PDF com a ferra menta `pdfid`. Assim podemos ver quantos objetos há no pdf, e se há código `JavaScript`.
```sh
$ pdfid ./malware.pdf
PDFiD 0.2.10 fcexploit.pdf
 PDF Header: %PDF-1.3
 obj                   19 # Quantidade de objetos
 endobj                18
 stream                 5
 endstream              5
 xref                   1
 trailer                1
 startxref              1
 /Page                  2
 /Encrypt               0
 /ObjStm                0
 /JS                    1
 /JavaScript            1 # Código JavaScript
```

#### Filtering Schemes
Os filtros são aplicados para dificultar a análise do artefato.
The common filtering schemes include:
- `FlateDecode`: A widely-used compression method based on the zlib/deflate algorithm. It’s commonly applied to text and image streams.
- `ASCII85Decode`: Encodes binary data into ASCII characters, making it suitable for embedding binary streams in text-based formats.
- `LZWDecode`: An older compression algorithm based on the Lempel-Ziv-Welch technique, used to reduce file size.
- `RunLengthDecode`: Compresses data using run-length encoding, which is effective for repetitive data sequences.

```sh
$ pdf-parser malware.pdf --search /Filter | grep Filter
    /Filter [ /FlateDecode /ASCII85Decode /LZWDecode /RunLengthDecode ]
    /Filter [ /FlateDecode /ASCII85Decode /LZWDecode /RunLengthDecode ]
    /Filter [ /FlateDecode /ASCII85Decode /LZWDecode /RunLengthDecode ]
    /Filter [ /FlateDecode /ASCII85Decode /LZWDecode /RunLengthDecode ]
```
The output indicates that each stream in the `fcexploit.pdf` file uses a combination of four filtering schemes: `FlateDecode`, `ASCII85Decode`, `LZWDecode`, and `RunLengthDecode`. These filters are applied in sequence, making it more complex to analyze the content directly.

#### JS code embed into Object Streams
The `/JavaScript` and `/Action` tags in a PDF indicate potential locations where scripts are embedded. The `/JavaScript` and `/Action` tags in a PDF indicate potential locations where scripts are embedded.

```sh
$ pdf-parser malware.pdf -f # -f faz o decode dos objetos de fluxo
obj 4 0
 Type: /Action
 Referencing: 5 0 R

  <<
    /Type /Action
    /S /JavaScript
    /JS 5 0 R
  >>
```

In this case, the analysis reveals that object 4 references JavaScript through the `/Type /Action`, `/S /JavaScript`, and `/JS 5 0 R` entries. The `5 0 R` indicates that object **5** contains the actual JavaScript code, referenced by object 4.
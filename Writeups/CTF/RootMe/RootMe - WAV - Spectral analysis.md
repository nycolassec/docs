#Category : Steganography #Difficult : Medium
***
Recebemos um arquivo de áudio `ch7.wav`, como ele é um arquivo de áudio podemos analisar as frequências para ver se nos revela algo. Utilizei o `sox` para isso.
```sh
sox ch7.wav -n spectrogram -x 256 -y 720
open spectrogram.png
```
![[Pasted image 20250526184452.png]]

**Answer :** `secret-password`
***
![[Pasted image 20250526184822.png]]
## Don't Touch My Fone
Looks like someone's dialing a phone number, see if you can figure out what it is! The flag format is the decoded phone number wrapped in DawgCTF{} with no formatting, so if the number is 123-456-7890, then the flag is DawgCTF{1234567890}.

O áudio se parece com  o som daqueles celulares antigos após discar algum número, podemos então submetê-lo a um `DTMF decoder` como o https://unframework.github.io/dtmf-detect/

![[Pasted image 20250422082905.png]]
Após o decode temos a resposta.

**Answer : `4104553500`**
***
## Mystery Signal I
I was listening to my scanner when I heard a strange signal. Can you decode it?

Após ouvir o áudio, identifiquei que se tratava de um `Código Morse`, então apliquei um `decoder`. Em https://morsecode.world/international/decoder/audio-decoder-adaptive.html

![[Pasted image 20250422084057.png]]
Após a tradução, temos a resposta.

**Answer : `L I S T E N C A R E F U L L Y`**
#Difficult : Easy
#Category : Endpoint Forensics
***
#### 1 - Based on the accounts of the witnesses and individuals close to the victim, it has become clear that the victim was interested in trading. This has led him to invest all of his money and acquire debt. Can you identify the `SHA256` of the trading application the victim primarily used on his phone?
Primeiro usei o `AlleApp` para fazer a análise dos logs e extrair em um diretório `out`. Após isso podemos ver o relatório do `AlleApp`
```bash
python3.12 ./aleapp.py -t fs -i /home/remnux/Desktop/temp_extract_dir/data/ -o /home/remnux/Desktop/temp_extract_dir/out/
```
![[Pasted image 20250416110826.png]]

Em `Installed Apps (GMS) for user 0`, temos os aplicativos que foram instalados. o Único que se refere a um aplicativo de `trading` é o `olymptrade`.
![[Pasted image 20250416111558.png]]

**Answer : `4f168a772350f283a1c49e78c1548d7c2c6c05106d8b9feb825fdc3466e9df3c`**
***
#### 2 - According to the testimony of the victim's best friend, he said, "`While we were together, my friend got several calls he avoided. He said he owed the caller a lot of money but couldn't repay now`". How much does the victim owe this person?
Em `SMS messages` temos uma mensagem de cobrança bem ameaçadora.
![[Pasted image 20250416112250.png]]

**Answer : `250000`**
***
#### 3 - What is the name of the person to whom the victim owes money?
Podemos fazer uma correlação do número da mensagem enviada via `SMS` em `SMS messages` e o números dos contatos em `Contacts`.
![[Pasted image 20250416112545.png]]

**Answer `Shady Wahab`**
***
#### 4 - Based on the statement from the victim's family, they said that on `September 20, 2023`, he departed from his residence without informing anyone of his destination. Where was the victim located at that moment?
Em `Recent Activity_0` temos um instantâneo do `google maps` na mesma data do ocorrido, nessa imagem conseguimos ver sua localização.
![[Pasted image 20250416113129.png]]

**Answer : `The Nile Ritz-Carlton`**
***
#### 5 - The detective continued his investigation by questioning the hotel lobby. She informed him that the victim had reserved the room for 10 days and had a flight scheduled thereafter. The investigator believes that the victim may have stored his ticket information on his phone. Look for where the victim intended to travel.
Em `Emulated Storage Metadata - Files` temos algumas pastas do sistema, não achei o conteúdo da pasta `Download` no `AlleApp` então decidi procurar no sistema de arquivos.
![[Pasted image 20250416132110.png]]

```bash
find ./name -name *Download*
```
![[Pasted image 20250416132204.png]]

A primeira pasta é a pasta listada no `AlleApp`, então podemos olhar dentro dela. Dentro dela, temos a imagem do `ticket` que foi citado.
```bash
cd ./media/0/Download
ls
```
![[Pasted image 20250416132456.png]]
![[Pasted image 20250416132534.png]]

**Answer  :`las vegas`**
***
#### 6 - After examining the victim's Discord conversations, we discovered he had arranged to meet a friend at a specific location. Can you determine where this meeting was supposed to occur?
Podemos obter a resposta olhando em `Discord Chats`.
![[Pasted image 20250416132928.png]]

**Answer : `The Mob Museum`**
***

![[Pasted image 20250416133142.png]]
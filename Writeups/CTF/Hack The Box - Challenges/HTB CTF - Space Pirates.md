#Difficult : Easy #Category : Crypto
***
#### Info
Recebemos um script em python `chall.py`.
![[Pasted image 20250513171651.png]]

E um arquivo de texto `msg.enc`.
![[Pasted image 20250513171737.png]]

O arquivo parece implementar o [Shamir’s Secret Sharing](https://medium.com/@keylesstech/a-beginners-guide-to-shamir-s-secret-sharing-e864efbf3648) para criptografar uma flag. Indico fortemente que estudem sobre esse método criptográfico para melhor entendimento.
***
#### Code analysis
Aqui temos um erro crucial, os coeficientes são calculados de acordo com hash MD5 do coeficiente anterior a partir da `secret`, como nos foi provido o coeficiente depois da secret, conseguimos achar todos os outros coeficientes.
###### Coeficientes
Esse código é usado para calcular os coeficientes, podemos reproduzi-lo
![[Pasted image 20250513173140.png]]

```python
from hashlib import md5
coeffs = [93526756371754197321930622219489764824]

def next_coeff(val):
    return int(md5(val.to_bytes(32, "big")).hexdigest(), 16)
for i in range(8):
    coeffs.append(next_coeff(coeffs[i]))

print(coeffs)
```
![[Pasted image 20250513173920.png]]
Assim conseguimos os 9 coeficientes.
###### Secret
Agora devemos reconstruir o polinômio. O polinômio completo tem a forma de *y = secret + c<sub>1</sub>x<sup>1</sup> + c<sub>2</sub>x<sup>2</sup> + ... + c<sub>9</sub>x<sup>9</sup> (mod p)*, sendo assim podemos isolar a `secret` e formar uma outra equação, *secret = ( c<sub>1</sub>x<sup>1</sup> - c<sub>2</sub>x<sup>2</sup> - ... - c<sub>9</sub>x<sup>9</sup> )mod p*

Esse é o código usado para fazer a criptografia da `secret message`.
![[Pasted image 20250513185139.png]]

E essa é a minha implementação.
```python
from hashlib import md5

coeffs = [93526756371754197321930622219489764824]
x,y = 21202245407317581090, 11086299714260406068
p = 92434467187580489687

def next_coeff(val):
    return int(md5(val.to_bytes(32, "big")).hexdigest(), 16)

for i in range(8):
    coeffs.append(next_coeff(coeffs[i]))

def recover_secret():
    sum_terms = 0
    for i in range(9):
        sum_terms += coeffs[i] * pow(x, i+1, p)
        sum_terms %= p

    secret = (y - sum_terms) % p
    return secret

print(recover_secret())
```
![[Pasted image 20250513185920.png]]
###### flag
Agora devemos implementar a função de descriptografia da flag. Esse é o código que faz a encriptação.
![[Pasted image 20250513192323.png]]

mas em vez de `cipher.encrypt` usaremos `cipher.decrypt`.
```python
from Crypto.Cipher import AES
from random import randbytes, seed
from hashlib import md5

coeffs = [93526756371754197321930622219489764824]
x,y = 21202245407317581090, 11086299714260406068
p = 92434467187580489687
ct = bytes.fromhex('1aaad05f3f187bcbb3fb5c9e233ea339082062fc10a59604d96bcc38d0af92cd842ad7301b5b72bd5378265dae0bc1c1e9f09a90c97b35cfadbcfe259021ce495e9b91d29f563ae7d49b66296f15e7999c9e547fac6f1a2ee682579143da511475ea791d24b5df6affb33147d57718eaa5b1b578230d97f395c458fc2c9c36525db1ba7b1097ad8f5df079994b383b32695ed9a372ea9a0eb1c6c18b3d3d43bd2db598667ef4f80845424d6c75abc88b59ef7c119d505cd696ed01c65f374a0df3f331d7347052faab63f76f587400b6a6f8b718df1db9cebe46a4ec6529bc226627d39baca7716a4c11be6f884c371b08d87c9e432af58c030382b737b9bb63045268a18455b9f1c4011a984a818a5427231320ee7eca39bdfe175333341b7c')

def next_coeff(val):
    return int(md5(val.to_bytes(32, "big")).hexdigest(), 16)

for i in range(8):
    coeffs.append(next_coeff(coeffs[i]))

def recover_secret():
    sum_terms = 0
    for i in range(9):
        sum_terms += coeffs[i] * pow(x, i+1, p)
        sum_terms %= p

    secret = (y - sum_terms) % p
    return secret

secret = recover_secret()

def decrypt_flag():
    seed(secret)
    key = randbytes(16)
    cipher = AES.new(key, AES.MODE_ECB)
    msg = cipher.decrypt(ct)
    return msg

print((decrypt_flag()).decode())
```
![[Pasted image 20250513192624.png]]
***
![[Pasted image 20250513192721.png]]






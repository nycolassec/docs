Vamos configurar o `Performance Monitor` para coletar as informações de : Tempo de processamento, Memória disponível e Disco; a cada 60 segundos por 5 minutos e salve em `C:\performance`.

Em `Server Manager`, podemos ir em `Tools` depois em `Performance Monitor`.
![[Pasted image 20250429084105.png]]

Agora em `Data Collector Sets > User Defined > New > Data Collector Set` criamos um novo `Data Collector` para definir o que será coletado.
![[Pasted image 20250429084402.png]]

Podemos nomear como `Performeance Monitor`, e marcar `Create manually (Advanced)` e então confirmar. 
![[Pasted image 20250429093847.png]]

Agora selecionamos `Create data logs > Performance Counter` e confirmamos.
![[Pasted image 20250429093948.png]]

Agora definimos o `Sample Interval` para `60 seconds`, pois queremos monitorara a cada 60 segundos, e então`Add...` e escolhemos o que queremos monitorar.
![[Pasted image 20250429094159.png]]

Até que é bem intuitivo, vamos adicionar : `Processor > % Processor Time > _Total`, `Memory > Available MBytes`, `PhysicalDisk > % Disk Time > 0 C:`, `PhysicalDisk > Disk Read Bytes/sec > 0 C:` e `PhysicalDisk > Disk Write Bytes/sec > 0 C:`  então selecionamos `OK` e `Next`
![[Pasted image 20250429094503.png]]

Após isso devemos escolher onde salvar os dados.
![[Pasted image 20250429091824.png]]

Podemos então definir o usuário que será usado para coletar as informações.
![[Pasted image 20250429091902.png]]

Agora podemos ir nas propriedades do `Data Collector`
![[Pasted image 20250429091955.png]]

Queremos um `Sample` a cada 60 segundos, como ele irá durar 5min(300sec), podemos definir para 5 `Samples`. Então aplicar e confirmar com o `OK`.
![[Pasted image 20250429092713.png]]

Para definir um tempo máximo para todos os `Data Collectors` de um `Data Collector Set`, selecionamos o `Data Collector Set  > Properties`, 
![[Pasted image 20250429093431.png]]
Agora em `Stop Condition` definimos o `Overall duration` para o tempo que queremos.
![[Pasted image 20250429093613.png]]

Podemos então iniciar a coleta.
![[Pasted image 20250429092417.png]]

E os arquivos de cada `Data Collector` estarão na pasta de destino.
![[Pasted image 20250429093717.png]]















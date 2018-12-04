
# Alerta Queda

  
  
  
  

## Problema

  

75% dos machucados ocorridos com pessoas com mais de 60 anos ocorrem na parte interna da residência, sendo na maioria no banheiro.

  

## Solução

  

Uma aplicação que se conecta com sensores de distância, onde os mesmos mandam para uma página na web, um alerta de acidente, quando uma queda de um indivíduo ocorrer no ambiente.

## Instruções para simular

  

1. Importar o arquivo “AlertaQuerdaNodeRed” no node red
2. Posicionar os sensores de acordo com a imagem “posicionamentoSensores” da pasta docs
3. Executar o broker localmente 
4. Dar o deploy no node red
5. Acessar a url  <Ip Raspberry>/simple
6. Passar pelo primeiro sensor e ficar posicionado na frente do segundo, após uns 5 segundos, faça a simulação de uma queda =0. 


  
  
    
  
## Funcionamento

No primeiro flow (imagem na pasta docs) temos a parte de publisher Mqtt do projeto. Onde temos sensores de distância SFR que faz publish continuamente dos dados. O node-red default não possui a library instalada para executar a comunicação com o dispositivo, então é necessário a adição da mesma. Após o funcionamento dos sensores é adicionado ao payload deles um payload de timestamp criando assim um payload principal com tempo e distância. Todo esse processo é feito através da função Merge. Quando começaram os testes descobrimos que quando utilizamos os sensores apareciam valores que não correspondiam com o comportamento atual do dispositivo. Sendo assim tivemos a ideia de pegar esses payloads principais e juntá-los em um array de tamanho 5 e nesse array seria depois utilizado a moda para terminar esses outliers. Seguindo o fluxo, após o array receber os 5 eventos necessário ele será enviado ao broker.
				
O segundo flow demonstra o recebimento dos dados capturados pelos sensores através do subscribe mqtt, em seguida esses dados são enviados para o websocket que por sua vez direciona para a web.

No app web temos a lógica de detecção, há dois websockets vindos do node-red que retornam um array com as distâncias em um intervalo de tempo específico até a parede, o sensor da porta chamado de “checkin” e o sensor que varre a sala na altura da cintura chamado de “cintura”, como um sensor tem uma taxa de erro tiramos a moda das   distâncias no array e temos naquele intervalo de tempo a moda das distâncias do sensor, toda a vez que recebemos um intervalo de novo guardamos a moda antiga, a detecção acontece quando temos uma variação da moda da última captura para a nova sendo a variação maior que o valor esperado, arbitrariamente escolhemos que a distância do objeto até o sensor não pode ser menor de 170 cm e se isso acontece então provavelmente alguém passou pelo sensor, caso a pessoa passe pelo sensor da porta temos uma ocorrência de entrada, neste caso será esperado que a pessoa interrompa o sensor que verifica se a sala estar vazia, neste caso se ele não o fizer e o sensor estiver marcado que alguém entrou no recinto, um aviso será emitido avisando que alguém daquela sala caiu.



## Autores

Arthur Justino;
David Francisco;
Victor Burgardt.


filters:: {"todo" true}

- [Contruir servidor p/ rastreador GV57CG](https://redmine.geocontrol.com.br/redmine/issues/19465)
- Comandos de pegar pacotes
  collapsed:: true
	- ~~`nc -l -p 8085 | hexdump -x | awk '{for(i=2;i<=NF;i++) printf $i}' | tee -a all_packets_gv57cg.out`~~ deprecated por ser little endiand. problema no hexdump. Use o abaixo em vez disso
	  collapsed:: true
		- nc -> netcat, para pegar pacotes remotos
		- hexdump -> para decifrar hexadecimal para caracteres legíveis
		- awk -> para formatar os pacotes corretamente
		- tee -> para imprimir na tela e em arquivo ao mesmo tempo
	- `nc -l -p 8085 | hexdump -X | cut -c 9- | awk '{for(i=1;i<=NF;i++) printf $i}' | tee -a pacote_teste_GTFRI.out2` imprime pacotes todos juntos
- # Roadmap
  collapsed:: true
	- DONE Model
		- DONE Device packet
			- DONE QueclinkGV57CGLocationMsgPacket
			- DONE QueclinkGV57CGHeartbeatMsgPacket
		- TODO Server packet
		  id:: 669e9ea0-87bd-443f-b04d-2f08cf6ef3e1
			- O dispositivo pode ser configurado para recever um ACK sempre que o servidor receber um pacote dele
			- > 3.5. Server Acknowledgement
				- If server acknowledgement is enabled by the command AT+GTSRI, the backend server should reply to the device whenever it receives a message from the device.
				- ![image.png](../assets/image_1722007057901_0.png)
			- DONE #dúvida vamos usar isso?
				- eventualmente sim, mas por enquanto não precisamos implementar.
			- Pré-requisitos do ACK:
				- Note: Only after both the commands AT+GTBSI and AT+GTSRI are properly set can the ACK messages and other report messages be sent to the backend server.
				- AT+GTBSI - 3.2.1.2. Backend Server Registration Information
					- SMS ACK Enable
						- <SMS ACK Enable>: A numeral to indicate whether to send an acknowledgement message to the original number when the command is sent by SMS.
						- 0: The device will send the acknowledgement message to the backend server according to the mode configured by the <Report Mode>. #this
						- 1: The device will send the acknowledgement message to the original number via SMS if the command is received via SMS.
					- Special SACK Enable
						- <Special SACK Enable>: This parameter defines whether the backend server will respond to the terminal with specified SACK messages when receiving the specified messages (i.e. +RESP:GTCID) from the terminal. It is valid when the parameter <SACK Enable> is disabled.
						- 0: The backend server will not reply with a specified SACK message when receiving a specified message from the terminal.
						- 1: The backend server will reply with a specified SACK message when receiving a specified message from the terminal.
					- Note: If both <Primary DNS Server> and <Secondary DNS Server> are 0.X.X.X, 127.X.X.X or 255.X.X.X,
					  the default DNS server obtained from network will be used.
					-
				- AT+GTSRI
					-
	- DONE Encoder
	- DONE Decoder #19479 #19465
	  id:: 668e8617-8825-4cfc-8db8-ee876337a9b5
		- DONE framePreamble
			- A lógica de pegar número de protocolo do NT20 parece ser diferente do GV57. Temos que modificar o código para ajustar
			- DONE Modificar lógica: Ele precisa pegar o frame type do bytes 1,2,3 do pacote (e não nos bytes 2,3 como no NT20/CRX3)
			- DONE perguntar ao Igor pra que serve esse framePreamble ser 1 ou 2.
				- Pelo que eu entendi, tem dois tipos de preâmbulo, no primeiro caso temos que ler o byte número 3, no segundo caso tempos que ler o byte número 4
				- ```javascript
				  // Implementação do NT20 - pra que serve esse 
				  const framePreamble =
				        data.readUint8(0) === ConcoxCRX3Decoder.FRAME_PREAMBLE_01[0] ? 1 : 2;
				  const frameType: ConcoxCRX3DeviceProtocol = data.readUint8(
				    framePreamble === 1 ? 3 : 4
				  ) as ConcoxCRX3DeviceProtocol;
				  
				  // Implementação sugerida pro GV57
				      const framePreamble =
				        data.readUint8(0) === QueclinkGV57CGDecoder.FRAME_PREAMBLE_01[0] ? 1 : 2;
				  
				  ```
			- DONE problema : protocolos diferentes colocam o comprimento do pacote em bytes diferentes
				- vide imagem:
					- ![image.png](../assets/image_1720106529385_0.png)
				- Como lidar com isso?
				- Temos que adaptar o código. Fazer um conjunto de IFs e condições que se adaptem as diferentes tipos
			- Algoritmo
				- Ler tipo de mensagem no header (protocolo)
				- se for RSP ler message type
					- switch dos tipos de message type: leitura do lenght
					- Se bit 31 do report mask for positivo: ajustar local de leitura do lenght
				- senão, se for heartbeat
					- leitura do lenght
			- DONE Ver Report Mask  e RSP Expansion mask. Pelo visto vai influenciar na localização do Lenght no pacote
				- DONE Perguntar para suporte da Queclink qual das duas implementações do GTFRI é a que será enviada. Que parte da configuração do rastreador decide isso? Vulgo, quano o RSP Expansion Mask é usado?
					- Literalmente não sei qual variável olhar pra saber se o RSP expansion mask vai aparecer ou não.
					- id:: 668eae89-2f14-415c-9976-be50f1aabb68
					  > O bit 31 da report mask (aparentemente) avisa se tem RSP Expansion Mask ou não. Só checar ele
				- > The composition of the HEX report message could be customized by the command AT+GTHRM
				- > The actual length of each HEX report message varies depending on mask settings in AT+GTHRM
			- DONE Entender e responder mensagem Queclink
				-
			- DONE implementar leitura do bit 31 do report mask. Adicionar isso ao algoritmo
				- ((668eae89-2f14-415c-9976-be50f1aabb68))
		- DONE parseNavigationMessage
		  id:: 6697cc37-7338-4744-944c-9686f331b4e8
			- DONE Corrigir erros
			- DONE problema do RSP Expansion Mask ainda não foi resolvido
				- Resolvido. Foi confirmado que a report mask é quem define se o RSP expansion mask está presente ou não
		- DONE ParseHeartbeatMessage
	- DONE Terminar tabela
	- DONE Device Handler
		- #estudando arquitetura do sistema : qual o papel do device handler de cada dispositivo?
		- #estudar como funciona o device handler genérico?
		- TODO #estudo #typescript constructor e super
		- TODO #estudo implements
		- DONE "Mockar" o encoder nohup
		  id:: 66b52d7a-83b9-4399-93a7-a2cda109972f
			- DONE Testar
	- DONE Transformer
	  id:: 66b51d81-5a6d-4f3a-be31-236a4eb12af9
		- DONE Achar o course status na documentação, tá perdido em algum lugar
			- É o azimuth
		- DONE conferir se datahora é realmente obrigatório
		- DONE Trocar o CALCULATED_CRC pelo valor realmente calculado (está retornando o lido do pacote)
			- não precisamos implementar, na verdade a existência desses campos é herança de uma aplicação legada
		- DONE Botar um warning caso as coisas do rastreador que precisamos estejam undefined (problema de configuração), mas não deve quebrar todo o pacote
			- Não se deve botar um warning, e sim jogar uma exceção. O pipeline garante que o pacote seja descartado caso haja exceções, e o restante do código não quebra
		- DONE Refator `digitalInputStatus` no model para permitir detecção de ignicção #importante
		  id:: 66bcc365-e30d-43b6-a5ee-18c1fc4a4d8c
			- apliquei a máscara mas não temos certeza se é isso mesmo. Requer testes mais compreensivos
				- Testamos exaustivamente e está funcionando
		- DONE Perguntar ao Igor qual a política de valores default
			- jogamos uma exceção depois do default, então o problema é irrelevante
		- DONE Consertar MESSAGE_TYPE
	- DONE Refatorar todos os objetos Boolean para primitivos boolean
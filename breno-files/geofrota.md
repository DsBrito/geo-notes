- aprendendo:
  collapsed:: true
	- TODO O volume era pra ser o `timescale_data`. de que lugar no arquivo do docker compose ele tirou esse nome? #aprender
	- TODO #aprender onde o docker guarda os logs de forma centralizada? Pra recuperarmos esse log
	- TODO #aprender semântica do package json https://pnpm.io/package_json
	- TODO #aprender https://semver.org/ [[semantic versioning]]
	  SCHEDULED: <2024-10-15 Tue>
- ~~LATER Fazer setup do #storybook #importante~~ aguardando aprovação coletiva do time
- DONE analisando código
  collapsed:: true
	- [[Oct 10th, 2024]]
	- [[Oct 11th, 2024]]
		- DONE Investigando erro de build no directus
			- https://duckduckgo.com/?t=ffab&q=node_modules%2F.pnpm%2Fisolated-vm%404.7.2%2Fnode_modules%2Fisolated-vm%3A+Running+install+script%2C+failed+in+2s+&ia=web
				- https://stackoverflow.com/questions/78615837/pnpm-install-falied-error-isolated-vm-running-install-script-falied
				- > Check if the version of NodeJS that you are currently using is compatible with the version of isolated-vm that you are trying to install.
				  https://www.npmjs.com/package/isolated-vm#requirements
				  For example, I was trying to install another library and I was getting a similar error, my version of node was 16, downgraded it to 12 and no more errors.  [node.js - Can anyone help fixing my error on installing isolated-vm? - Stack Overflow](https://stackoverflow.com/questions/72099210/can-anyone-help-fixing-my-error-on-installing-isolated-vm)
				- > If you are using a version of nodejs 20.x or later, you must pass --no-node-snapshot to node.
				- ~~TODO Descobrir como fazer isso pelo pnpm??? SCHEDULED: <2024-10-14 Mon>~~
				- a versão do node estava errada. Faltou executar `asdf install` pra instalar a versão do `.tool-versions` local
- DONE botando o directus pra rodar [[Oct 14th, 2024]][
  collapsed:: true
	- LATER Finalizar anotações da ordem do setup com explicações breves (colocar em algum readme de desenvolvimento) #importante
	- ```bash
	  # setup
	  
	  # directus
	  asdf install
	  # compilar as extensões customizadas
	  
	  # inicializar o directus pela primeira vez
	  pnpm run bootstrap
	  
	  ```
- DONE colocando ui pra rodar [[Oct 14th, 2024]]
  collapsed:: true
	- DONE Fazer setup do asdf (ver versão do node que o quasar e o vue pedem)
		- > The `/quasar.config` file is run by the Quasar CLI build system, so this code runs under Node directly, not in the context of your app. This means you can require modules like ‘fs’, ‘path’, Vite plugins, and so on. Make sure the ES features that you want to use in this file are [supported by your Node version](https://node.green/) (which should be >= 14.19.0) - [Configuring quasar.config file | Quasar Framework](https://quasar.dev/quasar-cli-vite/quasar-config-file#introduction)
	- TODO #WARNING   deprecated eslint@8.57.1: This version is no longer supported. Please see https://eslint.org/version-support for other options.
	- TODO #WARNING 5 deprecated subdependencies found: @humanwhocodes/config-array@0.13.0, @humanwhocodes/object-schema@2.0.3, glob@7.2.3, inflight@1.0.6, rimraf@3.0.2
	- DONE Erro: @types/node estava em 12.20.21 mas requer 18 ou 20
	  collapsed:: true
		- [Relationship between the version of node.js and the version of @types/node](https://stackoverflow.com/a/52404327)
		- Não consegui casar as duas. o node vai ser nodejs 20.18.0 e "@types/node": "^20.14.8", e é isto. Se der erro eu mudo
	- DONE Criar usuário no directus
	  SCHEDULED: <2024-10-15 Tue>
		- [[Oct 15th, 2024]]
			- directus não está aceitando login. Erro anterior no bootstrap criou database inválida que não é corrigida mesmo se rodarmos o bootstrap novamente.
			- DONE Deletando database e executando bootstrap de novo
				- ```bash
				  docker volume ls
				  # local     geofrota_timescale_data
				  docker volume rm --force geofrota_timescale_data 
				  # Error response from daemon: remove geofrota_timescale_data: volume is in use - [23fb03dba91eec610a91b489b9a24421f0538a695d90ae6fd4148176e8f327e3]
				  docker ps
				  # CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
				  ```
				- Como limpar volume que possui um container associado?
					- Vamos tentar limpar os arquivos dentro do container em vez disso
					- `docker exec -ti 23fb03dba91e bash`
					- dentro do container executamos `rm -rf /home`
					- o container reclamou mas deletou com sucesso. Container crashou, perdi o log do docker no processo (terminator fechou o terminal automaticamente)
					- rodei o bootstrap de novo...na branch sem as correções :clown
					- refiz o processo e rodou, funcionando
				-
- LATER analisando código: roteamento, páginas já implementadas, etc
  collapsed:: true
	- objetivo: definir quais coisas estão faltando no programa
	- DONE Precisamos de dados de teste
		- DONE Plugar o meu device gateway de teste no activeMQ local (config simples do .env)
		- DONE Configurar o geofrota-device-packet-handler
		  collapsed:: true
			- Ele está configurado pra pegar pacotes de um IP específico usando MQTT. Temos que trocar esse IP pra apontar pro ACtiveMQ
			- DONE Mexer no .env pra consertar endereço e configurações do banco [[Oct 16th, 2024]] Cadê a imagem do activeMQ que usei antes?
			  collapsed:: true
				- ```bash
				  cd ~/geocontrol/services-docker-setup/device-gateway-setup-prod/
				  ```
				- qual a porta do banco de dados mesmo? Ver no docker compose file, que na verdade puxa do ambiente
		- DONE Conectar o ActiveMQ no geofrota-device-packet-handler e na database postgres
		  collapsed:: true
			- [[Oct 16th, 2024]]
				- erro de execução do packer handler
				  collapsed:: true
					- ```verilog
					  [2024-10-16 18:50:08.156 +0000] ERROR (GprsMessageReceivedSource[MQTT]/1488869): error
					  {}
					      err: {
					        "type": "Error",
					        "message": "Invalid header flag bits, must be 0x0 for puback packet",
					        "stack":
					            Error: Invalid header flag bits, must be 0x0 for puback packet
					                at _parseHeader (/home/odnan/geocontrol/geofrota/packages/geofrota-device-packet-handler/node_modules/mqtt-packet/parser.js:63:34)
					                at parse (/home/odnan/geocontrol/geofrota/packages/geofrota-device-packet-handler/node_modules/mqtt-packet/parser.js:43:30)
					                at <anonymous> (/home/odnan/geocontrol/geofrota/packages/geofrota-device-packet-handler/node_modules/mqtt/build/lib/client.js:251:20)
					                at writeOrBuffer (/home/odnan/geocontrol/geofrota/packages/geofrota-device-packet-handler/node_modules/readable-stream/lib/internal/streams/writable.js:334:12)
					                at _write (/home/odnan/geocontrol/geofrota/packages/geofrota-device-packet-handler/node_modules/readable-stream/lib/internal/streams/writable.js:283:10)
					                at <anonymous> (/home/odnan/geocontrol/geofrota/packages/geofrota-device-packet-handler/node_modules/readable-stream/lib/internal/streams/writable.js:286:10)
					                at ondata (node:stream:2089:22)
					                at emit (node:events:183:48)
					                at addChunk (node:stream:1914:22)
					                at readableAddChunk (node:stream:1868:30)
					                at data (node:net:120:9)
					        "originalLine": 53,
					        "originalColumn": 41
					      }
					  
					  ```
					-
				- achei. Erro está em `await Promise.all(this.sources.map((p) => p.start()));`
				- mesmo resultado com device gateway ligado ou desligado
				- Será que eu conectei na porta errada? O que acontece se todo o resto estiver desligado?
					- Se desligarmos o activeMQ ele dá um simples erro de conexão
					  collapsed:: true
						- ```verilog
						  [2024-10-16 19:11:58.133 +0000] INFO (GprsMessageReceivedSource[MQTT]/1552502): Connection configured. Connecting
						  [2024-10-16 19:11:58.134 +0000] ERROR (GprsMessageReceivedSource[MQTT]/1552502): error
						  {"code":"ECONNREFUSED","syscall":"connect","errno":24}
						      err: {
						        "type": "Error",
						        "message": "Failed to connect",
						        "stack":
						            
						        "code": "ECONNREFUSED",
						        "syscall": "connect",
						        "errno": 24
						      }
						  
						  ```
				- mudamos a porta, tinha conectado na errada. GG
		- DONE Registrar no banco de dados um carro e um dispositivo [[Oct 16th, 2024]]
			- [[Oct 24th, 2024]] adicionados, mas mapa do front end parou de ser exibido
				- LATER Investigar bug no front
				  SCHEDULED: <2024-10-25 Fri>
		- DONE Corrigir erro
		  collapsed:: true
			- [[Oct 16th, 2024]]
			  collapsed:: true
				- erro
				  id:: 671017e8-542f-4081-b95f-0f38beeed125
				  collapsed:: true
					- ```verilog
					  {"length":146,"name":"error","severity":"ERROR","code":"22P02","where":"unnamed portal parameter $7 = '...'","originalLine":156,"originalColumn":12,"file":"numutils.c","routine":"pg_strtoint32_safe","stack":"Error: invalid input syntax for type integer: \"4.7\"\n    at <anonymous> (/home/odnan/geocontrol/geofrota/packages/geofrota-device-packet-handler/node_modules/pg-pool/index.js:45:11)\n    at processTicksAndRejections (native)"}[39m
					      err: {
					        "type": "DatabaseError",
					        "message": "invalid input syntax for type integer: \"4.7\"",
					        "stack":
					            Error: invalid input syntax for type integer: "4.7"
					                at <anonymous> (/home/odnan/geocontrol/geofrota/packages/geofrota-device-packet-handler/node_modules/pg-pool/index.js:45:11)
					                at processTicksAndRejections (native)
					        "length": 146,
					        "name": "error",
					        "severity": "ERROR",
					        "code": "22P02",
					        "where": "unnamed portal parameter $7 = '...'",
					        "originalLine": 156,
					        "originalColumn": 12,
					        "file": "numutils.c",
					        "routine": "pg_strtoint32_safe"
					      }
					  ```
				- erro do packer handler
				  collapsed:: true
					- ```verilog
					  [2024-10-21 14:38:42.367 +0000] WARN (Pipeline[DEFAULT]/1873062): Error persisting DB position: item={"position":{"azimute":212,"coordinates":[-40.274089,-20.231441],"ignition":true,"position_time":"2024-10-14T15:34:59.000Z","speed":58.1,"valid":false},"identifier":"97339650","modelName":"GV57CG"}
					  {"length":147,"name":"error","severity":"ERROR","code":"22P02","where":"unnamed portal parameter $7 = '...'","originalLine":156,"originalColumn":12,"file":"numutils.c","routine":"pg_strtoint32_safe","stack":"Error: invalid input syntax for type integer: \"58.1\"\n    at <anonymous> (/home/odnan/geocontrol/geofrota/packages/geofrota-device-packet-handler/node_modules/pg-pool/index.js:45:11)\n    at processTicksAndRejections (native)"}
					      err: {
					        "type": "DatabaseError",
					        "message": "invalid input syntax for type integer: \"58.1\"",
					        "stack":
					            Error: invalid input syntax for type integer: "58.1"
					                at <anonymous> (/home/odnan/geocontrol/geofrota/packages/geofrota-device-packet-handler/node_modules/pg-pool/index.js:45:11)
					                at processTicksAndRejections (native)
					        "length": 147,
					        "name": "error",
					        "severity": "ERROR",
					        "code": "22P02",
					        "where": "unnamed portal parameter $7 = '...'",
					        "originalLine": 156,
					        "originalColumn": 12,
					        "file": "numutils.c",
					        "routine": "pg_strtoint32_safe"
					      }
					  
					  ```
			- Aparentemente a coluna de velocidade está configurada como inteiro? Como eu mudo isso no banco de dados?? [[Oct 21st, 2024]]
			  id:: 671018f5-d48c-4d0a-8440-6e9183465c5a
				- Não tenho como modificar a imagem do timescaledb. Mas o directus é quem está gerenciando o banco de dados, a mudança provavelmente é nele
				- achei~ Arquivo DATABASE.md na pasta raiz do directus
				- #aprendendo Knex
				  collapsed:: true
					- > Knex.js is a SQL query builder for JavaScript, which is used extensively within Directus for database operations. It allows for writing migrations, seeding databases, and performing queries in a more manageable way. - [Directus Knex Integration Guide — Restack](https://www.restack.io/docs/directus-knowledge-directus-knex-integration)
					- > Documentação: float - [Schema Builder | Knex.js](https://knexjs.org/guide/schema-builder.html#float)
				- DONE Resetar o banco de dados para efetivar a mudança de tipo no schema (preciso? Creio que sim)
				- O erro persistiu mesmo após repetir a migração:
				  collapsed:: true
					- ```verilog
					  [2024-10-21 15:05:12.863 +0000] WARN (Pipeline[DEFAULT]/1908888): Error persisting DB position: item={"position":{"azimute":160,"coordinates":[-40.303551,-20.289371],"ignition":true,"position_time":"2024-10-14T18:52:44.000Z","speed":50.6,"valid":false},"identifier":"97339650","modelName":"GV57CG"}
					  {"length":147,"name":"error","severity":"ERROR","code":"22P02","where":"unnamed portal parameter $7 = '...'","originalLine":156,"originalColumn":12,"file":"numutils.c","routine":"pg_strtoint32_safe","stack":"Error: invalid input syntax for type integer: \"50.6\"\n    at <anonymous> (/home/odnan/geocontrol/geofrota/packages/geofrota-device-packet-handler/node_modules/pg-pool/index.js:45:11)\n    at processTicksAndRejections (native)"}
					      err: {
					        "type": "DatabaseError",
					        "message": "invalid input syntax for type integer: \"50.6\"",
					        "stack":
					            Error: invalid input syntax for type integer: "50.6"
					                at <anonymous> (/home/odnan/geocontrol/geofrota/packages/geofrota-device-packet-handler/node_modules/pg-pool/index.js:45:11)
					                at processTicksAndRejections (native)
					        "length": 147,
					        "name": "error",
					        "severity": "ERROR",
					        "code": "22P02",
					        "where": "unnamed portal parameter $7 = '...'",
					        "originalLine": 156,
					        "originalColumn": 12,
					        "file": "numutils.c",
					        "routine": "pg_strtoint32_safe"
					      }
					  
					  ```
				- Deletando volume corretamente: `docker compose --profile all down --volumes` https://stackoverflow.com/a/52326805 #ref
				- executando nova migração [[Oct 22nd, 2024]]
					- erro persiste:
						- database
						  collapsed:: true
							- ```verilog
							  database-1  | 2024-10-22 18:13:49.338 UTC [365] ERROR:  invalid input syntax for type integer: "60.9"
							  database-1  | 2024-10-22 18:13:49.338 UTC [365] CONTEXT:  unnamed portal parameter $7 = '...'
							  database-1  | 2024-10-22 18:13:49.338 UTC [365] STATEMENT:  INSERT INTO public.device_position(
							  database-1  | 	      id, device, position_time, coordinates, azimute, speed, ignition, valid)
							  database-1  | 	      VALUES (tsid_gen($1), $2, $3, ST_SetSRID(ST_MakePoint($4, $5), 4326), $6, $7, $8, $9) RETURNING id
							  database-1  | 2024-10-22 18:13:50.809 UTC [367] ERROR:  invalid input syntax for type integer: "51.6"
							  database-1  | 2024-10-22 18:13:50.809 UTC [367] CONTEXT:  unnamed portal parameter $7 = '...'
							  database-1  | 2024-10-22 18:13:50.809 UTC [367] STATEMENT:  INSERT INTO public.device_position(
							  database-1  | 	      id, device, position_time, coordinates, azimute, speed, ignition, valid)
							  database-1  | 	      VALUES (tsid_gen($1), $2, $3, ST_SetSRID(ST_MakePoint($4, $5), 4326), $6, $7, $8, $9) RETURNING id
							  database-1  | 2024-10-22 18:13:51.349 UTC [369] ERROR:  invalid input syntax for type integer: "37.8"
							  database-1  | 2024-10-22 18:13:51.349 UTC [369] CONTEXT:  unnamed portal parameter $7 = '...'
							  database-1  | 2024-10-22 18:13:51.349 UTC [369] STATEMENT:  INSERT INTO public.device_position(
							  database-1  | 	      id, device, position_time, coordinates, azimute, speed, ignition, valid)
							  database-1  | 	      VALUES (tsid_gen($1), $2, $3, ST_SetSRID(ST_MakePoint($4, $5), 4326), $6, $7, $8, $9) RETURNING id
							  database-1  | 2024-10-22 18:13:51.820 UTC [379] ERROR:  invalid input syntax for type integer: "27.6"
							  database-1  | 2024-10-22 18:13:51.820 UTC [379] CONTEXT:  unnamed portal parameter $7 = '...'
							  database-1  | 2024-10-22 18:13:51.820 UTC [379] STATEMENT:  INSERT INTO public.device_position(
							  database-1  | 	      id, device, position_time, coordinates, azimute, speed, ignition, valid)
							  database-1  | 	      VALUES (tsid_gen($1), $2, $3, ST_SetSRID(ST_MakePoint($4, $5), 4326), $6, $7, $8, $9) RETURNING id
							  database-1  | 2024-10-22 18:13:52.394 UTC [381] ERROR:  invalid input syntax for type integer: "1.1"
							  database-1  | 2024-10-22 18:13:52.394 UTC [381] CONTEXT:  unnamed portal parameter $7 = '...'
							  database-1  | 2024-10-22 18:13:52.394 UTC [381] STATEMENT:  INSERT INTO public.device_position(
							  database-1  | 	      id, device, position_time, coordinates, azimute, speed, ignition, valid)
							  database-1  | 	      VALUES (tsid_gen($1), $2, $3, ST_SetSRID(ST_MakePoint($4, $5), 4326), $6, $7, $8, $9) RETURNING id
							  database-1  | 2024-10-22 18:13:55.106 UTC [65] LOG:  checkpoint starting: time
							  
							  ```
						- packet handler
						  collapsed:: true
							- ```verilog
							  [2024-10-22 18:13:52.395 +0000] WARN (Pipeline[DEFAULT]/2090998): Error persisting DB position: item={"position":{"azimute":0,"coordinates":[-40.292379,-20.289394],"ignition":false,"position_time":"2024-10-13T13:39:31.000Z","speed":1.1,"valid":false},"identifier":"79257344","modelName":"GV57CG"}
							  {"length":146,"name":"error","severity":"ERROR","code":"22P02","where":"unnamed portal parameter $7 = '...'","originalLine":156,"originalColumn":12,"file":"numutils.c","routine":"pg_strtoint32_safe","stack":"Error: invalid input syntax for type integer: \"1.1\"\n    at <anonymous> (/home/odnan/geocontrol/geofrota/packages/geofrota-device-packet-handler/node_modules/pg-pool/index.js:45:11)\n    at processTicksAndRejections (native)"}
							      err: {
							        "type": "DatabaseError",
							        "message": "invalid input syntax for type integer: \"1.1\"",
							        "stack":
							            Error: invalid input syntax for type integer: "1.1"
							                at <anonymous> (/home/odnan/geocontrol/geofrota/packages/geofrota-device-packet-handler/node_modules/pg-pool/index.js:45:11)
							                at processTicksAndRejections (native)
							        "length": 146,
							        "name": "error",
							        "severity": "ERROR",
							        "code": "22P02",
							        "where": "unnamed portal parameter $7 = '...'",
							        "originalLine": 156,
							        "originalColumn": 12,
							        "file": "numutils.c",
							        "routine": "pg_strtoint32_safe"
							      }
							  
							  ```
					- DONE Corrigir esse erro
					  collapsed:: true
						- DONE no packet-handler: arredondar para inteiro
						- DONE no directus: Criar uma migração que muda o tipo de integer para shortint (só precisa de 2 bytes)
						  collapsed:: true
							- ...ok. Como eu escrevo uma migração?
							- `pnpm migration:create fix-speed-type-int-to-shortint`
							- No arquivo aplicamos as operações
							- > Funções relevantes: [Schema Builder | Knex.js](https://knexjs.org/guide/schema-builder.html#schema-building)
					- DONE Configurar pgadmin pra ver se mudou mesmo na database
						- PGadmin não conecta grr
						- Ah era só pegar o IP do container do docker de fato https://stackoverflow.com/a/72595405
						- Agora não dá erro mas dá timeout. Por que??
						  SCHEDULED: <2024-10-23 Wed>
						- [[Oct 23rd, 2024]]
							- finalizado, tinha que colocar os dockers na mesma rede interna
							- Para ver os dados, tem queir no schema, clicar com botão direito na tabela e ir em editar
						- TODO #aprender mais sobre docker neworking
- TODO #manutenção descobrir onde ficar o log do docker daemon
- DONE Analisar as duas opções de biblioteca de mapas. Criar um ramo pra cada e escolher qual vamos usar
  collapsed:: true
	- bibliotecas:
		- maplibre #ref
			- https://indoorequal.github.io/vue-maplibre-gl/ (plugin do vue -> **olhar documentação dos dois**)
			- Documentação Biblioteca https://maplibre.org/maplibre-gl-js/docs/
			- Documentação do plugin do Vue https://indoorequal.github.io/vue-maplibre-gl/api/
		- leaflet #ref
			- https://github.com/vue-leaflet/vue-leaflet wrapper do leafleft pra vue3
			- https://github.com/jhkluiver/Vue3Leaflet demo de leafleft com vue3 e quasar
				- > Note: leaflet is a great project, but I run into problem when dynamic updating layers.Now using [openlayers for vue 3](https://vue3openlayers.netlify.app/), I would recomment to use this project. - [GitHub - jhkluiver/Vue3Leaflet: Demo how to use leaflet in vue 3 web application (with leaflet directly and use use vue-leaflet library)](https://github.com/jhkluiver/Vue3Leaflet)
				-
	- ## #aprendendo #MapLibre com os [exemplos](https://indoorequal.github.io/vue-maplibre-gl/examples/)
	  collapsed:: true
		- DONE A basic map with a style and controls - [Basic - Examples | @indoorequal/vue-maplibre-gl](https://indoorequal.github.io/vue-maplibre-gl/examples/basic)
		- TODO #dúvida qual provedor de tiles vamos usar pro geofrota? O exemplo usa o [[MapTiler]], temos o nosso self hosted?
		- LATER [Custom Control - Examples | @indoorequal/vue-maplibre-gl](https://indoorequal.github.io/vue-maplibre-gl/examples/custom-control.html)
		  collapsed:: true
			- pulei, não parece tão relevante pra agora
		- DONE [Custom Marker - Examples | @indoorequal/vue-maplibre-gl](https://indoorequal.github.io/vue-maplibre-gl/examples/custom-marker)
		  collapsed:: true
			- TODO Testar, será que funciona com png?
			- TODO Precisamos de um overlay mais preciso. Esse é o marcador com as coordenadas aqui na geo:
			  collapsed:: true
				- ![image.png](../assets/image_1730129656421_0.png)
				-
		- LATER [Filter - Examples | @indoorequal/vue-maplibre-gl](https://indoorequal.github.io/vue-maplibre-gl/examples/filter)
		  collapsed:: true
			-
		- LATER  - [Geojson source - Examples | @indoorequal/vue-maplibre-gl](https://indoorequal.github.io/vue-maplibre-gl/examples/geojson.html)
		  collapsed:: true
			- animação é bem crua
			  collapsed:: true
				- Não gostei da forma que ele renderiza o caminho do geojson. Não é muito bonito visualmente nem elegante em implementação. O código fica feio e bagunçado bem rápido mesmo pra uma coisa simples.
				- Colocou-se as coordenadas a renderizar num vetor de origem, `lineString`. Colocou se as coordenadas [0] e [1] no vetor de coordenadas renderizadas, `geojsonSource`. Configurou-se então um temporizador para a cada segundo desconstruir `geojsonSource`, e adicionar ao final (manualmente, sem push nem nada) a coordenada em `lineString` que corresponde ao comprimento de `geojsonSource`. No início há apenas duas coordenadas, o comprimento é 2, então a coordenada em `lineString[2]` será colocada no final de `geojsonSource`. E assim por diante.
				- A metodologia de renderização do exemplo é crua demais, e absolutamente ilegível
				- que bom que não vamos usar a funcionalidade da animação
			- apanhando pra problemas de tipagem da biblioteca
			  collapsed:: true
			  SCHEDULED: <2024-10-29 Tue> [[Oct 31st, 2024]]
				- LATER #aprender detalhes do defineProps e como ele funciona, pra que ele serve. Acho que é ele que está dando problema
				- Vamos pra próxima funcionalidade enquanto isso
		- DONE Draggable Marker - [Draggable Marker - Examples | @indoorequal/vue-maplibre-gl](https://indoorequal.github.io/vue-maplibre-gl/examples/marker-draggable)
		- TODO #importante Tem como desenhar um polígono facilmente? [[Nov 4th, 2024]]
		  collapsed:: true
			- clicar em vários pontos e desenhar um polígono que ligue os vértices
			- Não tem nos exemplos, lendo a documentação da API
			- Tem, só usar o plugin que a biblioteca base tem nos exemplos da documentação
		- #ref https://github.com/maplibre/awesome-maplibre
	- #MapLibre plugins úteis
		- Desenhando polígonos: suporta maplibre V2 e V3: https://github.com/JamesLMilner/terra-draw
		- Desenhar polígonos e calcular áreas de forma mais simples: https://docs.mapbox.com/mapbox-gl-js/example/mapbox-gl-draw/
- DONE Fazer estudo comparativo das metodologias de tabelas de telemetria #importante 
  collapsed:: true
  DEADLINE: <2024-11-05 Tue>
	- https://chat.mistral.ai/chat/58719b54-7ebd-434f-980f-03f9f3ee92c3
	- # Resumo e conclusão
		- A abordagem 3, de tabelas híbridas, é a que atende da melhor forma os requisitos.
		- A necessidade de performance para queries de elementos específicos exige uma tabela estruturada
		- porém, adicionar outros atributos ao banco de dados no futuro é caro para bancos muito grandes (imagine dois milhões de linhas numa tabela para um único atributo novo)
		- Ao combinar uma tabela tradicional para os atributos mais críticos, com uma coluna de JSON binary para "expansão futura", atendemos simultaneamente os requisitos de performance e flexibilidade.
		- É necessário cautela com a coluna JSONB. Uma equipe indisciplinada rapidamente irá usar essa coluna como um coringa e adicionar campos lá dentro sem muita reflexão. Isso vai aumentar o débito técnico muito rapidamente.
		- Recomenda-se a seguinte prática: Dados acessados mais frequentemente e que não mudarão ao longo do tempo devem estar em colunas tradicionais. Dados que não são tão críticos e que estão sujeitos à mudanças, como a telemetria, podem ser colocados na coluna de JSONB, para que seja possível modificar facilmente as informações armazenadas neles com simplicidade e baixo risco. Ainda assim, deve ser realizada ampla discussão e reflexão antes de realmente adicionar um campo a mais nesta coluna.
	- ### Características do sistema
		- Grande volume de dados
		- Mais tipos de dados podem ser adicionados futuramente
	- ### Requisitos do caso de uso
		- Performance: mesmo com grande quantidade de dados, da ordem de milhões de linhas
		- flexibilidade: permitir adição de novos campos sem grandes dificuldades
	- # Abordagens sugeridas
		- collapsed:: true
		  1. Tabela única com coluna JSONBinary
			- colunas: id | veículo | dispositivo | data
			- vantagens #green
			  collapsed:: true
				- permite adição de novos tipos com simplicidade e facilidade (não é necessário adicionar N novas linhas a cada adição de tipo, onde N é o número de linhas da tabela)
			- desvantagens #red
			  collapsed:: true
				- menos performance para query de campos específicos dentro do JSONB
				- validação e segurança de tipos é menos robusta
		- collapsed:: true
		  2. tabela com estrutura normalizada
			- tabela tradicional. Uma coluna por dado
			- vantagens #green
			  collapsed:: true
				- alta performance
				- segurança e validação de tipos
			- desvantagens #red
			  collapsed:: true
				- penalidade e complexidade ao adicionar nova coluna
		- collapsed:: true
		  3. Tabela híbrida: colunas normalizadas com uma coluna JSONBinary
			- vantagens #green
			  collapsed:: true
				- Permite acesso rápido aos campos normalizados
				- Permite adição de campos extras na coluna JSONB sem a penalidade de adição nem alteração de schema
				- > If you're adding true metadata, or if your JSON is describing the information that does not need to be queried and is only used for display, it may be overkill to create a separate column for all of the data points. https://dba.stackexchange.com/a/268561/314624
				- > Observe que atualmente há suporte a [[indexação]] de chaves específicas dentro de um objeto JSON - https://stackoverflow.com/a/15367769/9790193
			- desvantagens #red
			  collapsed:: true
				- adiciona complexidade nas queries
				- dados em jsonb não são validados
				- > Chaves estrangeiras podem ser criadas entre colunas, mas não entre chaves em objetos JSON - https://stackoverflow.com/a/15367769/9790193
				- Pode gerar débito técnico galopante numa equipe indisciplinada. - para mais detalhes, vide: https://stackoverflow.com/a/35320171/9790193
				- JOINs e comparisons não são viáveis com valores dentro do JSON - https://stackoverflow.com/a/35320171/9790193
			- > Looking back, JSON allowed us to iterate very quickly and get something out the door. It was great. However after we reached a certain team size it's flexibility also allowed us to hang ourselves with a long rope of technical debt which then slowed down subsequent feature evolution progress. **Use with caution**. [mysql - Storing JSON in database vs. having a new column for each key - Stack Overflow](https://stackoverflow.com/questions/15367696/storing-json-in-database-vs-having-a-new-column-for-each-key/35320171#35320171)
		- collapsed:: true
		  4. Modelo EAV: Cada linha é um dado genérico, com coluna pra nome do atributo e para o valor
			- vantagens #green
			  collapsed:: true
				- muito fácil de adicionar novos tipos
			- desvantagens #red
			  collapsed:: true
				- pouca performance em query de dados específicos
				- aumenta complexidade
				- pouca segurança e validação de dados
				- Desperdício de espaço em armazenamento de dados
		- collapsed:: true
		  5. Uma tabela por tipo de dado
			- vantagens #green
			  collapsed:: true
				- simples de adicionar novos tipos de dados, só adicionar mais uma tabela. Mantém eficiência e validação em todos os dados
			- desvantagens #red
			  collapsed:: true
				- juntar dados de múltiplas tabelas é complexo
				- schemas mais complexos
		- collapsed:: true
		  6. Séries temporais com TimescaleDB Hypertables
			- Com exceção da maior eficiência com querys baseadas em tempo, tem as mesmas vantagens e desvantagens da tabela comum. Ainda precisamos alterar o esquema para inserir novos dados
- DONE Relatório do estudo
  DEADLINE: <2024-11-05 Tue 13:00>
- DONE pesquisar link no grupo:  abordagens para tabelas hierarquicas de db
  collapsed:: true
	- Não achei o link olhando no grupo do geodev todo
- LATER ver diretiva usemap
- TODO Implementar o UI
  id:: 6740bc1f-9543-4c95-9a79-31e258a533a1
	- DONE Aguardando login do Georast, para usar como lista de funcionalidades
	- DONE Ver se componente tree do quasar serve pra nossa visualização de hierarquia
	- ~~TODO Listar funcionalidades do front end~~
	  collapsed:: true
		- ~~Telas: vide [figma](https://www.figma.com/files/team/1441875054954819808/recents-and-sharing?fuid=1441875051217697379) e o [UI Kit Quasar para o Figma](https://www.figma.com/community/file/1379867024680261137) [como baixar?](https://www.youtube.com/watch?v=3CyVlzUs0rQ)~~
		  collapsed:: true
			- DONE Login
	- DOING Replicar aproximadamente a UI do GEORAST, atualizando com melhorias de usabilidade
	  id:: 6740d698-c41b-4685-894c-24e7ca4d57eb
		- TODO #docs Adicionar detalhes sobre organizational unit enclosure à documentação
		- DONE Colocando links de acesso
		- DONE barra lateral com árvore de decisão do quasar
		  collapsed:: true
			- When using relatively large data, for performance we recommend using the `no-transition` Boolean prop which will account for a significant runtime speed improvement.
			- ```
			  <q-tree no-transition ...
			  ```
			- ~~A árvore do exemplo é estática. Como coloco conteúdo dinâmico nela??~~
			  collapsed:: true
				- ~~É um algoritmo recursivo simples. A árvore espera um vetor de objetos, e a propriedade 'children' de cada objeto também. Só fazer um map do formato que o directus entrega pro que a árvore espera~~
			- DONE Usar o template disponível em '**OrganizationalUnitTree**'. Mover ele apenas para barra lateral (?)
			  id:: 6751a9b4-ddda-488f-8f33-4042cfbe169f
			- DONE Adicionar barra arrastável #ref
			  collapsed:: true
				- https://pdanpdan.github.io/posts/post_007.html
				- Problema de tipagem do v-touch-pan: https://github.com/quasarframework/quasar/discussions/17589
		- TODO Adicionar detalhe do .env do pgadmin ao env.example à #documentação
		- TODO Adicionar à [[documentação]] detalhe do docker-compose-dev file
		- TODO Adicionar à [[documentação]] o endereço de acesso a cada coisa
		- DONE Fazer dump do banco
		  collapsed:: true
			- Não conseguimos fazer restauração bem sucedida do banco em outra máquina. Perguntar ao pessoal do devops como faz essa parte operacional. É útil pra subir rapidamente ambientes de desenvolvimento
		- TODO Adicionar à documentação
			- Desenho da arquitetura do sistema
			- Como Configurar o device gateway
			- Como iniciar o activeMQ
			- Como configurar e iniciar o geofrota-device-packet-handler
			- Considerações sobre portas (qual porta de cada vai onde)
			- Setup inicial do directus para ter dados no banco
		- DONE Mudar o comportamento da árvore para selecionar todos abaixo do selecionado
		  collapsed:: true
			- DONE Using leaf-filtered behaviour does not show vehicles belonging to a parent with a single child.
			- Alternative: add doble-click to tick all childs of node
				- Como fazer isso com o quasar tree? Não há uma forma fácil de fazer
		- DONE Consertar o popup com close duplo
			- DONE Deixar o X de fechar maior
		- > [Effective Code Documentation: Best Practices for Developers](https://baransel.dev/post/effective-code-documentation/) #ref [[docs]]
		- >  [How To Write Good Documentation: The Professional Developer‘s Guide - ExpertBeacon](https://expertbeacon.com/how-to-write-good-documentation-the-professional-developers-guide/)
		-
	- Colocar open street map layer no maplibre?
	  collapsed:: true
		- TODO Ver com tota qual fonte de mapas usamos, mas o código é independente disso
	- DONE Conectar dados com o UI
		- DONE Criar dados de mock e colocar no banco (já não temos dados lá do rastreador da queclink?)
		- O que falta no directus pro UI acessar ele? #aprendendo como acessar a database com o directus
		  id:: 67472942-97d7-491e-a3ed-b8db7ffec101
			- directusService, uma camada que utiliza as funcionalidades prontas do directus ao realizar queries.
			- Já temos essa classe implementada no código
	- # Funcionalidades
		- DONE Mostrar miniaturas de veículos no mapa (localização mais recente)
		- DONE Ter veículo registrados
		- DONE Reescrevendo o mapa do geofrota sem o google maps, substituindo cada funcionalidade pela correspondente do maplibre
		  id:: 67489ca7-08f7-43e0-98ce-c7a1f77e4570
		  collapsed:: true
			- DONE Preciso descobrir porque não estão sendo mostrados nenhum veículo das localizações que já existem no banco de dados
			  collapsed:: true
				- O caminho é rastreador -> device-gateway -> activeMQ -> Database -> directus -> Storage -> UI
				- Em algum lugar algum componente está falhando.
				- Já temos localizações na Database,
				- DONE elas aparecem no directus?
				  collapsed:: true
					- Sim, tem posições dispositivos e veículos no directus
				- DONE Elas estão sendo salvas no storage?
				- faltava importar o MglMarker. Incrível
			- DONE Fazer os marcadores escalarem corretamente
			- DONE Fazer janela de pop up quando clicar no marcador
			  collapsed:: true
				- #aprendendo sobre [[template refs]] do [[vue]]
				  id:: 674daa4e-1d00-4f39-a599-44dad2fb1770
				  collapsed:: true
					- #ref
						- https://vuejs.org/api/composition-api-helpers.html#usetemplateref
						- https://vuejs.org/guide/essentials/template-refs.html
						- https://vuejs.org/guide/typescript/composition-api.html#typing-template-refs
						- https://vuejs.org/guide/typescript/composition-api.html#typing-component-template-refs
					- ref can also be used on a child component. In this case the reference will be that of a component instance:
					  collapsed:: true
						- ```vue
						  <script setup>
						  import { useTemplateRef, onMounted } from 'vue'
						  import Child from './Child.vue'
						  
						  const childRef = useTemplateRef('child')
						  
						  onMounted(() => {
						    // childRef.value will hold an instance of <Child />
						  })
						  </script>
						  
						  <template>
						    <Child ref="child" />
						  </template>
						  ```
					- erro `'popupRef.value' is possibly 'null'.`
					  collapsed:: true
						- ```vue
						  <template>
						    <mgl-popup ref="popup">
						      <h1>Hello</h1>
						      <p>HTML content</p>
						      <a href="#" @click.prevent="closePopup">Close popup</a>
						    </mgl-popup>
						  </template>
						  
						  <script setup lang="ts">
						  
						  import { useTemplateRef } from 'vue';
						  import { MglPopup } from '@indoorequal/vue-maplibre-gl';
						  
						  const popupRef = useTemplateRef('popup');
						  const closePopup = () => {
						    popupRef.value.remove(); // erro aqui
						  };
						  </script>
						  
						  <style scoped></style>
						  
						  ```
						- https://stackoverflow.com/questions/65026253/object-is-possibly-null-on-a-refnull
							- https://stackoverflow.com/questions/64613226/vue-3-composition-api-get-value-from-object-ref
						- https://medium.com/@shuhan.chan08/basic-usage-of-vue-3-5-usetemplateref-4b8d7a89bf7d
							- As shown in the example, you can only access the `ref` after the component has mounted. That’s why we use the `onMounted` lifecycle hook to focus on the input element. It's worth noting that initially, the value of `myInput` will be `null` since the element hasn't been rendered in the DOM yet.
							- If type inference isn’t as expected, you can explicitly specify the type using `useTemplateRef()` like this:
							- ```
							  const myInput = useTemplateRef<HTMLInputElement>(null)
							  ```
							- This ensures the `ref` for the input box correctly points to an HTML element and benefits from TypeScript's type checking.
						- minha solução
							- ```javascript
							  const popupRef = useTemplateRef<typeof MglPopup>('popup');
							  const closePopup = () => {
							    if (popupRef.value) {
							      popupRef.value.remove();
							    }
							  };
							  ```
					-
			- DONE Colocar informações na janela de popup que aparece
			- ~~TODO Fazer popup ter várias janelas~~
			- ~~TODO Implementar marcação de rota~~
	- ~~TODO Como vai funcionar cadastro de veículos no app?~~
	- ~~TODO Como configurar os campos de uma tabela nova do banco de dados no directus?~~
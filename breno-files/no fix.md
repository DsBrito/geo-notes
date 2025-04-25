alias:: gravado

- Definição
	- No contexto de rastreadores, pacotes gravados são aqueles gerados em momentos em que o rastreador não foi capaz de obter conexão com o GPS. Os dados de localização nesses pacotes são inválidos, ou copiados da última localização disponível
- Observações
	- No rastreador [[Queclink]] [[GV57CG]], a opção "discard no fix" é habilitada por padrão. Quando ativada, o rastreador não irá enviar pacotes caso não consiga conexão com GPS, ou seja, não haverão pacotes gravados. Caso ela seja desabilitada, o campo 'Satellites in Use' será zero, e a longitude e longitude serão zero.
	- GV57CG
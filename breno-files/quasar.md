- [[aprendendo]] [[quasar]]
	- ((662a73cd-5875-43ea-8105-2e6797659060))
	- introdução: O que vamos aprender?
		- Directives
		- Componentes
		- Directus
		- Boot file
		- Layout builder
		- backends
			- hookup to databases
			- localstorage, firebase, etc
	- Setup
		- Versão mínima do node: 12.22.1
		- Não usar versões ímpares do node! (experimentais)
		- Dica do tutorial: [[npm]] para pacotes globais, [[yarn]] para pacotes locais
		- `pnpm install -g @quasar/cli`
		- setup do [[asdf]] :
			- TODO Passar isso para um script de boilerplate de projetos web
			- ```bash
			  # instala última versão estável do node 20
			  asdf install nodejs latest:20
			  # lista versões atuais selecionadas
			  asdf current
			  # define versão atual do node
			  asdf local nodejs latest:20
			  ```
		- Setup projeto [[quasar]]
			- `pnpm create quasar`
			- Quasar v2
			- Vite 2 stable v1
			- Composition API, vamos convertendo on the fly
			- Linting, pinia, axios
			- Sass with SCSS
			- Prettier pq sim
			- Deixando PNPM instalar as dependência pra mim
			- Warning:
				- ```bash
				   WARN  Issues with peer dependencies found
				  .
				  ├─┬ @quasar/app-vite 1.8.4
				  │ └─┬ @vitejs/plugin-vue 2.3.4
				  │   └── ✕ unmet peer vite@^2.5.10: found 5.2.10
				  └─┬ vite 5.2.10
				    └── ✕ unmet peer @types/node@"^18.0.0 || >=20.0.0": found 12.20.55
				  
				  ```
				- ignorando por enquanto
	- TODO #issue por que o v-model tá quebrando na composition API?
	-
	- #aprender
		- O que é o pinia?
-
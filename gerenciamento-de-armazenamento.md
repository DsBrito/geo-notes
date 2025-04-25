# Explicação dos Comandos

Este documento fornece uma explicação detalhada de diversos comandos utilizados para gerenciamento de armazenamento e processos.

## Comandos de Gerenciamento de Armazenamento

### `df -h`

Mostra o uso do disco de forma legível para humanos. A opção `-h` exibe os tamanhos em unidades compreensíveis (KB, MB, GB, etc.).

### `du -sh ~/.local /* | grep G`

Mostra o tamanho dos diretórios dentro de `~/.local` e filtra apenas aqueles cujo tamanho é medido em gigabytes (`G`).

### `du -sh ~/.local ./* | grep G`

Semelhante ao comando anterior, mas inclui o diretório `.` (diretório atual) na análise.

### `du -sh ~/.local* | grep G`

Verifica o tamanho dos arquivos e diretórios que começam com `~/.local` e filtra apenas aqueles com tamanho em gigabytes.

### `du -ahd 1 /home/dionatasb/ | tee /tmp/du.txt`

Lista todos os arquivos e diretórios (incluindo arquivos ocultos) em `/home/dionatasb/`, mostrando o tamanho de cada um até um nível de profundidade (`-d 1`). O comando `tee` redireciona a saída para o arquivo `/tmp/du.txt`, permitindo visualização ao mesmo tempo em que salva a informação.

## Comandos de Gerenciamento de Processos

### `top`

Exibe em tempo real os processos em execução no sistema, incluindo informações sobre consumo de CPU e memória.

### `npkill`

Ferramenta para localizar e excluir diretórios `node_modules` no sistema, ajudando a liberar espaço em disco.

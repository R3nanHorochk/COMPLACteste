# COMPLAC

COMPLAC é um compilador completo para a linguagem **SLAC²**, implementado em C99 e estruturado em torno de analisador léxico, tabela de símbolos com controle de escopo, parser descendente recursivo, análise semântica e gerador de código assembly para a arquitetura **MEPA**.

## Requisitos

* GCC ou outro compilador compatível com o padrão C99
* Ambiente Unix/Linux (o projeto utiliza `make` e shell POSIX)

## Compilação

No diretório raiz do projeto, execute:

```bash
make clean && make
```

Isso gera o executável `complac` na pasta atual.

Para remover os artefatos de compilação:

```bash
make clean
```

## Execução

```bash
./complac <arquivo.slac> [--tokens] [--symtab] [--trace]
```

Exemplo:

```bash
./complac teste.slac --tokens --symtab --trace
```

### Arquivos gerados e opções da linha de comando

Por padrão, a compilação bem-sucedida produz o código assembly MEPA correspondente ao arquivo de entrada (ex.: `teste.mepa`). As opções adicionais habilitam relatórios auxiliares de depuração:

* `--tokens`: gera um arquivo `.tk` com a sequência de tokens do programa-fonte
* `--symtab`: gera um arquivo `.ts` com a tabela de símbolos
* `--trace`: gera um arquivo `.trc` com mensagens de rastreamento da execução

Os arquivos de saída mantêm o nome base do arquivo de entrada, alterando apenas a extensão:

* `teste.mepa` (código objeto/assembly MEPA)
* `teste.tk`
* `teste.ts`
* `teste.trc`

## Arquitetura e fluxo de compilação

* A função `main()` em `main.c` coordena a inicialização dos módulos: argumentos de linha de comando, diagnósticos, logs, analisador léxico, tabela de símbolos, gerador de código e parser.
* O parser utiliza *lookahead* de um token para desambiguar construções como chamadas de sub-rotinas, acessos a vetores e atribuições, validando tipos e escopos durante a descida recursiva.
* O módulo `gerador.c` emite as instruções da arquitetura MEPA (gerenciamento de pilha, controle de fluxo com rótulos, chamadas de funções/procedimentos, operações aritméticas/lógicas e entrada/saída).
* O fluxo principal de compilação segue as etapas:
  1. leitura do código-fonte `.slac`
  2. análise léxica (tokenização)
  3. análise sintática e semântica com gerenciamento de escopos na tabela de símbolos
  4. geração das instruções assembly no arquivo `.mepa`
  5. fechamento dos recursos e emissão de diagnósticos

## Estrutura do projeto

* `main.c`: ponto de entrada e orquestração das fases do compilador
* `lex.c` / `lex.h`: analisador léxico
* `token.c` / `token.h`: definição e representação de categorias de tokens
* `parser.c` / `parser.h`: parser descendente recursivo e análise semântica
* `symtab.c` / `symtab.h`: tabela de símbolos com suporte a escopos global e local
* `gerador.c` / `gerador.h`: geração de código assembly para a arquitetura MEPA
* `diag.c` / `diag.h`: tratamento e formatação de erros léxicos, sintáticos e semânticos
* `opt.c` / `opt.h`: processamento de opções da linha de comando
* `log.c` / `log.h`: geração dos arquivos auxiliares de log (`.tk`, `.ts`, `.trc`)
* `Makefile`: regras de compilação e limpeza do projeto

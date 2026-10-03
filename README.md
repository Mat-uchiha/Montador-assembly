# Montador Assembly

Montador desenvolvido em Python para converter um programa escrito em Assembly para código de máquina hexadecimal.

O programa interpreta as instruções presentes no arquivo `programa.asm`, realiza a codificação de acordo com o conjunto de instruções definido no montador e gera o arquivo `exe3.txt`.

## Funcionamento

O montador utiliza uma memória de **256 posições**, inicialmente preenchida com `00`.

Cada instrução Assembly é convertida em sua representação binária e posteriormente armazenada em hexadecimal na memória.

Algumas instruções ocupam **1 posição de memória**, enquanto instruções que possuem um valor ou endereço imediato, como `DATA`, `JMP` e saltos condicionais, ocupam **2 posições**.

## Requisitos

É necessário ter o Python instalado.

Como o código utiliza a estrutura `match/case`, é recomendado utilizar:

```text
Python 3.10 ou superior
```

## Estrutura dos arquivos

O montador espera encontrar o seguinte arquivo no mesmo diretório:

```text
programa.asm
```

Após a montagem, será criado:

```text
exe3.txt
```

Estrutura básica do projeto:

```text
projeto/
├── montador_assembly.py
├── programa.asm
└── exe3.txt
```

O arquivo `exe3.txt` é criado automaticamente ao executar o montador.

## Como executar

Crie o arquivo `programa.asm` com o programa que deseja montar.

Exemplo:

```asm
DATA R0, 10
DATA R1, 20
ADD R0, R1
OUT DATA, R0
```

Depois execute:

```bash
python montador_assembly.py
```

Ao finalizar, o montador criará o arquivo:

```text
exe3.txt
```

O arquivo começa com:

```text
v3.0 hex words plain
```

seguido pelo conteúdo das 256 posições de memória.

## Sintaxe do Assembly

As instruções podem utilizar espaços ou vírgulas como separadores.

Por exemplo, as duas formas abaixo são interpretadas da mesma maneira:

```asm
ADD R0 R1
```

```asm
ADD R0, R1
```

O montador converte automaticamente o conteúdo do arquivo para letras maiúsculas antes de interpretar as instruções.

### Registradores

| Registrador | Código binário |
|---|---|
| `R0` | `00` |
| `R1` | `01` |
| `R2` | `10` |
| `R3` | `11` |

## Conjunto de instruções

### Operações entre registradores

Formato:

```asm
INSTRUCAO RA, RB
```

| Instrução | Opcode | Exemplo |
|---|---:|---|
| `LD` | `0000` | `LD R0, R1` |
| `ST` | `0001` | `ST R0, R1` |
| `ADD` | `1000` | `ADD R0, R1` |
| `SHR` | `1001` | `SHR R0, R1` |
| `SHL` | `1010` | `SHL R0, R1` |
| `NOT` | `1011` | `NOT R0, R1` |
| `AND` | `1100` | `AND R0, R1` |
| `OR` | `1101` | `OR R0, R1` |
| `XOR` | `1110` | `XOR R0, R1` |
| `CMP` | `1111` | `CMP R0, R1` |

A codificação dessas instruções possui o formato:

```text
OOOO RRA RRB
```

onde `OOOO` representa o opcode e os dois últimos campos representam os registradores.

## Entrada e saída

As instruções `IN` e `OUT` recebem um tipo e um registrador.

Formato:

```asm
IN TIPO, REGISTRADOR
OUT TIPO, REGISTRADOR
```

Os tipos disponíveis são:

| Tipo | Código |
|---|---:|
| `DATA` | `0` |
| `ADDR` | `1` |

Exemplos:

```asm
IN DATA, R0
OUT DATA, R0

IN ADDR, R1
OUT ADDR, R1
```

## DATA

A instrução `DATA` associa um valor a um registrador.

Formato:

```asm
DATA REGISTRADOR, VALOR
```

Exemplo:

```asm
DATA R0, 25
```

Essa instrução ocupa duas posições de memória: uma para a instrução e outra para o valor informado.

Os valores podem ser escritos em decimal, binário ou hexadecimal.

```asm
DATA R0, 10
DATA R1, 0B1010
DATA R2, 0X0A
```

Valores decimais negativos também são convertidos para a representação de 8 bits.

Exemplo:

```asm
DATA R0, -1
```

## Saltos

### JMP

Realiza um salto para um endereço informado diretamente.

Formato:

```asm
JMP ENDERECO
```

Exemplo:

```asm
JMP 20
```

A instrução ocupa duas posições de memória: uma para o opcode e outra para o endereço.

### JMPR

Utiliza um registrador como parte da instrução de salto.

Formato:

```asm
JMPR REGISTRADOR
```

Exemplo:

```asm
JMPR R0
```

## Saltos condicionais

O montador reconhece as seguintes instruções condicionais:

| Instrução | Código |
|---|---:|
| `JZ` | `0001` |
| `JE` | `0010` |
| `JEZ` | `0011` |
| `JA` | `0100` |
| `JAZ` | `0101` |
| `JAE` | `0110` |
| `JAEZ` | `0111` |
| `JC` | `1000` |
| `JCZ` | `1001` |
| `JCE` | `1010` |
| `JCEZ` | `1011` |
| `JCA` | `1100` |
| `JCAZ` | `1101` |
| `JCAE` | `1110` |
| `JCAEZ` | `1111` |

Formato:

```asm
Jxx ENDERECO
```

Exemplo:

```asm
CMP R0, R1
JE 20
```

Os saltos condicionais ocupam duas posições de memória: uma contendo a instrução e outra contendo o endereço de destino.

## CLF

A instrução `CLF` é montada sem operandos.

```asm
CLF
```

Seu código binário é:

```text
01100000
```

## Comentários

Linhas iniciadas com `;` são ignoradas pelo montador.

Exemplo:

```asm
; Inicialização dos registradores
DATA R0, 10
DATA R1, 20

; Realiza a soma
ADD R0, R1
```

Comentários após uma instrução na mesma linha não são tratados especificamente pelo parser atual. Portanto, é recomendado utilizar comentários em linhas separadas.

## Exemplo de programa

```asm
; Carrega dois valores
DATA R0, 10
DATA R1, 20

; Soma os valores
ADD R0, R1

; Envia o resultado para a saída
OUT DATA, R0
```

Após executar:

```bash
python montador_assembly.py
```

o montador converte cada instrução para código hexadecimal e grava o resultado em `exe3.txt`.

## Formato da saída

O arquivo gerado possui o seguinte formato:

```text
v3.0 hex words plain
XX
XX
XX
...
```

Cada linha após o cabeçalho representa uma posição da memória.

A memória possui **256 posições**, e posições que não receberam instruções permanecem inicialmente com o valor:

```text
00
```

## Observações

O nome do arquivo de entrada e do arquivo de saída está definido diretamente no código:

```python
asm = read_archive_asm('programa.asm')
```

```python
write_output('exe3.txt')
```

Portanto, caso seja necessário utilizar outros nomes, essas chamadas devem ser alteradas no código.

O parser atual também considera qualquer instrução que não corresponda aos casos explícitos como um salto condicional. Por isso, instruções inválidas ou erros de digitação podem gerar erro durante a montagem em vez de uma mensagem específica indicando instrução desconhecida.

Projeto desenvolvido como implementação de um montador Assembly em Python para a disciplina de organização de computadores.

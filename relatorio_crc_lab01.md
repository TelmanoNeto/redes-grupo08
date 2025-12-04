# Relatório – Lab01 de Detecção de Erros (CRC)

Quisemos transformar o notebook `Redes_Grupo_08.ipynb` em uma entrega completa para o laboratório de CRC. O texto abaixo conta, de forma direta, o que já funciona, o que observamos nos experimentos e que ajustes ainda faltam para o trabalho ficar redondo.

## 1. O que já fizemos

- A dupla `xor_bits` + `calcular_crc_manual` está implementada exatamente como o professor sugeriu e passa pelo teste clássico do slide (mensagem `1101011111`, gerador `10011` → CRC `0010`). Esse trecho aparece logo no início do notebook.
- A mesma função foi colada novamente em uma célula mais abaixo. Vale deixar só uma versão para facilitar futuras mexidas.

## 2. Desempenho: manual versus biblioteca

### 2.1 Ambiente

- Rodamos tudo em um Intel Core i7‑4790 @ 3,60 GHz (4 núcleos/8 threads, x86_64). Os dados do `lscpu` estão no terminal para referência.
- A biblioteca usada foi a `crc`, via `Calculator(Crc16.MODBUS)`.
- Escolhemos os tamanhos 1500, 3000, 6000 e 16000 bytes. O enunciado pede 1500, 4500 e 9000 no mínimo, então ainda precisamos ajustar essa lista.

### 2.2 Números coletados

| Tamanho (bytes) | Tempo manual (s) | Pico manual (KiB) | Tempo biblioteca (s) | Pico biblioteca (KiB) |
|----------------:|-----------------:|------------------:|---------------------:|----------------------:|
| 1500            | 0,2700           | 105,75            | 0,0974               | 1,22                  |
| 3000            | 0,5382           | 211,22            | 0,1597               | 1,22                  |
| 6000            | 1,0837           | 422,16            | 0,3379               | 1,22                  |
| 16000           | 3,9506           | 1125,28           | 0,8855               | 1,22                  |

### 2.3 Impressões

- O código manual escala mais ou menos linearmente, mas sofre porque mantém strings gigantes na memória. Em 16 KB o tempo passa de 3,9 s e o uso de RAM chega a ~1,1 MiB.
- A biblioteca fica praticamente estável: menos de 1 s e ~1,2 KiB em todos os casos, mostrando o ganho de trabalhar em bytes e com uma implementação otimizada.
- Os gráficos do notebook mostram essas curvas, porém ainda falta uma célula em texto comentando o hardware e explicando o que vemos nos plots, como o enunciado pede.

## 3. Cenário personalizado

### 3.1 Montagem da mensagem base

- Cada integrante codificou o próprio nome em ASCII (8 bits por caractere) e usou o gerador indicado pelo último dígito da matrícula. Os valores estão impressos no notebook.
- Ainda precisamos preencher a planilha solicitada no README e deixar registrado que ela foi entregue.

### 3.2 Como injetamos erros

- A função `aplicar_erro` está quase certa, mas subtrai 1 da posição sorteada (`pos -= 1`). Como o `randint` inclui o zero, às vezes acabamos aplicando rajadas no fim do quadro em vez do início. Basta alinhar essa lógica para não perder o primeiro bit.
- A lista `padroes_erros` traz dez padrões fixos. O enunciado fala em “variar posição e tipo dos erros inseridos de forma aleatória”. Uma ideia simples é sortear também qual padrão aplicar (ou gerar padrões aleatórios) a cada um dos 10 testes.

### 3.3 O que saiu dos 10 testes

Os quadros abaixo resumem o que foi impresso no notebook. Sempre que o CRC manual terminou em todos os zeros, marquei “Não” na coluna “Detectado (Manual)”. Nessas horas a biblioteca continuou apontando erro, o que mostra que existe um bug só na nossa implementação.

#### André — gerador `10001000000100001`

| # | Padrão             | Posição | Detectou manual? | Detectou biblioteca? |
|--:|--------------------|--------:|------------------|----------------------|
| 1 | 11000000000000101  | 139     | Sim              | Sim                  |
| 2 | 10001000000100001  | 168     | **Não**          | Sim                  |
| 3 | 10011000000010001  | 147     | Sim              | Sim                  |
| 4 | 1011010010110      | 165     | Sim              | Sim                  |
| 5 | 11100010101        | 120     | Sim              | Sim                  |
| 6 | 100111000111       | 93      | Sim              | Sim                  |
| 7 | 1010000110101      | 85      | Sim              | Sim                  |
| 8 | 110101100001       | 134     | Sim              | Sim                  |
| 9 | 1110010100111      | 55      | Sim              | Sim                  |
|10 | 10000011101001     | 66      | Sim              | Sim                  |

#### Telmano — gerador `11000000000000101`

| # | Padrão             | Posição | Detectou manual? | Detectou biblioteca? |
|--:|--------------------|--------:|------------------|----------------------|
| 1 | 11000000000000101  | 102     | **Não**          | Sim                  |
| 2 | 10001000000100001  | 62      | Sim              | Sim                  |
| 3 | 10011000000010001  | 139     | Sim              | Sim                  |
| 4 | 1011010010110      | 184     | Sim              | Sim                  |
| 5 | 11100010101        | 2       | Sim              | Sim                  |
| 6 | 100111000111       | 9       | Sim              | Sim                  |
| 7 | 1010000110101      | 47      | Sim              | Sim                  |
| 8 | 110101100001       | 176     | Sim              | Sim                  |
| 9 | 1110010100111      | 219     | Sim              | Sim                  |
|10 | 10000011101001     | 180     | Sim              | Sim                  |

#### Vinícius — gerador `10001000000100001`

| # | Padrão             | Posição | Detectou manual? | Detectou biblioteca? |
|--:|--------------------|--------:|------------------|----------------------|
| 1 | 11000000000000101  | 34      | Sim              | Sim                  |
| 2 | 10001000000100001  | 176     | **Não**          | Sim                  |
| 3 | 10011000000010001  | 118     | Sim              | Sim                  |
| 4 | 1011010010110      | 119     | Sim              | Sim                  |
| 5 | 11100010101        | 106     | Sim              | Sim                  |
| 6 | 100111000111       | 13      | Sim              | Sim                  |
| 7 | 1010000110101      | 94      | Sim              | Sim                  |
| 8 | 110101100001       | 35      | Sim              | Sim                  |
| 9 | 1110010100111      | 182     | Sim              | Sim                  |
|10 | 10000011101001     | 18      | Sim              | Sim                  |

#### Adriano — gerador `10011000000010001`

| # | Padrão             | Posição | Detectou manual? | Detectou biblioteca? |
|--:|--------------------|--------:|------------------|----------------------|
| 1 | 11000000000000101  | 36      | Sim              | Sim                  |
| 2 | 10001000000100001  | 1       | Sim              | Sim                  |
| 3 | 10011000000010001  | 20      | **Não**          | Sim                  |
| 4 | 1011010010110      | 47      | Sim              | Sim                  |
| 5 | 11100010101        | 61      | Sim              | Sim                  |
| 6 | 100111000111       | 25      | Sim              | Sim                  |
| 7 | 1010000110101      | 47      | Sim              | Sim                  |
| 8 | 110101100001       | 53      | Sim              | Sim                  |
| 9 | 1110010100111      | 27      | Sim              | Sim                  |
|10 | 10000011101001     | 32      | Sim              | Sim                  |

### 3.4 O tal “ponto cego”

- Em todos os grupos, o único caso que passou despercebido pelo nosso CRC foi justamente quando injetamos um padrão idêntico ao gerador daquela matrícula. O algoritmo da biblioteca flagrou o erro, então sabemos que a limitação não é do CRC-16 em si, e sim de alguma etapa da nossa divisão.
- Como cada falha apareceu em apenas um dos dez testes (o que coincidiu com o gerador), o relatório final deve apontar explicitamente qual teste foi esse e descrever o padrão/posição usados, como o enunciado pede.
- Não vimos nenhum caso em que a biblioteca falhasse. Isso reforça que precisamos revisar a lógica de atualização da janela depois do XOR ou a forma como concatenamos bits no quadro.

## 4. Próximos passos

1. Ajustar o loop de benchmarks para usar os tamanhos pedidos (1500, 4500, 9000 bytes) e registrar no notebook o hardware + interpretação dos gráficos.
2. Corrigir `aplicar_erro` e tornar o sorteio dos padrões/posições realmente aleatório cada vez que rodarmos os 10 testes.
3. Revisitar `calcular_crc_manual` para descobrir por que rajadas iguais ao gerador geram resto zero; depois, documentar o “ponto cego” em texto.
4. Preencher a planilha da Parte 4.1 e citar no notebook onde ela pode ser encontrada.

Com esses ajustes, a história fica completa: temos os códigos, os dados e uma análise clara tanto da parte de desempenho quanto dos limites práticos do CRC que implementamos.

# Relatório CRC - Lab 01

## Padrões de erro utilizado
Os 10 padrões testados (ordem usada nos testes):
1. 11000000000000101
2. 10001000000100001
3. 10011000000010001
4. 1011010010110
5. 11100010101
6. 100111000111
7. 1010000110101
8. 110101100001
9. 1110010100111
10. 10000011101001

## Posições reportadas por aluno (pos = índice informado na execução):
- André: 139,147,165,120,93,85,134,55,66 (detectados); 168 não detectado.
- Telmano: 62,139,184,2,9,47,176,219,180 (detectados); 102 não detectado.
- Vinicius: 34,118,119,106,13,94,35,182,18 (detectados); 176 não detectado.
- Adriano: 36,1,47,61,25,47,53,27,32 (detectados); 20 não detectado.

## Detecção e falhas
- Erros detectados: para cada aluno, 9 dos 10 padrões foram sinalizados pelas duas implementações (manual e biblioteca CRC16.MODBUS).
- Erros não detectados: 4 casos no total, sempre com a implementação manual zerando o CRC (falso negativo), enquanto a biblioteca retornou CRC diferente de zero. Casos:
  - André: padrão 2 em pos 168.
  - Telmano: padrão 1 em pos 102.
  - Vinicius: padrão 2 em pos 176.
  - Adriano: padrão 3 em pos 20.

- A única falha observada foi na implementação manual; a biblioteca detectou todos os padrões inseridos.

## Limitações e comparação
- Limitação estrutural do CRC: não detecta padrões de erro que sejam múltiplos do polinômio gerador. Isso explica porque alguns padrões específicos podem zerar o resto.
- Os falsos negativos indicam que a rotina manual não está alinhada com o polinômio/offset do CRC16.MODBUS (ou há bug no cálculo). Mesmo assim, a biblioteca marcou todos os quadros corrompidos.
- O transmissor e o verificador precisam usar exatamente o mesmo polinômio e convenções; qualquer discrepância pode deixar passar erros.

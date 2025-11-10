# Teste de Espaço Vetorial (CLI)

Este utilitário em Python testa, por amostragem, os 8 axiomas de espaço vetorial para conjuntos de vetores ou matrizes, com operações configuráveis. Você pode usá-lo totalmente via terminal (PowerShell no Windows).

## Requisitos

- Python 3.9+
- Pacotes:
  - numpy

Instale as dependências:

```powershell
pip install -r requirements.txt
```

## Uso básico

Ajuda geral:

```powershell
python .\main.py --help
```

Testar o espaço vetorial padrão (adição normal, multiplicação escalar normal) em R^3 com 200 ensaios:

```powershell
python .\main.py --tipo vetor --dim 3 --ensaios 200 --operacoes padrao
```

Testar matrizes 2x2 com operações padrão:

```powershell
python .\main.py --tipo matriz --rows 2 --cols 2 --operacoes padrao
```

## Presets de operações

- `padrao`: u + v = u + v, k * u = k u (o padrão de espaços vetoriais).
- `soma_deslocada`: u ⊕ v = u + v + 1 (bias de uns); viola axiomas (útil para ver falhas).
- `escalar_estranho`: k ⊗ u = (k+1) u; também viola axiomas.

Exemplo (mostrar falha):

```powershell
python .\main.py --tipo vetor --dim 3 --operacoes soma_deslocada --ensaios 50
```

## Elementos e zero personalizados

Você pode fornecer uma lista de elementos (para amostragem) e/ou o elemento zero via JSON:

- `--elementos`: string JSON com uma lista de vetores/matrizes.
- `--elementos-arquivo`: caminho de um arquivo JSON contendo a lista.
- `--zero`: elemento zero em JSON.

Exemplos (vetores 2D):

```powershell
# elementos explícitos (lista de vetores)
python .\main.py --tipo vetor --dim 2 --elementos "[[0,0],[1,2],[2,4]]" --zero "[0,0]"
```

Exemplo (matrizes 2x2 via arquivo):

Arquivo `matrizes.json`:

```json
[[[1,0],[0,1]], [[0,0],[0,0]], [[2,3],[4,5]]]
```

Comando:

```powershell
python .\main.py --tipo matriz --rows 2 --cols 2 --elementos-arquivo .\matrizes.json
```

## Escolha imediata da operação

Se você não passar `--operacoes`, ao iniciar o programa ele já exibirá um menu para escolher:

```
== Seleção de Operações ==
  1) padrao         (u+v, k*u)
  2) soma_deslocada (u+v+1)
  3) escalar_estranho ((k+1)*u)
Opção [1/2/3] [1]:
```

Basta digitar 1, 2 ou 3 (Enter escolhe 1). Isso ocorre mesmo no modo não interativo sempre que você omite `--operacoes`.

## Operações customizadas (avançado)

Use lambdas em string com `--unsafe-eval` (cuidado: avalia código):

```powershell
# Adição padrão, escalar padrão (apenas como exemplo)
python .\main.py --tipo vetor --dim 3 --unsafe-eval --add "lambda u,v: u+v" --sm "lambda u,k: k*u"
```

As lambdas recebem:
- `add(u, v)` → retorna mesmo tipo de `u`.
- `sm(u, k)` → retorna mesmo tipo de `u`.

Você pode usar `np` (NumPy) dentro das lambdas.

## Entrada manual simplificada (apenas números)

Se você quer sempre digitar os números dos vetores/matrizes, sem precisar passar JSON ou entrar no modo interativo completo, use:

```powershell
python .\main.py --entrada-manual
```

Esse modo pergunta somente:
- Tipo (vetor/matriz) e dimensões (se não informados por flags),
- Elemento zero (opcional),
- Lista de elementos (n vetores/matrizes),
- Operações (menu instantâneo),
- Número de ensaios (se não informado).

Você também pode combinar flags para adiantar escolhas:

```powershell
# Vetor 3D, perguntando apenas zero, elementos e operações
python .\main.py --entrada-manual --tipo vetor --dim 3 --ensaios 50

# Matriz 2x3
python .\main.py --entrada-manual --tipo matriz --rows 2 --cols 3
```

## Tolerâncias e reprodutibilidade

- `--rtol` e `--atol` controlam a comparação numérica (via `numpy.allclose`).
- `--seed` fixa a semente do gerador aleatório para reprodutibilidade.

## Códigos de saída

- `0` → todos os axiomas passaram.
- `1` → algum axioma falhou.
- `2` → erro de execução (ex.: shape inválido, JSON malformado).

## Exemplos rápidos

```powershell
# R^3 padrão, 100 ensaios
python .\main.py --tipo vetor --dim 3 --operacoes padrao --ensaios 100

# Matrizes 3x2 padrão
python .\main.py --tipo matriz --rows 3 --cols 2 --operacoes padrao

# Mostrar falhas com operações não lineares
python .\main.py --tipo vetor --dim 2 --operacoes escalar_estranho --ensaios 50
```

## Observações

- O método é probabilístico: elementos são amostrados ao acaso (ou da lista fornecida). Aumente `--ensaios` para maior confiança.
- Ao fornecer elementos, garanta que todos tenham o shape correspondente ao tipo escolhido.

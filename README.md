# Classificador Linear por Mínimos Quadrados — Reta Separadora

**Disciplina:** SPOMVAL — Vetores, Geometria Analítica e Álgebra Linear
**Curso:** Sistemas de Informação — 4º semestre
**Docente:** Josceli
**Grupo 3 — Tema 2**
**Integrantes:**
- Marcos Paulo — [@MpSantana31](https://github.com/MpSantana31)
- Luiz Otávio — [@Luizzzzzzzz](https://github.com/Luizzzzzzzz)

Trabalho de Álgebra Linear: classificador linear binário em 2D, treinado por
mínimos quadrados ou perceptron. O programa lê um CSV, monta o sistema normal
`AᵀAx = Aᵀb`, resolve por eliminação de Gauss com pivoteamento parcial,
classifica novos pontos pelo sinal de `w·p + b` e gera os gráficos do
relatório.

## Instalação

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

## Testes

```bash
python -m unittest discover -s tests
```

## Modo padrão: roda tudo

```bash
python main.py
```

Sem argumentos, o programa:

1. garante os CSVs em `dados/` — baixa **banknote** (UCI 267) e **WDBC**
   (UCI 17) se faltarem, e gera os dois casos sintéticos usados nos testes;
2. roda **mínimos quadrados** e **perceptron** nos dois datasets;
3. gera os gráficos dos dois datasets em `graficos/<dataset>/<modelo>/`
   (coloridos e `_pb`) e escreve `saida/resultados.csv`.

Métricas do MQ no modo padrão:

| Conjunto | `det(AᵀA)` | Resíduo `||Az−b||²` |
|:--|--:|--:|
| `banknote` | `6.678269e+11` | `549.690918` |
| `wdbc` | `3.775219e+10` | `229.493476` |

Os casos sintéticos `caso_1_separavel` e `caso_2_degenerado` são gerados
junto, mas ficam fora do modo padrão; eles são usados nos comandos de teste
abaixo.

## Resultados

| Dataset | Modelo | n | Acurácia | Baseline | Ganho |
|:--|:--|--:|--:|--:|--:|
| banknote | MQ | 1.372 | 88,1% | 55,5% | +32,6pp |
| banknote | Perceptron | 1.372 | 86,7% | 55,5% | +31,2pp |
| wdbc | MQ | 569 | 88,4% | 62,7% | +25,7pp |
| wdbc | Perceptron | 569 | 82,1% | 62,7% | +19,3pp |

Gráfico comparativo: `graficos/comparacao.png` (e `graficos/comparacao_pb.png`),
gerado no modo padrão. O MQ supera o perceptron nos dois datasets.

## Comandos individuais

Nos exemplos abaixo, `--sem-graficos` mantém a saída curta. Remova a flag para
gerar também os PNGs do conjunto e do modelo escolhidos.

### 1. Notas falsas (UCI 267)

```bash
python main.py --sem-graficos
```

Saída esperada:

```text
=== MÍNIMOS QUADRADOS | dados/banknote.csv ===
Pontos: 1372 | +1: 610 | -1: 762
Baseline (maioria): 55,5%
w = (-0.228358, -0.046126) | b = 0.076930
Reta: -0.98x₁ -0.20x₂ +0.33 = 0
Acurácia: 88,1% (1209/1372) | ganho: +32,6pp
VP=515 VN=694 FP=68 FN=95
Sensibilidade: 84,4% | Especificidade: 91,1%
det(AᵀA) = 6.678269e+11
Resíduo ||Az-b||² = 549.690918
```

### 2. Câncer de mama — WDBC (UCI 17)

```bash
python main.py --csv dados/wdbc.csv --colunas-x radius_mean texture_mean --sem-graficos
```

Saída esperada:

```text
=== MÍNIMOS QUADRADOS | dados/wdbc.csv ===
Pontos: 569 | +1: 212 | -1: 357
Baseline (maioria): 62,7%
w = (0.182730, 0.044950) | b = -3.703388
Reta: +0.97x₁ +0.24x₂ -19.68 = 0
Acurácia: 88,4% (503/569) | ganho: +25,7pp
VP=158 VN=345 FP=12 FN=54
Sensibilidade: 74,5% | Especificidade: 96,6%
det(AᵀA) = 3.775219e+10
Resíduo ||Az-b||² = 229.493476
```

O CSV do WDBC traz as 30 features. O padrão usa `radius_mean` ×
`texture_mean`; `radius_worst` × `texture_worst` chega a 91,6%. Como o
positivo é **maligno**, a sensibilidade de 74,5% significa 54 falsos
negativos — o relatório deve discutir o limiar e o custo desse erro.

## Casos de teste sintéticos

Os dois CSVs abaixo são gerados automaticamente por `python main.py`, mas
ficam fora do modo padrão.

### Caso separável

```bash
python main.py --csv dados/caso_1_separavel.csv --colunas-x x1 x2 --sem-graficos
```

Saída esperada:

```text
=== MÍNIMOS QUADRADOS | dados/caso_1_separavel.csv ===
Pontos: 40 | +1: 20 | -1: 20
Baseline (maioria): 50,0%
w = (0.212766, 0.297872) | b = 0.000000
Reta: +0.58x₁ +0.81x₂ +0.00 = 0
Acurácia: 100,0% (40/40) | ganho: +50,0pp
VP=20 VN=20 FP=0 FN=0
Sensibilidade: 100,0% | Especificidade: 100,0%
det(AᵀA) = 1.880000e+05
Resíduo ||Az-b||² = 2.127660
```

### Caso degenerado (não separável)

```bash
python main.py --csv dados/caso_2_degenerado.csv --colunas-x x1 x2 --sem-graficos
```

Saída esperada:

```text
=== MÍNIMOS QUADRADOS | dados/caso_2_degenerado.csv ===
Pontos: 120 | +1: 61 | -1: 59
Baseline (maioria): 50,8%
w = (-0.013223, -0.006612) | b = 0.016667
Reta: -0.89x₁ -0.45x₂ +1.13 = 0
Acurácia: 50,0% (60/120) | ganho: -0,8pp
VP=37 VN=23 FP=36 FN=24
Sensibilidade: 60,7% | Especificidade: 39,0%
det(AᵀA) = 1.756920e+08
Resíduo ||Az-b||² = 119.702204
```

Esse caso é intencionalmente não separável por uma reta. O MQ encontra uma
fronteira, mas a acurácia fica no nível do acaso — é o comportamento
degenerado usado na defesa do trabalho.

## Perceptron

O perceptron usa a mesma regra de decisão, mas aprende só quando erra. A
ordem dos pontos é embaralhada a cada época com semente fixa, e o treino para
quando não há erros.

```bash
python main.py --csv dados/caso_1_separavel.csv --colunas-x x1 x2 \
  --modelo perceptron --sem-graficos
```

Saída esperada:

```text
=== PERCEPTRON | dados/caso_1_separavel.csv ===
Pontos: 40 | +1: 20 | -1: 20
Baseline (maioria): 50,0%
w = (0.010000, 0.025000) | b = -0.010000
Reta: +0.37x₁ +0.93x₂ -0.37 = 0
Acurácia: 100,0% (40/40) | ganho: +50,0pp
VP=20 VN=20 FP=0 FN=0
Sensibilidade: 100,0% | Especificidade: 100,0%
Convergiu: sim | épocas: 2
Erros por época: 1 -> 0
```

Nos dois datasets reais, o perceptron não converge em 100 épocas porque as
classes não são perfeitamente separáveis: banknote 86,7% e WDBC 82,1%.

```bash
python main.py --modelo perceptron --sem-graficos
```

Resultado esperado no banknote: 86,7%, `Convergiu: não`, 100 épocas.

## Normalização z-score

Use `--normalizar` para treinar com cada feature de média 0 e desvio 1. O
programa converte `w` e `b` de volta para a escala original, então a reta e os
gráficos continuam nas unidades dos dados.

```bash
python main.py --normalizar --sem-graficos
```

Resultado esperado: mesma acurácia de 88,1%, mesmo resíduo (`549.690918`) e
`det(AᵀA) = 2.402597e+09` no espaço normalizado.

A volta para a escala original usa:

```text
w_original_j = w_j / desvio_j
b_original   = b − Σ (w_j · média_j / desvio_j)
```

A normalização também ajuda o perceptron: com `--normalizar`, ele chega a
88,0% no banknote (mas ainda não converge em 100 épocas).

Como o programa avalia no próprio conjunto de treino, as médias e os desvios
são calculados sobre todos os pontos. Se houver separação treino/teste, calcule
esses valores apenas no treino e reaproveite no teste para não vazar informação.

## Classe positiva explícita

Os CSVs do repositório já usam rótulos `-1/+1`, então não precisam de nada.
Para dados com texto ou `0/1`, declare qual valor é a classe `+1`:

```bash
python main.py --csv dados/meu.csv --colunas-x a b --classe-positiva spam
```

Sem `--classe-positiva`, rótulos fora de `-1/+1` geram erro em vez de depender
da ordem das linhas.

## Gráficos

Cada execução gera, em `graficos/<dataset>/<modelo>/`:

```text
graficos/
  banknote/
    mq/
      dispersao.png / dispersao_pb.png
      erros.png / erros_pb.png
      histograma.png / histograma_pb.png
      confusao.png / confusao_pb.png
      curva_limiar.png / curva_limiar_pb.png
    perceptron/
      ...
  wdbc/
    mq/
      ...
    perceptron/
      ...
```

Exemplos: `graficos/banknote/mq/dispersao.png`,
`graficos/wdbc/mq/erros.png`, `graficos/caso_1_separavel/perceptron/dispersao.png`.

Cada arquivo também ganha uma versão em preto e branco com sufixo `_pb`
(por exemplo, `dispersao_pb.png`), adequada para impressão em journals.

A pasta `graficos/` está no `.gitignore`; os PNGs são artefatos locais e não
vão para o repositório.

## Classificar um ponto novo

```bash
python main.py --sem-graficos --ponto 1.5 2.0
```

O ponto usa as features padrão do dataset principal (`variance` × `skewness`):

```text
Pontos novos:
  (1.5, 2) -> f(p) = -0.3579 -> Classe -1
```

## Opções da CLI

| Flag | Padrão | Função |
|:--|:--|:--|
| `--csv` | `dados/banknote.csv` | CSV de entrada |
| `--colunas-x` | `variance skewness` | duas features |
| `--coluna-y` | `classe` | coluna de rótulos |
| `--classe-positiva` | — | valor que representa `+1` |
| `--modelo` | `mq` | `mq` ou `perceptron` |
| `--normalizar` | desligado | z-score + volta à escala original |
| `--taxa-aprendizado` | `0.01` | taxa do perceptron |
| `--epocas` | `100` | máximo de épocas do perceptron |
| `--semente` | `42` | semente do embaralhamento |
| `--dir-saida` | `graficos` | pasta-base dos PNGs |
| `--titulo` | derivado do CSV | título dos gráficos |
| `--ponto` | — | classifica um ponto novo (repetível) |
| `--tamanho-ponto` | automático | tamanho dos marcadores |
| `--sem-graficos` | desligado | só métricas |

Use `python main.py --help` para ver tudo.

## Dados

A pasta `dados/` é criada pelo próprio `main.py` (download dos datasets e
geração dos casos sintéticos) e está no `.gitignore`; os CSVs não vão para o
repositório.

- `dados/banknote.csv` — UCI 267 (Banknote Authentication), 1.372 cédulas.
  Features: `variance`, `skewness`, `curtosis`, `entropy`.
  Rótulo: `-1` = genuína, `+1` = falsa. O comando padrão usa
  `variance` × `skewness`.
- `dados/wdbc.csv` — UCI 17 (Breast Cancer Wisconsin Diagnostic), 569 amostras
  e as 30 features originais. Rótulo: `-1` = benigno, `+1` = maligno.
  O comando padrão usa `radius_mean` × `texture_mean`.
- `dados/caso_1_separavel.csv` — 40 pontos sintéticos, separáveis pela reta
  `x₁ + x₂ = 0`.
- `dados/caso_2_degenerado.csv` — 120 pontos sintéticos com rótulos ruidosos,
  não separáveis linearmente.

## Matemática do método

Para cada ponto de treino `pᵢ = (xᵢ, yᵢ)`, monta-se a linha
`aᵢ = [1, xᵢ, yᵢ]` e o rótulo `bᵢ ∈ {−1, +1}`. Com isso:

```text
A = [a₁; a₂; ...; aₙ]   (n × 3)
b = [b₁; b₂; ...; bₙ]   (n × 1)
z = [b, w₁, w₂]          (3 × 1)
```

O método dos mínimos quadrados minimiza `||Az − b||²`. O mínimo satisfaz o
sistema normal:

```text
AᵀA z = Aᵀb
```

`AᵀA` é 3×3 e é resolvida por eliminação de Gauss com pivoteamento parcial,
implementada em `classificador/minimos_quadrados.py` — sem
`numpy.linalg.lstsq`, `sklearn` ou `scipy`.

O treino devolve:

- `det(AᵀA)`: determinante da matriz normal. Perto de zero indica sistema
  mal condicionado ou singular.
- `||Az − b||²`: soma dos erros quadráticos da solução encontrada.

A classificação de um ponto `p` é feita pelo sinal do escore:

```text
f(p) = w·p + b = w₁x + w₂y + b
f(p) ≥ 0  →  Classe +1
f(p) < 0  →  Classe −1
```

O produto interno `w·p` é o conceito central do Tema 2. A reta de separação é
`w₁x + w₂y + b = 0`; o vetor `w` é normal a ela.

**Ponto de defesa:** mínimos quadrados minimiza o erro quadrático em relação
aos rótulos, mas **não maximiza a margem**. Quem maximiza a margem é a SVM.
Por isso o MQ pode errar pontos mesmo quando existe uma reta que separa os
dados, e não garante a maior folga (margem) entre as classes.

## Documentação teórica

A pasta `docs/` tem anotações curtas, em linguagem simples, com a matemática do
trabalho, que serviram de base para a construção do relatório:

- [01 — Produto interno e hiperplano](docs/01-produto-interno-e-hiperplano.md)
- [02 — Mínimos quadrados](docs/02-minimos-quadrados.md)
- [03 — Classificação e métricas](docs/03-classificacao-e-metricas.md)
- [04 — Perceptron e normalização](docs/04-perceptron-e-normalizacao.md)
- [05 — Exemplo numérico completo](docs/05-exemplo-numerico-completo.md)
- [Classificador Linear Relatório](docs/Classificador-Linear-Relatorio.pdf)

## Estrutura do repositório

```text
main.py                         CLI, modo “tudo” e pipeline
classificador/
  dados.py                      leitura de CSV e classe positiva explícita
  modelo.py                     f(x) = w·x + b e regra de decisão
  minimos_quadrados.py          sistema normal, Gauss, determinante e resíduo
  normalizacao.py               z-score e volta à escala original
  treinamento.py                perceptron com shuffle e flag convergiu
  avaliacao.py                  acurácia, matriz de confusão, métricas por limiar
  grafico.py                    pontos, reta, regiões, histograma, matriz e curva
tests/                        testes da lógica central (unittest)
  test_core.py
docs/                         anotações teóricas e relatório formal do trabalho
  01-produto-interno-e-hiperplano.md
  02-minimos-quadrados.md
  03-classificacao-e-metricas.md
  04-perceptron-e-normalizacao.md
  05-exemplo-numerico-completo.md
  Classificador-Linear-Relatorio.pdf
dados/                        gerado por main.py; ignorado pelo git
  banknote.csv
  wdbc.csv
  caso_1_separavel.csv
  caso_2_degenerado.csv
graficos/                     gerado por main.py; ignorado pelo git
  <dataset>/<modelo>/*.png
  comparacao.png
saida/                        tabela comparativa versionada
  resultados.csv
requirements.txt
```

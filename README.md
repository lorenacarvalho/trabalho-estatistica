# Artigo de Estatística Aplicada — Modelagem do Consumo de Combustível

Artigo científico desenvolvido para a disciplina de **Estatística Aplicada (2026.1)** do
Departamento de Ciência da Computação da **UFCG**, aplicando **regressão linear múltipla
(MRLM)** à base de dados `Auto`.

O relatório é escrito em R Markdown e compilado em PDF no template oficial do
[LEA/UFCG](modeloUFCG.cls), com análise, tabelas e figuras geradas dinamicamente a partir
do código R embutido no documento.

## Equipe

- Anna Lívia dos Santos Macedo Costa
- Lorena Nascimento Carvalho Gama
- Mirelle Maria de Oliveira Rocha

**Professores:** Profa. Dra. Amanda Gomes · Prof. Dr. Jerfson Bruno · Prof. Dr. Gilberto Matos

## A base de dados

[`05_Auto_ISLR.csv`](05_Auto_ISLR.csv) — características técnicas de **392 automóveis** de
modelos entre 1970 e 1982, proveniente do pacote `ISLR` do R. Reúne dados de motor, peso,
desempenho e origem do veículo.

A variável resposta é **`mpg`** (consumo em milhas por galão — quanto maior, mais econômico
o veículo).

| Variável | Descrição | Unidade |
|---|---|---|
| `mpg` | Consumo de combustível — **variável resposta** | milhas/galão |
| `cylinders` | Número de cilindros do motor (3 a 8) | contagem |
| `displacement` | Cilindrada do motor | polegadas cúbicas |
| `horsepower` | Potência do motor | cavalos (hp) |
| `weight` | Peso do veículo | libras |
| `acceleration` | Tempo para acelerar de 0 a 60 mph | segundos |
| `year` | Ano do modelo (dois últimos dígitos) | ano |
| `origin` | Origem: 1 = americano, 2 = europeu, 3 = japonês | categórica |
| `name` | Nome do veículo | texto |

Notas relevantes para a análise:

- **Não há valores ausentes.** Esta é a versão já tratada da base: o arquivo original possui
  397 registros, dos quais 5 têm `horsepower` marcado como `?` e foram removidos.
- **`origin` deve ser convertida em fator** antes da modelagem. Está codificada como inteiro
  (1/2/3), mas é uma variável qualitativa — o R a trataria erroneamente como numérica.
  Distribuição: 245 americanos, 68 europeus e 79 japoneses.
- **`name` não é utilizável como preditora** diretamente, por ser praticamente única por
  observação.
- As variáveis de porte do motor (`cylinders`, `displacement`, `horsepower`, `weight`) são
  fortemente correlacionadas entre si, o que exige diagnóstico de **multicolinearidade**
  antes de interpretar coeficientes individuais.

## Estrutura do repositório

| Arquivo | Descrição |
|---|---|
| [`Artigo-Estatistica.Rmd`](Artigo-Estatistica.Rmd) | **Documento principal** — texto do artigo e todo o código R da análise |
| [`05_Auto_ISLR.csv`](05_Auto_ISLR.csv) | Base de dados fornecida pelo professor |
| [`orientacoes.tex`](orientacoes.tex) | Checklist de aderência às exigências do trabalho, anexado ao fim do PDF |
| [`modeloUFCG.bib`](modeloUFCG.bib) | Referências bibliográficas (formato BibTeX) |
| [`modeloUFCG.cls`](modeloUFCG.cls) | Classe LaTeX do template LEA/UFCG |
| [`RS.bst`](RS.bst) | Estilo de formatação das citações |
| [`lea_logo_bw.png`](lea_logo_bw.png) | Logo do LEA usado no cabeçalho |

Os arquivos `modeloUFCG.cls`, `RS.bst` e `lea_logo_bw.png` compõem o template institucional
e **não devem ser modificados**.

Arquivos gerados pela compilação (`.pdf`, `.tex`, `.log`, `.aux`, `Artigo-Estatistica_files/`)
são descartáveis e estão listados no [`.gitignore`](.gitignore) — apenas o código-fonte é
versionado.

## Requisitos

- **R** 4.6.1 ou superior
- **Distribuição LaTeX** — TinyTeX é suficiente (`tinytex::install_tinytex()`)
- **RStudio** (opcional, mas é a forma mais prática de compilar)

Pacotes R utilizados:

```r
install.packages(c("rmarkdown", "bookdown", "knitr", "ggplot2", "ggfortify",
                   "GGally", "corrplot", "broom", "hnp", "lm.beta"))
```

O template `modeloUFCG` (v0.6.0) não está no CRAN e precisa ser instalado à parte, a partir
do repositório do Prof. Jerfson Bruno:

```r
remotes::install_github("jerfsonbruno/ModeloUFCG")
```

## Como compilar

Pelo **RStudio**: abra `Artigo-Estatistica.Rmd` e clique em **Knit**.

Pela linha de comando:

```bash
Rscript -e "rmarkdown::render('Artigo-Estatistica.Rmd')"
```

O resultado é o arquivo `Artigo-Estatistica.pdf`.

> No Windows, o R normalmente não fica no `PATH`. Se o comando acima não for reconhecido,
> use o caminho completo do executável, por exemplo
> `"C:\Program Files\R\R-4.6.1\bin\Rscript.exe"`.

## Escopo da análise

O trabalho segue o roteiro definido pelos professores (detalhado em
[`orientacoes.tex`](orientacoes.tex)):

1. Definição do problema, características dos dados e objetivo da análise
2. Descrição da base: tamanho amostral, variáveis, significado e unidades
3. Análise exploratória — dados ausentes e outliers
4. Análises uni e bivariadas — correlação, multicolinearidade e box-plots
5. Ajuste do modelo inicial com todas as variáveis explicativas
6. Seleção do melhor modelo por critérios estatísticos (AIC, BIC, teste F e *stepwise*)
7. Verificação dos pressupostos do MRLM e resolução das violações encontradas
8. Intervalo de confiança para o valor médio esperado e intervalo de predição para uma
   nova observação, ambos com interpretação prática

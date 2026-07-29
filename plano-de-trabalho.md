# Plano de Trabalho — Artigo de Estatística Aplicada

Guia passo a passo para produzir o artigo, com as atividades explicadas e divididas entre as
três integrantes da equipe. A ideia é que cada pessoa **entenda o que está fazendo e por quê**,
não apenas execute.

- **Base de dados:** [`05_Auto_ISLR.csv`](05_Auto_ISLR.csv) — 392 automóveis (1970–1982).
- **Objetivo do artigo:** explicar/prever o consumo de combustível (`mpg`) a partir das
  características do carro.
- **Onde o trabalho acontece:** o arquivo [`Artigo-Estatistica.Rmd`](Artigo-Estatistica.Rmd),
  que mistura texto e código R e gera o PDF final.

---

## Como dividimos o trabalho

O artigo tem uma ordem natural: primeiro **conhecemos os dados**, depois **construímos o modelo**,
e por fim **validamos e usamos o modelo**. Dividimos essas três frentes, uma por pessoa:

| Frente | Responsável (sugestão) | Do que trata |
|---|---|---|
| **A — Contexto e exploração dos dados** | Anna Lívia | Introdução, descrição da base e análise exploratória (itens 1 a 4) |
| **B — Construção e seleção do modelo** | Lorena | Modelo inicial, multicolinearidade e escolha do melhor modelo (itens 5 e 6) |
| **C — Validação e uso do modelo** | Mirelle | Diagnóstico, correção de problemas, variáveis categóricas e previsões (itens 7 e 8) |

> A divisão por nome é uma **sugestão** — troquem conforme a preferência de cada uma. O importante
> é que cada frente tenha uma dona principal, mas as três revisam o conjunto no final.

**Ordem das coisas (dependências):** a Frente A pode começar já. A Frente B usa os dados
compreendidos pela A. A Frente C usa o modelo escolhido pela B. Por isso o ideal é a A adiantar a
exploração enquanto B e C estudam a teoria e preparam o código das suas partes.

---

## FRENTE A — Contexto e exploração dos dados
**Responsável sugerida: Anna Lívia**

Objetivo desta frente: apresentar o problema e mostrar, com números e gráficos, *como os dados
se comportam* antes de qualquer modelo. Boa parte já está adiantada no documento
[`analise-exploratoria.md`](analise-exploratoria.md) — aqui a tarefa é transformá-lo em texto de
artigo.

### A1. Introdução (item 1)
*O que é:* o parágrafo de abertura que situa o leitor.
1. Explicar o tema: o que determina o consumo de combustível de um carro?
2. Apresentar a base `Auto` (392 carros dos anos 1970–82, do pacote ISLR).
3. Declarar o objetivo: modelar `mpg` (milhas por galão) e identificar quais características mais
   influenciam a economia.
4. Listar, em um parágrafo, as etapas que o artigo vai percorrer.

### A2. Descrição da base (item 2)
*O que é:* a "ficha técnica" dos dados.
1. Informar o tamanho: **n = 392 observações**, 9 variáveis, **sem dados ausentes**.
2. Montar uma tabela com cada variável, seu significado e unidade (já pronta no README).
3. Registrar um cuidado importante: a base é a versão *limpa* (o original tinha 397 linhas; 5 com
   `horsepower` faltante foram removidas).

### A3. Análise exploratória — dados ausentes e outliers (item 3)
*O que é:* uma primeira "olhada" nos dados para achar problemas.
1. Confirmar, **com código R**, que não há valores ausentes (`colSums(is.na(dados))`).
2. Fazer histograma de `mpg` → mostrar que a distribuição é **assimétrica à direita** (isso
   depois justifica a transformação log).
3. Fazer box-plots para procurar valores extremos (outliers).
4. Comentar o que se observa (ex.: há carros muito econômicos formando uma cauda).

### A4. Análise bivariada — correlação e box-plots (item 4, parte descritiva)
*O que é:* olhar as variáveis **em pares**, para ver quais andam juntas.
1. Gerar a matriz de correlação (`corrplot`) e a matriz de dispersão (`ggpairs`).
2. Fazer box-plot de `mpg` por `origin` (mostra que japoneses/europeus são mais econômicos).
3. Escrever a leitura dos dois achados centrais:
   - as variáveis de motor têm forte relação **negativa** com o consumo;
   - essas variáveis são muito correlacionadas **entre si** → primeiro indício de
     **multicolinearidade** (o assunto que a Frente B vai aprofundar).

**Entregáveis da Frente A:** seções de Introdução, Descrição da base e Análise exploratória
escritas no `.Rmd`, com tabelas e figuras.

---

## FRENTE B — Construção e seleção do modelo
**Responsável sugerida: Lorena**

Objetivo desta frente: construir o modelo de regressão, diagnosticar o problema de
multicolinearidade que a exploração antecipou, e escolher — com critério estatístico — o melhor
modelo. É o "coração" do artigo.

### B1. Entender e ajustar o modelo inicial (item 5)
*O que é:* uma regressão linear é uma fórmula que estima `mpg` a partir das outras variáveis; cada
variável ganha um "peso" (coeficiente).
1. Converter `origin` em **fator** (categórica), antes de tudo.
2. Ajustar o modelo com **todas** as explicativas:
   `mpg ~ cylinders + displacement + horsepower + weight + acceleration + year + origin`.
3. Apresentar a tabela de coeficientes.

### B2. Diagnosticar multicolinearidade (item 4, parte técnica)
*O que é:* multicolinearidade acontece quando duas ou mais explicativas carregam quase a mesma
informação — isso deixa os coeficientes instáveis e sem sentido.
1. Calcular o **VIF** (Fator de Inflação de Variância) de cada variável. Regra: VIF > 10 = problema
   sério. Na nossa base, `displacement` dá **~23** e outras três passam de 10.
2. Mostrar o sintoma clássico: no modelo completo, `displacement` aparece com sinal **positivo**
   (sugerindo, absurdamente, que motor maior economiza combustível) — sinal trocado é a "impressão
   digital" da multicolinearidade.
3. Explicar, em texto, o que isso significa e por que precisa ser resolvido.

### B3. Comparar modelos candidatos e selecionar (item 6)
*O que é:* testar versões do modelo com menos variáveis e escolher a melhor por critério objetivo.
1. Criar modelos candidatos removendo as variáveis colineares (ex.: manter `weight` e descartar
   `displacement`/`cylinders`/`horsepower`).
2. Comparar os candidatos por três critérios:
   - **AIC** e **BIC** — quanto menor, melhor (equilibram ajuste × simplicidade);
   - **teste F** — compara dois modelos "aninhados".
3. Rodar também a seleção automática `step()` e mostrar um ponto interessante: ela **mantém** as
   variáveis colineares, porque o AIC pune complexidade mas não multicolinearidade. Isso justifica
   escolher o modelo pensando na **interpretação**, não só no ajuste.
4. Chegar ao modelo reduzido **`mpg ~ weight + year + origin`** (mantém quase todo o poder
   explicativo e elimina a multicolinearidade).

**Entregáveis da Frente B:** seções de Modelo inicial, Multicolinearidade/VIF e Seleção do modelo,
com as tabelas de VIF, comparação de modelos e testes.

---

## FRENTE C — Validação e uso do modelo
**Responsável sugerida: Mirelle**

Objetivo desta frente: verificar se o modelo escolhido **respeita as regras** da regressão linear,
corrigir o que estiver errado, incorporar a variável de origem corretamente e, por fim, **usar o
modelo para prever**.

### C1. Verificar os pressupostos e resolver violações (item 7 — o mais valorizado)
*O que é:* a regressão linear só é confiável se os "resíduos" (erros do modelo) tiverem certas
propriedades: variância constante, normalidade e independência.
1. Gerar os gráficos de diagnóstico (`autoplot`) e o **gráfico de envelope simulado**.
2. Testar **homocedasticidade** (variância constante) — o teste vai acusar **violação**
   (heterocedasticidade).
3. **Resolver:** aplicar a transformação **`log(mpg)`**. Mostrar que, após o log, o teste passa,
   o ajuste melhora e o **Box-Cox** confirma que o log é a transformação indicada.
4. Explicar em palavras simples por que isso acontece (consumo se comporta como um "inverso" do
   peso — daí o log linearizar a relação).

### C2. Identificar pontos atípicos, de alavanca e influentes
*O que é:* algumas observações "puxam" o modelo mais que as outras; convém checar se distorcem os
resultados.
1. Calcular resíduos estudentizados, alavancagem e **distância de Cook**.
2. Listar as observações que ultrapassam os limiares.
3. Reajustar o modelo sem essas observações e comparar: se os coeficientes quase não mudam,
   concluir que são variação natural (não erros) e mantê-las.

### C3. Variáveis dummy — a origem do carro (parte do item 6/8)
*O que é:* como `origin` é categórica (3 níveis), o R a transforma em variáveis 0/1 ("dummies"),
adotando uma categoria como referência.
1. Explicar a codificação: uma categoria vira **referência** (fica embutida no intercepto) e as
   outras são comparadas a ela.
2. Interpretar os coeficientes (ex.: quanto um carro japonês é mais econômico que um americano).
3. Demonstrar, com `relevel()`, que **trocar a categoria de referência muda a interpretação, mas
   não as previsões** do modelo.

### C4. Previsões — intervalos de confiança e de predição (itens 8a e 8b)
*O que é:* usar o modelo final para estimar o consumo em dois carros hipotéticos.
1. Escolher 2 conjuntos de valores das explicativas, **dentro** da faixa observada nos dados.
2. Calcular o **intervalo de confiança** (para o consumo **médio** de carros com aquelas
   características) — item 8a.
3. Calcular o **intervalo de predição** (para **um carro individual** novo) — item 8b.
4. Interpretar a diferença: o de predição é sempre mais largo, porque soma a incerteza individual.

**Entregáveis da Frente C:** seções de Diagnóstico, Pontos influentes, Variáveis dummy e Previsões,
com gráficos de resíduos, envelope e tabelas de intervalos.

---

## Tarefas coletivas (as três juntas)

Estas partes ninguém faz sozinha — combinem um momento para fechar juntas:

| Tarefa | Descrição |
|---|---|
| **Metodologia** | Cada uma escreve o "como fizemos" da sua frente; depois juntam num texto único. |
| **Conclusão** | Resumir os principais achados do artigo — feito só no final, quando tudo estiver pronto. |
| **Revisão cruzada** | Cada uma lê a parte da outra, conferindo se está compreensível e coerente. |
| **Compilação (knit)** | Gerar o PDF final no RStudio (botão *Knit*), conferir tabelas, figuras e referências. |
| **Checklist final** | Conferir, item a item, o arquivo [`orientacoes.tex`](orientacoes.tex) — hoje ele ainda cita o exemplo `iris` e precisa ser atualizado para a base `Auto`. |

---

## Sugestão de sequência (para não travar)

1. **Todas juntas:** ler este plano e o [`analise-exploratoria.md`](analise-exploratoria.md) para
   entender a base.
2. **Frente A** adianta a exploração e a descrição (não depende de ninguém).
3. **Frente B** começa a modelagem assim que a A entender bem as variáveis.
4. **Frente C** prepara a teoria (pressupostos, dummies, previsões) e entra em ação quando a B
   definir o modelo final.
5. **Todas juntas:** metodologia, conclusão, revisão e compilação do PDF.

> **Dica:** guardem as dúvidas que aparecerem para levar à professora no acompanhamento — há um
> conjunto de perguntas sugeridas que podemos anexar aqui quando quiserem.

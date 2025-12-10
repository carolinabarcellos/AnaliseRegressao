# Análise de Regressão: Sono em Mamíferos

Este repositório contém o relatório e o código utilizados para analisar os fatores biológicos e comportamentais que influenciam o **tempo total de sono** (`total_sleep`) em diferentes espécies de mamíferos. O estudo foi conduzido utilizando regressão linear múltipla, técnicas modernas de seleção de variáveis (Stepwise e LASSO), transformações de Box–Cox e validação cruzada.

## 🏷️ Status e Tecnologias
![Feito em R](https://img.shields.io/badge/R-Feito%20em-%23276DC2?style=flat&logo=R&logoColor=white)
![Badge Concluído](https://img.shields.io/badge/Status-Conclu%C3%ADdo-brightgreen)

---

## 👥 Membros do Grupo

* **Carolina Penido Barcellos**
* **Gabrielly Xavier dos Santos**
* **Matheus Soares dos Santos de Freitas**

---

## 🎯 Objetivo

O objetivo principal do trabalho é identificar quais características — como peso corporal, duração da gestação, exposição a predadores, entre outras — são mais relevantes para explicar a variação no tempo de sono dos mamíferos.

A **variável resposta (Y)** é:

* `total_sleep`: número de horas de sono total por dia.

As **variáveis explicativas (X)** incluem:

* `body_wt`, `brain_wt`, `life_span`, `gestation`,
* `predation`, `exposure`, `danger`,
* além das componentes do sono (`dreaming` e `non_dreaming`) — removidas posteriormente por multicolinearidade perfeita.

---

## 🧼 1. Limpeza e Preparação dos Dados

Ulizamos a linguagem de programação R.
Foram removidas observações com valores ausentes e carregadas bibliotecas essenciais como **dplyr**, **ggplot2**, **car**, **readr**, **tidyr** e **corrplot**.

Também foi verificada a distribuição da variável resposta através de um histograma, que revelou comportamento **não normal e multimodal**.

---

## 📊 2. Estatísticas Descritivas e Correlações

Foram avaliadas:

* estatísticas descritivas das variáveis,
* correlações entre pares de variáveis explicativas (ex.: `body_wt` × `brain_wt`),
* correlação entre cada variável explicativa e `total_sleep`,
* matriz de correlação com **heatmap**.

A análise identificou diversos pares de variáveis com forte correlação, indicando **multicolinearidade**, especialmente envolvendo:

* `body_wt`, `brain_wt`, `danger` e `predation`.

---

## 📦 3. Regressão Linear Múltipla

Um primeiro modelo completo foi ajustado:

```
Y ~ body_wt + brain_wt + life_span + gestation + predation + exposure + danger
```

Os **VIFs** revelaram forte multicolinearidade. Várias rodadas de remoção de variáveis foram realizadas, mas apesar de aumentar a estabilidade, houve queda no R² ajustado.

---

## 🔀 4. Stepwise (AIC)

Foi aplicada seleção Stepwise bidirecional, que melhorou o AIC e o R² ajustado, mas **não corrigiu os problemas de multicolinearidade**.

Mesmo após testar uma versão sem `danger`, os VIFs permaneceram altos, e o desempenho foi inferior à alternativa seguinte.

---

## 🧲 5. LASSO

O método LASSO foi utilizado por ser mais robusto em presença de multicolinearidade. Usou-se validação cruzada para seleção automática do parâmetro λ.

O modelo resultante selecionou:

```
total_sleep ~ gestation + danger
```

Ambas as variáveis com coeficientes significantes.

O modelo LASSO apresentou **melhor R² ajustado e maior simplicidade**, sendo escolhido como modelo base.

---

## 🔧 6. Transformação Box–Cox

A análise dos resíduos do modelo LASSO indicou:

* não-linearidade,
* heterocedasticidade (confirmada pelo teste de Breusch-Pagan),
* normalidade dos erros adequada.

Aplicou-se uma transformação de Box–Cox na variável resposta para corrigir variância não constante.

O modelo transformado (`model_box`) apresentou:

* resíduos normalizados,
* heterocedasticidade resolvida,
* ausência de padrões sistemáticos,
* melhor adequação geral.

---

## 🔁 7. Bootstrap

Foi realizado bootstrap com **1999 reamostragens** para estimar a incerteza dos coeficientes do modelo Box–Cox.

Foram gerados:

* distribuições dos coeficientes via histogramas,
* intervalos de confiança pelo método percentil.

Os coeficientes demonstraram estabilidade sob reamostragem.

---

## 📐 8. Validação Cruzada

Para avaliar o potencial preditivo, utilizou-se validação cruzada **k-fold (k=10)** com divisão treino/teste.

A validação buscou medir:

* RMSE,
* MAE,
* R²,
  para comparar diferentes modelos preditivos.

---

## ✅ Conclusão Final

O modelo final recomendado para explicar o sono total em mamíferos foi o modelo transformado via **Box–Cox**, derivado da seleção do LASSO:

```
Y* ~ gestation + danger + life_span
```

Principais conclusões:

* A transformação Box–Cox corrigiu os últimos problemas estatísticos do modelo.
* O LASSO foi superior ao Stepwise para lidar com multicolinearidade.
* As variáveis mais importantes para explicar `total_sleep` foram:

  * **gestation**,
  * **danger**,
  * **life_span** (adicionada no modelo transformado).

O modelo final apresentou bom ajuste, erros normalizados e variância constante.

---

## 📁 Estrutura do Repositório

```
├── dataset/                     # Dados brutos utilizados na análise
│   └── mammals.csv              # Base com informações das espécies
├── codigo/                      # Código-fonte do projeto
│   └── analise_regressao.Rmd    # Arquivo principal de análise estatística
├── doc/                         # Documentação final
│   └── TP___Análise_de_Regressão.pdf   # Relatório em PDF
├── ilustracoes/                 # Imagens ilustrativas externas
├── imagens/                     # Gráficos e imagens geradas na análise
├── libs/                        # Dependências HTML/CSS/JS geradas automaticamente
├── Slides_files/                # Arquivos auxiliares dos slides (Xaringan)
│   ├── figure-html/             # Gráficos exportados dos slides
│   ├── header-attrs-2.30/       # Metadados usados pelo RMarkdown
│   └── remark-css-0.0.1/        # Estilos do Xaringan
├── .gitignore                   # Arquivos e pastas ignorados pelo Git
├── README.md                    # Documentação geral do repositório
├── Rmarkdown_Regressao.Rmd      # Relatório analítico em RMarkdown
├── Rmarkdown_Regressao.html     # Relatório renderizado em HTML
├── Slides.Rmd                   # Código dos slides da apresentação
├── Slides.html                  # Slides renderizados
└── project.Rproj                # Projeto do RStudio/Posit

```

---

## 🛠 Tecnologias Utilizadas

* R (versão 4.x)
* Bibliotecas: **dplyr**, **ggplot2**, **car**, **glmnet**, **caret**, **corrplot**, **MASS**, **tidyr**, **readr**

---

## 📜 Licença

Este projeto está licenciado sob a **MIT License**.

---

## 📬 Contato

Dúvidas, sugestões ou melhorias são bem-vindas.

---

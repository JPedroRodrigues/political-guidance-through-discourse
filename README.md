# Orientação Política Através do Discurso

Este repositório reúne uma análise exploratória de discursos parlamentares e
notícias relacionadas a partidos e temas políticos, com foco especial em como
posicionamentos sobre armas, segurança pública e legislação podem estar
associados às orientações partidárias.

## Sobre o projeto

O objetivo principal é investigar se os discursos parlamentares podem ser
posicionados no espectro político a partir de seus argumentos e embeddings.
As notícias publicadas por partidos serão usadas como uma fonte complementar
para representar posições políticas e comparar os discursos com referências
partidárias.

A análise é feita em notebooks Jupyter com uso de `pandas`, `numpy`,
`matplotlib`, `seaborn`, `torch` e `transformers`.

## Dados disponíveis

O projeto contém dois conjuntos de dados em JSON Lines:

- `content/firearm-carry-speeches-2014-2026.jsonl`: discursos e falas de
  parlamentares, com campos como deputado, partido, UF, legislatura, tipo de
  discurso, resumo, transcrição, tema e posição (`stance`).
- `content/noticia_partidos.jsonl`: notícias e textos políticos associados a
  partidos, com informações como título, texto, data de publicação, link,
  palavras-chave e partido identificado.

Esses arquivos permitem comparar discursos institucionais com menções na
mídia e observar padrões de posicionamento ao longo do tempo.

## Objetivos de análise

- identificar padrões de posicionamento partidário em temas relevantes;
- explorar como discursos parlamentares e notícias expressam argumentos sobre
  segurança, armas e políticas públicas;
- comparar dados textuais com atributos estruturados dos partidos e dos
  parlamentares;
- produzir visualizações e conclusões preliminares para uma investigação mais
  ampla.

## Embeddings disponíveis

Os argumentos dos discursos e as notícias são transformados em vetores pelo
mesmo modelo BERTimbau:
`neuralmind/bert-large-portuguese-cased`.

- `argument_embeddings.pt`: embeddings dos argumentos parlamentares;
- `news_embeddings.pt`: embeddings de 1.061 notícias;
- dimensão dos vetores: 1.024;
- pooling: média dos embeddings dos tokens (`mean pooling`);
- notícias: texto formado pela combinação do título e do corpo da notícia.

Os artefatos também armazenam metadados para permitir a associação dos
vetores aos discursos, partidos, datas, links e textos originais.

## Estratégia de modelagem

A primeira tarefa será classificar o discurso em três faixas do espectro
político, pois a base possui poucos exemplos em algumas categorias mais
específicas. A proposta de agrupamento é:

- `Esquerda`: Esquerda e Centro-esquerda;
- `Centro`: Centro;
- `Direita`: Centro-direita, Direita e Extrema-direita.

Os rótulos originais, incluindo a classificação em mais categorias, poderão
ser usados em uma análise secundária. Discursos sem espectro definido serão
retirados da etapa supervisionada, mas podem continuar na análise exploratória.

### Comparação de modelos

O experimento deve comparar modelos adequados para poucos exemplos e vetores
de alta dimensão:

1. `DummyClassifier` como referência mínima;
2. `LogisticRegression` como baseline oficial;
3. `LinearSVC` como modelo principal;
4. `KNeighborsClassifier` com distância de cosseno como comparação baseada em vizinhança.

A normalização L2 dos embeddings e o balanceamento das classes são aplicados
dentro de um `Pipeline`. No `LinearSVC`, o valor de `C` é escolhido dentro dos
folds internos da validação aninhada. No KNN, `n_neighbors` e os pesos dos
vizinhos são escolhidos pelo mesmo procedimento.

### Validação

Como vários discursos pertencem ao mesmo evento e podem ter o mesmo contexto,
a divisão não será feita apenas de forma aleatória por linha. Será usada
validação cruzada estratificada e agrupada por `event_id`.

A métrica principal será `macro-F1`, acompanhada de balanced accuracy e F1 por
classe. A avaliação final usa validação cruzada aninhada e agrupada por
`event_id`: os folds internos escolhem hiperparâmetros e os folds externos
funcionam como teste não observado. Uma análise adicional agrupada por
`deputy_id` pode verificar se o modelo está aprendendo posicionamentos ou
apenas o estilo de determinados parlamentares.

### Resultados atuais

Com 207 discursos que possuem espectro político válido, os resultados globais
da validação aninhada foram:

| Modelo | Macro-F1 | Balanced accuracy |
| --- | ---: | ---: |
| `LogisticRegression` | 0,573 | 0,592 |
| `KNN` com cosseno | 0,597 | 0,573 |
| `LinearSVC` | 0,699 | 0,681 |

O `LinearSVC` é, por enquanto, o modelo principal. O KNN supera o baseline e
funciona como comparação baseada em similaridade, mas fica abaixo do SVC. A
classe `Centro` continua sendo a mais difícil e possui poucos exemplos.

### Uso das notícias

As notícias não serão misturadas diretamente aos discursos no primeiro
treinamento supervisionado, pois foram produzidas em uma distribuição
temporal e editorial diferente. Elas serão usadas inicialmente como protótipos
partidários:

1. calcular um centróide dos embeddings das notícias de cada partido;
2. associar os partidos às três faixas ideológicas;
3. calcular centróides por faixa, dando peso igual a cada partido;
4. comparar cada discurso com esses centróides por similaridade de cosseno.

Esse método será um experimento complementar de similaridade. Uma notícia mais
próxima do centróide do partido poderá ser selecionada como exemplo
representativo para inspeção qualitativa, mas não substituirá todas as notícias
do partido no treinamento.

O teste direto por centróides alcançou macro-F1 de 0,302 e balanced accuracy de
0,335. Portanto, os centróides são mantidos para interpretação e recuperação de
textos semelhantes, não como substitutos do classificador supervisionado.

## Primeiro acesso

### 1. Clone o projeto

```bash
git clone <URL_DO_REPOSITORIO>
cd political-guidance-through-discourse
```

Se a pasta já estiver disponível localmente, basta abrir a raiz do projeto no
VS Code:

```bash
code .
```

### 2. Crie e ative um ambiente virtual

No Linux ou macOS:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

No Windows PowerShell:

```powershell
py -m venv .venv
.venv\Scripts\Activate.ps1
```

### 3. Instale as dependências

Com o ambiente virtual ativado:

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

### 4. Abra o notebook

No VS Code, abra o arquivo `ml-project.ipynb` e
execute as células em sequência. Também é possível rodar o Jupyter a partir do
terminal com:

```bash
jupyter notebook
```

## Estrutura do projeto

```text
.
├── content/
│   ├── argument_embeddings.pt
│   ├── firearm-carry-speeches-2014-2026.jsonl
│   ├── news_embeddings.pt
│   └── noticia_partidos.jsonl
├── news_party_centroids.pt
├── news_party_representatives.csv
├── ml-project.ipynb
├── political-guidance-through-discourse.ipynb
├── requirements.txt
├── README.md
├── .gitignore
└── .venv/
```

- `ml-project.ipynb`: preparação dos dados e geração dos embeddings dos
  argumentos e das notícias.
- `political-guidance-through-discourse.ipynb`: exploração dos dados, limpeza,
  análise e visualizações.
- `content/firearm-carry-speeches-2014-2026.jsonl`: discursos parlamentares e
  metadados sobre posição e contexto político.
- `content/noticia_partidos.jsonl`: notícias e textos relacionados a partidos e
  temas políticos.
- `content/argument_embeddings.pt`: embeddings dos argumentos dos discursos.
- `content/news_embeddings.pt`: embeddings das notícias e seus metadados.
- `news_party_centroids.pt`: centróides dos embeddings das notícias por partido.
- `news_party_representatives.csv`: notícia mais próxima do centróide de cada partido.
- `requirements.txt`: dependências Python do projeto.
- `README.md`: documentação e instruções de uso.
- `.gitignore`: arquivos locais que não devem ser versionados, como o ambiente
  virtual e caches do Jupyter.

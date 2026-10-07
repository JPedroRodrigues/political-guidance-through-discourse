# Orientação Política Através do Discurso

Este repositório reúne uma análise exploratória de discursos parlamentares e
notícias relacionadas a partidos e temas políticos, com foco especial em como
posicionamentos sobre armas, segurança pública e legislação podem estar
associados às orientações partidárias.

## Sobre o projeto

O objetivo principal é investigar se os discursos parlamentares podem ser
posicionados em três faixas do espectro político a partir de seus argumentos.
As notícias associadas a partidos funcionam como dados de referência para
representar posições políticas e treinar o classificador.

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

## Fluxo da análise

O notebook `political-guidance-through-discourse.ipynb` executa as seguintes
etapas:

1. carrega as notícias partidárias e os discursos parlamentares;
2. extrai os argumentos estruturados dos discursos;
3. padroniza siglas partidárias e classifica os partidos por espectro;
4. normaliza as transcrições e remove discursos duplicados;
5. mascara menções ao próprio partido do deputado;
6. explora tamanho dos textos, termos frequentes e distribuição partidária;
7. gera ou carrega embeddings produzidos pelo BERTimbau;
8. compara TF-IDF e embeddings na classificação do espectro político.

O mascaramento mantém o argumento original em `argument` e cria
`argument_masked`. As siglas e nomes partidários são substituídos por tokens
específicos, como `MASKPARTY_A`, para evitar que palavras comuns, como
“novo”, sejam confundidas com o partido NOVO. Os modelos usam o texto
mascarado, enquanto o texto original permanece disponível para inspeção.

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

Os rótulos originais são primeiro consolidados nas três faixas. Discursos sem
espectro definido não devem ser usados na avaliação supervisionada.

### Comparação de representações

O notebook treina o mesmo `LinearSVC` em duas representações:

1. TF-IDF, usado como baseline textual;
2. embeddings do BERTimbau, normalizados com norma L2.

O parâmetro `C` é escolhido por `GridSearchCV` nas notícias, usando
`StratifiedGroupKFold` agrupado por partido. Em seguida, o modelo é aplicado
aos argumentos dos discursos. O balanceamento das classes é feito com
`class_weight="balanced"`.

### Métricas

O notebook imprime a acurácia balanceada na validação das notícias, a
acurácia balanceada no teste dos argumentos e um relatório de classificação
por faixa. Os resultados devem ser registrados junto da versão dos dados e
dos artefatos de embeddings usados no experimento.

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

No VS Code, selecione o ambiente `.venv` como kernel, abra o arquivo
`political-guidance-through-discourse.ipynb` e execute as células em sequência.
Também é possível iniciar o Jupyter pelo terminal:

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

- `political-guidance-through-discourse.ipynb`: preparação, limpeza,
  mascaramento, análise exploratória, geração/carregamento de embeddings e
  classificação.
- `ml-project.ipynb`: notebook auxiliar de experimentação, quando aplicável.
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

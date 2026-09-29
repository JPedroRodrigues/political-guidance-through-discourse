# Orientação Política Através do Discurso

Análise de notícias e discursos relacionados a partidos políticos para investigar
como falas de deputados podem ser posicionadas em relação às orientações
partidárias.

## Sobre o projeto

Este repositório contém uma análise exploratória inicial dos dados de notícias de
partidos. O trabalho é desenvolvido em um notebook Jupyter, usando `pandas`,
`numpy`, `matplotlib` e `seaborn` para carregar, inspecionar e visualizar os
dados.

## Primeiro acesso

### 1. Obtenha o projeto

Clone o repositório e entre na pasta do projeto:

```bash
git clone <URL_DO_REPOSITORIO>
cd political-guidance-through-discourse
```

Se você já recebeu a pasta do projeto, basta abri-la no VS Code:

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

O ambiente virtual mantém as dependências deste projeto separadas das
instalações globais do Python.

### 3. Instale as dependências

Com o ambiente virtual ativado, execute:

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

Feito isso, no VSCode, abra e execute o notebook.

## Estrutura do projeto

```text
.
├── content/
│   └── noticia_partidos.jsonl
├── political-guidance-through-discourse.ipynb
├── requirements.txt
├── README.md
└── .gitignore
```

- `political-guidance-through-discourse.ipynb`: notebook com a análise
	exploratória, incluindo carregamento dos dados, metadados e visualizações.
- `content/noticia_partidos.jsonl`: conjunto de dados em JSON Lines; cada linha
	representa uma notícia e contém campos como título, texto, data, link,
	palavras-chave encontradas e partido.
- `requirements.txt`: versões das bibliotecas Python necessárias para executar
	a análise.
- `README.md`: documentação e instruções de uso do projeto.
- `.gitignore`: arquivos locais que não devem ser versionados, como o ambiente
	virtual, caches e checkpoints do Jupyter.


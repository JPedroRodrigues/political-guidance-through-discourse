# Orientação Política Através do Discurso

Este repositório reúne uma análise exploratória de discursos parlamentares e
notícias relacionadas a partidos e temas políticos, com foco especial em como
posicionamentos sobre armas, segurança pública e legislação podem estar
associados às orientações partidárias.

## Sobre o projeto

O objetivo principal é investigar se os discursos e as menções políticas se
alinhavam com a identidade partidária, a linha ideológica e as posições
coletivas dos partidos em temas sensíveis. A análise é feita em um notebook
Jupyter com uso de `pandas`, `numpy`, `matplotlib` e `seaborn`, além de
processamento textual e exploração dos metadados presentes nos dados.

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

No VS Code, abra o arquivo `political-guidance-through-discourse.ipynb` e
execute as células em sequência. Também é possível rodar o Jupyter a partir do
terminal com:

```bash
jupyter notebook
```

## Estrutura do projeto

```text
.
├── content/
│   ├── firearm-carry-speeches-2014-2026.jsonl
│   └── noticia_partidos.jsonl
├── political-guidance-through-discourse.ipynb
├── requirements.txt
├── README.md
├── .gitignore
└── .venv/
```

- `political-guidance-through-discourse.ipynb`: notebook principal com a
  exploração dos dados, limpeza, análise e visualizações.
- `content/firearm-carry-speeches-2014-2026.jsonl`: discursos parlamentares e
  metadados sobre posição e contexto político.
- `content/noticia_partidos.jsonl`: notícias e textos relacionados a partidos e
  temas políticos.
- `requirements.txt`: dependências Python do projeto.
- `README.md`: documentação e instruções de uso.
- `.gitignore`: arquivos locais que não devem ser versionados, como o ambiente
  virtual e caches do Jupyter.

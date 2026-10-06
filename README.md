# Orientação Política Através do Discurso

Este repositório reúne uma análise de discursos parlamentares e notícias
associadas a partidos políticos, com foco na investigação de padrões textuais
relacionados ao espectro político no contexto de debates sobre posse e porte de
armas, segurança pública e legislação.

## Sobre o projeto

O objetivo principal é investigar em que medida argumentos presentes em
discursos parlamentares podem ser associados a três faixas do espectro
político:

- **Esquerda**: Esquerda e Centro-esquerda;
- **Centro**: Centro;
- **Direita**: Centro-direita, Direita e Extrema-direita.

A abordagem utiliza análise exploratória de texto, TF-IDF e embeddings gerados
com BERTimbau. Diferentes classificadores são comparados sob uma estratégia de
validação que procura evitar vazamento de informação entre treino e teste.

As notícias publicadas ou associadas aos partidos são utilizadas como fonte
complementar para análise e representação textual das posições partidárias.

## Dados disponíveis

O projeto contém dois conjuntos principais de dados em JSON Lines:

- `content/firearm-carry-speeches-2014-2026.jsonl`: 256 registros de discursos
  e falas parlamentares, contendo informações como parlamentar, partido,
  estado, evento, transcrição, resumo, argumentos, posição e espectro político.

- `content/noticia_partidos.jsonl`: 1.061 notícias e textos políticos
  associados a partidos, contendo título, texto, data, link, palavras-chave e
  partido identificado.

Dos 256 registros de discursos, 207 possuem espectro político válido para a
tarefa supervisionada em três classes:

| Classe | Amostras |
| --- | ---: |
| Esquerda | 102 |
| Direita | 83 |
| Centro | 22 |

O desbalanceamento, principalmente da classe Centro, é considerado durante a
avaliação dos modelos.

## Análise exploratória de texto

A análise exploratória inclui:

- distribuição do tamanho dos textos;
- análise de frequência e TF-IDF;
- unigramas e bigramas;
- termos relativamente discriminantes por faixa política;
- comparação entre argumentos originais e argumentos com identidade partidária
  mascarada.

No corpus analisado, após o mascaramento da identidade partidária, foram
observados padrões lexicais distintos entre as três classes. Esses resultados
devem ser interpretados como características deste corpus e deste tema
específico, e não como definições gerais das diferentes orientações políticas.

## Auditoria de qualidade dos dados

Antes da modelagem foi realizada uma auditoria para identificar possíveis
fontes de vazamento de informação.

Entre as 207 amostras supervisionadas:

- 78 registros estavam envolvidos em transcrições duplicadas;
- essas duplicações correspondiam a 39 transcrições distintas;
- 37 grupos de transcrições duplicadas apareciam associados a eventos
  diferentes;
- 45 dos 207 argumentos (21,7%) mencionavam explicitamente o próprio partido.

Apenas agrupar os folds por `event_id` não era suficiente para impedir que
conteúdos idênticos aparecessem simultaneamente em treino e teste.

### Agrupamento anti-leakage

Foi criado um identificador de grupo que une eventos conectados por
transcrições duplicadas.

Os 37 `event_id` distintos da base supervisionada resultaram em 35 grupos
anti-leakage.

Na divisão final em cinco folds, nenhuma transcrição idêntica apareceu
simultaneamente nos conjuntos de treino e teste.

## Identidade partidária e party masking

Como 21,7% dos argumentos mencionavam explicitamente a sigla do próprio
partido, foi realizado um experimento de ablação.

A sigla partidária foi substituída pelo marcador `[PARTY]`, preservando o
restante do argumento.

O procedimento reduziu as menções explícitas do próprio partido de:

```text
45 → 0
# Classificador de documentos: resultados

## Resumo
O classificador sugere três campos de um documento a partir do texto: disciplina, tipo e área. A versão
atual (tentativa 5) usa **uma única rede neural** que devolve os três campos de uma vez, treinada sobre
vetores TF-IDF de 565 documentos públicos do ASSIST QuickSearch, com classes reduzidas (3 disciplinas,
4 tipos, 3 áreas). Em relação à tentativa 4, a entrada caiu de 20.000 para 5.000 termos e cada campo
ganhou uma camada oculta própria de 64 neurônios. No teste, o F1 macro de tipo subiu de **0,810 para
0,861**, com disciplina e área estáveis, e o modelo ficou quatro vezes menor. A seção "Histórico" resume
as tentativas anteriores.

## Tentativa 5: camada própria por campo e 5.000 termos (novembro de 2026)

Duas mudanças sobre a tentativa 4; divisão, `weight_decay=1e-3` e parada antecipada são os mesmos:
- **Camada oculta própria por campo:** 64 neurônios (ReLU, dropout 0,3) entre a camada compartilhada de
  256 e cada saída. Antes as três saídas liam direto da camada compartilhada.
- **Entrada menor:** TF-IDF com os 5.000 termos mais frequentes em vez de 20.000.

F1 macro no teste (113 documentos), com as duas redes treinadas no mesmo arquivo e na mesma divisão:

| Campo | Tentativa 4 retreinada | Tentativa 5 |
|---|---|---|
| Disciplina | 0,939 | **0,941** |
| Tipo | 0,810 | **0,861** |
| Área | 0,858 | 0,858 |
| **Média** | 0,869 | **0,887** |

Na validação, com 5 sementes (cada uma sorteia outra divisão e outra inicialização), a média do F1 macro
foi 0,849 na tentativa 4, 0,860 só com a camada própria, 0,861 só com 5.000 termos e 0,857 com as duas.
A variação entre sementes, de cerca de 4 pontos, é maior que essas diferenças. Pesos de perda por campo
também foram testados e não trouxeram ganho.

Ressalvas:
- **O arquivo de treino não é o da tentativa 4.** São os mesmos 565 documentos, mas com rótulos de área
  mais recentes (302 / 204 / 59 em vez de 310 / 201 / 54). Por isso a área aparece com 0,858 e não com
  os 0,770 da tentativa 4: a diferença vem dos rótulos, não da rede.
- O teste é uma divisão só, com 8 STANDARD e 13 HANDBOOK, e as duas classes seguem se confundindo
  (4 handbooks previstos como standard).
- A arquitetura não é o gargalo; o que limita tipo e área é o número de exemplos das classes pequenas.

O `model.pt` caiu de 20 MB para 5 MB. As métricas por classe estão em
`backend/classifier_artifacts/metadata.json`.

## Tentativa 4: rede única com classes reduzidas (565 documentos)

Base da tentativa 5: os dados, a divisão e o método abaixo continuam valendo.

### Dados
- Fonte: ASSIST QuickSearch (base pública do Departamento de Defesa dos EUA), cujos termos permitem a
  cópia e a distribuição. O EverySpec foi descartado porque proíbe download automatizado
  (`backend/scripts/dataset/README.md`).
- 565 documentos com texto utilizável (mínimo de 2.000 caracteres, corte em 50.000), uma linha por
  designação base, para que revisões do mesmo documento fiquem no mesmo conjunto.

| Campo | Classes | Origem do rótulo | Distribuição |
|---|---|---|---|
| Disciplina | MECHANICAL, ELECTRICAL, MATERIALS | grupo FSC (2 dígitos) dos metadados | 214 / 131 / 220 |
| Tipo | STANDARD, HANDBOOK, SPECIFICATION, COMMERCIAL_ITEM | categoria do documento nos metadados | 61 / 37 / 362 / 105 |
| Área | DESIGN, MATERIALS, OTHER_STAGE | IA rotuladora com critérios escritos pelo time | 310 / 201 / 54 |

- A área é a etapa de manufatura do documento. A IA rotuladora escolhe entre seis etapas com critérios
  versionados (`area_criteria_v2.md`), temperatura 0 e resposta restrita ao catálogo; as quatro etapas
  raras foram agrupadas em OTHER_STAGE e o CSV guarda a etapa fina.
- Os rótulos de área foram conferidos contra planilhas de referência preenchidas por um modelo mais forte
  e revisadas pelo time: 98,3% de concordância nos 60 documentos usados para refinar os critérios e
  93,3% em 30 documentos novos. Este último é o teto do campo área.
- A rotulagem rodou no Groq, por serem documentos públicos. A rodada final para o cliente será refeita
  com Ollama em máquina do time.

### Método
- Pré-processamento: minúsculas, pontuação, stopwords e lematização.
- Entrada: TF-IDF de unigramas e bigramas, `min_df=2`, 20.000 termos, `sublinear_tf`. Os vetores ficam
  no MongoDB (coleção `document_vectors`) e o treino lê de lá.
- Divisão única por grupo, estratificada por disciplina: 361 treino, 91 validação, 113 teste
  (`random_state=42`).
- Rede PyTorch em CPU: camada compartilhada de 256 neurônios (ReLU, dropout 0,3) e três saídas softmax;
  perda igual à soma das três cross-entropies com pesos de classe balanceados; Adam; parada antecipada
  pela média do F1 macro de validação (paciência 15).
- Linha de base: regressão logística por campo com `class_weight="balanced"`, sobre o mesmo TF-IDF.
- Hiperparâmetros: grade pequena avaliada só na validação. Adotado `weight_decay=1e-3` (média 0,881
  contra 0,850 do padrão); o filtro de tokens de OCR piorou e ficou desligado.

### Resultados no teste (113 documentos)

| Campo | Regressão logística | Rede única (ajustada) | Rede única (padrão) |
|---|---|---|---|
| Disciplina | 0,916 | **0,949** | 0,939 |
| Tipo | 0,750 | **0,800** | 0,744 |
| Área | **0,811** | 0,770 | 0,799 |
| **Média** | 0,826 | **0,840** | 0,827 |

- Disciplina é o campo mais fácil; nenhum documento elétrico errou.
- Em tipo, HANDBOOK (F1 0,636) e STANDARD (0,667) se confundem, porque os dois têm linguagem de
  orientação e não de requisito.
- Em área, OTHER_STAGE (0,522, 11 exemplos no teste) puxa a média para baixo. A regressão logística
  acerta mais nela, e a diferença cabe em 2 ou 3 documentos.
- A decisão pela rede única se apoia na média, que a favorece em 1,4 ponto.

### Embedding
O professor pediu o uso de embeddings. O plano prevê uma rede única sobre embedding pré-treinado
(`bge-small-en-v1.5`, congelado, média por trechos), a ser adotada só se superar a rede com TF-IDF nos
mesmos dados. Ainda não foi executada.

### Artefatos e comandos
O modelo atual fica em `backend/classifier_artifacts/` (`model.pt`, `vectorizer.joblib` e `metadata.json`
com versões, hash do dataset, hiperparâmetros e métricas). Os comandos de coleta, rotulagem, vetorização,
ajuste e treino estão no `backend/README.md`.

## Histórico das tentativas anteriores

| Tentativa | Documentos | Classes (disc./tipo/área) | F1 macro: disciplina / tipo / área |
|---|---|---|---|
| 1. Base inicial, rótulos por modelo de linguagem | 133 | 5 / 2 / 5 | 0,489 / 0,481 / 0,248 |
| 2a. QuickSearch, metadados como rótulo | 208 | 8 / 7 / 5 | 0,500 / 0,281 / 0,436 |
| 2b. Idem, com balanceamento de classes | 208 | 8 / 7 / 5 | 0,781 / 0,433 / 0,620 |
| 3. Amostra ampliada | 478 | 10 / 7 / 5 | 0,832 / 0,723 / 0,691 |
| 4. Rede única, classes reduzidas, área por IA | 565 | 3 / 4 / 3 | 0,949 / 0,800 / 0,770 |
| 5. Camada própria por campo, 5.000 termos, área rerrotulada | 565 | 3 / 4 / 3 | 0,941 / 0,861 / 0,858 |

Na tentativa 1 o gargalo era volume e qualidade de rótulo. As tentativas 2 e 3 trocaram os rótulos pelos
metadados do QuickSearch (objetivos e reproduzíveis), mas a área media o órgão preparador, não a etapa de
manufatura. A tentativa 4 redefine a área como etapa de manufatura, reduz as classes e troca os três modelos
por uma rede única. Os números não são comparáveis entre tentativas porque as classes mudaram; entre a
4 e a 5 as classes são as mesmas, mas os rótulos de área mudaram.

## Limitações e próximos passos
- O resultado mede a capacidade de reproduzir rótulos do QuickSearch e de uma IA rotuladora; um acervo da
  organização, classificado por especialistas, continua sendo a base correta para o uso real.
- Refazer a rotulagem de área com Ollama em máquina do time e repetir a comparação (números finais).
- Executar a variante C com embedding e decidir pelo critério já definido.
- Coletar mais exemplos de HANDBOOK, STANDARD e das etapas agrupadas em OTHER_STAGE; é o que limita
  tipo e área hoje.
- Avaliar com validação cruzada em vez de uma divisão única, para que a comparação entre configurações
  não dependa de poucos documentos.

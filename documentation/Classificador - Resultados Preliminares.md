# Classificador de documentos: resultados

## Resumo
O classificador sugere três campos de um documento a partir do texto: disciplina, tipo e área. A versão
atual (tentativa 4) usa **uma única rede neural** que devolve os três campos de uma vez, treinada sobre
vetores TF-IDF de 565 documentos públicos do ASSIST QuickSearch, com classes reduzidas (3 disciplinas,
4 tipos, 3 áreas). Comparada com a linha de base (uma regressão logística por campo, nos mesmos dados e
na mesma divisão), a rede única ficou com F1 macro médio de **0,840 contra 0,826**, vencendo em
disciplina e tipo e perdendo em área. A seção "Histórico" resume as tentativas anteriores.

## Tentativa 4: rede única com classes reduzidas (565 documentos)

### Dados
- Fonte: ASSIST QuickSearch (base pública do Departamento de Defesa dos EUA). Os termos do site permitem a
  cópia e a distribuição do conteúdo; o EverySpec foi avaliado e descartado porque proíbe download
  automatizado (`backend/scripts/dataset/README.md`).
- 565 documentos com texto utilizável (mínimo de 2.000 caracteres, corte em 50.000), uma linha por
  designação base (revisões do mesmo documento ficam no mesmo conjunto).
- Classes reduzidas para caber no volume de dados:

| Campo | Classes | Origem do rótulo | Distribuição |
|---|---|---|---|
| Disciplina | MECHANICAL, ELECTRICAL, MATERIALS | grupo FSC (2 dígitos) dos metadados | 214 / 131 / 220 |
| Tipo | STANDARD, HANDBOOK, SPECIFICATION, COMMERCIAL_ITEM | categoria do documento nos metadados | 61 / 37 / 362 / 105 |
| Área | DESIGN, MATERIALS, OTHER_STAGE | IA rotuladora com critérios escritos pelo time | 310 / 201 / 54 |

- A área é a etapa de manufatura a que o documento se refere. A IA rotuladora escolhe entre seis etapas
  (DESIGN, MATERIALS, PROCESSING, INSPECTION_TEST, PACKAGING_STORAGE, NO_CLEAR_STAGE), com critérios
  versionados (`area_criteria_v2.md`), temperatura 0 e resposta restrita ao catálogo. As quatro etapas raras
  (54 documentos, cerca de 10%) foram agrupadas em OTHER_STAGE para o treino; o CSV guarda a etapa fina.

### Qualidade do rótulo de área
Os rótulos da IA foram conferidos contra planilhas de referência preenchidas por um modelo mais forte
(Claude Fable 5.1), com as respostas conferidas pelo time:

| Amostra | Concordância (3 classes) | Concordância (6 etapas) |
|---|---|---|
| 60 documentos (usados para refinar os critérios v1 -> v2) | 98,3% | 98,3% |
| 30 documentos novos (hold-out, nunca vistos pelos critérios) | 93,3% | 86,7% |

O número do hold-out é o teto do campo área: o modelo não pode ser melhor que o rótulo que aprende.
Nesta rodada a rotulagem rodou no Groq (modelos `gpt-oss-120b`, `qwen3.8-27b` e `gpt-oss-20b`, com
cadeia de fallback por limite de requisições) para destravar o treino; os documentos são públicos.
A rodada final para o cliente será refeita com Ollama em máquina do time, e a concordância medida de novo.

### Método
- Pré-processamento igual ao das tentativas anteriores (minúsculas, pontuação, stopwords, lematização).
- Entrada: TF-IDF de unigramas e bigramas, `min_df=2`, 20.000 termos, `sublinear_tf`. Os vetores são
  gravados no MongoDB (coleção `document_vectors`, um registro por texto com a versão do vetorizador) e o
  treino lê de lá; a mesma coleção receberá os embeddings densos do RAG.
- Divisão única por grupo, estratificada por disciplina: 361 treino, 91 validação, 113 teste (`random_state=42`).
- Modelo B: rede PyTorch em CPU, camada compartilhada de 256 neurônios (ReLU, dropout 0,3) e três saídas
  softmax; perda = soma das três cross-entropies com pesos de classe balanceados; Adam; parada antecipada
  pela média do F1 macro de validação (paciência 15).
- Linha de base A: regressão logística por campo com `class_weight="balanced"`, sobre o mesmo TF-IDF.
- Critério de decisão: média simples do F1 macro dos três campos no teste; os três F1 são reportados.

### Hiperparâmetros
Grade pequena, um ajuste por vez a partir do padrão, avaliada só na validação (o teste não foi olhado):

| Configuração | Disciplina | Tipo | Área | Média (validação) |
|---|---|---|---|---|
| padrão (dropout 0,3, weight decay 1e-4) | 0,924 | 0,822 | 0,803 | 0,850 |
| weight decay 1e-5 | 0,924 | 0,748 | 0,803 | 0,825 |
| **weight decay 1e-3** | 0,934 | 0,914 | 0,795 | **0,881** |
| dropout 0,2 | 0,924 | 0,822 | 0,803 | 0,850 |
| dropout 0,5 | 0,936 | 0,822 | 0,795 | 0,851 |
| filtro de tokens de OCR (ex.: "20z") | 0,897 | 0,768 | 0,795 | 0,820 |

Adotado: `weight_decay=1e-3`. O filtro de tokens piorou e ficou desligado.

### Resultados no teste (113 documentos)

| Campo | A: regressão logística | B: rede única (ajustada) | B padrão |
|---|---|---|---|
| Disciplina | 0,916 | **0,949** | 0,939 |
| Tipo | 0,750 | **0,800** | 0,744 |
| Área | **0,811** | 0,770 | 0,799 |
| **Média** | 0,826 | **0,840** | 0,827 |

Por classe, rede única ajustada (precisão / recall / F1 / amostras):

| Classe | P | R | F1 | n |
|---|---|---|---|---|
| ELECTRICAL | 0,929 | 1,000 | 0,963 | 26 |
| MATERIALS (disciplina) | 0,952 | 0,909 | 0,930 | 44 |
| MECHANICAL | 0,953 | 0,953 | 0,953 | 43 |
| COMMERCIAL_ITEM | 1,000 | 0,882 | 0,938 | 17 |
| HANDBOOK | 0,778 | 0,538 | 0,636 | 13 |
| SPECIFICATION | 0,937 | 0,987 | 0,961 | 75 |
| STANDARD | 0,600 | 0,750 | 0,667 | 8 |
| DESIGN | 0,873 | 0,859 | 0,866 | 64 |
| MATERIALS (área) | 0,921 | 0,921 | 0,921 | 38 |
| OTHER_STAGE | 0,500 | 0,545 | 0,522 | 11 |

### Interpretação
- **Disciplina** é o campo mais fácil: vocabulário de cada classe é distinto. Nenhum documento elétrico errou.
- **Tipo:** HANDBOOK e STANDARD se confundem entre si (4 handbooks previstos como standard), porque os dois
  têm linguagem de orientação e não de requisito. SPECIFICATION e COMMERCIAL_ITEM vão bem.
- **Área:** OTHER_STAGE, com 11 exemplos no teste, é a classe que puxa a média para baixo; mistura quatro
  etapas diferentes e o rótulo tem teto de 93%. A regressão logística acerta mais nela, e a diferença
  (0,811 contra 0,770) cabe em 2 ou 3 documentos.
- O teste é pequeno: um documento muda o F1 de HANDBOOK, STANDARD ou OTHER_STAGE em vários pontos.
  A decisão pela rede única se apoia na média, que a favorece em 1,4 ponto.

### Embedding
O professor pediu o uso de embeddings. O plano prevê uma variante C (rede única sobre embedding
pré-treinado `bge-small-en-v1.5`, congelado, média por trechos), a ser adotada só se superar a rede
com TF-IDF nos mesmos dados. Essa variante ainda não foi executada; o TF-IDF foi entregue primeiro por
ser o conteúdo visto em aula e não exigir dependência nova. A infraestrutura do Mongo já prevê o campo
do embedding denso.

### Artefatos e comandos
Modelo salvo em `backend/classifier_artifacts/` (`model.pt`, `vectorizer.joblib`, `metadata.json` com
versões, hash do dataset, divisão, hiperparâmetros e métricas). Os comandos de coleta, rotulagem,
vetorização, ajuste, comparação e treino estão no `backend/README.md`.

## Histórico das tentativas anteriores

| Tentativa | Documentos | Classes (disc./tipo/área) | F1 macro: disciplina / tipo / área |
|---|---|---|---|
| 1. Base inicial, rótulos por modelo de linguagem | 133 | 5 / 2 / 5 | 0,489 / 0,481 / 0,248 |
| 2a. QuickSearch, metadados como rótulo | 208 | 8 / 7 / 5 | 0,500 / 0,281 / 0,436 |
| 2b. Idem, com balanceamento de classes | 208 | 8 / 7 / 5 | 0,781 / 0,433 / 0,620 |
| 3. Amostra ampliada | 478 | 10 / 7 / 5 | 0,832 / 0,723 / 0,691 |
| 4. Rede única, classes reduzidas, área por IA | 565 | 3 / 4 / 3 | 0,949 / 0,800 / 0,770 |

Na tentativa 1 o gargalo era volume e qualidade de rótulo. As tentativas 2 e 3 trocaram os rótulos pelos
metadados do QuickSearch (objetivos e reproduzíveis), mas a área media o órgão preparador, não a etapa de
manufatura. A tentativa 4 redefine a área como etapa de manufatura, reduz as classes e troca os três modelos
por uma rede única. Os números não são comparáveis entre tentativas porque as classes mudaram.

## Limitações e próximos passos
- O resultado mede a capacidade de reproduzir rótulos do QuickSearch e de uma IA rotuladora; um acervo da
  organização, classificado por especialistas, continua sendo a base correta para o uso real.
- Refazer a rotulagem de área com Ollama em máquina do time e repetir a comparação (números finais).
- Executar a variante C com embedding e decidir pelo critério já definido.
- Integrar o modelo ao endpoint de sugestões, passando o texto bruto do documento.

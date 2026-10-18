# Classificador de documentos: resultados preliminares

## Resumo
Treinamos três classificadores independentes (disciplina, tipo de documento e área) a partir do texto
dos documentos. Na primeira tentativa, com 133 documentos, o desempenho ficou abaixo do necessário
(F1 macro de 0,25 a 0,49). A causa principal era a falta de um acervo consolidado e rotulado.
Para contornar isso, montamos uma nova base a partir do ASSIST QuickSearch, usando os metadados do
próprio site como rótulos, e fizemos três rodadas de melhoria. Com 478 documentos o F1 macro chegou a
0,83 (disciplina), 0,72 (tipo) e 0,69 (área). A seção "Comparação das tentativas" resume a evolução.

## Tentativa 1: base inicial (133 documentos)
- 134 documentos coletados, dos quais 133 utilizáveis (um PDF sem texto extraível foi descartado).
- Rótulos gerados com apoio de um modelo de linguagem (Gemini), porque o time não tem especialistas
  para classificar os documentos com acurácia.
- No campo área, usamos apenas a primeira área indicada como principal.

| Campo | Distribuição dos documentos |
|---|---|
| Disciplina | MAT 58, ELE 27, EST 22, ENS 13, HID 13 |
| Tipo de documento | ESP 122, NOR 11 |
| Área | MFG 55, SIS 44, EST 19, QUA 10, CER 5 |

Das 8 disciplinas, 7 tipos e 6 áreas do catálogo, a base cobre 5, 2 e 5 classes, respectivamente.

## Método
- Pré-processamento: minúsculas, remoção de pontuação e stopwords, lematização.
- Modelo: TF-IDF (unigramas e bigramas) com regressão logística, um por campo.
- Divisão estratificada: 106 documentos para treino e 27 para teste (`random_state=42`).
- Métricas: acurácia e F1 macro (média do F1 de todas as classes com o mesmo peso).

## Resultados

| Campo | Acurácia | F1 macro |
|---|---|---|
| Disciplina | 0,630 | 0,489 |
| Tipo de documento | 0,926 | 0,481 |
| Área | 0,481 | 0,248 |

### Disciplina
| Classe | Precisão | Recall | F1 | Amostras no teste |
|---|---|---|---|---|
| ELE | 1,000 | 0,400 | 0,571 | 5 |
| ENS | 1,000 | 0,333 | 0,500 | 3 |
| EST | 1,000 | 0,500 | 0,667 | 4 |
| HID | 0,000 | 0,000 | 0,000 | 3 |
| MAT | 0,545 | 1,000 | 0,706 | 12 |

### Tipo de documento
| Classe | Precisão | Recall | F1 | Amostras no teste |
|---|---|---|---|---|
| ESP | 0,926 | 1,000 | 0,962 | 25 |
| NOR | 0,000 | 0,000 | 0,000 | 2 |

### Área
| Classe | Precisão | Recall | F1 | Amostras no teste |
|---|---|---|---|---|
| CER | 0,000 | 0,000 | 0,000 | 1 |
| EST | 0,333 | 0,250 | 0,286 | 4 |
| MFG | 0,500 | 0,909 | 0,645 | 11 |
| QUA | 0,000 | 0,000 | 0,000 | 2 |
| SIS | 0,500 | 0,222 | 0,308 | 9 |

## Interpretação
- **Disciplina:** o modelo tende à classe majoritária (MAT). Quando prevê outra classe, acerta, mas
  deixa de reconhecer a maioria dos casos delas.
- **Tipo de documento:** a acurácia de 92,6% é enganosa. O modelo prevê ESP em todos os casos, e
  ESP já representa 92% dos documentos. O F1 macro (0,481) mostra que a classe NOR não é reconhecida.
- **Área:** o desempenho é o mais fraco. SIS foi confundida com MFG em 7 dos 9 casos.
- O conjunto de teste é muito pequeno (27 documentos, de 1 a 5 por classe minoritária). Um único erro
  muda o recall dessas classes em 20 a 100 pontos percentuais, então os números são indicativos.

## Limitação da tentativa 1: falta de um acervo consolidado
A qualidade dos dados compromete o resultado:
1. **Volume insuficiente:** cerca de 130 documentos para até 21 classes, quando o planejado eram
   cerca de 40 exemplos por classe.
2. **Classes desbalanceadas e ausentes:** várias classes do catálogo não têm nenhum exemplo.
3. **Rótulos sem validação humana:** foram inferidos por um modelo de linguagem, sem especialista
   para conferir.
4. **Sem acervo de referência:** não há uma coleção consolidada de documentos já classificados pela
   organização, que seria a base correta para treinar e avaliar o modelo.

## Tentativa 2: nova coleta pelo ASSIST QuickSearch (208 documentos)
O ASSIST QuickSearch é a base pública do Departamento de Defesa dos EUA com especificações e normas
militares. Em vez de rotular com um modelo de linguagem, usamos os metadados do site:
- **Tipo de documento:** a categoria do documento (Doc Category), por exemplo Detail Specification.
- **Disciplina:** o grupo FSC (Federal Supply Class, os 2 primeiros dígitos), por exemplo FSC16 para
  componentes de aeronave. Os rótulos viram `FSC16`, `FSC59` etc.
- **Área:** o órgão que preparou o documento (Preparing Activity), agrupado em NAVY, ARMY, DLA, GSA e AIR_FORCE.

Isso dá rótulos objetivos, reproduzíveis e sem custo de API. A limitação é que as classes passam a ser as
do QuickSearch, e não as do catálogo do projeto.

**Coleta (scripts em `backend/scripts/dataset/`):** listagem de 27.957 documentos ativos; busca da página
de detalhes de uma amostra de 599 documentos (60 por grupo FSC); download dos PDFs com Distribution
Statement A (liberados ao público). Só 298 dos 599 têm PDF público: a outra metade são normas de terceiros
(ASTM, SAE, ISO). Depois de descartar 90 PDFs com pouco texto, sobraram 208 documentos.

**Problemas encontrados e resolvidos:** o filtro "Ativo" se perdia a partir da página 2 da busca; o leitor
de PDF falhava em 86 arquivos antigos (resolvido extraindo página a página); a escolha da revisão correta
do documento foi corrigida para "a mais recente que não seja aviso".

### Resultados com 208 documentos (teste de 42 documentos)
| Campo | Acurácia | F1 macro |
|---|---|---|
| Disciplina (8 classes) | 0,643 | 0,500 |
| Tipo (7 classes) | 0,571 | 0,281 |
| Área (5 classes) | 0,738 | 0,436 |

O modelo tendia às classes maiores: classes pequenas (Handbook, Military Standard, Air Force) ficavam com F1 zero.

### Ajuste: balanceamento de pesos de classe
Uma linha de configuração na regressão logística (`class_weight="balanced"`) faz um erro numa classe pequena
custar mais no treino. Mesmos 208 documentos:

| Campo | Acurácia | F1 macro |
|---|---|---|
| Disciplina | 0,810 | 0,781 |
| Tipo | 0,643 | 0,433 |
| Área | 0,762 | 0,620 |

O ganho foi grande, principalmente nas classes minoritárias.

### Experimento descartado: remover grupos FSC pequenos
Para tirar a classe `OTHER` de disciplina, descartamos os documentos dos grupos pequenos. Isso reduziu a base
para 168 documentos e piorou a área (F1 0,496), porque cada documento serve aos três campos. Não foi adotado.

## Tentativa 3: amostra ampliada (478 documentos)
Restringimos a amostra a normas do governo (prefixos MIL-, A-A- e federais), que têm 77% a 100% de PDF
público, contra 0% das normas de terceiros. Foram buscados 954 documentos, dos quais 637 têm PDF público;
478 viraram linhas utilizáveis depois de descartar 159 com pouco texto.

| Campo | Distribuição dos documentos |
|---|---|
| Disciplina | FSC16 59, FSC91 58, FSC47 52, FSC61 51, FSC80 49, FSC53 48, FSC31 46, FSC59 45, FSC68 42, FSC95 28 |
| Tipo | DETAIL_SPEC 210, CID 86, FEDERAL_SPEC 61, PERFORMANCE_SPEC 59, MILITARY_STD 31, HANDBOOK 25, OTHER 6 |
| Área | NAVY 185, DLA 141, ARMY 107, AIR_FORCE 35, OTHER 10 |

### Resultados com 478 documentos (treino de 382, teste de 96)
| Campo | Acurácia | F1 macro |
|---|---|---|
| Disciplina (10 classes) | 0,823 | 0,832 |
| Tipo (7 classes) | 0,750 | 0,723 |
| Área (5 classes) | 0,740 | 0,691 |

- **Disciplina:** todas as classes têm F1 acima de 0,68; FSC59 acerta tudo. FSC91 e FSC16 se confundem.
- **Tipo:** CID tem F1 de 0,97. O erro principal é Detail Specification confundida com Performance Specification.
  Handbook e Military Standard, com 5 e 6 exemplos no teste, ficam perto de 0,45 a 0,50.
- **Área:** DLA vai bem (F1 0,87). Exército é o mais difícil (8 de 22 foram previstos como Marinha), provavelmente
  porque as capas citam comandos parecidos.

## Comparação das tentativas
| Tentativa | Documentos | F1 macro: disciplina / tipo / área |
|---|---|---|
| 1. Base inicial (rótulos por modelo de linguagem) | 133 | 0,489 / 0,481 / 0,248 |
| 2a. QuickSearch, sem balanceamento | 208 | 0,500 / 0,281 / 0,436 |
| 2b. QuickSearch, com balanceamento | 208 | 0,781 / 0,433 / 0,620 |
| 3. Amostra ampliada, com balanceamento | 478 | 0,832 / 0,723 / 0,691 |

As comparações têm ressalvas: o conjunto de classes mudou entre as tentativas (na 1, tipo tinha 2 classes; nas
demais, 7), e o conjunto de teste também. O ganho vem de três fatores combinados: rótulos objetivos, mais dados
e balanceamento de classes.

## Conclusão atualizada
- A hipótese da primeira tentativa se confirmou: volume e qualidade dos rótulos eram o gargalo. Com rótulos dos
  metadados e 478 documentos, o desempenho passou de insuficiente para razoável.
- O resultado ainda mede a capacidade de reproduzir as categorias do QuickSearch, não do catálogo da organização.
  Um acervo próprio, já classificado por especialistas, continua sendo a base correta para o uso real.
- O conjunto de teste tem de 1 a 12 documentos por classe. As métricas por classe oscilam, e uma validação
  cruzada daria números mais confiáveis.

## Próximos passos
- Salvar os três modelos treinados (joblib) com um arquivo de metadados e criar a função de predição.
- Fazer validação cruzada para obter intervalos de confiança das métricas.
- Mapear as classes do QuickSearch para o catálogo da organização, ou substituir o dataset por um acervo próprio
  classificado, se existir.
- Integrar ao endpoint de sugestões, passando o texto bruto do documento ao modelo.

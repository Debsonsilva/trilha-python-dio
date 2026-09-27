# Detecção de fraudes em transações de cartão

Projeto desenvolvido durante a trilha de Python da DIO.

A proposta foi treinar modelos de machine learning para identificar fraude em transações de cartão de crédito. O principal problema da base é o desbalanceamento: existem muitas transações normais e pouquíssimas fraudes. Por isso, olhar só para acurácia pode dar uma impressão errada de que o modelo está funcionando bem.

## O que tem no projeto

O notebook `deteccao_fraudes_cartao.ipynb` faz o processo completo:

- carrega o dataset por link, sem salvar o CSV no GitHub;
- confere a proporção entre transações normais e fraudes;
- cria `LogAmount` e uma variável de hora;
- padroniza as variáveis de valor e tempo;
- separa treino, validação e teste usando `stratify`;
- treina Regressão Logística, Random Forest e XGBoost;
- trata o desbalanceamento usando pesos de classe;
- ajusta o limiar de decisão usando a base de validação;
- compara recall, precisão, F1, ROC-AUC e PR-AUC;
- mostra as curvas ROC e precisão x recall;
- verifica importância de variáveis;
- usa SHAP para explicar o XGBoost.

## Por que não usei acurácia como principal métrica

Se quase todas as transações são normais, um modelo que responde "normal" quase sempre pode ter uma acurácia enorme e mesmo assim deixar passar as fraudes.

Neste projeto eu dei mais atenção ao **recall da classe de fraude**, sem ignorar precisão e F1.

- **Recall:** quantas fraudes reais o modelo conseguiu encontrar.
- **Precisão:** entre os alertas de fraude, quantos realmente eram fraude.
- **F1:** equilíbrio entre precisão e recall.

## Preparação dos dados

Além das variáveis `V1` até `V28`, a base possui `Time`, `Amount` e `Class`.

Eu criei:

- `LogAmount`: log do valor da transação;
- `Hour`: hora aproximada calculada a partir de `Time`.

Também usei `StandardScaler` nas variáveis relacionadas a tempo e valor.

A divisão foi feita em treino, validação e teste. A validação foi usada para escolher o limiar, deixando o teste somente para a comparação final.

## Modelos

Foram comparados:

1. Regressão Logística;
2. Random Forest;
3. XGBoost.

A Regressão Logística serviu como baseline. No Random Forest usei `class_weight` e no XGBoost usei `scale_pos_weight` com base na proporção entre as classes.

## Limiar de decisão

Em vez de usar 0,50 em todos os casos, testei os limiares na base de validação.

A regra usada foi tentar manter pelo menos 85% de recall na validação e, dentro desses pontos, escolher a melhor precisão. Se isso não for possível, o código usa o maior F1.

As métricas finais e o limiar escolhido ficam salvos nas saídas do notebook.

## SHAP

Usei SHAP no XGBoost para visualizar quais variáveis mais aumentaram ou diminuíram a chance prevista de fraude.

Como as variáveis `V1` a `V28` foram anonimizadas com PCA, não dá para traduzir cada uma para algo como "tipo de loja" ou "local da compra". Mesmo assim, o SHAP ajuda a enxergar quais componentes tiveram mais peso na decisão do modelo.

## O que eu fiz diferente

Além da comparação dos três modelos, eu separei uma base de validação só para escolher o limiar. Assim, o teste final não é usado para tomar essa decisão.

Também acrescentei uma variável de hora e usei o SHAP tanto de forma geral quanto em uma transação de exemplo.

## Arquivos

- `deteccao_fraudes_cartao.ipynb` - notebook principal, com código, tabelas e gráficos.
- `requirements-fraude.txt` - bibliotecas usadas no projeto.

## Como executar

```bash
pip install -r requirements-fraude.txt
jupyter notebook deteccao_fraudes_cartao.ipynb
```

O dataset é baixado automaticamente pelo próprio notebook.

---

Este repositório também contém os exercícios que venho fazendo durante a trilha de Python da DIO.

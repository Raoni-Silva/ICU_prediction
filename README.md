![SirioLibanes](https://raw.githubusercontent.com/Raoni-Silva/BootcampAlura/main/ProjetoFinal/SirioLibanes.png)

Trabalho realizado como projeto de conclusão do Bootcamp Data Science Aplicada da ALURA.

**Data:** 15/04/2021

# Predição de Internação

Modelo de aprendizado de máquina para previsão da necessidade de internação em pacientes do Hospital Sírio Libanês positivos para COVID-19.

Para acessar o notebook [clique aqui](https://nbviewer.jupyter.org/github/Raoni-Silva/ICU_prediction/blob/main/ICU_prediction.ipynb).

## **BASE DE DADOS**

Esta [base de dados](https://www.kaggle.com/S%C3%ADrio-Libanes/covid19) contém dados anonimizados do **Hospital Sírio Libanês** das unidades de São Paulo e Brasília. Todos os dados foram tornados anônimos de acordo com as melhores práticas e recomendações internacionais.

Os dados foram limpos e escalados por coluna de acordo com *Min Max Scaler* para caberem entre -1 e 1.

## **OBJETIVO**

**TAREFA 01**

Prever a admissão na UTI de casos confirmados de COVID-19.

**- Questionamento:** com base nos dados disponíveis, é viável prever quais pacientes precisarão de suporte em unidade de terapia intensiva?

**- Objetivo:** fornecer aos hospitais a resposta mais precisa possível, para que os recursos da UTI possam ser administrados ou que se organize a transferência do paciente.

**TAREFA 02**

Prever a **NÃO** admissão à UTI de casos confirmados de COVID-19.

**- Questionamento:** com base na amostra de dados amplamente disponíveis, é viável prever quais pacientes precisarão de suporte de unidade de terapia intensiva?

**- Objetivo:** Fornecer aos hospitais locais e temporários uma resposta boa o suficiente, para que os médicos da linha de frente possam dar alta com segurança e acompanhar remotamente esses pacientes.


## **RESUMO**

**PREPARAÇÃO DA ESTRUTURA DO PROJETO**

* Instalação e importação de bibliotecas além da definição das funções utilizadas no *notebook*.

**EXPLORANDO A BASE DE DADOS**

* Carregando a base de dados
* Exploração inicial da base de dados

**PROCESSAMENTO DOS DADOS**

* **Tratamento de dados faltantes:**

    Pacientes que possuem alguns atributos sem preenchimento (*NaN*) em alguma janela, mas possuem valores nas janelas subsequentes ou anteriores foram preenchidos com *back-fill* e *forward-fill*, utilizando apenas os dados do mesmo paciente e considerando apenas as janelas em que este não havia dado entrada na UTI (**"ICU" = 0**).
    
    Pacientes que possuem algum atributo completamente nulo (*NaN*) foram excluídos da base. Ex. **PATIENT_VISIT_IDENTIFIER** = **199**.

* **Exclusão dos registros de janelas em que o paciente foi internado:**

    Todas as janelas em que o paciente está internado foram excluídas, porém o atributo "**ICU**" foi marcado como 1 (internado) nos demais registros destes pacientes para que essa informação, que é nosso **atributo alvo**, não se perca.

    Os pacientes que foram **internado** já na **primeira janela** foram excluídos da base de dados por completo.

**SELEÇÃO DE VARIÁVEIS**

* **Tratando variáveis categóricas**

    Primeiramente foi feito o tratamento de variáveis categóricas através do método "***dummies***", gerando uma matriz de "zeros" e "uns" com o número de colunas igual ao número de categorias daquele atributo menos 1 (n - 1).

* **Eliminando colunas com valores únicos**

    Os atributos de valores únicos não agregam nada aos algoritmos de Machine Learning e portanto são dispensáveis. Imagine uma base de dados de pacientes com câncer de próstata em que há uma coluna de gênero. Como há apenas um gênero que pode apresentar câncer de próstata na espécie humana, não há a necessidade desse atributo.

* **Seleção de atributos**

    É feita uma análise de correlação entre cada atributo e os demais e aqueles que possuem uma correlação alta (acima de **0.90**, neste trabalho), um dos dois é eliminado, uma vez que ambos os atributos estão "dizendo" a mesma coisa. Essa etapa é necessária para tornar a base mais enxuta e não redundante, reduzindo a energia de processamento dos algoritmos e o tempo.

**PREPARAÇÃO DOS DADOS**

* **Processando a base de dados**

    Neste ponto são excluídos todos os registros de janelas superiores a "0-2". A ideia de manter apenas a primeira janela de cada paciente é para evitar o vazamento de dados entre pacientes e além disso procurar prever se o paciente será internado ou não o mais rápido possível, conforme diretriz do Hospital Sírio Libanês no tópico "**Quanto antes melhor**".

* **Dados de Treino | Teste**

    Os dados foram separados em "treino" e "teste" para que os algoritmos possam aprender e depois checar seu aprendizado com uma base diferente da que usou para aprender. Os dados foram separados na proporção de 70/30 (treino/teste) e estratificado de forma a manter a mesma proporção do atributo alvo da base toda em cada base.

    Apesar de ser feita uma nova divisão treino/teste em cada algoritmo durante o treinamento dos algoritmos, essa primeira divisão é utilizada na etapa de "Otimização automática" através do "*GridSearchCV*".

**MODELOS DE MACHINE LEARNING**

Chegamos ao coração do trabalho.  A metodologia aplicada neste ponto é a de rodar primeiramente diversos modelos através da biblioteca PyCaret, sem nenhum tipo de "*tune*", para afunilar minhas opções. São, então, selecionados os 6 melhores algoritmos, baseado em seu score AUC e a partir daí feitas diversas formas de otimização do modelo:

* Otimização PyCaret Tune
* Otimização automática através da biblioteca "*GridSearchCV*"
* Otimização manual

Por fim é realizado um comparativo de score AUC para saber qual das formas de otimização se saiu melhor dentro de um mesmo algoritmo e ao final uma comparação global entre todos os modelos.

**CONCLUSÃO**

O modelo **Extra Trees Classifier** com **otimização manual** obteve o melhor **score AUC** e com o valor de **0.7862**.

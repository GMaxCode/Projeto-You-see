# Guia de preparação --- Sistema de Recomendação de Transporte Público

## 1. Para que serve este guia

Este documento que organiza o que o grupo precisa estudar, preparar e
decidir antes e durante o desenvolvimento do trabalho de Algoritmos e
Programação.

A proposta não é começar copiando um código pronto. O objetivo é que
todos entendam o problema, dividam as tarefas, construam o algoritmo por
etapas e consigam explicar as decisões na apresentação.

## 2. O que o sistema precisa fazer

O usuário informa duas localizações por coordenadas geográficas:

-   **Origem:** latitude e longitude do local onde deseja embarcar.
-   **Destino:** latitude e longitude do local onde deseja chegar.

A aplicação deverá:

1.  Validar as coordenadas recebidas.
2.  Consultar a API Olho Vivo da SPTrans.
3.  Obter informações de linhas, sentidos e pontos de parada.
4.  Calcular quais paradas estão próximas da origem e do destino.
5.  Encontrar linhas que atendam às duas regiões, considerando somente
    viagens diretas.
6.  Escolher uma linha e identificar embarque e desembarque.
7.  Consultar a previsão de chegada do próximo ônibus ao ponto de
    embarque.
8.  Mostrar os dados e um mapa interativo.
9.  Informar claramente quando não houver uma opção válida ou quando a
    API não fornecer algum dado.

**Importante:** encontrar uma parada perto da origem e outra perto do
destino não é suficiente. As duas paradas precisam pertencer à mesma
linha e ao mesmo sentido de operação, e a sequência da linha precisa
permitir o deslocamento da origem para o destino.

## 3. O que todos do grupo precisam saber

Todos devem compreender pelo menos o fluxo geral do projeto. Não é
necessário que todos sejam especialistas em tudo, mas ninguém deve ficar
sem entender como o sistema funciona.

### 3.1 Python e lógica de programação

Revisem estes assuntos:

-   Variáveis e tipos de dados: `int`, `float`, `str`, `bool`.
-   Entrada e saída de dados: `input()` e `print()`.
-   Condicionais: `if`, `elif` e `else`.
-   Repetições: `for` e `while`.
-   Listas e dicionários.
-   Funções: parâmetros, retorno com `return` e escopo.
-   Tratamento de erros com `try` e `except`.
-   Importação e uso de bibliotecas.
-   Organização do código em funções pequenas.

**Por que isso importa?** O projeto vai receber dados, percorrer muitas
paradas, comparar distâncias, filtrar linhas e tratar situações em que a
API não retorna o que esperamos.

### 3.2 APIs e requisições HTTP

Entendam estes conceitos:

-   **API:** uma forma de um sistema disponibilizar dados ou
    funcionalidades para outro sistema.
-   **Endpoint:** endereço específico usado para consultar determinado
    recurso.
-   **Requisição:** pedido enviado à API.
-   **Resposta:** conteúdo devolvido pela API.
-   **JSON:** formato comum de troca de dados, organizado em objetos e
    listas. ex: {"jsonh": 34.78}
-   **Token:** credencial usada para autenticar a aplicação.
-   **Parâmetros:** informações enviadas junto à requisição.
-   **Status HTTP:** código que indica se a requisição funcionou ou
    falhou.

Com a biblioteca `requests`, estudem como:

-   Fazer uma requisição `GET`.
-   Enviar cabeçalhos de autenticação.
-   Verificar o status da resposta.
-   Converter a resposta JSON para estruturas Python.
-   Definir timeout.
-   Tratar erros de conexão e respostas inesperadas.

Não basta a requisição funcionar uma vez. O código deve verificar se a
resposta tem os campos esperados antes de utilizá-los.

### 3.3 Coordenadas e distância geográfica

Uma coordenada geográfica é formada por:

-   **Latitude:** posição norte/sul.
-   **Longitude:** posição leste/oeste.

As coordenadas devem ser tratadas como números (`float`). O grupo
precisa validar se os valores são numéricos e se estão nos intervalos
possíveis:

-   Latitude: de -90 a 90.
-   Longitude: de -180 a 180.

Para calcular a distância entre dois pontos da superfície terrestre,
estudem a **fórmula de Haversine**. Ela é mais apropriada do que
calcular uma distância euclidiana simples diretamente entre latitude e
longitude.

A fórmula usa o raio médio da Terra e retorna a distância aproximada
entre duas coordenadas. O grupo pode implementar o cálculo usando `math`
ou avaliar uma biblioteca auxiliar, mas precisa saber explicar o método
escolhido.

Definam um raio de busca entre 1 e 1.000 metros e justifiquem a escolha.
Um raio muito pequeno pode não encontrar paradas; um raio muito grande
pode recomendar uma parada distante demais.

### 3.4 Estruturas de dados

A API provavelmente devolverá dados em JSON. O grupo deve aprender a
navegar por listas e dicionários.

Exemplo ilustrativo de estrutura (não representa necessariamente os
nomes reais dos campos da API):

``` python
parada = {
    "nome": "Terminal Exemplo",
    "latitude": -23.5,
    "longitude": -46.6
}
```

Saibam responder:

-   Como acessar um valor de um dicionário?
-   Como percorrer uma lista de paradas?
-   Como verificar se uma chave existe?
-   Como lidar com um campo ausente ou com valor `None`?
-   Como guardar as paradas encontradas para processá-las depois?

Não assumam os nomes nem o formato dos campos antes de conferir a
documentação e uma resposta real da API.

## 4. O que estudar na documentação Olho Vivo

Documentação oficial:\
https://www.sptrans.com.br/desenvolvedores/api-do-olho-vivo-guia-de-referencia/

Cada integrante deve conhecer a documentação, mas o grupo pode dividir a
investigação dos endpoints.

### 4.1 Cadastro e autenticação

-   Cada grupo deve providenciar o cadastro exigido no portal da
    SPTrans.
-   Criem uma aplicação e obtenham o token.
-   Leiam na documentação como o token deve ser enviado.
-   Testem a autenticação antes de começar o algoritmo.

**Segurança:** nunca coloquem o token diretamente no notebook que será
publicado no GitHub. Utilizem uma variável de ambiente ou um arquivo
local de configuração que esteja listado no `.gitignore`. Publiquem, se
necessário, um arquivo de exemplo sem o token real.

### 4.2 Endpoints que o grupo precisa localizar

Na documentação, identifiquem quais endpoints permitem:

-   Consultar linhas e seus sentidos.
-   Consultar pontos de parada e suas coordenadas.
-   Descobrir quais pontos pertencem a determinada linha e sentido, se
    essa informação estiver disponível.
-   Consultar a previsão de chegada de veículos a um ponto de parada.

Registrem para cada endpoint:

  Informação     O que anotar
  -------------- --------------------------------------------------
  Finalidade     Que informação ele fornece?
  Método         GET ou outro método?
  Parâmetros     O que precisa ser enviado?
  Autenticação   Como o token é informado?
  Resposta       Quais campos são retornados?
  Limitações     Há paginação, filtros ou dados que podem faltar?

Não escolham endpoints apenas pelo nome. Façam uma requisição de teste,
observem o JSON real e anotem os campos importantes.

## 5. Como dividir o trabalho

Uma sugestão para um grupo de quatro pessoas. Se o grupo tiver mais ou
menos integrantes, adaptem as responsabilidades.

  -----------------------------------------------------------------------
  Frente                  Responsabilidade        Entrega
                          principal               
  ----------------------- ----------------------- -----------------------
  API e autenticação      Estudar a documentação, Funções de consulta e
                          autenticar e testar     exemplos de respostas
                          endpoints               

  Coordenadas e           Validar coordenadas e   Função de validação e
  distâncias              implementar o cálculo   função de distância
                          geográfico              

  Algoritmo de            Cruzar paradas, linhas  Lógica de filtragem e
  recomendação            e sentidos; selecionar  seleção
                          embarque e desembarque  

  Resultados e            Organizar dados, criar  Mapa, demonstração e
  apresentação            mapa, testar erros e    materiais da
                          preparar explicação     apresentação
  -----------------------------------------------------------------------

Mesmo com a divisão, todos devem revisar o trabalho dos colegas e
entender como os módulos se conectam.

## 6. Planejamento do algoritmo

Antes de programar, desenhem o fluxo do sistema. Uma possível sequência
é:

1.  Receber as coordenadas de origem e destino.
2.  Validar os valores.
3.  Autenticar na API.
4.  Consultar os dados necessários de linhas e paradas.
5.  Organizar os dados em estruturas Python.
6.  Calcular a distância entre a origem e cada parada relevante.
7.  Separar as paradas que estão dentro do raio da origem.
8.  Repetir o processo para o destino.
9.  Relacionar as paradas próximas com suas linhas e sentidos.
10. Encontrar combinações em que embarque e desembarque pertençam à
    mesma linha e ao mesmo sentido.
11. Verificar, quando os dados permitirem, se a ordem das paradas é
    compatível com a viagem.
12. Selecionar uma opção conforme critérios definidos pelo grupo.
13. Consultar a previsão de chegada no ponto de embarque.
14. Exibir a recomendação e o mapa.
15. Informar uma mensagem adequada se nenhuma opção for encontrada.

### Uma decisão importante: como escolher a melhor linha?

O enunciado pede que o grupo selecione uma opção, mas não define uma
fórmula de pontuação. O grupo precisa definir e justificar seus
critérios.

Uma primeira versão simples pode priorizar:

-   Menor distância a pé da origem até o ponto de embarque.
-   Menor distância a pé do ponto de desembarque até o destino.
-   Existência de uma sequência válida de paradas no mesmo sentido.
-   Disponibilidade de previsão de chegada.

Se houver mais de uma opção, vocês podem criar uma pontuação. Por
exemplo, somar as distâncias de caminhada até embarque e depois do
desembarque ao destino. Expliquem as limitações desse critério: ele não
considera necessariamente o tempo total dentro do ônibus ou o trânsito.

**Atenção ao sentido:** uma linha pode operar em dois sentidos. Não
misturem paradas de sentidos diferentes. Também não considerem válida
uma combinação se o ônibus passar primeiro pelo ponto de desembarque e
depois pelo ponto de embarque.

## 7. Funções que provavelmente serão necessárias

O grupo deve decidir os nomes e parâmetros finais, mas é interessante
separar responsabilidades em funções.

  -----------------------------------------------------------------------
  Função sugerida                     Responsabilidade
  ----------------------------------- -----------------------------------
  `validar_coordenadas()`             Conferir se latitude e longitude
                                      são válidas

  `autenticar_api()`                  Obter ou preparar a autenticação

  `consultar_linhas()`                Buscar informações de linhas

  `consultar_paradas()`               Buscar pontos e coordenadas

  `calcular_distancia()`              Calcular a distância entre duas
                                      coordenadas

  `filtrar_paradas_proximas()`        Encontrar paradas dentro do raio

  `encontrar_linhas_candidatas()`     Cruzar as paradas de origem e
                                      destino

  `selecionar_recomendacao()`         Escolher uma opção direta válida

  `consultar_previsao()`              Consultar a chegada do ônibus

  `exibir_resultado()`                Apresentar as informações ao
                                      usuário

  `gerar_mapa()`                      Criar o mapa interativo
  -----------------------------------------------------------------------

Esses nomes são sugestões, não uma obrigação. Evitem criar uma única
função enorme que faça tudo. Cada função deve ter uma responsabilidade
clara e, sempre que possível, receber dados por parâmetros e devolver
resultados com `return`.

## 8. Bibliotecas: o que aprender

  Biblioteca   Para que pode ser usada
  ------------ -----------------------------------------------------
  `requests`   Fazer requisições HTTP à API
  `math`       Cálculos matemáticos, incluindo Haversine
  `pandas`     Organizar e analisar dados tabulares, se necessário
  `folium`     Criar mapa interativo com marcadores e trajetos
  `plotly`     Alternativa para visualizações interativas

Não instalem todas as bibliotecas sem necessidade. Comecem com
`requests` e `math`; adicionem as demais quando a etapa correspondente
estiver funcionando.

## 9. Testes que o grupo deve realizar

Não testem somente um caso em que tudo funciona. Preparem cenários
diferentes.

-   Coordenadas válidas com uma linha direta disponível.
-   Coordenadas em formato inválido, como texto no lugar de número.
-   Latitude ou longitude fora do intervalo permitido.
-   Nenhuma parada encontrada dentro do raio.
-   Paradas próximas, mas sem uma linha em comum.
-   Linha em comum, mas com sentido ou sequência incompatível.
-   API fora do ar ou sem conexão com a internet.
-   Token inválido ou expirado.
-   Resposta vazia ou com campos ausentes.
-   Previsão de chegada indisponível.
-   Mais de uma linha candidata.

Para cada teste, anotem: entrada, resultado esperado, resultado obtido e
se o sistema tratou a situação corretamente.

## 10. Organização do notebook e do GitHub

Uma estrutura possível para o notebook:

1.  **Apresentação:** objetivo e integrantes.
2.  **Configuração:** importações e leitura segura do token.
3.  **Entrada e validação:** coordenadas e raio.
4.  **Integração com a API:** funções de autenticação e consulta.
5.  **Tratamento dos dados:** organização de linhas e paradas.
6.  **Cálculo de distâncias:** método utilizado e testes.
7.  **Recomendação:** filtragem e seleção da linha.
8.  **Previsão:** consulta da chegada do ônibus.
9.  **Mapa:** origem, destino, embarque, desembarque e paradas
    relevantes.
10. **Resultado:** resumo da recomendação.
11. **Testes e limitações:** cenários testados e problemas conhecidos.
12. **Conclusão:** decisões e possíveis melhorias.

No GitHub, mantenham o notebook organizado e incluam um `README.md`
explicando o projeto, como executar e quais bibliotecas instalar. Não
enviem tokens, senhas ou outros segredos.

Antes da entrega:

-   Reiniciem o kernel e executem o notebook do início ao fim.
-   Verifiquem se as células funcionam na ordem correta.
-   Confiram se o mapa aparece no notebook.
-   Removam saídas que exponham credenciais.
-   Confirmem que o repositório pode ser acessado pelo professor.

## 11. Fluxograma para a apresentação

O fluxograma deve mostrar a lógica do sistema, inclusive os caminhos de
erro. Um esboço inicial:

``` text
Início
  |
Receber origem e destino
  |
Validar coordenadas
  |
Coordenadas válidas?
  | Não
  +------> Exibir erro e encerrar
  |
 Sim
  |
Autenticar e consultar API
  |
Dados disponíveis?
  | Não
  +------> Informar falha e encerrar
  |
 Sim
  |
Buscar paradas próximas da origem e do destino
  |
Encontrar linha e sentido em comum
  |
Existe viagem direta válida?
  | Não
  +------> Informar que não há recomendação
  |
 Sim
  |
Selecionar linha e pontos
  |
Consultar previsão de chegada
  |
Exibir recomendação e mapa
  |
Fim
```

O grupo deve adaptar o fluxograma ao algoritmo que realmente
implementar. Não apresentem uma etapa no fluxograma que não exista no
código.

## 12. Cuidados e dificuldades que merecem atenção

-   **Volume de dados:** consultar a API repetidamente dentro de vários
    laços pode deixar o programa lento. Investiguem quais consultas
    podem ser feitas uma vez e reutilizadas.
-   **Limites da API:** respeitem as regras e limitações descritas pela
    SPTrans.
-   **Dados incompletos:** nem toda linha ou parada necessariamente terá
    todos os campos ou previsões.
-   **Distância não é tempo:** a distância geográfica em linha reta não
    representa exatamente o percurso a pé.
-   **Ordem das paradas:** para garantir que a viagem é direta, é
    necessário considerar a sequência das paradas no sentido correto,
    caso a API forneça essa informação.
-   **Previsão:** a previsão de chegada é dinâmica e pode mudar; não
    tratem um valor consultado como garantia.
-   **Mapa:** um mapa com marcadores não significa que vocês calcularam
    o trajeto real do ônibus. Deixem claro o que está sendo exibido.
-   **Token:** não publiquem credenciais no notebook ou no GitHub.

## 13. Checklist do grupo

### Antes de programar

-   [ ] Todos entenderam o objetivo e os requisitos.
-   [ ] O grupo fez o cadastro e obteve o token da API.
-   [ ] A autenticação foi testada.
-   [ ] Os endpoints necessários foram identificados na documentação.
-   [ ] O grupo analisou exemplos reais de respostas JSON.
-   [ ] O raio de busca foi escolhido e justificado.
-   [ ] O critério para selecionar a recomendação foi definido.
-   [ ] As responsabilidades foram divididas.

### Durante o desenvolvimento

-   [ ] As coordenadas são validadas.
-   [ ] As requisições possuem tratamento de erros e timeout.
-   [ ] As distâncias são calculadas corretamente.
-   [ ] As paradas próximas são filtradas.
-   [ ] Linhas e sentidos são cruzados corretamente.
-   [ ] A recomendação não exige troca de ônibus.
-   [ ] A previsão de chegada é consultada quando disponível.
-   [ ] O mapa apresenta os pontos exigidos.
-   [ ] Existem testes para situações de erro.

### Antes de entregar e apresentar

-   [ ] O notebook executa do começo ao fim.
-   [ ] O repositório GitHub está organizado.
-   [ ] O token não está publicado.
-   [ ] O fluxograma corresponde ao código.
-   [ ] O grupo consegue explicar o cálculo de distância.
-   [ ] O grupo consegue explicar como encontrou a linha recomendada.
-   [ ] A demonstração utiliza coordenadas reais e um cenário preparado.
-   [ ] As limitações do sistema estão documentadas.

## 14. Perguntas para verificar se todos entenderam

Cada integrante deve tentar responder sem consultar o código:

1.  O que é uma API e para que vamos usar a Olho Vivo?
2.  Qual é a diferença entre latitude e longitude?
3.  Por que não devemos comparar latitude e longitude usando apenas uma
    subtração simples?
4.  O que a fórmula de Haversine calcula?
5.  Como descobrir se uma parada está dentro do raio escolhido?
6.  Por que duas paradas próximas não garantem que exista uma viagem
    direta?
7.  Por que precisamos considerar o sentido e a sequência das paradas?
8.  O que é JSON e como ele é representado em Python?
9.  O que deve acontecer se a API retornar uma lista vazia?
10. Por que o token não pode ser publicado no GitHub?
11. Qual critério o grupo usa para escolher entre duas linhas
    candidatas?
12. O que o mapa mostra e o que ele não é capaz de garantir?

Se alguém não souber responder, esse é um bom assunto para revisar
juntos antes da apresentação.

## 15. Ordem recomendada para começar

Não tentem construir o sistema inteiro de uma vez. Avancem em pequenas
entregas:

1.  **Entender a API:** autenticar e fazer uma consulta simples.
2.  **Entender o JSON:** localizar os campos de linhas, sentidos,
    paradas e coordenadas.
3.  **Testar a distância:** calcular a distância entre duas coordenadas
    conhecidas.
4.  **Filtrar paradas:** testar com uma pequena lista de paradas antes
    de usar todos os dados da API.
5.  **Relacionar linhas:** descobrir como identificar paradas da mesma
    linha e do mesmo sentido.
6.  **Montar a recomendação:** selecionar uma viagem direta.
7.  **Consultar a previsão:** obter a previsão para o ponto de embarque.
8.  **Criar o mapa:** adicionar os marcadores necessários.
9.  **Tratar erros e testar:** simular entradas e respostas
    problemáticas.
10. **Preparar a entrega:** revisar notebook, GitHub, fluxograma e
    demonstração.

**Conselho final:** priorizem uma versão simples que funcione e que
todos consigam explicar. É melhor ter um algoritmo menor, correto e bem
justificado do que um projeto cheio de recursos que o grupo não entende.

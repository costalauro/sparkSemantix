# sparkSemanttix

Qual o objetivo do comando cache em Spark?

Tem como objetivo melhorar a eficiência ao permitir o reuso de resultados intermediários para operações lazy através do armazenamento, visto que estas operações 
em códigos interativos tendem a ocasionar em disparos desnecessários e repetitivos de resultados intermediários como por exemplo a leitura de um txt.

O mesmo código implementado em Spark é normalmente mais rápido que a implementação equivalente em MapReduce. Por quê?

Quando estamos fazemos essa comparação o fator memória fica evidente para a explicação do porque Spark é mais rápido.
No MapReduce, o resultado de cada job é escrito em disco, e precisa ser lido novamente do disco quando passado ao job seguinte o que torna necessário de rodar vários jobs 
MapReduce em sequência em vez de um único job. 

Já no Spark, os resultados intermediários são passados diretamente entre as operações através do caching desses dados em memória, o que reduz
a necessidade de escrita/leitura em disco, o que o torna superior ao MapReduce mesmo na execução de apenas 1 job, pois jobs Spark podem ser iniciados mais rapidamente devido ao
Spark manter a JVM em constantemente em execução em cada nó, precisando apenas iniciar uma nova thread, que é um processo extremamente mais rápido,
enquanto o MapReduce inicia uma nova instância para cada execução.

Qual é a função do SparkContext ?

Funciona como um conector do programa, independente da linguagem, com o ambiente de execução Spark. Serve para a passagem de configurações de alocação de recursos e para
a criação de RDD's, váriaveis de broadcast, acumuladores e para iniciar um job.

Explique com suas palavras o que é Resilient Distributed Datasets (RDD)

RDD é o conceito principal de abstração de dados do Spark. E tem as seguinte características:

1 - Resiliente à falhas, pois é capaz reprocessar partes de dados perdidas devido a falhas nos nós
2 - Distribuídos, podem estar divididos em partições através de diferentes nós em um cluster. 
3 - Imutáveis, são objetos de leitura, para que haja mutação é necessário realizar funções de transformação que criam novos RDD's
4 - Processamento paralelo, podem ocorrer operações em diferentes partições de um RDD simultâneamente 
5 - Lazyness, só são acessíveis e só se transformam quando ocorre uma ação
6 - Possuem valores categorizados em tipos.

GroupByKey é menos eficiente que reduceByKey em grandes dataset. Por quê?

É menos eficiente, pois quando ocorre uma agregação após um GroupByKey os resultados parciais não são calculados então um volume muito grande é transferido através dos executores
não levando em consideração a quantidadede memória disponível podendo ser necessário a realização de escrita em disco o que impacta diretamente o desempenho. 

Já quando utilizamos o reduceByKey o spark realiza o cálculo dos resultados parciais, pois tem conhecimento da operação passada que é informada como parâmetro para a atual,
sendo assim é gerado um volume de dados menor a ser transferido, o que é mais performático.

** Explique o que o código Scala abaixo faz **

1. val textFile = sc . textFile ( "hdfs://..." )
2. val counts = textFile . flatMap ( line => line . split ( " " ))
3.           . map ( word => ( word , 1 ))
4.           . reduceByKey ( _ + _ )
5. counts . saveAsTextFile ( "hdfs://..." )

1- Leitura de arquivo txt;
2- As linhas são separar em palavras e cada uma tem suas correspondentes a cada linha sendo transformadas em uma única coleção de palavras;
3- Map nas palavras por chave-valor, onde a chave é igual à palavra e o valor é inicialmente 1;
4- Ocorre o processo de agregação por chave; 
5- O RDD que armazenou a contagem das palavras é salvo em um arquivo txt.
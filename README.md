REDIS

redis-cli -> conecta no banco


################################KEYS#################################
KEYS * -> mostra todas as chaves cadastradas
KEYS "resultados*" -> traz todas chaves com inicio resultado
KEYS "resultado:1*-05-2015:megasena" -> traria todos os resultados de 10-05-2015 até 19-05-2015
? -> 1 caracter
* -> vários caracteres
[13] -> 1 ou 3

################################GET##################################
SET "CHAVE" "VALOR" -> salva um par chave valor no banco
MSET "CHAVE" "VALOR" "CHAVE" "VALOR" -> salvar vários ao mesmo tempo
HSET "CHAVE1" "CAMPO1" "VALOR" \__ você salva me um chave mais de umn valor, ou seja, utiliza um hash dentro do seu valor
HSET "CHAVE1" "CAMPO2" "VALOR" /

HMSET "CHAVE1" "CAMPO1" "VALOR_CAMPO1" "CAMPO1" "VALOR_CAMPO2" -> salva vários campos em uma chave

################################SET##################################
GET "CHAVE"
HGET "CHAVE1" "CAMPO1" -> recupera os valores salvos em HSET
HGETALL "CHAVE1" -> retornará todas as chaves e valores relacionados aos hashs de hashs

DEL "CHAVE"
HDEL "CHAVE1" "CAMPO1" -> deleta os valores salvos em HSET


################################EXPIRE##############################
EXPIRE "CHAVE" TEMPO_SEGUNDOS -> Faz com que uma chave seja removida após o tempo definido (muito usado para armazenar dados de sessão de um usuário).
Como esse usuário pode acessar outros servidores (no caso de aplicações escaláveis), ele não encontraria as informações da sessão. Para resolver isso armazenamos
no REDIS e assim podemos acessar de qlqr servidor. Usando o Expire podemos fazer por exemplo um carrinho de compra que seja excluído em 10 minutos.

TTL "CHAVE1" -> verifica quanto tempo a chave ainda tem até que expire

################################INCREMENTO##########################
INCR "CHAVE" -> incrementa o valor armazenado pela chave
INCRBY "CHAVE" QUANTIDADE -> incrementa com o valor especificado
INCRBYFLOAT "CHAVE" QUANTIDADE -> incrementa com o valor decimal especificado

DECR "CHAVE" -> decrementa o valor armazenado pela chave 
DECRBY "CHAVE" QUANTIDADE -> decrementa com o valor especificado

################################SETBIT##########################
SETBIT "CHAVE" "IDENTIFICADOR de 0até 2³²" "0 ou 1" -> Utilizada por exemplo para: quando vc quer saber se um usuario acessou o sistema no dia 25-05-2015
Exemplo: SETBIT "ACESSO:25-05-2015" "0" "1"

################################GETBIT##########################
GETBIT "CHAVE" "IDENTIFICADOR" -> retorna 0 ou 1 

################################BITCOUNT##########################
BITCOUNT "CHAVE" -> retorna a quantidade de '1' que tem naquela chave

################################BITOP##########################
BITOP OPERADOR(AND ou OR) "NOVACHAVE"  "CHAVE1" "CHAVE2" "CHAVE3"-> cria um "alias" com a nova operação. ele vai pegar a chave 1, chave 2 e chave 3 e usar o operador escolhido. 
Para usar vc pode utilizar o GETBIT NOVACHAVE IDENTIFICADOR

################################LPUSH##########################
LPUSH "chave" "Valor" -> cria uma lista e coloca um valor à esquerda (No início)
RPUSH ->adiciona na direita (no final)


################################LPOP##########################
LPOP "chave" -> retorna o primeiro valor da lista e o remove
RPOP -> retorna o ultimo valor da lista e o remove
BLPOP "CHAVE" TEMPO_DE_ESPERA -> Faz um pop...caso nao tenha elementos ele fica aguardando (o tempo de espera). Caso passe o tempo... ele retorna nil. caso nesse tempo entre algum item na lista, o mesmo é retornado. se no tempo de espera você definir o valor 0 ele vai ficar ativo até entrar algum item na lista
################################LRANGE##########################
LRANGE "chave" POSICAO_INICIAL POSICAO_FINAL -> Retornar os items da posicao_inicial até a final

################################LLEN##########################
LLEN "chave" -> Retorna o tamanho da lista

################################LTRIM##########################
LTRIM "chave" POSICAO_INICIAL POSICAO_FINAL -> Limpa todos os items que não estão dentro do range especificado


################################SET##########################
SADD "CHAVE" "VAL1" "VAL2" "VAL3"-> cria um conjunto
SCARD "CHAVE" -> retorna a quantidade de elementos
SREM "CHAVE" "VALOR" -> remove um valor do conjunto
SMEMBERS "CHAVE" -> Retorna todos os items do conjunto
SISMEMBER "CHAVE" "VALOR"-> verifica se um valor existe no conjunto
SINTER "CONJUNTO1" "CONJUNTO2"... -> Compara os itens em comum dos conjuntos
SDIFF "CONJUNTO1" "CONJUNTO2"...-> Retorna o conjunto 1 excluíndo os elementos iguais ao conjunto 2
SUNION "CONJUNTO1" "CONJUNTO2"...-> Retorna os elementos de todos os conjuntos sem repetir

################################Z##########################
ZADD "CHAVE" "valor1" "valor2"-> adiciona o item em um conjunto ordenado. o primeiro valor eh a chave q será comparada
ZCARD "CHAVE" -> retorna a quantidade de itens da coleção ordenada
ZREVRANGE "CHAVE" POSICAO_INICIAL POSICAO_FINAL WISHSCORES-> mostra todos os itens entre as posições especificadas


###############OUTROS#####################
TYPE "CHAVE" -> retorna qual o tipo da chave...(ZSET, string, set, )
FLUSHALL -> apaga tudo
################################PADRÃO###############################
padrão de nome de chaves separados por : -> exemplo resultado:17-02-2016:megasena 


#########Estratégia de Persistência#########
Snapshoting-> Grava o estado completo da memória em um arquivo no disco, em intervalos de tempo pré-configurados
Por exemplo, podemos instruir a instância do Redis para realizar o backup a cada 3 segundos e 3 atualizações do dataset. Quando os critérios forem atingidos, o Redis vai fazer um fork() e iniciará o backup, enquanto a instância continua operando sem bloqueios de I/O. Para começar, inicie uma instância do Redis conforme abaixo:
~$ redis-server --port 9999 --daemonize yes --save 3 3

Log appending-> A cada nova instrução que modifica o dataset, proveniente de qualquer cliente conectado, a mesma é gravada em um arquivo de log no disco. Caso o Redis seja reiniciado, o dataset é reconstruído a partir deste log, com a execução das instruções gravadas.
Para iniciar uma instância no modo AOF, basta executar:
~$ redis-server --port 9998 --daemonize yes --save "" --appendonly yes

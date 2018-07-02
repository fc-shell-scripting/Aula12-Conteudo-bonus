- [Conteúdo Bônus](#conteúdo-bônus)
    - [Printf](#printf)
        - [Printf com múltiplos argumentos](#printf-com-múltiplos-argumentos)
        - [Printf Com múltiplos dados no template](#printf-com-múltiplos-dados-no-template)
        - [formatação dos dados](#formatação-dos-dados)
    - [Operadores unários](#operadores-unários)
    - [source](#source)
    - [dialog](#dialog)
        - [buildlist](#buildlist)
        - [calendar](#calendar)
        - [checklist](#checklist)
        - [dselect](#dselect)
        - [editbox](#editbox)
        - [form](#form)
        - [fselect](#fselect)
        - [gauge](#gauge)
        - [infobox](#infobox)
        - [inputbox](#inputbox)
        - [inputmenu](#inputmenu)
        - [menu](#menu)
        - [msgbox](#msgbox)
        - [passwordbox](#passwordbox)
        - [pause](#pause)
        - [prgbox](#prgbox)
        - [radiolist](#radiolist)
        - [rangebox](#rangebox)
        - [textbox](#textbox)
        - [timebox](#timebox)
        - [treeview](#treeview)
        - [yesno](#yesno)

# Conteúdo Bônus

---

Você chegou até aqui! Parabéns! O básico de shell scripting foi passado nas aulas anteriores. O material desta aula é voltado para conteúdo adicional, que não é, necessáriamente, básico, mas traz algumas coisas interessantes para serem usados no dia a dia.

Nesta aula vamos resolver alguns problemas que tínhamos em exercícios anteriores, como a formatação de tabelas, verificar se um diretório ou arquivo existe, etc..

## Printf

O comando printf serve para mostrar informações na tela. Ele pode substituir o comando _echo_ e permite a formatação da mensagem. 

Um exemplo de _hello world_ seria:

```shell
#!/bin/bash
printf "%s" "hello world"
```

Conforme o exemplo acima, podemos ver que o comando printf recebe uma lista de argumentos. O prmiero deles vai ser o template que vamos usar. Este template é o esqueleto da mensagem, e nele vão ficar as informações referentes a formatação dos dados.

Os próximos argumentos são os dados para preencher o template. Dentro do template temos _%s_, que identifica que o prmiero argumento será uma string. Este é o tipo de dado que mais vamos utilizar, mas veremos outros tipos de dados posteriormente. O segundo parâmetro é _hello world_, que é a string a ser usada dentro do template.

### Printf com múltiplos argumentos

E se quisermos mostrar vários dados neste mesmo template? Nesse caso podemos passar mais argumentos para o comando, mantendo o mesmo template. O comando _printf_ entende que se tiver mais argumentos que posições no template, então o comando deve se repetir. Isso é um pouco confuso no começo, mas depois fica claro:

```shell
#!/bin/bash
printf "%s\n" sempre linhas novas
```

O comando acima irá mostrar tres linhas. cada linha contém uma palavra da frase "sempre linhas novas". Vamos analisar o que o comando irá fazer, passo a passo:

1. _printf_ recebe o template a ser usado: "%s\n"
2. _printf_ pega apenas o próximo argumento, para preencher no template.
3. _printf_ substitui %s pelo argumento do passo anterior
4. _printf_ mostra na ela a mensagem do template, depois da substituição, e faz uma quebra de linha, conforme o template.
5. _printf_ verifica que ainda existem argumentos passados, e repete os passos 2,3, e 4  para os próximos argumentos.

E se o template tiver mais de um placeholder (%s, %d,etc..) ? O _printf_ sempre busca a quantidade de parâmetros de acordo com o template, então ele pode buscar os parâmetros de 2 em 2, 3 em 3, etc.. de acordo com o template:

```shell
#!/bin/bash
# Este comando busca os argumentos de 2 em dois. Um para o nome, e outro para o sobrenome
printf "Nome: %s - Sobrenome:%s\n" nome sobrenome fernando crozetta
```

### Printf Com múltiplos dados no template

Vamos analisar um exemplo com mais complexo?

```shell
#!/bin/bash
printf "Ola %s, sao %s horas e %s minutos\n" $(whoami) $(date +%H) $(date +%M)
```

Neste exemplo, vemos que o template recebe três strings. Vamos separar em três partes o template:

__Template parte 1:__

A primeira parte do template:

>"_Ola %s,_"

Este _%s_ vai ser preenchido com o nome do usuário atual, para isso, vamos usar o comando _whoami_.

__Template parte 2:__

A segunda parte do template:

>"_sao %s horas_"

Este _%s_ será substituído pela hora atual, e para isso vamos utilizar o comando _date +%H_.

__Template parte 3:__

A última parte do template:

>"_e %s minutos\n_"

Este %s será substituído pelos minutos atuais. Para isso, vamos utilizar o comando _date +%M_.

Desta forma, vamos passar 4 argumentos ao printf: o template e mais três referentes aos dados, na ordem em que aparecem.

Apesar de ser um pouco confuso no começo, esta forma de trabalho é muito comum em outras linguagens de programação, e ainda permite que os dados sejam formatados, como veremos a seguir:

### formatação dos dados

Quando fazíamos tabelas, nas aulas anteriores, sempre precisáva
alcular o tamanho dos dados para poder fechar a tabela. Quando usamos o printf, podemos fazer isso definindo o tamanho de cada string. 

Para exemplificar, vamos fazer uma tabela de dados contendo o nome de um produto e a quantidade em estoque:

Desejamos algo neste formato:

| PRODUTO | ESTOQUE |
|:--------|--------:|
|Maca     |        5|
|Pera     |       12|
|Abacate  |       25|

Então podemos fazer o script da seguinte maneira:

```shell
#!/bin/bash
# O tamanho da tabela será 25 por enquanto
linha="+-----------------------+\n"
templateCabecalho="| %10s | %8s |\n"
templateDados="| %10s | %08d |\n"

printf $linha
printf "$templateCabecalho" "PRODUTO" "ESTOQUE"
printf $linha
printf "$templateDados" "Maca" "5" "Pera" "12" "Abacate" "25"
```

Vamos analisar o _templateCabecalho_ primeiro:

Ele possui um caracter pipe no começo, que será a lateral esquerda da tabela. Depos existe u _%10s_, que é um _%s_ como vimos anteriormente, mas com tamanho 10. Isso quer dizer que se o dado passado tiver menos que 10 caracteres, o printf irá completar com espaços na esquerda. Isso resulta em um alinhamento a direita.

Depois outro pipe, e _%8s_, que irá definir o tamanho do segundo parâmetro como 8. Se tiver menos que isso, será acrescentado a esquerda também.

Agora vamos analisar o _templateDados_:
os pipes serão printados para mostrar as laterais da tabela, e divisões entre os campos. Igual no template anterior. O primeiro placeholder é _%10s_, que é uma string de tamanho 10, igual ao template anterior, também.

Temos por ultimo um placeholder diferente: _%08d_. O _%d_ indica que o valor é um dígito, e não uma string. O dígito 8 indica o tamanho do dígito, e o 0 antes, indica que deve ser preenchido com zeros a esquerda para completar o tamanho. O 0 pode ser substituído por um espaço, se você desejar preencher com espaços.

Comparando com o exemplo da tabela dada no início, ainda não está bem como desejado. Precisamos alinhar os nomes à esquerda e remover os zeros à esquerda dos dados de estoque.

Para inverter o alinhamento, vamos adicionar um hífen após o _%_ do placeholder. para centralizar o texto "PRODUTO", vamos adicionar 2 espaços após a palavra. para remover os zeros à esquerda, vamos trocar o 0, por um espaço. O script ficará assim:

```shell
#!/bin/bash
# O tamanho da tabela será 25 por enquanto
linha="+-----------------------+\n"
templateCabecalho="| %10s | %8s |\n"
templateDados="| %-10s | % 8d |\n"

printf $linha
printf "$templateCabecalho" "PRODUTO  " "ESTOQUE"
printf $linha
printf "$templateDados" "Maca" "5" "Pera" "12" "Abacate" "25"
printf $linha
```

Agora vamos adicionar um valor para cada produto. Valores podem conter conter separadores decimais, como vígulas e pontos. Você precisa prestar atenção a qual deles o seu sistema está usando. Em PT-BR, o separador é vírgula, mas em inglês, por exemplo, o separador é um ponto. Se O exeplo abaixo não funcionar, troque as vírgulas por pontos.

Para valores com vírgula, podemos usar _%f_, que identifica um float. Float é um tipo de dados com casas decimais. Nós podemos configurar a precisão das casas decimais, usando o modificador pornto. _%.2f_ é um númoro com duas casas decimais após a vírgula.

A tabela deve ficar assim:

| PRODUTO | ESTOQUE | VALOR UNITARIO |
|:--------|--------:|---------------:|
|Maca     |        5|           12,30|
|Pera     |       12|           21,00|
|Abacate  |       25|            5,60|

O script deve ficar assim:

```shell
#!/bin/bash
linha="+----------------------------------------+\n"
templateCabecalho="| %10s | %8s | %14s |\n"
templateDados="| %-10s | % 8d | %14.2f |\n"

printf $linha
printf "$templateCabecalho" "PRODUTO  " "ESTOQUE" "VALOR UNITARIO"
printf $linha
printf "$templateDados" "Maca" "5" "12,3" "Pera" "12" "21,0" "Abacate" "25" "5,6"
printf $linha
```

Executando este script, você deve receber a seguinte informação na tela:

```text
+----------------------------------------+
|  PRODUTO   |  ESTOQUE | VALOR UNITARIO |
+----------------------------------------+
| Maca       |        5 |          12,30 |
| Pera       |       12 |          21,00 |
| Abacate    |       25 |           5,60 |
+----------------------------------------+
```

## Operadores unários

---

Até a aula anterior, Toda vez que fosse necessário verificar se algum arquivo ou diretório existia, seria necessário fazer um comando _ls_, seguindo de um cut para buscar alguma informação. Mas existe uma forma mais simples de realizar este tipo de verificação, e outras verificações de retorno binário.

Um exemplo simples:

```shell
if [[ -f /caminho/do/arquivo.txt ]]; then
    echo "arquivo existe"
else
    echo "arquivo nao existe"
fi
```

O nome de operador unário se deve ao fato de não estarmos comparando uma variável com outra, mas apenas verificando se algo existe.

O operador _-f_ verifica se um arquivo existe. Existem outros operadore que você pode usar. Entre eles:

1. -d: Verifica se o arquivo passado é um diretório
2. -h: Verfica se o arquivo passado é um link simbólico
3. -r: Verifica se o arquivo é existe e possui permissão de leitura
4. -w: Verifica se o arquivo é existe e possui permissão de gravação
5. -x: Verifica se o arquivo é existe e possui permissão de execução
6. -s: Verifica se o arquivo existe e o tamanho é maior que 0

## source

O comando source permite que sejam executados programas dentro do shell.
Podemos usar este comando para importar configurações globais para os sscripts que criarmos

```shell
source arquivo.cfg
```

Este comando é o equivalente ao comando _._ (ponto)

## dialog

O programa dialog permite a criação de janelas em prompts, permitindo a criação de interfaces simples para a interação com o usuário.
Vocẽ pode instalar o programa usando o instalador de pacotes do sistema operacional. 

> Em sistemas _Debian_, ou baseados em _Debian_, o programa _whiptail_ pode ser usado para substituir o comando _dialog_.
> O _whiptail_ pode já estar instalado.

Você pode ler o manual do _dialog_, usando o comando:

```shell
man dialog
```

Mas, caso queira apenas um resumo de como montar uma determinada janela, use o comando

```shell
dialog -h
```

Por padrão, sempre usarei o parâmetro _--stdout_ para jogar os retornos em tela e facilitando o uso dos dados dentro de scripts

### buildlist
o Build list te permite adicionar/remover itens de uma determinada lista. Você pode definir quais itens estarão selecionados ou não, e quando o usuário confirmar as seleções, será retornada a lista de elementos selecionados.

__OBS:__ Você pode usar os caracteres _^_ e _$_ para navegar para o primeiro e segundo menu, respectivamente. Ou, adicione o parâmetro _--visit-items_ para navegar com _TAB_ e com as setas.

No exemplo abaixo, você verá uma lista de itens para montar um sanduíche:

```shell
#!/bin/bash

dialog --stdout --visit-items --buildlist "Escolha alguns dos itens:"  20 50 5 \
t1 "Hamburguer" off  \
t2 "Segundo hamburguer" on \
t3 "Alface" off \
t4 "Queijo" on \
t5 "Molho especial" on \
t6 "Cebola" off \
t7 "Picles" on \
t8 "Pão, com gergilim" off
# Amo muito tudo isso
```

Vamos decompor o programa acima:

- As barras no final de cada linha são usadas para quebrar uma linha de comando em várias (facilita a leitura). Elas não fazem parte do comando
- O comando _dialog --stdout --visit-items --buildlist "Escolha alguns dos itens:"_ é para montar uma buildlist, usando o std como saída, e podendo acessar os itens com as setas.
- Os três valores passados(20,50,5) identificam altura,largra e altura da lista, respectivamente.
- t1 "Hamburguer" e _off_ identificam a tag a ser retornada, a descrição do item, e se ele está selecionado.

Quando um item está marcado como _on_, ele ficará na lista da direita, pois já está selecionado. Para adicionar/remover itens das listas utilize a barra de espaço.

As tag selecionadas serão retornadas quando o usuário der enter no botão _OK_.

### calendar

O calendário é uma opção fácil para o usuário entrar com uma data. a forma mais simples de uso, passamos apenas um título, altura e largura. O próprio _dialog_ se encarrega de deixar o dia selecionado como o dia atual:

```shell
dialog --calendar "Escolha uma data" 0 0
```

Você também pode passar os dados de _dia_, _mês_ e _ano_ para que o comando defina a data incial do calendário:

```shell
dialog --calendar "Escolha uma data" 0 0 22 12 1992
```

### checklist

Como o nome já diz, este tipo de janela monta um checklist para o usuário marcar as opções que desejar. Quando confirmado, os valores serão retornados em formato de lista:

```shell
dialog --checklist 20 50 5 \
t1 "item 1" off \
t2 "item 2" on
```

Perceba que a sintaxe é muito parecida com o buildlist, por exemplo. Sua função é muito parecida, apenas sua forma de apresentação para o usuário é diferente.

### dselect

esta janela é usada para escolher um diretório. o usuário pode colocar _/_ após o diretório escolhido para acessar o diretório e ver seus subdiretórios. No exemplo abaixo vamos passar o diretório _/_ como diretório para iniciar a consulta:

```shell
dialog --dselect / 20 50
```

### editbox

o editbox abre um arquivo e permite que o usuário edite seus dados. Quando clicado em _OK_, o conteúdo do arquivo é jogado na tela, sem alterar o arquivo original.

Para este exemplo, crie um arquivo contendo algumas palavras e salve com o nome _teste.txt_
em seguida, execute o comando:

```shell
dialog --editbox teste.txt 0 0
```

### form

O form gera um formulário para o usuário preencher. Por ser mais complexos, vamos ver o exemplo e depois analisar seus parâmetros:

```shell
#!/bin/bash

dialog --form "preencha os dados:" 25 50 10 \
"Nome:" 1 1 "Fernando Crozetta" 1 10 30 0 \
"Telefone:" 2 1 "912345678" 2 10 10 0 \
"email:" 3 1 "fernando@czetta.com" 3 10 20 0
```

Os primeiros quatro parâmetros indicam um texto a ser mostrado, a altura, largura e altura do formulário.

Depois, para cada item do formulário:

- Descrição do campos
- Altura no formulário
- Início do texto no formulário
- valor padrão (pode ser "", para indicar vazio)
- Altura do campo de resposta, dentro do formulário
- Coluna de início do campo de resposta
- Tamanho do campo de resposta
- Tamanho do texto dentro do campo de resposta. (se for 0, é definido como o tamanho do campo)

### fselect

O comando fselect serve para selecionar um arquivo do sistema. Seu funcionamento é igual ao do dselect:

```shell
dialog --fselect / 25 50
```

### gauge

Este widget é usado para mostrar a porcentagem em uma barra de progresso. 

Como ele mostra apenas um valor, você precisa atualizar os dados a cada passo. 

Um dos exemplos mais comuns de se encontrar, é:

```shell
#!/bin/bash
for i in {0..100..25}; do
    sleep 0.06
    echo "$i" | dialog --gauge "aguarde" 7 70 0
done
```

### infobox

A janela de infobox mostra uma jnela com a informação, e sai sem pedir confirmação do usuário.

```shell
dialog --infobox "Hello world" 25 50
```

### inputbox

O inputbox serve para solicitar uma string para o usuário:

```shell
dialog --iputbox "Digite seu nome" 25 50
```

Podemos também passar mais um argumento, que será usado como valor default:

```shell
dialog --iputbox "Digite seu nome" 25 50 "Fernando Crozetta"
```

### inputmenu

input menu é um menu que permite a edição de um campo. Este widget cria um botão chamado renomear, que permite a alteração de um campo. Quando o usuário finalizar, será mostrado _RENAMED campo valor_.

```shell
dialog --inputmenu "teste" 25 50 10 nome "nome1" fone "" email "fernando@czetta.com"
```

### menu

A opção de menu permite ao usuário escolher um item de uma lista.

```shell
dialog --menu "menu" 25 50  10 t1 "opção 1" t2 "opção 2"
```

### msgbox

Este widget mostra uma janela com informações, igual ao infobox. Pórém, será mostrado um botão de _OK_ para que o usuário confirme.

```shell
dialog --msgbox "hello world" 25 50
```

### passwordbox

O passwordbox funciona como um input, mas ele não mostra o que foi digitado.

```shell
dialog --passwordbox "senha:" 25 50
```

Você também pode adicionar a opção _--insecure_ que vai mostrar um asterisco para cada caracter digitado:

```shell
dialog --insecure --passwordbox "senha:" 25 50
```

### pause

Este dialog irá mostrar um contador de tempo na parte inferior da janela, com uma contagem regressiva. Passe além do texto,altura e largura, um valor de tempo, em segundos. No exemplo o tempo é 2:

```shell
dialog --pause "aguarde" 0 0 2
```

### prgbox

Este widget mostra o resultado de um comando na tela, na forma de um msgbox:

```shell
dialog --prgbox "echo '2+2' | bc" 0 0
```

### radiolist

Radiolist mostra uma lista de opções e permite que o usuário escolha apenas um. Quando outra opção for marcada, todas as outras serão desmarcadas.

```shell
dialog --radiolist "Opções" 25 50 10 \
t1 "opção 1" on \
t2 "opção 2" off \
t3 "opção 3" off
```

### rangebox

Este widget mostra uma barra de progresso para o usuário escolher um valor. as setas para cima e para baixo diminuem e aumentam o valor. Além da altura e largura, passar o valor mínimo, máximo e default. No exemplo, o valor mínimo é 1, o máximo é 100, e o default é 50:

```shell
dialog --rangebox "escolha um valor" 0 0 1 100 50
```

### textbox

Este widget carrega o conteúdo de um arquivo e mostra na tela, como um msgbox

```shell
dialog --textbox teste.txt 0 0
```

### timebox

Carrega uma janela para escolha de horário. Por padrão, carrega o horário atual do sistema:

```shell
dialog --timebox 0 0
```

Mas pode ser chamado passando um horário específico (Horas minutos e segundos):

```shell
dialog --timebox 0 0 12 23 34
```

### treeview

tree view mostra os dados seguinto uma hierarquia. para simplificar a compreensão, o script de exemplo possui indentação nos itens que forem filhos dos itens superiores:

```shell
#!/bin/bash

dialog --treeview "tree view" 25 50 10 \
t1 "item 1.0" On 0   \
        t2 "item 1.1" OFF 1 \
        t3 "item 1.2" OFF 1 \
        t4 "item 1.3" off 1 \
t5 "item 2.0" off  0 \
        t6 "item 2.1.0" off 1 \
                t7 "item 2.1.1" off 2 \
                t8 "item 2.1.2" off 2 \
        t9 "item 2.2" off 1 \
        t10 "item2.3" off 1 \
```

Os parâmetros no ínicio são _texto_, _altura_, _largura_, _altura da lista_.

Para cada item, na ordem:

- tag do item
- descrição do item
- status(on/off) para saber se está marcado. Apenas um por treeview pode ser on.
- nível da hierarquia (começando em zero para os itens de raiz)

### yesno

O yesno abre um ajanela para que o usuário possa confirmar/cancelar.

a sua forma mais simples:

```shell
dialog --yesno "deseja sair?" 0 0
```
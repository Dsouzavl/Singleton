# O padrão _Singleton_

O padrão de projeto _Singleton_ é utilizado para permitir que exista apenas uma instância da classe que será acessada globalmente e compartilhada para todas as classes que desejam utilizá-la. O único caso em que achei utilização de _Singleton_ nas minhas aplicações até hoje, foi para classes de configurações, já que não faz sentido eu ter cópias dela espalhadas, existem outros casos com certeza, mas mantenha a idéia de que deve ser apenas utilizado _Singleton_ quando queremos que exista apenas uma instância da classe sendo utilizada, quando vocÊ não quer replicar algo que pode ser único, mesmo que tenha uma mutabilidade cosntante, mas não em pouco perído de tempo, por exemplo um **response** de uma aplicação que só se altera a cada 1 mês ou 1 semana, faz mais sentido quando esse response em "memória" como um _Singleton_, do que ficar fazendo request toda hora para a aplicação, pense como seria se possuíssemos várias aplicações que fazem request constante, quando o response é o mesmo durante 1 mês ou 1 semana, isso tá mais com cara de ataque DDos do que funcionalidade, se guardássemos esse response como _Singleton_, e o distribuíssemos a cada request recebido, seria um caso de uso vantajoso para _Singleton_. 

Sabendo disso, vou demonstrar um exemplo de como criar uma classe _Singleton_ usando **c#**:            

```csharp
    public class ClassicSingleton
    {
        // Criamos uma propriedade estática que receberá nosso objeto único do tipo ClassicSingleton
        private static ClassicSingleton singleInstance;

        // Utilizamos um construtor privado, para impedir que a classe seja instânciada, e apenas nosso método GetSingleInstance() seja capaz de construir o objeto.
        private Singleton() { }

        // Criamos um método público, estático para que possa ser acessado  por classes é óbvio, responsável por retornar nossa instância única, ou criar, se o valor da nossa propriedade for nulo.
        public static Singleton GetSingleInstance()
        {
            if(singleInstance == null)
            {
                singleInstance = new ClassicSingleton();
                return singleInstance;
            }
            else
            {
                return singleInstance;
            }
        } 
    }
```
Parece simples correto? Correto, mas o código acima tem problemas, por exemplo, em aplicações _Multi-Thread_, podemos ter o caso de ambas as threads chamarem o método ao mesmo tempo, e se o valor da propriedade for nulo, será retornado duas instâncias da classe _Singleton_, e isso não está correto. Há duas formas que eu conheço de resolvermos isso, observe:

```csharp
    public class SingletonWithLock
    {
        private static SingletonWithLock singleInstance;

        private static readonly object syncObj = new object();

        private SingletonWithLock() { }

        public static SingletonWithLock GetSingleInstance()
        {
            if(singleInstance == null)
            {
                lock (syncObj)
                {
                    if (singleInstance == null)
                    {
                        singleInstance = new SingletonWithLock();
                    }
                }
                return singleInstance;
            }
            else
            {
                return singleInstance;
            }
        }
    }
```

Nessa primeira maneira, estamos utilizando lock, o que o lock faz é bloquear um determinado objeto, marcar um bloco instrução como crítico onde apenas a thread que acessou primeiro irá executar, e todas as outras que tentarem acessar aquele bloco ficarão bloqueadas, criamos um novo object  para utilizar no lock pois ele exige como parâmetro um objeto para bloquear , após isso será feita uma segunda verificação para checar se o valor da nossa propriedade não foi alterada, e o algoritmo restante será executado assim como na classe **ClassicSingleton**, após o bloco crítico ser executado, o lock será liberado, e as outras threads executarão os blocos, mas paramos de correr o risco de ter duas instâncias da classe, pois o valor da propriedade já foi alterado caso fosse nulo, então apenas o valor da propriedade será retornado. A outra maneira de evitarmos o acontecimento de termos duas instânciações é:

```csharp
    
    public class StaticSingleton
    {
        // Dessa forma, usamos o modificador readonly para que o valor da propriedade possa ser inicializado com um novo objeto, então ela nunca vai ser nula, mas vai conter sempre a mesma instância 
        // que foi inicializada com a classe.
        private readonly static StaticSingleton singleInstance = new StaticSingleton();

        private StaticSingleton() { }

        // Temos um método que apenas retorna a instância criada quando a nossa propriedade foi inicializada.
        // não precisamos fazer verificação se a propriedade é ou não é nula, pois ela já foi inicializada.
        public static StaticSingleton GetInstance()
        {
            return singleInstance;
        }
    }
``` 

Dessa forma, marcamos a propriedade como _readonly_, e já inicializamos o seu valor quando a classe for carregada, e nosso método apenas retorna a propriedade que já tem seu valor incializado. Dessa forma evitamos que exista mais de uma instância da classe _Singleton_, pois o valor da propriedade é inicializado quando a classe é carregada, então ela nunca vai ser nula.

## Final

O padrão _Singleton_ é um padrão de casos e casos, não abuse de seu uso, mas também não se negue a utilizá-lo, ele pode vir a calhar em certas situações.

Este é o primeiro, dos 5 artigos que estarei postando até terça-feira, espero que lhe ajudem a compreender um pouco mais sobre Padrões de Projeto, que como eu ouvi uma vez: _"São o caminho, a luz e a salvação"_ quando se trata de escrever software orientado a objeto. 

Obrigado a todos os leitores! Bom fim de semana.

### _P.s: Obrigado Hian Castilho, pelo exemplo do request DDos_. 
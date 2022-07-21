# TypeScript: princípios SOLID

## O que iremos fazer?

### Requisitos

Esse conteúdo pressupõe que o aluno já tem algum conhecimento sobre:

- **Programação orientada a objetos**

### Objetivo

O objetivo deste conteúdo é mostrar os princípios do SOLID aplicado no Typescript.

### O que será abordado

- **O que é SOLID?**
- **S - Single responsibility (Princípio de única responsabilidade)**
- **O - Open/closed (Princípio de aberto/fechado)**
- **L - Liskov substitution (Princípio da substituição de Liskov)**
- **I - Interface segregation (Princípio da segregação de Interface)**
- **D - Dependency inversion (Princípio da inversão de dependência)**

---

## 1. O que é SOLID?

É um termo criado pelo engenheiro de software Robert C. Martin(também conhecido como "Uncle Bob").

Solid(ou S.O.L.I.D) é um acrônimo para cinco princípios do design de software orientado a objetos.

O objetivo é criar softwares que sejam mais fáceis de: 

- **Entender**
- **Alterar**
- **E dar manutenção.**

Cada letra do acrônimo são princípios que significam:

- **S** - Single responsibility (Princípio de única responsabilidade)
- **O** - Open/closed (Princípio de aberto/fechado)
- **L** - Liskov substitution (Princípio da substituição de Liskov)
- **I** - Interface segregation (Princípio da segregação de Interface)
- **D** - Dependency inversion (Princípio da inversão de dependência)

---
## 2. Single responsibility (Princípio de única responsabilidade)

O Princípio de única responsabilidade diz: 
>*"todo modulo, classe ou função deve ser responsável por apenas uma parte do programa"*

Por exemplo, uma função não deve implementar o envio de email junto com a geração de relatórios.

#### Violando o princípio
    class FakeEmailSender{
        static sendEmail(email: string, body: string): boolean{
            console.log(`Email enviado para ${email} com o conteúdo: ${body}`)

            return true;
        }
    }

    //gerarRelatorioEEnviaEmail está violando o princípio
    function gerarRelatorioEEnviaEmail(email: string, idsDosRelatorios: number[]){
        // Gera o relatório
        let relatorios:string="";
        for(let index=0 ; index < idsDosRelatorios.length; index++){
            relatorios= relatorios + `relatorio ${index}\n`
        }

        // Envia o email
        FakeEmailSender.sendEmail(email, relatorios);
    }


    gerarRelatorioEEnviaEmail('teste@gmail.com', [1,2,3,4])

#### Atendendo o princípio

    class FakeEmailSender{
        static sendEmail(email: string, body: string): boolean{
            console.log(`Email enviado para ${email} com o conteúdo: ${body}`)

            return true;
        }
    }


    function gerarRelatorioEEnviaEmail(email: string, idsDosRelatorios: number[]){
        // Gera o relatório
        let relatorio=gerarRelatorio(idsDosRelatorios);

        // Envio de email
        FakeEmailSender.sendEmail(email, relatorio);
    }

    // Implementação da geração do relatório
    function gerarRelatorio(idsDosRelatorios: number[]): string{
        let relatorios:string="";
        for(let index=0 ; index < idsDosRelatorios.length; index++){
            relatorios= relatorios + `relatorio ${index}\n`
        }

        return relatorios;
    }


    gerarRelatorioEEnviaEmail('teste@gmail.com', [1,2,3,4])


#### Qual o benefício quando se segue o princípio

- A manutenção e evolução desse código fica mais fácil quando cada método tem apenas uma responsabilidade. 

- Evita que métodos ou funções fiquem muito grandes. 

- Facilita a compreensão do código.


---
## 3. Open/closed (Princípio de aberto/fechado)

O Princípio de aberto/fechado diz: 

>*"entidades de software(classes, módulos, funções e etc.) devem estar abertas para extensão mas fechada para modificação"*

Por exemplo, não ter uma mesma classe para representar diferente tipos de "formatos".

#### Violando o princípio

    enum Forma{
        Quadrado,
        Tringulo
    }

    // Desenhar viola o princípio
    function Desenhar(formas:Forma[]){
        for(const forma of formas){
            let resultado:string=""

            switch (forma){
                case Forma.Quadrado:
                    resultado=`
                        ******
                        *    *
                        *    *
                        *    *
                        ******`
                    break;
                case Forma.Tringulo:
                    resultado=`
                           *
                          * *
                         *   *
                        *******`
                    break;
            }
            
            console.log(resultado)
        }
    }

    let formasDeExemplo:Forma[]=[];

    formasDeExemplo.push(Forma.Quadrado);
    formasDeExemplo.push(Forma.Tringulo);
    Desenhar(formasDeExemplo)

#### Atendendo o princípio

    abstract class Forma{
        public abstract Forma():string;
    }

    class Quadrado extends Forma{
        public Forma(): string {
            return `
            ******
            *    *
            *    *
            *    *
            ******`
        }
    }

    class Tringulo extends Forma{
        public Forma(): string {
            return `
               *
              * *
             *   *
            *******`
        }
    }

    function Desenhar(formas:Forma[]){
        for(const forma of formas){
            console.log(forma.Forma())
        }
    }

    let formasDeExemplo:Forma[]=[];

    formasDeExemplo.push(new Quadrado);
    formasDeExemplo.push(new Tringulo);
    Desenhar(formasDeExemplo)

#### Qual o benefício quando se segue o princípio
- Para adicionar um novo desenho apenas crie uma nova classe que estende "Forma". Não é necessário mudar a função "Desenhar". 

- Reduz a chance de uma nova funcionalidade quebrar algo que estava funcionando.
A função "Desenhar" nao fica com a responsabilidade se saber como desenhar as "Formas"

---
## 4. Liskov substitution (Princípio da substituição de Liskov)

Esse principio é baseado no conceito de "substituibilidade" - um principio que diz que um objeto, por exemplo da classe "Roda", e um objeto, por exemplo da subclasse "RodaDeTitanio" que estende "Roda", devem poder serem substituídos entre si sem atrapalhar o funcionamento correto do programa.

Caso o principio esteja sendo violado ele indica que o design dessas classes estão ruins.

#### Violando o princípio

    class Ave{
        public Voar(){
            console.log('Ok! Estou voando weeeee');
        }
    }

    class Pato extends Ave{
        public Grasnar(){
            console.log('Qua Qua Qua');
        }
    }

    class Penguin extends Ave{
        public override Voar(){
            throw new Error('Sei voar não!!!');
        }

        public Nadar(){
            console.log('Bora nadarr');
        }
    }


    function fazerAveVoar(ave:Ave){
        ave.Voar();
    }

    const pato = new Pato;
    const penguin = new Penguin;

    fazerAveVoar(pato)
    fazerAveVoar(penguin)

#### Atendendo o princípio

    class AveVoadora{
        public Voar(){
            console.log('Ok! Estou voando weeeee');
        }
    }

    class AveNadadora{
        public Nadar(){
            console.log('Bora nadarr');
        }
    }

    class Pato extends AveVoadora{
        public Grasnar(){
            console.log('Qua Qua Qua');
        }
    }

    class Penguin extends AveNadadora{
    }


    function fazerAveVoar(ave:AveVoadora){
        ave.Voar();
    }

    function fazerAveNadar(ave:AveNadadora){
        ave.Nadar();
    }

    const pato = new Pato;
    const penguin = new Penguin;

    fazerAveVoar(pato)
    fazerAveNadar(penguin)



#### Qual o benefício quando se segue o princípio
- Ajudar a fazer a modelagem das classes de forma correta.

- Permite que seja usado uma sub classe sem o receio do comportamento não fazer sentido(ou produzir erros inesperados).

#### Explicação em video usando outro exemplo

https://www.youtube.com/watch?v=eble4QE2mBw

---
## 5. Interface segregation (Princípio da segregação de Interface)

O Princípio da segregação de Interface diz:

>*nenhum código deve ser forçado a depender de métodos/funções que ele não usa*

É preferível tem varias interfaces especificas do que apenas uma interface geral.

#### Violando o princípio

    interface Veiculo{
        Cor: string;
        Preco: number;
        Ligar(): void;
        Voar(): void;
    }

    // Carro depende de uma interface que não usa o método "Voar"
    class Carro implements Veiculo{
        Cor: string;
        Preco: number;
        Ligar(): void {
            console.log('Ligando carro');
        }
        Voar(): void {
            throw new Error("Carro não consegue voar");
        }

        constructor(cor:string, preco: number){
            this.Cor = cor;
            this.Preco = preco;
        }
    }

    class Aviao implements Veiculo{
        Cor: string;
        Preco: number;
        Ligar(): void {
            console.log('Ligando avião');
        }
        Voar(): void {
            console.log('Iniciando voo');
        }

        constructor(cor:string, preco: number){
            this.Cor = cor;
            this.Preco = preco;
        }
    }


#### Atendendo o princípio

    interface Veiculo{
        Cor: string;
        Preco: number;
        Ligar(): void;
    }

    interface Voador{
        Voar(): void
    }

    class Carro implements Veiculo{
        Cor: string;
        Preco: number;
        Ligar(): void {
            console.log('Ligando carro');
        }

        constructor(cor:string, preco: number){
            this.Cor = cor;
            this.Preco = preco;
        }
    }

    class Aviao implements Veiculo,Voador{
        Cor: string;
        Preco: number;
        Ligar(): void {
            console.log('Ligando avião');
        }
        Voar(): void {
            console.log('Iniciando voo');
        }

        constructor(cor:string, preco: number){
            this.Cor = cor;
            this.Preco = preco;
        }
    }



#### Qual o benefício quando se segue o princípio
- Separa melhor as dependências já que as classes passam a depender apenas do que realmente precisam para funcionar.
- Ajudar a desacoplar o código deixando mais fácil de mudá-lo.

---
## 6. Dependency inversion (Princípio da inversão de dependência)

O Princípio da inversão de dependência diz:

>*dependa de abstrações, não da implementação*

Dependa de interfaces ou classes abstratas invés da implementação.

#### Violando o princípio

    class Produto{
        public Nome: string;
        public Preco: number;
        public Id: number;

        constructor(nome: string, preco: number, id: number){
            this.Nome = nome;
            this.Preco = preco;
            this.Id = id;
        }
    }

    class FakeBancoDeDados {
        static Salvar(objeto: {Id: number}){
            console.log(`Salvando id: ${objeto.Id}`);
        }

        static Atualizar(objeto: {Id: number}){
            console.log(`Atualizando id: ${objeto.Id}`); ;
        }
    }
    // "ProdutoService" viola o principio
    // "ProdutoService" depende do "FakeBancoDeDados" aonde está implementado
    // o acesso ao banco de dados
    class ProdutoService{
        public static Salvar(produto: Produto){
            FakeBancoDeDados.Salvar(produto);
        }

        public static Atualizar(produto: Produto){
            FakeBancoDeDados.Atualizar(produto);
        }
    }

    const produto=new Produto('Produto1', 10, 0);

    ProdutoService.Salvar(produto);
    produto.Nome='Produto modificado';
    ProdutoService.Atualizar(produto);


#### Atendendo o princípio

        class Produto{
        public Nome: string;
        public Preco: number;
        public Id: number;

        constructor(nome: string, preco: number, id: number){
            this.Nome = nome;
            this.Preco = preco;
            this.Id = id;
        }
    }

    interface BancoDeDados{
        Salvar(objeto: {Id: number}):void;
        Atualizar(objeto: {Id: number}):void;
    }

    class FakeBancoDeDados {
        Salvar(objeto: {Id: number}):void{
            console.log(`Salvando id: ${objeto.Id}`);
        }

        Atualizar(objeto: {Id: number}):void{
            console.log(`Atualizando id: ${objeto.Id}`); ;
        }
    }
    // "ProdutoService" não viola o principio
    // "ProdutoService" depende da Interface BancoDeDados
    // qualquer outra classe que implementar BancoDeDados
    // poderia ser usada
    class ProdutoService{
        public Salvar(produto: Produto){
            this.bancoDeDados.Salvar(produto);
        }

        public Atualizar(produto: Produto){
            this.bancoDeDados.Atualizar(produto)
        }

        constructor(bancoDeDados: BancoDeDados){
            this.bancoDeDados = bancoDeDados
        }

        private bancoDeDados: BancoDeDados;
    }

    //A implementação agora é passada pelo construtor de ProdutoService
    const produtoService = new ProdutoService(new FakeBancoDeDados);

    const produto=new Produto('Produto1', 10, 0);

    produtoService.Salvar(produto);
    produto.Nome='Produto modificado';
    produtoService.Atualizar(produto);



#### Qual o benefício quando se segue o princípio
- Ajudar a desacoplar o código deixando mais fácil de mudá-lo.
- Fica mais fácil realizar testes(por exemplo: teste unitários).

## Exercícios

Sobre o código a seguir:

    class Usuario{
        public Nome: string;
        public Salario: number;
        public ContaBancaria: string;

        constructor(nome: string, salario: number, contaBancaria: string){
            this.Nome = nome;
            this.Salario = salario;
            this.ContaBancaria = contaBancaria;
        }
    }

    class ContraCheque{
        static Gerar(usuarios: Usuario[]):string[]{
            let contraCheques=[];
            for(const usuario of usuarios){
                let contraCheque = `Usuário ${usuario.Nome}
    Remuneração:${usuario.Salario}`

                contraCheques.push(contraCheque);

                console.log(`Transferindo ${usuario.Salario} para conta ${usuario.ContaBancaria}`)
                // Implementação da transferência
                // ....
                console.log('Transferido')
            }

            return contraCheques;
        }
    }

    const usuarios = [new Usuario('Alan', 3000,"1111-1"), new Usuario('Caio', 5000, "2222-2"), new Usuario('Joana', 6000, '3333-3'), new Usuario('Maria', 6500, '4444-4')]

    const contraCheques = ContraCheque.Gerar(usuarios);

    console.log(contraCheques);

1. O código do método "ContraCheque.Gerar()" está violando qual principio do SOLID? 
- A) Single responsibility (Princípio de única responsabilidade)
- B) Open/closed (Princípio de aberto/fechado)
- C) Liskov substitution (Princípio da substituição de Liskov)
- D) Interface segregation (Princípio da segregação de Interface)
- E) Dependency inversion (Princípio da inversão de dependência)

2. Refatore o código atendendo ao principio violado

3. Crie uma classe DepartamentoPessoalService com um método FechamentoDoMes e mova a função TransferirSalarios para ela.

4. Implemente as seguintes tarefas usando todos os princípios do SOLID:

    1. Crie uma classe "UsuarioPessoaJuridica" que tenha que ter o campo "ValorMensal" invés do campo "Salario"

    2. Implemente no FechamentoDoMes as execuções:
        1. Buscar todos os Usuários
        2. Gerar os contra cheques
        3. Salvar os contra cheques em um Banco de Dados fake
        4. Transferir os salários/ValorMensal de cada usuário.





## Recursos adicionais

https://pt.wikipedia.org/wiki/SOLID

https://en.wikipedia.org/wiki/SOLID

[Filipe Deschamps:
SOLID fica FÁCIL com Essas Ilustrações](https://www.youtube.com/watch?v=6SfrO3D4dHM)

Site usado no video: https://www.typescriptlang.org/play

<details>
<summary>Código usado no video antes de refatorar</summary>

    class Pagamento{
        public NumeroCartao: string;
        public NumeroDeParcelas: number;
        public Valor: number;
        
        constructor(numeroCartao: string, numeroDeParcelas: number, valor: number){
            this.NumeroCartao = numeroCartao;
            this.NumeroDeParcelas = numeroDeParcelas;
            this.Valor = valor;
        }
    }

    class PagamentoPix extends Pagamento{
        public contaOrigem: string;
        public contaDestino: string;

        constructor(numeroCartao: string, 
                    numeroDeParcelas: number, 
                    valor: number, 
                    contaOrigem: string, 
                    contaDestino: string){
            super(numeroCartao, numeroDeParcelas, valor);
            this.contaOrigem = contaOrigem;
            this.contaDestino = contaDestino;
        }
    }

    class PagamentoService{

        public ProcessarPagamento(pagamento: Pagamento){
            this.validarNumeroDoCartao(pagamento);
            this.validarParcela(pagamento);
            this.validarValor(pagamento);
        }

        validarNumeroDoCartao(pagamento: Pagamento) {
            console.log(`validando número do cartão: ${pagamento.NumeroCartao}`);
        }

        validarParcela(pagamento: Pagamento) {
            console.log(`validando se número de parcelas(${pagamento.NumeroDeParcelas}) é permitido`);
        }

        validarValor(pagamento: Pagamento) {
            console.log(`validando se valor pago(${pagamento.Valor}) é o esperado`);
        }
    }

    const pagamentoService= new PagamentoService;

    const pagamento = new Pagamento('0123 4567 8977 9999', 2, 50);
    pagamentoService.ProcessarPagamento(pagamento);

    // const pagamentoComPix = new PagamentoPix(null, null, 50, '9999-0', '3333-0');
    // pagamentoService.ProcessarPagamento(pagamento);

</details>

<details>
<summary>Código usado no video depois de refatorar</summary>

    class Pagamento{
        public Valor: number;
        
        public ValidarPagamento(){
            console.log(`validando pagamento: ${this.Valor}`);
        }

        constructor(valor: number){    
            this.Valor = valor;
        }
    }

    class PagamentoCredito extends Pagamento{
        public NumeroCartao: string;
        public NumeroDeParcelas: number;

        public ValidarPagamento() {
            this.validarNumeroDoCartao();
            this.validarParcela();
            this.validarValor();
        }

        constructor(numeroCartao: string, numeroDeParcelas: number, valor: number){
            super(valor);
            this.NumeroCartao = numeroCartao;
            this.NumeroDeParcelas = numeroDeParcelas;
        }

        validarNumeroDoCartao() {
            console.log(`validando número do cartão: ${this.NumeroCartao}`);
        }

        validarParcela() {
            console.log(`validando se número de parcelas(${this.NumeroDeParcelas}) é permitido`);
        }

        validarValor() {
            console.log(`validando se valor pago(${this.Valor}) é o esperado`);
        }
    }

    class PagamentoPix extends Pagamento{
        public ContaDeOrigem: string;
        public ContaDeDestino: string;

        public ValidarPagamento() {
            this.validarContaDeOrigem()
            this.validarContaDeDestino()
            this.validarValor()
        }

        constructor(contaOrigem: string,
                    contaDestino: string,
                    valor: number){
            super(valor);
            this.ContaDeOrigem = contaOrigem;
            this.ContaDeDestino = contaDestino;
        }

        validarContaDeOrigem(){
            console.log(`validando conta de origem ${this.ContaDeOrigem}`);
        }

        validarContaDeDestino(){
            console.log(`validando conta de destino ${this.ContaDeDestino}`);
        }

        validarValor(){
            console.log(`validando se valor pago(${this.Valor}) é o esperado`);
        }
    }

    class PagamentoService{

        public ProcessarPagamento(pagamento: Pagamento){
            pagamento.ValidarPagamento();
        }
    }

    const pagamentoService= new PagamentoService;

    //const pagamento = new PagamentoCredito('0123 4567 8977 9999', 2, 50);
    //pagamentoService.ProcessarPagamento(pagamento);

    const pagamentoComPix = new PagamentoPix('9999-0', '3333-0', 30.99);
    pagamentoService.ProcessarPagamento(pagamentoComPix);

</details>

### Gabarito

1. A

2. "ContraCheque.Gerar()" não deve ser responsável por transferir salários.

        class Usuario{
            public Nome: string;
            public Salario: number;
            public ContaBancaria: string;

            constructor(nome: string, salario: number, contaBancaria: string){
                this.Nome = nome;
                this.Salario = salario;
                this.ContaBancaria = contaBancaria;
            }
        }

        class FakeBankService{
            static Transferir(contaDestino: string, valor: number){
                console.log(`Transferindo ${valor} para conta ${contaDestino}`)
                // Implementação da transferência
                // ....
                console.log('Transferido')
            }
        }

        function TransferirSalarios(usuarios: Usuario[]){
            for(const usuario of usuarios){
                FakeBankService.Transferir(usuario.ContaBancaria, usuario.Salario);
            }
        }

        class ContraCheque{
            static Gerar(usuarios: Usuario[]):string[]{
                let contraCheques=[];
                for(const usuario of usuarios){
                    let contraCheque = `Usuário ${usuario.Nome}
        Remuneração:${usuario.Salario}`

                    contraCheques.push(contraCheque);
                }

                return contraCheques;
            }
        }

        const usuarios = [new Usuario('Alan', 3000,"1111-1"), new Usuario('Caio', 5000, "2222-2"), new Usuario('Joana', 6000, '3333-3'), new Usuario('Maria', 6500, '4444-4')]

        const contraCheques = ContraCheque.Gerar(usuarios);

        TransferirSalarios(usuarios);


        console.log(contraCheques);

3. 

    class Usuario{
        public Nome: string;
        public Salario: number;
        public ContaBancaria: string;

        constructor(nome: string, salario: number, contaBancaria: string){
            this.Nome = nome;
            this.Salario = salario;
            this.ContaBancaria = contaBancaria;
        }
    }

    class DepartamentoPessoalService{
        static TransferirSalarios(usuarios: Usuario[]){
            for(const usuario of usuarios){
                FakeBankService.Transferir(usuario.ContaBancaria, usuario.Salario);
            }
        }
    }

    class FakeBankService{
        static Transferir(contaDestino: string, valor: number){
            console.log(`Transferindo ${valor} para conta ${contaDestino}`)
            // Implementação da transferência
            // ....
            console.log('Transferido')
        }
    }

    class ContraCheque{
        static Gerar(usuarios: Usuario[]):string[]{
            let contraCheques=[];
            for(const usuario of usuarios){
                let contraCheque = `Usuário ${usuario.Nome}
    Remuneração:${usuario.Salario}`

                contraCheques.push(contraCheque);
            }

            return contraCheques;
        }
    }

    const usuarios = [new Usuario('Alan', 3000,"1111-1"), new Usuario('Caio', 5000, "2222-2"), new Usuario('Joana', 6000, '3333-3'), new Usuario('Maria', 6500, '4444-4')]

    const contraCheques = ContraCheque.Gerar(usuarios);

    DepartamentoPessoalService.TransferirSalarios(usuarios);


    console.log(contraCheques);

4. 

    abstract class Usuario{
        public Nome: string;
        
        public ContaBancaria: string;

        constructor(nome: string, contaBancaria: string){
            this.Nome = nome;
            this.ContaBancaria = contaBancaria;
        }

        abstract PegarRemuneracao(): number;
    }

    class UsuarioCLT extends Usuario{

        public Salario: number;

        constructor(nome: string, contaBancaria: string, salario: number){
            super(nome, contaBancaria);
            this.Salario = salario;
        }

        PegarRemuneracao(): number {
            return this.Salario;
        }
    }

    class UsuarioPessoaJuridica extends Usuario{
        public ValorMensal: number;

        constructor(nome: string, contaBancaria: string, valorMensal: number){
            super(nome, contaBancaria);
            this.ValorMensal = valorMensal;
        }

        PegarRemuneracao(): number {
            return this.ValorMensal;
        }
    }

    interface IUsuarioRepository{
        getAll():Usuario[];
    }

    class UsuarioRepository implements IUsuarioRepository{
        getAll(): Usuario[] {
            return [new UsuarioCLT('Alan', '1111-1', 3000), new UsuarioPessoaJuridica('Caio', '2222-2', 5000), new UsuarioCLT('Joana', '3333-3', 6000), new UsuarioPessoaJuridica('Maria', '4444-4', 6500)];
        }
    }

    interface IBancoDeDados{
        Salvar(contraCheques: string[]):void;
    }

    class FakeBancoDeDados implements IBancoDeDados{
        contraCheques:string;

        constructor(){
            this.contraCheques = ''
        }

        Salvar(contraCheques: string[]){
            this.contraCheques+= contraCheques;

            console.log(`Salvando contra cheques ${this.contraCheques}`)
        }
    }

    interface IBankService{
        Transferir(contaDestino: string, valor: number):void;
    }

    class FakeBankService implements IBankService{
        Transferir(contaDestino: string, valor: number){
            console.log(`Transferindo ${valor} para conta ${contaDestino}`)
            // Implementação da transferência
            // ....
            console.log('Transferido')
        }
    }

    interface IContraChequeService{
        Gerar(usuarios: Usuario[]):string[];
    }

    class ContraChequeService implements IContraChequeService{
        Gerar(usuarios: Usuario[]):string[]{
            let contraCheques=[];
            for(const usuario of usuarios){
                let contraCheque = `Usuário ${usuario.Nome}
    Remuneração:${usuario.PegarRemuneracao()}`

                contraCheques.push(contraCheque);
            }

            return contraCheques;
        }
    }

    class DepartamentoPessoalService{

        private usuarioRepository: IUsuarioRepository;
        private contraChequeService: IContraChequeService;
        private bancoDeDados: IBancoDeDados;
        private bankService: IBankService;

        FechamentoDoMes(){
            //Buscar todos ao usuários
            const usuarios = this.usuarioRepository.getAll();

            //Gerar os contra cheques
            const contraCheques = this.contraChequeService.Gerar(usuarios);

            //Salvar os contra cheques em um Banco de Dados fake
            this.bancoDeDados.Salvar(contraCheques);

            //Transferir os salários/ValorMensal de cada usuário.
            this.TransferirSalarios(usuarios);
        }

        TransferirSalarios(usuarios: Usuario[]){
            for(const usuario of usuarios){
                this.bankService.Transferir(usuario.ContaBancaria, usuario.PegarRemuneracao());
            }
        }

        constructor(usuarioRepository: IUsuarioRepository, 
                    contraChequeService: IContraChequeService, 
                    bancoDeDados: IBancoDeDados,
                    bankService: IBankService){
            this.usuarioRepository = usuarioRepository;
            this.contraChequeService = contraChequeService;
            this.bancoDeDados = bancoDeDados;
            this.bankService = bankService;
        }
    }

    const usuarioRepository = new UsuarioRepository();
    const contraChequeService = new ContraChequeService();
    const fakeBancoDeDados = new FakeBancoDeDados();
    const fakeBankService = new FakeBankService();

    const departamentoPessoalService = new DepartamentoPessoalService(usuarioRepository, contraChequeService, fakeBancoDeDados, fakeBankService);

    departamentoPessoalService.FechamentoDoMes();

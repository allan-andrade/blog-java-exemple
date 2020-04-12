# blog-java-exemple
Code Blog with java spring boot
Tecnologias utilizadas spring MVC
Spring security
Thymeleaf
Bootstrap (layout – front)
Postgres (banco de dados)
Aws elastic beanstalk
Aws rds


Start spring: https://start.spring.io/
Maven Project
Java
selecionando 6 dependencias: spring web, spring data jpa, thymeleaf, spring dev tools, spring security, PostgreSQL driver

Com o PostgreSQL instalado
abra o sql Shell e faça login com usuário postgres (padrão assim que instala o programa)
próximo passo é criar o banco
create database codeblog;
\c codeblog;

Configurar o application properties com seu respectivo usuário, senha, e porta préconfigurados. No meu caso, ficou assim:
spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
#Banco local – CodeBlog
spring.datasource.url= jdbc:postgresql://localhost:5432/codeblog
spring.datasource.username=postgres
spring.datasource.password=12345678
spring.jpa.hibernate.ddl-auto=update  
#cria automaticamente as modificações criadas com spring jpa aqui no código

Criar um model contendo
@Entity -> Contém dependências do Spring Data. Através daqui identificamos que esses modelos pertencem a uma tabela
@Table(name=”TB_POST”) -> nome da tabela
@id -> id que toda tabela deve ter
@GeneratedValue(strategy = GenerationType.AUTO) -> id gerado automaticamente sempre que necessário
@NotBlack -> informar que campo é obrigatório
@JsonFormat(shape = JsonFormat.Shape.STRING,pattern = "dd-MM-yyyy") -> indicando exatamente qual o formato que a data deve aparecer
@Lob -> fará com que o banco aceite textos (posts) bem grande e trate isso de forma diferente visando a performance

Criar uma repository (classe designada para exercer transições do banco de dados) com uma interface.
Essa interface irá ser extendida ao JpaRepository onde dentro dela irá ter a classe do model criado anteriormente e o id:
extends JpaRepository<Post, Long>
Com isso a aplicação terá acesso a métodos já prontos como findAll, findById, save, delete, etc. E não precisaremos criar manualmente.

Se neste momento você rodar sua aplicação. A tabela “td_post” (nome que foi dado) irá ser criada.
Para comprovação da criação, abra o sql shell (se houver fechado), acesse o banco com o comando:
\c codeblog
e 
\d ->irá mostrar as tabelas
\d tb_post  -> irá mostrar a descrição dessa tabela

Criar uma pasta service com uma interface pois será onde irá ser definido os métodos. E dentro dela iremos criar um serviceImplement para implementar esses métodos.
Na interface que nomeamos como CodeblogService iremos criar 3 métodos:
    List<Post> findall();
    Post findById(long id);
    Post save(Post post);
Na pasta que criamos dentro de service iremos criar uma classe de nome CodeblogServiceImpl e nela colocaremos “implements CodeblogService”.
Ao fazer essa implementação os 3 métodos serão implementados com @Override por serem métodos da interface.
Os 3 métodos serão implementados com retorno nulo. Mas iremos fazer um ponto de injeção do Repositório que já criamos com o comando:
	@Autowired
	CodeblogRepository codeblogRepository;
Com isso iremos fazer com que a busca dos posts retornarão um post e não um nulo.
O comando ficará assim:
	@Autowired
    	CodeblogRepository codeblogRepository;
  @Override
    public List<Post> findall() {
        return codeblogRepository.findAll();
    }

    @Override
    public Post findById(long id) {
        return codeblogRepository.findById(id).get();
    }
Importante que se coloque @Service, que é um stereotype do Spring Framework para mostrar que essa classe é um Bin gerenciável por ele. E com isso conseguiremos criar pontos de injeção utilizando a service.

    @Override
    public Post save(Post post) {
        return codeblogRepository.save(post);
    }
O repositório se encontra vazio. Agora é hora de popularmos ele para testes.
Iremos criar um novo package de nome Utils com a classe DummyData, com ponto de injeção (@AutoWired para o repositório CodeblogRepository).
Será necessário construir um método com a injeção @PostConstruct; Essa injeção irá ser construir tudo que está dentro do método assim que executarmos a aplicação.
Uma vez rodado a aplicação e enxergado que elas estão na tabela (select * from tb_post), esse PostConstuct pode ser comentado para que não fique duplicando informações na tabela.




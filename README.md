Introdução ao codigo
Esse é um codigo que foi desenvolvido para autenticar e autorizar diferentes tipos de usuarios, dando permissões e restringindo acesso a diferentes funcionalidades. É uma pagina Web (Java SpringBoot) e que faz requisições a um banco de dados (Mongo DB) e utiliza RestApi e TokensJWT. Abaixo explicarei cada funcionalidade das minhas classes.

PASTA APPLICATION
CLASSE JwtRestapiApplication(APPLICATION)


Descrição:

Anotação @SpringBootApplication: Marca a classe como uma aplicação Spring Boot.
Anotação @EnableMongoRepositories: Habilita a detecção de repositórios do MongoDB.
Método main: Ponto de entrada da aplicação.
PASTA CONFIG
CLASSE AppCongig(CONFIG)


Descrição:

Anotação @Configuration: Marca a classe como uma fonte de definições de beans.
Anotação @EnableWebSecurity: Habilita a segurança web do Spring Security.
Método securityFilterChain(HttpSecurity http): Configura as regras de segurança HTTP.
csrf(AbstractHttpConfigurer::disable): Desabilita a proteção CSRF.
authorizeHttpRequests(request -> request...): Define regras de autorização para diferentes endpoints.
addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class): Adiciona o filtro de autenticação JWT antes do filtro de autenticação padrão.
Método passwordEncoder(): Define o bean para o codificador de senhas BCrypt.
PASTA CONTROLLER
CLASSE AauthController(CONTROLLER)


Descrição:

Anotação @RestController: Marca a classe como um controlador REST.
Método register(@RequestBody User user): Registra um novo usuário.
Método login(@RequestParam String username, @RequestParam String password): Autentica o usuário e retorna um token JWT.
Método extractRole(@PathVariable String token): Extrai a role do token JWT.
PASTA MODEL
CLASSE User(MODEL)


Descrição:

Anotação @Document: Marca a classe como um documento MongoDB.
Atributos: id, username, password, role - representam os campos do documento de usuário.
Getters e Setters: Métodos para acessar e modificar os atributos.
PASTA REPOSITORY
CLASSE UserRepostirory(REPOSITORY)


Descrição:

Anotação @Repository: Marca a interface como um repositório do Spring Data.
Interface MongoRepository<User, String>: Extende a interface MongoRepository fornecida pelo Spring Data, especificando a entidade User e o tipo do ID (String).
Método findByUsername(String username): Declaração de um método personalizado para encontrar um usuário pelo nome de usuário.
PASTA SECURITY
CLASSE JwtAuthenticationFilter(SECURITY)


Descrição:

Anotação @Component: Marca a classe como um componente do Spring.
Método doFilterInternal: Filtra as requisições HTTP para validar o token JWT e autenticar o usuário.
Autenticação do Usuário: Extração do nome de usuário do token JWT e configuração do contexto de segurança.
CLASSE JwtUtil(SECURITY)


Descrição:

@Component: Marca a classe como um bean gerenciado pelo Spring.
Construtor JwtUtil(@Value("${jwt.secret}") String secret): Inicializa a chave secreta usada para assinar os tokens JWT.
generateToken(String username, String role): Gera um token JWT com o nome de usuário e a role.
extractUsername(String token): Extrai o nome de usuário do token JWT.
extractRole(String token): Extrai a role do token JWT.
PASTA SERVICE
CLASSE AuthService(SERVICE)


Descrição:

@Service: Marca a classe como um serviço do Spring.
authenticateUser(String username, String password): Autentica o usuário verificando as credenciais e gera um token JWT.
extractUsername(String token): Extrai o nome de usuário do token JWT.
extractRole(String token): Extrai a role do token JWT.
CLASSE UserService(SERVICE)


Descrição:

@Service: Marca a classe como um serviço do Spring.
registerUser(User user): Registra um novo usuário após verificar se o nome de usuário já existe e codificar a senha.
PASTA TEST
CLASSE SecretKeyGenerator(TEST)


Descrição:

Classe para Gerar Chave Secreta: Utilizada para gerar uma chave secreta para assinatura de tokens JWT.
Método main: Gera e imprime uma chave secreta codificada em base64.
DIAGRAMA


Explicação Passo a Passo do Diagrama
JwtRestapiApplication: Ponto de entrada da aplicação Spring Boot.
AppConfig: Configura as regras de segurança, define os beans de segurança e adiciona o filtro JWT.
AuthController: Controlador responsável por gerenciar as requisições de registro, login e extração de roles. Chama o UserService para registrar usuários e o AuthService para autenticar e extrair roles.
AuthService: Contém a lógica de autenticação e interação com o repositório de usuários. Utiliza o JwtUtil para gerar e validar tokens JWT.
UserService: Gerencia o registro de usuários, verificando a existência de nomes de usuário e codificando senhas antes de salvar.
UserRepository: Repositório que interage com o banco de dados MongoDB para operações de CRUD de usuários. Implementa métodos personalizados como findByUsername.
User: Modelo que representa um usuário no sistema, com atributos id, username, password e role.
JwtUtil: Utilitário que gerencia a geração, extração e validação de tokens JWT.
JwtAuthenticationFilter: Filtra as requisições HTTP para validar tokens JWT e autenticar usuários.
SecretKeyGenerator: Classe utilizada para gerar uma chave secreta para assinatura de tokens JWT.
Web Application: Consiste em páginas HTML (login.html, register.html, home.html) que interagem com o backend para registro, login e acesso a diferentes seções baseadas na role do usuário.
INCIAÇÃO DO CODIGO
Para se iniciar a aplicação execute primeiro a classe de SecretKeyGenerator e gere uma token, logo depois vá até a classe Application.Properties e dentro do metodo jwt.secret= coloque a chave gerada. Depois execute a classe JwtRestapiApplication e na sua URL digite http://localhost:8080/register.html e faça um novo registro.

TELAS DA MINHA APLICAÇÃO
Register.html


register.html: Página de registro que permite ao usuário criar uma nova conta.
Formulário de Registro: Envia uma requisição POST para /register com o nome de usuário, senha e role.
Script JavaScript: Processa a resposta do servidor e redireciona o usuário para a página de login.


Depois de registrar pela aba de /register.html, o usuario é cadastrado no banco de dados
Login.html


login.html: Página de login que permite ao usuário inserir seu nome de usuário e senha.
Formulário de Login: Envia uma requisição GET para /login com o nome de usuário e senha.
Script JavaScript: Processa a resposta do servidor e armazena o token JWT no localStorage.


Usando o Insomnia consigo fazer uma requisição GET diretamente pro meu Banco de Dados e ele recupera o registro adiconado do usuario e seu codigo JWT
Home.html
home.html: Página inicial que mostra informações diferentes com base na role do usuário.
Seções de Usuário, Moderador e Admin: Seções diferentes visíveis com base na role do usuário.
Funções de Adicionar e Remover Campos: Funções JavaScript para adicionar e remover campos que salvam as alterações no localStorage.
Funções de Salvar e Carregar Campos Dinâmicos: Funções JavaScript para salvar e carregar campos dinâmicos do localStorage.
Home.html/ADMIN


Essa aba da página so é acessada quem tem a role == Admin
ele poderá adicionar/remover/visuzalizar campos inputs na Página
Home.html/MODERADOR


Essa aba da página so é acessada quem tem a role == Moderador
ele poderá adicionar/visuzalizar campos inputs na Página
Home.html/USER


Essa aba da página so é acessada quem tem a role == User
ele poderá somente visuzalizar campos inputs na Página

# Access Keycloak

Laboratório local de identidade do Access Stack. Este é um repositório independente dos projetos `acess-gateway`, `access-web`, `device-simulator` e `access-infra`. O Keycloak usa seu próprio PostgreSQL e não participa da autenticação das aplicações atuais.

## Iniciar

1. Copie `.env.example` para `.env` e substitua as duas senhas por valores diferentes e exclusivos. O arquivo `.env` não deve ser versionado.
2. Execute `docker compose up -d` nesta pasta.
3. Abra `http://localhost:8180/admin/` e entre com o usuário `admin` e a senha definida em `KEYCLOAK_ADMIN_PASSWORD`.

O Compose usa a imagem `quay.io/keycloak/keycloak:26.8.2` em modo de desenvolvimento e PostgreSQL 17 com volume exclusivo. Somente a porta `8180` do Keycloak é publicada, em `127.0.0.1`. O banco não publica portas. O endereço configurado em `KC_HOSTNAME` faz os links OIDC apontarem para `http://localhost:8180`.

## Exercício de OIDC

1. No console, crie um realm chamado `access-lab`.
2. Em **Clients**, crie um cliente OpenID Connect chamado `access-lab-client`, com autenticação de cliente desativada. Para um exercício com navegador, configure uma URI de redirecionamento local específica para a aplicação de teste antes de iniciar o fluxo de login.
3. Em **Users**, crie um usuário de demonstração e defina uma senha não temporária. Use valores fictícios.
4. Confira o documento de descoberta em `http://localhost:8180/realms/access-lab/.well-known/openid-configuration`. O campo `issuer` deve ser `http://localhost:8180/realms/access-lab`.

Esses usuários e clientes existem apenas no Keycloak. A web PHP continua usando sua senha e sessão; o gateway continua autenticando o token administrativo e as chaves dos dispositivos. Nenhuma aplicação consome os tokens OIDC deste laboratório.

## Parar e preservar dados

`docker compose down` para os contêineres e preserva o volume do banco. `docker compose down -v` remove o volume e apaga realms, clientes e usuários; execute esse comando somente se quiser reiniciar o laboratório do zero.

Este Compose é para estudo local. `start-dev` usa padrões inseguros e não é uma configuração de produção. Consulte a [documentação oficial de contêineres do Keycloak](https://www.keycloak.org/server/containers) para os requisitos de implantação.

## Padrão de documentação e configuração

Este repositório existe para fornecer um ambiente local de identidade e OIDC para o Access Gateway. O README acima apresenta o escopo, a arquitetura e as tecnologias específicas do projeto; esta seção padroniza o ponto de entrada para quem deseja reproduzi-lo localmente.

### Clonar e configurar

~~~powershell
git clone https://github.com/Ivictors/access-keycloak.git
cd access-keycloak
~~~

Consulte os pré-requisitos e comandos de execução descritos neste README antes de iniciar. Use variáveis de ambiente, arquivos locais ignorados pelo Git e serviços de desenvolvimento para credenciais; não substitua placeholders por segredos no README e não versione chaves, tokens ou senhas.

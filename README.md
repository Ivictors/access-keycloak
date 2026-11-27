# Access Keycloak

## Integração local com o Access Stack

O realm `access-lab` é importado na primeira inicialização. Ele contém o cliente confidencial `access-web`, a função `access-admin` e um usuário de demonstração com essa função. Defina `ACCESS_WEB_OIDC_CLIENT_SECRET` e `ACCESS_LAB_USER_PASSWORD` no `.env` antes de iniciar. O usuário de demonstração é `access-admin`; sua senha é o valor local de `ACCESS_LAB_USER_PASSWORD`.

O cliente `access-web` aceita somente o callback `http://localhost:8000/?oidc_callback=1`, exige PKCE S256 e permite o retorno a `http://localhost:8000/` após o logout do SSO. Se o realm já existia antes dessas opções, atualize o cliente pelo console; a importação não sobrescreve realms existentes.

Para integrar web e gateway, inicie primeiro este Compose e depois use o arquivo `compose.oidc.yaml` do `access-infra`. O Keycloak permanece dono do próprio banco. A rede `access-keycloak_identity_app` permite que web e gateway alcancem o Keycloak por `http://keycloak:8080`, enquanto o navegador usa `http://localhost:8180`. O emissor anunciado nos tokens é o endereço do navegador; o gateway busca as chaves pelo endereço interno e também confere emissor, público destinatário e função administrativa.

O importador do Keycloak ignora um realm que já existe. Se `access-lab` tiver sido criado manualmente antes desta configuração, faça os ajustes no console ou reinicie somente o volume deste laboratório quando não houver dados a preservar.

Laboratório local de identidade do Access Stack. Este é um repositório independente dos projetos `acess-gateway`, `access-web`, `device-simulator` e `access-infra`. O Keycloak usa seu próprio PostgreSQL e pode autenticar a web e o acesso administrativo ao gateway no modo OIDC opcional.

## Iniciar

1. Copie `.env.example` para `.env` e preencha as quatro credenciais com valores diferentes e exclusivos. O arquivo `.env` não deve ser versionado.
2. Execute `docker compose up -d` nesta pasta.
3. Abra `http://localhost:8180/admin/` e entre com o usuário `admin` e a senha definida em `KEYCLOAK_ADMIN_PASSWORD`.

O Compose usa a imagem `quay.io/keycloak/keycloak:26.8.2` em modo de desenvolvimento e PostgreSQL 17 com volume exclusivo. Somente a porta `8180` do Keycloak é publicada, em `127.0.0.1`. O banco não publica portas. O endereço configurado em `KC_HOSTNAME` faz os links OIDC apontarem para `http://localhost:8180`.

## Exercício de OIDC

1. No console, confira o realm `access-lab`, o cliente confidencial `access-web` e o usuário de demonstração `access-admin` importados na primeira inicialização.
2. Confira o documento de descoberta em `http://localhost:8180/realms/access-lab/.well-known/openid-configuration`. O campo `issuer` deve ser `http://localhost:8180/realms/access-lab`.

No modo sem integração, a web PHP continua usando sua senha e sessão, e o gateway continua autenticando o token administrativo e as chaves dos dispositivos. No modo OIDC, web e gateway usam o realm `access-lab`, enquanto as chaves dos dispositivos continuam no gateway.

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

# Decisões técnicas

Resumo das decisões que mais pesaram no projeto, em contexto, decisão e resultado.

## 1. Um demo público sem backend

- **Contexto:** eu queria que qualquer pessoa pudesse percorrer o produto inteiro sem login, sem
  que o link público tocasse em dados ou serviços reais.
- **Decisão:** o demo não faz nenhuma chamada de backend. Um roteador local atende as rotas da API
  no navegador, sobre um estado guardado em `localStorage`. O link público é um projeto de hospedagem
  separado, com o mesmo código, sem credenciais do Firebase.
- **Resultado:** se alguma chamada real escapasse, ela falharia em vez de tocar em dados. O estado é por
  visitante e pode ser resetado.

## 2. Ligado por variável e desligado por padrão

- **Contexto:** o painel exige sessão, e o servidor não enxerga o `localStorage`. Um atalho para o demo
  poderia virar brecha em produção.
- **Decisão:** o demo só existe quando uma variável de ambiente o liga no build. Sem ela, o botão de
  entrada some, as rotas públicas do demo respondem 404 e o cookie de demo é ignorado. As rotas reais
  de `/api/*` não foram alteradas e continuam exigindo sessão.
- **Resultado:** a produção não carrega o código do demo, e um teste falha se algo fora do demo o importar.

## 3. Mesmos contratos, mesmos componentes

- **Contexto:** um demo que reimplementa o produto envelhece rápido e deixa de mostrar o que ele faz.
- **Decisão:** os handlers do demo seguem os contratos das rotas reais e reaproveitam as regras puras
  (esquema da proposta, validação de lead, CSV de convites, resposta pública). A página da proposta é
  importada do app real por alias, em vez de copiada.
- **Custo:** o questionário do Smart Pass vive em outro repositório e entrou como cópia registrada
  (origem e alterações documentadas) para o demo.

## 4. Semente com datas relativas

- **Contexto:** uma semente com datas fixas faz a proposta "publicada" parecer expirada com o tempo.
- **Decisão:** a semente guarda deslocamentos em dias a partir de "hoje" (fuso de São Paulo).
- **Resultado:** a proposta publicada continua válida e a expirada continua expirada em qualquer data.

## 5. Página pública que não revela nada a mais

- **Contexto:** o link da proposta é público e vai para o cliente.
- **Decisão:** a API pública exige uma chave, devolve só os campos da página, responde sempre o mesmo
  "não encontrada" para qualquer falha de acesso e calcula a expiração a cada requisição. As imagens
  passam por um proxy que recusa propostas expiradas ou despublicadas.
- **Resultado:** o cliente não consegue distinguir "não existe" de "não é mais válida" nem acessar o que foi despublicado.

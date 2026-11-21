# dos600aos1000-keepalive

Mantém o site **https://dos600aos1000.onrender.com** acordado para eliminar o
*cold start* do Render (plano free).

## Por que existe

O Render free "dorme" após ~15 minutos sem nenhuma requisição. A próxima visita
demora **30–60 s** para o servidor voltar (cold start) — o código do site em si
sobe em ~1,3 s, então o atraso é da infraestrutura do Render, não do projeto.

## Como funciona

- `.github/workflows/keepalive.yml` faz um ping HTTP leve em
  `https://dos600aos1000.onrender.com/api/config` **a cada 5 minutos**, o que
  reinicia o contador de inatividade do Render e mantém a instância quente.
- Um **heartbeat diário** commita uma linha em `heartbeat.txt` para o GitHub não
  desativar o workflow agendado por inatividade do repositório (60 dias).

## Por que é um repositório público

Repositórios **privados** só têm **2.000 minutos** de GitHub Actions por mês; um
ping a cada 5 minutos gastaria ~8.640 minutos e pararia no meio do mês. Em
repositórios **públicos** os minutos de Actions são ilimitados. Aqui não há
código do produto — apenas o ping.

## Alternativa (redundância)

Para uma segunda camada de garantia, cadastre também um monitor gratuito no
UptimeRobot (intervalo de 5 min) apontando para
`https://dos600aos1000.onrender.com/api/config`.

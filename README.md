# dos600aos1000-keepalive

Mantém o site **https://dos600aos1000.onrender.com** acordado para eliminar o
*cold start* do Render (plano free).

## Por que existe

O Render free "dorme" após ~15 minutos sem nenhuma requisição. A próxima visita
demora **30–60 s** para o servidor voltar (cold start). O código do site sobe em
~1,3 s, então o atraso é da infraestrutura do Render, não do projeto.

## Como funciona

- Um cron **raro** (a cada 2 horas) dispara **um job longo** que fica ~5h30 no ar
  pingando `https://dos600aos1000.onrender.com/api/config` **a cada 5 minutos**
  por dentro. Como o job dura bem mais que o intervalo do cron, os jobs ficam
  **sobrepostos**: mesmo que um disparo do GitHub atrase ou seja pulado, continua
  existindo job cobrindo o período.
- Por que não um cron de `*/5`: o agendador do GitHub é impreciso e atrasa muito
  em frequências altas — no teste ele rodou de 1 em 1 hora, e **não** de 5 em
  5 min. Com um job longo, só dependemos de um disparo raro, que é confiável.
- Um **heartbeat diário** commita uma linha em `heartbeat.txt` para o GitHub não
  desativar o workflow agendado por inatividade do repositório (60 dias).

## Por que é um repositório público

Repositórios **privados** só têm **2.000 minutos** de GitHub Actions por mês; um
job que fica ~5h30 rodando gastaria isso em poucos dias e pararia. Em
repositórios **públicos** os minutos de Actions são ilimitados. Aqui não há
código do produto — apenas o ping.

## Alternativas para garantia total

- **UptimeRobot** (grátis, intervalo de 5 min): cadastre um monitor HTTP para
  `https://dos600aos1000.onrender.com/api/config`. Segunda camada independente.
- **Render pago** (~US$ 7/mês): elimina o cold start na origem (o serviço não
  dorme). É a solução definitiva, sem depender de ping.

Sondas de prontidão e atividade do Kubernetes
Os aplicativos podem se tornar não confiáveis por diversos motivos, por exemplo:
• Perda temporária de conexão
• Erros de configuração
• Erros de aplicação
Os desenvolvedores podem usar sondas para monitorar seus aplicativos. As sondas permitem que os desenvolvedores tomem
conhecimento de eventos como o status do aplicativo, o uso de recursos e erros.
O monitoramento desses eventos é útil para solucionar problemas, mas também pode auxiliar no planejamento e gerenciamento de
recursos.
Uma sonda é uma verificação periódica que monitora a integridade de uma aplicação. Os desenvolvedores podem configurar sondas
usando o cliente de linha de comando kubectl ou um modelo de implantação YAML.
Atualmente, existem três tipos de sondas no Kubernetes:
Sondagem de
inicialização: Uma sondagem de inicialização verifica se o aplicativo dentro de um contêiner foi iniciado. As
sondagens de inicialização são executadas antes de qualquer outra sondagem e, a menos que sejam concluídas
com sucesso, desativam as demais. Se um contêiner falhar na sondagem de inicialização, ele será finalizado e seguirá a política
de reinicialização do pod.
Esse tipo de teste é executado apenas na inicialização, diferentemente dos testes de prontidão, que são executados
periodicamente.
A sonda de inicialização é configurada no atributo spec.containers.startupprobe da configuração do pod.
As sondagens de
prontidão determinam se um contêiner está pronto para atender solicitações. Se a sondagem de prontidão retornar um
estado de falha, o Kubernetes remove o endereço IP do contêiner dos endpoints de todos os serviços.
Os desenvolvedores usam sondas de prontidão para instruir o Kubernetes a não receber tráfego quando um contêiner em
execução estiver em execução. Isso é útil ao aguardar que um aplicativo execute tarefas iniciais demoradas, como estabelecer
conexões de rede, carregar arquivos e aquecer caches.
A sonda de prontidão é configurada no atributo spec.containers.readinessprobe da configuração do pod.
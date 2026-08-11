Atividades:

1) Por que um endereço 192.168.0.5 nunca aparece diretamente na internet  
Porque 192.168.0.5 é um endereço privado (da faixa 192.168.0.0/16). Endereços privados são usados só dentro de redes locais e não são roteáveis na internet pública.

2) Diferença entre Static NAT, Dynamic NAT e PAT (NAT Overload) uma frase cada  
- Static NAT: mapeia um IP privado para um IP público fixo e permanente (1:1).  
- Dynamic NAT: usa um pool de IPs públicos e atribui temporariamente um IP público a um privado quando necessário (1:1, mas não fixo).  
- PAT (NAT Overload): permite que muitos IPs privados compartilhem um único IP público usando portas diferentes (multiplexação por porta).

3) Um roteador tem apenas um IP público. Como ele consegue atender 30 dispositivos ao mesmo tempo?  
Usando PAT (NAT Overload) o roteador traduz os IPs privados de cada dispositivo para o mesmo IP público, diferenciando as conexões por números de porta. Assim várias sessões simultâneas ficam identificadas por (IP público, porta).

4) Por que aplicativos de videochamada costumam usar STUN/TURN/ICE?  
Porque muitos usuários estão atrás de NATs e firewalls, o que dificulta conexões diretas. O STUN tenta descobrir o mapeamento NAT para permitir conexão direta, TURN atua como relé quando a conexão direta falha, e ICE coordena e escolhe automaticamente a melhor forma para estabelecer a chamada.

5) O que é CGNAT e por que ele dificulta expor um serviço próprio à internet?  
CGNAT (Carrier Grade NAT) é quando o provedor de internet usa NAT em larga escala e atribui IPs privados a clientes, fazendo vários assinantes compartilharem um IP público. Isso dificulta expor serviços porque você não tem um IP público exclusivo para receber conexões de entrada.

Prática: montando tabelas de tradução NAT

CENÁRIO A — Tradução simples  
Situação: Host 192.168.1.5:52000 acessa um site na porta 443 usando o IP público 201.10.5.90.  
Inside Local: 192.168.1.5:52000
Inside Global (pública): 201.10.5.90:52000
Protocolo / Destino: TCP → site:443
porta pública escolhida: 52000

CENÁRIO B — Conflito de portas  
Situação: Dois hosts internos saem usando a mesma porta de origem 50000.  
Como o roteador resolve (PAT): ele mantém mapeamentos únicos alterando a porta pública para cada sessão, assim cada par (IP interno:porta) vira um par único (IP público:porta). Exemplo:

Inside Local:192.168.1.5:50000
Inside Global (pública): 201.10.5.90:50000
Protocolo / Destino: TCP → siteA:443

Inside Local:192.168.1.6:50000
Inside Global (pública): 201.10.5.90:50001
Protocolo / Destino: TCP → siteB:443

CENÁRIO C — Expondo um serviço  
Situação: servidor interno 192.168.1.20:8080 deve ser acessível pela porta 80 do IP público.  
Configuração necessária: criar um port forwarding / Static NAT (DNAT) que mapeie 201.10.5.90:80 → 192.168.1.20:8080 e liberar a porta no firewall.

Public (externo): 201.10.5.90:80
Private (interno): 192.168.1.20:8080
Protocolo: TCP (HTTP)
Ação necessária: configurar port forwarding e liberar firewall

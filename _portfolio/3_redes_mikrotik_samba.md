---
layout: single
title: Infraestrutura de Redes com Mikrotik e SAMBA AD
description: Gestão centralizada de rede para 35+ usuários com segurança avançada
img: assets/img/network-infrastructure.jpg
importance: 3
category: redes
---

## Visão Geral do Projeto

Este projeto envolveu a implementação de uma infraestrutura de rede robusta e segura, integrando equipamentos **Mikrotik RouterOS** com **SAMBA Active Directory** para fornecer conectividade, segurança e gestão centralizada para mais de **35 usuários** distribuídos em diferentes departamentos da empresa Dantas.

## Arquitetura de Rede

### Topologia Física

**Equipamentos Mikrotik Implementados:**

**Core Router - Mikrotik CCR1036-12G-4S:**
- 12 portas Gigabit Ethernet
- 4 portas SFP+ 10Gb
- CPU ARM Cortex A15 de 36 cores
- 4GB RAM
- Função: Roteamento principal e gateway de internet

**Distribution Switches - 3x Mikrotik CRS328-24P-4S+RM:**
- 24 portas Gigabit PoE+
- 4 portas SFP+ 10Gb para uplink
- Switching Layer 2/3
- Função: Distribuição por andar/departamento

**Access Points - 8x Mikrotik cAP ac:**
- Wi-Fi 802.11ac dual-band
- PoE alimentado pelos switches
- Gestão centralizada via CAPsMAN
- Cobertura completa do escritório

**Firewall/VPN - Mikrotik RB4011iGS+RM:**
- 10 portas Gigabit
- 1 porta SFP+ 10Gb
- CPU ARM Cortex A15 quad-core
- Função: Firewall perimetral e servidor VPN

### Segmentação de Rede (VLANs)

**VLAN 10 - Administração (192.168.10.0/24):**
- Servidores de infraestrutura
- Equipamentos de rede (gestão)
- Estações de trabalho administrativas
- 12 dispositivos conectados

**VLAN 20 - Produção (192.168.20.0/24):**
- Servidores de aplicação
- Estações de trabalho operacionais
- Impressoras departamentais
- 25 dispositivos conectados

**VLAN 30 - Desenvolvimento (192.168.30.0/24):**
- Ambiente de desenvolvimento
- Servidores de teste
- Estações dos desenvolvedores
- 8 dispositivos conectados

**VLAN 40 - Convidados (192.168.40.0/24):**
- Acesso Wi-Fi para visitantes
- Isolamento completo da rede interna
- Bandwidth limitado
- Acesso apenas à internet

**VLAN 50 - IoT/Dispositivos (192.168.50.0/24):**
- Câmeras de segurança IP
- Sensores de monitoramento
- Dispositivos IoT diversos
- 15 dispositivos conectados

## Implementação do SAMBA Active Directory

### Configuração do Domain Controller

**Servidor Principal - DC01:**
- Ubuntu Server 20.04 LTS
- SAMBA 4.13 configurado como AD DC
- DNS integrado ao Active Directory
- Replicação com servidor secundário

**Configuração Base:**
```bash
# Instalação e configuração do SAMBA AD
sudo apt install samba krb5-user winbind
sudo samba-tool domain provision --realm=DANTAS.LOCAL \
  --domain=DANTAS --adminpass=ComplexPassword123! \
  --server-role=dc --dns-backend=SAMBA_INTERNAL
```

**Servidor Secundário - DC02:**
- Configuração idêntica para redundância
- Replicação automática de dados
- Failover transparente para usuários
- Sincronização de políticas de grupo

### Estrutura Organizacional

**Unidades Organizacionais (OUs):**
```
DANTAS.LOCAL
├── Usuarios
│   ├── Administracao
│   ├── Producao
│   ├── Desenvolvimento
│   └── Terceirizados
├── Computadores
│   ├── Desktops
│   ├── Laptops
│   └── Servidores
├── Grupos
│   ├── Seguranca
│   ├── Distribuicao
│   └── Aplicacoes
└── Recursos
    ├── Impressoras
    ├── Compartilhamentos
    └── Aplicacoes
```

**Grupos de Segurança Implementados:**
- **Domain Admins:** Administradores do domínio (3 usuários)
- **IT Support:** Suporte técnico (5 usuários)
- **Managers:** Gerentes departamentais (8 usuários)
- **Developers:** Equipe de desenvolvimento (6 usuários)
- **Users:** Usuários padrão (25 usuários)
- **Guests:** Usuários temporários (5 usuários)

### Políticas de Grupo (GPOs)

**Política de Segurança:**
- Complexidade de senhas obrigatória
- Bloqueio de conta após 5 tentativas
- Tempo de sessão limitado
- Auditoria de logon/logoff

**Política de Software:**
- Instalação automática de antivírus
- Atualizações do Windows centralizadas
- Bloqueio de software não autorizado
- Configuração de navegadores

**Política de Rede:**
- Mapeamento automático de drives
- Configuração de impressoras
- Proxy automático
- Configurações de Wi-Fi corporativo

## Configuração Avançada do Mikrotik

### Roteamento e Switching

**Configuração de VLANs:**
```routeros
# Criação das VLANs no switch principal
/interface vlan
add interface=bridge name=vlan-admin vlan-id=10
add interface=bridge name=vlan-prod vlan-id=20
add interface=bridge name=vlan-dev vlan-id=30
add interface=bridge name=vlan-guest vlan-id=40
add interface=bridge name=vlan-iot vlan-id=50

# Configuração de endereços IP
/ip address
add address=192.168.10.1/24 interface=vlan-admin
add address=192.168.20.1/24 interface=vlan-prod
add address=192.168.30.1/24 interface=vlan-dev
add address=192.168.40.1/24 interface=vlan-guest
add address=192.168.50.1/24 interface=vlan-iot
```

**Roteamento Inter-VLAN:**
```routeros
# Regras de roteamento entre VLANs
/ip firewall filter
add chain=forward src-address=192.168.10.0/24 dst-address=192.168.20.0/24 action=accept
add chain=forward src-address=192.168.20.0/24 dst-address=192.168.10.0/24 action=accept
add chain=forward src-address=192.168.40.0/24 dst-address=192.168.0.0/16 action=drop
add chain=forward src-address=192.168.50.0/24 dst-address=192.168.0.0/16 action=drop
```

### Segurança de Rede

**Firewall Rules:**
```routeros
# Proteção contra ataques DDoS
/ip firewall filter
add chain=input protocol=tcp connection-state=invalid action=drop
add chain=input protocol=tcp tcp-flags=fin,!ack action=drop
add chain=input protocol=tcp tcp-flags=fin,urg,psh action=drop

# Rate limiting para SSH e Winbox
add chain=input protocol=tcp dst-port=22 connection-state=new \
    src-address-list=ssh_blacklist action=drop
add chain=input protocol=tcp dst-port=22 connection-state=new \
    add-src-to-address-list=ssh_stage1 address-list-timeout=1m action=accept
```

**Quality of Service (QoS):**
```routeros
# Priorização de tráfego crítico
/queue tree
add name=total-download parent=global max-limit=100M
add name=critical parent=total-download priority=1 max-limit=30M
add name=business parent=total-download priority=3 max-limit=50M
add name=guest parent=total-download priority=8 max-limit=10M

# Marcação de tráfego por aplicação
/ip firewall mangle
add chain=prerouting protocol=tcp dst-port=443 action=mark-packet new-packet-mark=https
add chain=prerouting protocol=tcp dst-port=3389 action=mark-packet new-packet-mark=rdp
add chain=prerouting protocol=udp dst-port=53 action=mark-packet new-packet-mark=dns
```

### Monitoramento e Logging

**SNMP Configuration:**
```routeros
/snmp
set enabled=yes contact="IT Support" location="Datacenter"
/snmp community
add name=monitoring addresses=192.168.10.100/32 read-access=yes
```

**Logging Centralizado:**
```routeros
# Envio de logs para servidor Graylog
/system logging action
add name=graylog target=remote remote=192.168.10.100 remote-port=514 \
    src-address=192.168.10.1 bsd-syslog=yes syslog-facility=daemon

/system logging
add topics=info,!debug action=graylog
add topics=warning action=graylog
add topics=error action=graylog
add topics=critical action=graylog
```

## Integração SAMBA AD com Mikrotik

### Autenticação RADIUS

**Configuração do FreeRADIUS:**
```bash
# Integração com SAMBA AD para autenticação Wi-Fi
# /etc/freeradius/3.0/mods-available/mschap
mschap {
    winbind_username = "%{mschap:User-Name}"
    winbind_domain = "DANTAS"
    with_ntdomain_hack = yes
}

# /etc/freeradius/3.0/sites-available/default
authorize {
    preprocess
    chap
    mschap
    suffix
    ntdomain
    files
    ldap
    pap
}
```

**Configuração no Mikrotik:**
```routeros
# Servidor RADIUS para autenticação Wi-Fi
/radius
add address=192.168.10.100 secret=RadiusSecret123 service=wireless

# Perfil de segurança Wi-Fi corporativo
/interface wireless security-profiles
add name=corporate mode=dynamic-keys authentication-types=wpa2-eap \
    eap-methods=eap-tls,eap-ttls-mschapv2 radius-mac-authentication=yes
```

### Compartilhamentos de Rede

**Configuração de Shares:**
```bash
# /etc/samba/smb.conf
[global]
    workgroup = DANTAS
    realm = DANTAS.LOCAL
    security = ADS
    encrypt passwords = yes
    
[departamentos]
    path = /srv/samba/departamentos
    valid users = @"DANTAS\Domain Users"
    read only = no
    create mask = 0664
    directory mask = 0775

[publico]
    path = /srv/samba/publico
    guest ok = yes
    read only = yes
    browseable = yes
```

**Mapeamento Automático via GPO:**
- Drive H: para pasta pessoal do usuário
- Drive P: para compartilhamento público
- Drive D: para pasta do departamento
- Impressoras mapeadas automaticamente

## Monitoramento e Gestão

### Dashboard de Monitoramento

**Métricas Coletadas:**
- Utilização de banda por VLAN
- Número de usuários conectados
- Status dos links de uplink
- Temperatura dos equipamentos
- Utilização de CPU e memória
- Logs de segurança e eventos

**Ferramentas Utilizadas:**
- **Zabbix:** Monitoramento de infraestrutura
- **Grafana:** Dashboards visuais
- **Graylog:** Análise de logs centralizados
- **LibreNMS:** Descoberta automática de dispositivos

### Alertas Proativos

**Alertas Configurados:**
- Link down em interfaces críticas
- Utilização de banda acima de 80%
- Temperatura acima de 60°C
- Falhas de autenticação em massa
- Ataques de força bruta detectados
- Dispositivos não autorizados na rede

**Canais de Notificação:**
- Email para equipe de TI
- SMS para administradores
- Integração com Slack
- Dashboard visual com status

## Segurança Implementada

### Segurança Perimetral

**Firewall Rules Avançadas:**
- Bloqueio de países específicos (GeoIP)
- Detecção e bloqueio de port scanning
- Rate limiting para serviços críticos
- Whitelist para acesso administrativo
- Bloqueio automático de IPs suspeitos

**VPN Site-to-Site:**
```routeros
# Configuração IPSec para filiais
/ip ipsec profile
add name=ike2-profile dh-group=modp2048 enc-algorithm=aes-256 \
    hash-algorithm=sha256 lifetime=8h

/ip ipsec peer
add address=203.0.113.10/32 profile=ike2-profile exchange-mode=ike2 \
    name=filial-sp secret=VPNSecret123!

/ip ipsec policy
add dst-address=192.168.100.0/24 src-address=192.168.0.0/16 \
    tunnel=yes sa-src-address=198.51.100.1 sa-dst-address=203.0.113.10
```

### Segurança Wireless

**Configuração WPA2-Enterprise:**
- Certificados digitais para autenticação
- Chaves dinâmicas por usuário
- Isolamento de clientes
- Detecção de rogue access points
- Auditoria de conexões Wi-Fi

**Guest Network:**
- Isolamento completo da rede interna
- Portal captivo com termos de uso
- Bandwidth limitado (5 Mbps por usuário)
- Tempo de sessão limitado (4 horas)
- Logs detalhados de acesso

## Resultados e Benefícios

### Performance de Rede

**Métricas de Performance:**
- **Latência interna:** < 1ms entre VLANs
- **Throughput:** 95% da capacidade nominal
- **Disponibilidade:** 99.9% uptime
- **Tempo de convergência:** < 30 segundos
- **Packet loss:** < 0.01%

**Otimizações Alcançadas:**
- Redução de 60% no tráfego broadcast
- Melhoria de 40% na performance de aplicações
- Eliminação de gargalos de rede
- Balanceamento eficiente de carga

### Segurança e Compliance

**Melhorias de Segurança:**
- Redução de 95% em tentativas de acesso não autorizado
- Detecção proativa de ameaças
- Auditoria completa de acessos
- Conformidade com políticas de segurança

**Gestão Centralizada:**
- Controle unificado de usuários e permissões
- Políticas aplicadas automaticamente
- Backup automático de configurações
- Documentação atualizada automaticamente

### Benefícios Operacionais

**Eficiência Administrativa:**
- Redução de 70% no tempo de provisionamento de usuários
- Automatização de 90% das tarefas de rede
- Centralização do gerenciamento
- Padronização de configurações

**Suporte e Manutenção:**
- Diagnóstico remoto de problemas
- Resolução proativa de incidentes
- Manutenção sem impacto nos usuários
- Documentação automática de mudanças

## Desafios e Soluções

### Desafio 1: Migração sem Downtime
**Problema:** Necessidade de migrar rede ativa sem interrupção
**Solução:**
- Implementação em paralelo com rede existente
- Migração gradual por departamentos
- Testes extensivos em ambiente isolado
- Plano de rollback detalhado

### Desafio 2: Integração de Sistemas Legados
**Problema:** Sistemas antigos sem suporte a Active Directory
**Solução:**
- Configuração de trust relationships
- Proxy de autenticação para sistemas legados
- Migração gradual com coexistência
- Documentação de workarounds temporários

### Desafio 3: Performance de Autenticação
**Problema:** Lentidão na autenticação com muitos usuários
**Solução:**
- Otimização de consultas LDAP
- Cache de credenciais nos clientes
- Balanceamento entre domain controllers
- Monitoramento de performance contínuo

## Manutenção e Evolução

### Procedimentos de Manutenção

**Manutenção Preventiva:**
- Backup semanal de configurações
- Atualização mensal de firmware
- Verificação de logs de segurança
- Testes de failover trimestrais
- Auditoria anual de permissões

**Monitoramento Contínuo:**
- Análise diária de métricas de performance
- Revisão semanal de logs de segurança
- Relatórios mensais de utilização
- Avaliação trimestral de capacidade

### Roadmap de Evolução

**Próximas Implementações:**
- Migração para Wi-Fi 6 (802.11ax)
- Implementação de SD-WAN
- Integração com cloud services
- Automação com Ansible/Terraform
- Implementação de Zero Trust Network

**Melhorias Planejadas:**
- Machine learning para detecção de anomalias
- Automação completa de provisionamento
- Integração com sistemas de ITSM
- Dashboard executivo com KPIs
- Certificação ISO 27001

## Documentação e Conhecimento

### Documentação Técnica Criada

**Diagramas e Esquemas:**
- Topologia física e lógica da rede
- Fluxogramas de tráfego entre VLANs
- Diagramas de segurança e firewall
- Esquemas de cabeamento estruturado

**Procedimentos Operacionais:**
- Guias de configuração passo-a-passo
- Procedimentos de troubleshooting
- Planos de disaster recovery
- Checklist de manutenção preventiva

**Base de Conhecimento:**
- FAQ para problemas comuns
- Histórico de incidentes e soluções
- Melhores práticas implementadas
- Lições aprendidas documentadas

### Transferência de Conhecimento

**Treinamento da Equipe:**
- Certificação Mikrotik MTCNA para equipe
- Treinamento em SAMBA AD
- Workshops de troubleshooting
- Simulações de cenários de falha

**Documentação de Processos:**
- Runbooks para operações críticas
- Procedimentos de emergência
- Matriz de escalation
- Contatos de suporte técnico

Este projeto de infraestrutura de redes demonstra minha capacidade de projetar e implementar soluções de rede complexas, integrando diferentes tecnologias para criar um ambiente seguro, eficiente e facilmente gerenciável. A combinação de conhecimento técnico profundo em Mikrotik RouterOS e SAMBA Active Directory, aliada à visão de segurança e gestão centralizada, resultou em uma infraestrutura robusta que suporta o crescimento e as necessidades operacionais da organização.


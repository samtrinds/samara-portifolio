---
layout: single
title: Infraestrutura Híbrida com Proxmox
description: Gestão de 27 servidores em ambiente híbrido com alta disponibilidade
img: assets/img/proxmox-infrastructure.jpg
importance: 2
category: infraestrutura
---

## Visão Geral do Projeto

Este projeto representa a implementação e gestão de uma infraestrutura híbrida robusta, combinando **9 servidores físicos e 18 máquinas virtuais** gerenciadas através da plataforma Proxmox VE. O ambiente foi projetado para suportar as operações críticas da empresa Dantas, garantindo alta disponibilidade, performance otimizada e facilidade de manutenção.

## Arquitetura da Infraestrutura

### Ambiente Físico

**Servidores Físicos (9 unidades):**
- **3 Servidores de Produção:** Dell PowerEdge R740
  - 2x Intel Xeon Gold 6248R (24 cores cada)
  - 128GB RAM DDR4 ECC
  - Storage: 4x SSD NVMe 1TB em RAID 10
  - Redundância de fonte de alimentação

- **3 Servidores de Virtualização:** HP ProLiant DL380 Gen10
  - 2x Intel Xeon Silver 4214R (12 cores cada)
  - 256GB RAM DDR4 ECC
  - Storage: 6x SSD SATA 2TB em RAID 6
  - Placas de rede 10Gb para cluster

- **2 Servidores de Backup:** Supermicro SuperServer
  - 1x Intel Xeon E-2278G (8 cores)
  - 64GB RAM DDR4 ECC
  - Storage: 12x HDD 8TB em RAID 6 (capacidade total: 80TB)

- **1 Servidor de Monitoramento:** Custom Build
  - AMD Ryzen 7 5800X (8 cores)
  - 32GB RAM DDR4
  - Storage: 2x SSD NVMe 500GB em RAID 1

### Ambiente Virtual (Proxmox VE)

**Cluster Proxmox:**
- 3 nós em cluster para alta disponibilidade
- Shared storage via Ceph distribuído
- Live migration entre nós
- Backup automatizado integrado

**Distribuição das VMs:**

**Serviços de Infraestrutura (6 VMs):**
- **2x Domain Controllers (Windows Server 2019)**
  - 4 vCPUs, 8GB RAM, 100GB storage
  - Active Directory, DNS, DHCP
  - Configuração em alta disponibilidade

- **2x File Servers (Ubuntu Server 20.04)**
  - 2 vCPUs, 4GB RAM, 2TB storage
  - SAMBA, NFS, FTP
  - Replicação entre servidores

- **1x Backup Server (Proxmox Backup Server)**
  - 4 vCPUs, 16GB RAM, 10TB storage
  - Backup centralizado de todas as VMs
  - Deduplicação e compressão

- **1x Monitoring Server (Ubuntu Server 20.04)**
  - 4 vCPUs, 8GB RAM, 500GB storage
  - Zabbix, Graylog, Grafana
  - Coleta de métricas de toda infraestrutura

**Aplicações de Negócio (8 VMs):**
- **2x Web Servers (Ubuntu Server 20.04)**
  - 4 vCPUs, 8GB RAM, 200GB storage
  - Apache, PHP, SSL certificates
  - Load balancing com HAProxy

- **2x Database Servers (Ubuntu Server 20.04)**
  - 6 vCPUs, 16GB RAM, 1TB storage
  - MySQL 8.0 em configuração Master-Slave
  - Backup automatizado a cada 4 horas

- **2x Application Servers (CentOS 8)**
  - 4 vCPUs, 12GB RAM, 300GB storage
  - Java applications, Tomcat
  - Integração com sistemas ERP

- **1x Development Server (Ubuntu Server 20.04)**
  - 2 vCPUs, 4GB RAM, 200GB storage
  - Ambiente de desenvolvimento e testes
  - Git, Jenkins, Docker

- **1x VPN Server (pfSense)**
  - 2 vCPUs, 2GB RAM, 50GB storage
  - OpenVPN, site-to-site connections
  - Firewall e routing avançado

**Serviços de Suporte (4 VMs):**
- **1x Mail Server (Ubuntu Server 20.04)**
  - 2 vCPUs, 4GB RAM, 500GB storage
  - Postfix, Dovecot, SpamAssassin
  - Webmail com Roundcube

- **1x Proxy Server (Ubuntu Server 20.04)**
  - 2 vCPUs, 4GB RAM, 100GB storage
  - Squid proxy, content filtering
  - Cache para otimização de banda

- **1x DHCP/DNS Server (Ubuntu Server 20.04)**
  - 1 vCPU, 2GB RAM, 50GB storage
  - ISC DHCP, BIND9
  - Redundância para serviços de rede

- **1x Log Server (Ubuntu Server 20.04)**
  - 2 vCPUs, 8GB RAM, 2TB storage
  - Rsyslog, Logrotate
  - Centralização de logs de toda infraestrutura

## Implementação e Configuração

### Fase 1: Planejamento e Design

**Análise de Requisitos:**
- Levantamento de cargas de trabalho existentes
- Projeção de crescimento para 3 anos
- Definição de SLAs e RPOs/RTOs
- Análise de dependências entre sistemas

**Design da Arquitetura:**
- Dimensionamento de recursos (CPU, RAM, storage)
- Planejamento de rede (VLANs, subnets, routing)
- Estratégia de backup e disaster recovery
- Políticas de segurança e acesso

### Fase 2: Instalação e Configuração Base

**Instalação do Proxmox VE:**
```bash
# Configuração inicial do cluster Proxmox
pvecm create infrastructure-cluster
pvecm add 192.168.1.101  # Nó 2
pvecm add 192.168.1.102  # Nó 3

# Configuração do storage Ceph
ceph osd create-simple /dev/sdb
ceph osd create-simple /dev/sdc
ceph osd create-simple /dev/sdd
```

**Configuração de Rede:**
- VLAN 10: Serviços de infraestrutura
- VLAN 20: Aplicações de negócio
- VLAN 30: Desenvolvimento e testes
- VLAN 40: Backup e replicação
- VLAN 50: Gerenciamento (IPMI, switches)

### Fase 3: Migração de Sistemas

**Estratégia de Migração:**
1. **Sistemas não-críticos primeiro** (desenvolvimento, testes)
2. **Migração gradual em horários de baixo uso**
3. **Testes extensivos antes da migração de produção**
4. **Planos de rollback para cada sistema**

**Processo de Migração:**
- Backup completo do sistema origem
- Criação da VM com recursos adequados
- Migração de dados via rsync ou clonezilla
- Configuração de rede e serviços
- Testes de funcionalidade
- Cutover em janela de manutenção

### Fase 4: Otimização e Monitoramento

**Otimizações Implementadas:**
- **CPU:** Configuração de CPU pinning para VMs críticas
- **Memória:** Implementação de memory ballooning
- **Storage:** Configuração de cache modes otimizados
- **Rede:** Bonding de interfaces para redundância

**Monitoramento Implementado:**
- Métricas de performance de todas as VMs
- Alertas proativos para recursos críticos
- Dashboards personalizados no Grafana
- Relatórios automatizados de capacidade

## Tecnologias e Ferramentas Utilizadas

### Virtualização
- **Proxmox VE 7.x:** Plataforma de virtualização principal
- **KVM/QEMU:** Hypervisor de base
- **LXC:** Containers para serviços leves
- **Ceph:** Storage distribuído para o cluster

### Backup e Disaster Recovery
- **Proxmox Backup Server:** Backup de VMs
- **Bacula:** Backup de dados críticos
- **rsync:** Sincronização de arquivos
- **ZFS:** Snapshots para recovery rápido

### Monitoramento e Gestão
- **Zabbix:** Monitoramento de infraestrutura
- **Grafana:** Dashboards e visualizações
- **Prometheus:** Coleta de métricas
- **Graylog:** Centralização de logs

### Rede e Segurança
- **pfSense:** Firewall e VPN
- **HAProxy:** Load balancing
- **Fail2ban:** Proteção contra ataques
- **OpenVPN:** Acesso remoto seguro

## Resultados e Benefícios Alcançados

### Performance e Disponibilidade

**Métricas de Disponibilidade:**
- **Uptime geral:** 99.8% (SLA de 99.5%)
- **Tempo médio de recovery:** Reduzido de 4h para 30min
- **Zero downtime** para manutenções planejadas
- **RPO:** 4 horas (backup automatizado)
- **RTO:** 30 minutos (restore de VM)

**Otimização de Recursos:**
- **Consolidação:** Redução de 40% no número de servidores físicos
- **Utilização de CPU:** Aumento de 35% para 75%
- **Utilização de RAM:** Aumento de 40% para 80%
- **Economia de energia:** Redução de 35% no consumo

### Operacional e Financeiro

**Benefícios Operacionais:**
- **Provisionamento:** Redução de 2 semanas para 2 horas
- **Backup:** Automatização completa com verificação
- **Manutenção:** Redução de 60% no tempo de manutenção
- **Escalabilidade:** Capacidade de crescer 300% sem hardware adicional

**Benefícios Financeiros:**
- **CAPEX:** Economia de R$ 200.000 em hardware
- **OPEX:** Redução de 40% nos custos operacionais
- **Licenciamento:** Otimização de licenças Windows Server
- **Energia:** Economia de R$ 15.000/ano em eletricidade

## Desafios Enfrentados e Soluções

### Desafio 1: Migração de Sistema Legado
**Problema:** Sistema ERP crítico em hardware antigo sem documentação
**Solução:** 
- Análise forense do sistema existente
- Criação de VM com hardware emulado
- Migração em etapas com testes extensivos
- Documentação completa do processo

### Desafio 2: Performance de Storage
**Problema:** Latência alta em aplicações de banco de dados
**Solução:**
- Implementação de cache SSD nos nós Ceph
- Otimização de parâmetros do kernel
- Separação de workloads por tipo de storage
- Monitoramento detalhado de I/O

### Desafio 3: Gestão de Recursos
**Problema:** Overcommit de recursos causando degradação
**Solução:**
- Implementação de resource pools
- Políticas de QoS por tipo de aplicação
- Monitoramento proativo com alertas
- Balanceamento automático de carga

## Segurança e Compliance

### Medidas de Segurança Implementadas

**Segurança de Acesso:**
- Autenticação multifator para administradores
- Segregação de redes por função
- Firewall distribuído entre VLANs
- Logs de auditoria centralizados

**Proteção de Dados:**
- Criptografia de dados em repouso
- Backup criptografado offsite
- Políticas de retenção de dados
- Testes regulares de restore

**Monitoramento de Segurança:**
- IDS/IPS integrado ao pfSense
- Análise de logs de segurança
- Alertas de atividades suspeitas
- Relatórios de compliance automatizados

## Manutenção e Evolução

### Procedimentos de Manutenção

**Manutenção Preventiva:**
- Updates mensais em janelas programadas
- Verificação de integridade do storage
- Testes de backup e recovery
- Análise de performance e capacidade

**Manutenção Corretiva:**
- Procedimentos de troubleshooting documentados
- Escalation matrix para diferentes tipos de problema
- Ferramentas de diagnóstico automatizadas
- Planos de contingência para falhas críticas

### Roadmap de Evolução

**Curto Prazo (6 meses):**
- Implementação de containers Docker
- Upgrade para Proxmox VE 8.x
- Expansão do cluster Ceph
- Automação com Ansible

**Médio Prazo (1 ano):**
- Migração para cloud híbrida (AWS)
- Implementação de Kubernetes
- CI/CD pipeline completo
- Infrastructure as Code

**Longo Prazo (2 anos):**
- Multi-cloud strategy
- AI/ML para otimização automática
- Zero-trust security model
- Edge computing integration

## Documentação e Transferência de Conhecimento

### Documentação Criada

**Documentação Técnica:**
- Diagramas de arquitetura detalhados
- Procedimentos de instalação e configuração
- Runbooks para operações comuns
- Planos de disaster recovery

**Documentação Operacional:**
- Guias de troubleshooting
- Procedimentos de manutenção
- Políticas de backup e recovery
- Matriz de responsabilidades

### Treinamento da Equipe

**Programa de Capacitação:**
- Treinamento em Proxmox VE
- Certificação em virtualização
- Workshops práticos
- Mentoria contínua

**Transferência de Conhecimento:**
- Sessões de knowledge sharing
- Documentação de lições aprendidas
- Criação de base de conhecimento
- Vídeos explicativos dos processos

## Impacto no Negócio

Este projeto de infraestrutura híbrida transformou completamente a capacidade tecnológica da empresa, proporcionando:

**Agilidade de Negócio:**
- Provisionamento rápido de novos ambientes
- Escalabilidade sob demanda
- Flexibilidade para novos projetos
- Redução significativa do time-to-market

**Confiabilidade Operacional:**
- Alta disponibilidade dos serviços críticos
- Recovery rápido em caso de falhas
- Manutenção sem impacto nos usuários
- Monitoramento proativo de problemas

**Eficiência de Custos:**
- Otimização do uso de recursos
- Redução de custos operacionais
- Economia em licenciamento
- Menor consumo de energia

**Preparação para o Futuro:**
- Base sólida para cloud migration
- Capacidade de adoção de novas tecnologias
- Escalabilidade para crescimento do negócio
- Flexibilidade para mudanças de requisitos

Este projeto demonstra minha capacidade de projetar, implementar e gerenciar infraestruturas complexas, combinando conhecimento técnico profundo com visão estratégica de negócio para entregar soluções que geram valor real e duradouro para a organização.


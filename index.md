---
layout: default
title: Infra Linux
description: Infra Linux é a base de conhecimento prática para Linux, infraestrutura, DevOps, Kubernetes, redes, monitoramento e troubleshooting.
---

<section class="home-dashboard-hero">
  <div class="home-dashboard-hero-content">
    <span class="home-eyebrow">Base de conhecimento técnica</span>
    <h1>Infra Linux</h1>
    <p>Documentação prática de Linux, redes, DevOps e infraestrutura — organizada para consulta rápida no dia a dia.</p>
  </div>
  <div class="home-dashboard-stats">
    <div class="home-stat">
      <span class="home-stat-number">10</span>
      <span class="home-stat-label">Áreas</span>
    </div>
    <div class="home-stat">
      <span class="home-stat-number">{{ site.pages | size }}</span>
      <span class="home-stat-label">Páginas</span>
    </div>
    <div class="home-stat">
      <span class="home-stat-number">{{ site.time | date: "%Y" }}</span>
      <span class="home-stat-label">Atualizado</span>
    </div>
  </div>
</section>

<section class="home-grid" id="areas-documentadas" aria-label="Áreas documentadas">

  <a class="home-card home-card-large" href="{{ 'linux/' | relative_url }}">
    <span class="home-card-icon">◉</span>
    <h3>Linux</h3>
    <p>Administração, comandos, LVM e certificados.</p>
  </a>

  <a class="home-card home-card-large" href="{{ 'devops/' | relative_url }}">
    <span class="home-card-icon">⚙</span>
    <h3>DevOps</h3>
    <p>Git, CI/CD, Jenkins, Ansible, Docker e Kubernetes.</p>
  </a>

  <a class="home-card" href="{{ 'redes/' | relative_url }}">
    <span class="home-card-icon">◎</span>
    <h3>Redes</h3>
    <p>DNS, TCP/IP, proxy e conectividade.</p>
  </a>

  <a class="home-card" href="{{ 'squid/' | relative_url }}">
    <span class="home-card-icon">◉</span>
    <h3>Squid</h3>
    <p>Proxy, ACLs, autenticação e logs.</p>
  </a>

  <a class="home-card" href="{{ 'monitoramento/' | relative_url }}">
    <span class="home-card-icon">▥</span>
    <h3>Monitoramento</h3>
    <p>Zabbix, Grafana, métricas e alertas.</p>
  </a>

  <a class="home-card" href="{{ 'nutanix/' | relative_url }}">
    <span class="home-card-icon">◇</span>
    <h3>Nutanix</h3>
    <p>Virtualização, Prism e infraestrutura.</p>
  </a>

  <a class="home-card" href="{{ 'vmware/' | relative_url }}">
    <span class="home-card-icon">▣</span>
    <h3>VMware</h3>
    <p>ESXi, iDRAC, RAID e hosts físicos.</p>
  </a>

  <a class="home-card" href="{{ 'watchguard/' | relative_url }}">
    <span class="home-card-icon">◈</span>
    <h3>WatchGuard</h3>
    <p>Firewall, políticas, VPN e troubleshooting.</p>
  </a>

  <a class="home-card" href="{{ 'windows/' | relative_url }}">
    <span class="home-card-icon">⊞</span>
    <h3>Windows</h3>
    <p>Estações, ferramentas e administração.</p>
  </a>

  <a class="home-card home-card-wide" href="{{ 'troubleshooting/' | relative_url }}">
    <span class="home-card-icon">◆</span>
    <h3>Troubleshooting</h3>
    <p>Erros, diagnósticos e soluções práticas para o dia a dia.</p>
  </a>

</section>
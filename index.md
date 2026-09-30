---
layout: default
title: Pangolim
description: Base de conhecimento técnica sobre infraestrutura e tecnologia.

---

<section class="home-dashboard-hero">
  <div class="home-dashboard-hero-content">
    <span class="home-eyebrow">Base de conhecimento técnico</span>

<h1 class="home-title">
  {% include logo.html class="home-title-icon" %}
  <span>Pangolim</span>
</h1>

<p>
  Documentação prática para estudo, consulta e administração de ambientes de TI.
</p>

  </div>

  <div class="home-dashboard-stats">
    <div class="home-stat">
      <span class="home-stat-number">{{ site.pages | size }}</span>
      <span class="home-stat-label">Páginas</span>
    </div>
  </div>
</section>

<section class="home-tech" aria-labelledby="principais-tecnologias">
  <div class="home-section-heading">
    <span class="home-eyebrow">Tecnologias</span>

<h2 id="principais-tecnologias">Principais áreas</h2>

<p>
  Conteúdos organizados por tecnologias, plataformas e áreas de infraestrutura.
</p>

  </div>
</section>

<section class="home-grid" id="areas-documentadas" aria-label="Áreas documentadas">

  <a class="home-card home-card-large" href="{{ 'devops/' | relative_url }}">
    <span class="home-card-icon">⚙</span>
    <h3>DevOps</h3>
    <p>Git, CI/CD, Jenkins, Ansible, Docker, Kubernetes e automação.</p>
  </a>

  <a class="home-card home-card-large" href="{{ 'linux/' | relative_url }}">
    <span class="home-card-icon">◉</span>
    <h3>Linux</h3>
    <p>Administração, comandos, armazenamento, serviços e certificações.</p>
  </a>

  <a class="home-card" href="{{ 'redes/' | relative_url }}">
    <span class="home-card-icon">◎</span>
    <h3>Redes</h3>
    <p>DNS, TCP/IP, proxy, conectividade e diagnóstico.</p>
  </a>

  <a class="home-card" href="{{ 'squid/' | relative_url }}">
    <span class="home-card-icon">◉</span>
    <h3>Squid</h3>
    <p>Proxy, ACLs, autenticação, regras e logs.</p>
  </a>

  <a class="home-card" href="{{ 'monitoramento/' | relative_url }}">
    <span class="home-card-icon">▥</span>
    <h3>Monitoramento</h3>
    <p>Zabbix, Grafana, métricas, alertas e observabilidade.</p>
  </a>

  <a class="home-card" href="{{ 'nutanix/' | relative_url }}">
    <span class="home-card-icon">◇</span>
    <h3>Nutanix</h3>
    <p>Virtualização, Prism e administração de infraestrutura.</p>
  </a>

  <a class="home-card" href="{{ 'vmware/' | relative_url }}">
    <span class="home-card-icon">▣</span>
    <h3>VMware</h3>
    <p>ESXi, hosts, armazenamento e infraestrutura.</p>
  </a>

  <a class="home-card" href="{{ 'watchguard/' | relative_url }}">
    <span class="home-card-icon">◈</span>
    <h3>WatchGuard</h3>
    <p>Firewall, políticas, VPN e troubleshooting.</p>
  </a>

  <a class="home-card" href="{{ 'windows/' | relative_url }}">
    <span class="home-card-icon">⊞</span>
    <h3>Windows</h3>
    <p>Administração, ferramentas e integração com ambientes de TI.</p>
  </a>

  <a class="home-card" href="{{ 'troubleshooting/' | relative_url }}">
    <span class="home-card-icon">◆</span>
    <h3>Troubleshooting</h3>
    <p>Diagnóstico, investigação de problemas e soluções práticas.</p>
  </a>

</section>
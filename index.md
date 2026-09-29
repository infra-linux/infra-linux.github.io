---
layout: default
title: Pangolim
description: Base de conhecimento técnica sobre Linux, infraestrutura, redes, DevOps, Kubernetes, monitoramento e troubleshooting.
---

<section class="home-dashboard-hero">
  <div class="home-dashboard-hero-content">
    <span class="home-eyebrow">Base de conhecimento técnica</span>

<svg xmlns="http://www.w3.org/2000/svg" width="0" height="0" style="position:absolute" aria-hidden="true">
  <symbol id="icon-pangolin" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round">
    <path d="M12 4 A8 8 0 1 0 20 12 A5 5 0 1 1 12 4"/>
    <path d="M10 8l2 2"/>
    <path d="M13 7l2 2"/>
    <path d="M15 10l2 2"/>
    <path d="M11 11l2 2"/>
    <path d="M14 13l2 2"/>
    <circle cx="18" cy="8" r="0.6"/>
    <path d="M19 8h2"/>
  </symbol>
</svg>

<h1 class="home-title">
<svg class="home-title-icon" aria-hidden="true">
#icon-pangolin</use>
</svg>
Pangolim
</h1>

<p>
  Um espaço para estudar, documentar e consultar conhecimentos de
  infraestrutura e tecnologia de forma prática, organizada e direta.
</p>

<p>
  Aqui você encontra documentação, comandos, procedimentos, exemplos
  e soluções para problemas encontrados no dia a dia da administração
  de ambientes de TI.
</p>

  </div>

  <div class="home-dashboard-stats">
    <div class="home-stat">
      <span class="home-stat-number">{{ site.pages | size }}</span>
      <span class="home-stat-label">Páginas</span>
    </div>
  </div>
</section>

<section class="home-intro" aria-labelledby="objetivo-pangolim">
  <div class="home-intro-content">
    <span class="home-eyebrow">Sobre o projeto</span>

<h2 id="objetivo-pangolim">Conhecimento técnico para consulta rápida</h2>

<p>
  O objetivo do Pangolim é reunir conhecimentos de infraestrutura,
  sistemas e ferramentas de TI em um único lugar, facilitando o estudo,
  a consulta e a resolução de problemas.
</p>

<p>
  A documentação é construída de forma prática, com exemplos de comandos,
  configurações, procedimentos e troubleshooting, priorizando conteúdos
  que possam ser utilizados tanto no aprendizado quanto no trabalho.
</p>

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
    <p>Administração de sistemas, comandos, armazenamento, serviços e certificações.</p>
  </a>
  
  <a class="home-card" href="{{ 'redes/' | relative_url }}">
    <span class="home-card-icon">◎</span>
    <h3>Redes</h3>
    <p>DNS, TCP/IP, proxy, conectividade e diagnóstico de rede.</p>
  </a>

  <a class="home-card" href="{{ 'squid/' | relative_url }}">
    <span class="home-card-icon">◉</span>
    <h3>Squid</h3>
    <p>Proxy, ACLs, autenticação, regras e análise de logs.</p>
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
    <p>ESXi, hosts, armazenamento e infraestrutura física.</p>
  </a>

  <a class="home-card" href="{{ 'watchguard/' | relative_url }}">
    <span class="home-card-icon">◈</span>
    <h3>WatchGuard</h3>
    <p>Firewall, políticas, VPN e troubleshooting.</p>
  </a>

  <a class="home-card" href="{{ 'windows/' | relative_url }}">
    <span class="home-card-icon">⊞</span>
    <h3>Windows</h3>
    <p>Estações, ferramentas, administração e integração com ambientes de TI.</p>
  </a>

  <a class="home-card" href="{{ 'troubleshooting/' | relative_url }}">
    <span class="home-card-icon">◆</span>
    <h3>Troubleshooting</h3>
    <p>
      Diagnóstico de erros, investigação de problemas e soluções práticas.
    </p>
  </a>

</section>

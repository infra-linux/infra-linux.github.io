---
layout: default
title: Buscar no conteúdo
---

# Buscar no conteúdo

<label class="visually-hidden" for="globalSearchInput">Termo de busca</label>
<input class="global-search-input" id="globalSearchInput" type="search" placeholder="Ex.: Kubernetes, LVM, certificado SSL" autocomplete="off">
<p id="globalSearchStatus" class="search-status" aria-live="polite">Digite ao menos dois caracteres para buscar.</p>
<div id="globalSearchResults" class="search-results"></div>

<script>
  (() => {
    const input = document.getElementById('globalSearchInput');
    const status = document.getElementById('globalSearchStatus');
    const results = document.getElementById('globalSearchResults');
    let pages = [];

    fetch('{{ '/search.json' | relative_url }}')
      .then(response => response.ok ? response.json() : Promise.reject())
      .then(data => { pages = data; })
      .catch(() => { status.textContent = 'Não foi possível carregar o índice de busca.'; });

    const escapeHtml = value => value.replace(/[&<>'"]/g, character => ({ '&': '&amp;', '<': '&lt;', '>': '&gt;', "'": '&#39;', '"': '&quot;' })[character]);

    input.addEventListener('input', () => {
      const term = input.value.trim().toLocaleLowerCase('pt-BR');
      if (term.length < 2) {
        results.innerHTML = '';
        status.textContent = 'Digite ao menos dois caracteres para buscar.';
        return;
      }
      const matches = pages.filter(page => `${page.title} ${page.content}`.toLocaleLowerCase('pt-BR').includes(term)).slice(0, 20);
      status.textContent = `${matches.length} resultado(s) encontrado(s).`;
      results.innerHTML = matches.map(page => `<article><h2><a href="${escapeHtml(page.url)}">${escapeHtml(page.title)}</a></h2><p>${escapeHtml(page.content.slice(0, 220))}...</p></article>`).join('') || '<p>Nenhum conteúdo encontrado.</p>';
    });
  })();
</script>

#!/usr/bin/env python3
"""
Script para buscar concursos públicos em São Paulo com critérios:
- Salário > R$ 10.000
- Mais de 10 vagas
- Atuação em SP
- Status: inscrições abertas, edital publicado ou autorizado

Gera o arquivo index.html atualizado.
"""

import requests
from bs4 import BeautifulSoup
from datetime import datetime
import json
import re
import os

# Critérios
SALARIO_MIN = 10000
VAGAS_MIN = 10

def parse_salary(text):
    if not text:
        return 0
    # Extrai números de strings como "até R$ 29.716,00" ou "R$ 11.359"
    numbers = re.findall(r'[\d\.]+', text.replace('.', '').replace(',', '.'))
    if numbers:
        try:
            return float(numbers[-1])
        except:
            return 0
    return 0

def parse_vagas(text):
    if not text:
        return 0
    match = re.search(r'(\d+)', str(text))
    return int(match.group(1)) if match else 0

def scrape_pci():
    """Tenta raspar o PCI Concursos (pode precisar de ajustes se o site mudar)"""
    concursos = []
    try:
        headers = {'User-Agent': 'Mozilla/5.0 (compatible; ConcursosBot/1.0)'}
        r = requests.get('https://www.pciconcursos.com.br/concursos/sp/', headers=headers, timeout=15)
        r.raise_for_status()
        soup = BeautifulSoup(r.text, 'html.parser')
        # Ajuste o seletor conforme a estrutura atual do site
        # Este é um exemplo básico — sites mudam com frequência
        items = soup.select('.concurso-item, .item-concurso, article, .card')[:20]  # limite para não sobrecarregar
        for item in items:
            text = item.get_text(' ', strip=True)
            if 'SP' not in text and 'São Paulo' not in text:
                continue
            # Extração simples (melhorar conforme necessário)
            title = item.find(['h2', 'h3', 'a'])
            title = title.get_text(strip=True) if title else 'Concurso SP'
            salario = parse_salary(text)
            vagas = parse_vagas(text)
            if salario >= SALARIO_MIN and vagas >= VAGAS_MIN:
                concursos.append({
                    'orgao': title,
                    'cargo': 'Ver edital',
                    'vagas': vagas,
                    'salario': salario,
                    'status': 'open',
                    'statusLabel': 'Inscrições abertas / Edital publicado',
                    'local': 'São Paulo (SP)',
                    'escolaridade': 'Ver edital',
                    'banca': 'Ver edital',
                    'prova': 'Ver edital',
                    'link': 'https://www.pciconcursos.com.br/concursos/sp/',
                    'detalhes': 'https://www.pciconcursos.com.br/concursos/sp/'
                })
    except Exception as e:
        print(f'Erro ao raspar PCI: {e}')
    return concursos

def get_fallback_data():
    """Dados conhecidos como fallback (atualize manualmente se necessário)"""
    return [
        {
            'orgao': 'DPE-SP (Defensoria Pública do Estado de São Paulo)',
            'cargo': 'Defensor Público',
            'vagas': 70,
            'salario': 29716,
            'status': 'open',
            'statusLabel': 'Inscrições abertas até 11/11/2026',
            'local': 'São Paulo (SP)',
            'escolaridade': 'Superior em Direito + 3 anos de prática jurídica',
            'banca': 'FCC',
            'prova': '10/01/2027',
            'link': 'https://www.acheconcursos.com.br/concurso-dpe-sp',
            'detalhes': 'https://jcconcursos.com.br/noticia/concursos/concurso-dpe-sp-publicado-edital-para-70-vagas-de-defensor-145934'
        },
        {
            'orgao': 'TRF-3 (Tribunal Regional Federal da 3ª Região)',
            'cargo': 'Analista e Técnico Judiciário',
            'vagas': 11,
            'salario': 20013,
            'status': 'open',
            'statusLabel': 'Inscrições abertas até 23/11/2026',
            'local': 'São Paulo e Mato Grosso do Sul',
            'escolaridade': 'Nível Superior',
            'banca': 'Vunesp',
            'prova': '31/01/2027',
            'link': 'https://comunidadedosconcursos.com.br/concurso-trf3-sp-ms-2026/',
            'detalhes': 'https://folha.qconcursos.com/n/concurso-trf3-edital-2026'
        },
        {
            'orgao': 'IFSP (Instituto Federal de São Paulo)',
            'cargo': 'Professor EBTT',
            'vagas': 47,
            'salario': 13753,
            'status': 'open',
            'statusLabel': 'Inscrições abertas até 17/11/2026',
            'local': 'Vários campi em SP',
            'escolaridade': 'Graduação na área',
            'banca': 'Comissão própria',
            'prova': '13/12/2026',
            'link': 'https://concursopublico.ifsp.edu.br/',
            'detalhes': 'https://comunidadedosconcursos.com.br/concurso-ifsp-sp-2026/'
        },
        {
            'orgao': 'SETEC Campinas',
            'cargo': 'Diversos (Procurador, Engenheiro etc.)',
            'vagas': 40,
            'salario': 11427,
            'status': 'open',
            'statusLabel': 'Inscrições abertas até 04/12/2026',
            'local': 'Campinas - SP',
            'escolaridade': 'Fundamental a Superior',
            'banca': 'INDEPAC',
            'prova': '17/01/2027',
            'link': 'https://g1.globo.com/sp/campinas-regiao/concursos-e-emprego/noticia/2026/09/09/concurso-da-setec-em-campinas-edital-abre-40-vagas-com-salario-de-ate-r-114-mil.ghtml',
            'detalhes': 'https://comunidadedosconcursos.com.br/concurso-setec-campinas-sp-2026/'
        }
    ]

def generate_html(concursos):
    data_js = json.dumps(concursos, ensure_ascii=False, indent=2)
    today = datetime.now().strftime('%d/%m/%Y')
    html = f'''<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Concursos SP - Salário > R$10k | +10 Vagas</title>
<style>
  :root {{ --bg:#0f1419; --card:#1a2332; --accent:#3b82f6; --accent2:#10b981; --text:#e2e8f0; --muted:#94a3b8; --border:#334155; }}
  * {{ box-sizing:border-box; margin:0; padding:0; }}
  body {{ font-family:system-ui,-apple-system,sans-serif; background:var(--bg); color:var(--text); line-height:1.5; padding:1rem; }}
  header {{ text-align:center; padding:2rem 1rem; background:linear-gradient(135deg,#1e3a8a,#1e40af); border-radius:16px; margin-bottom:1.5rem; }}
  h1 {{ font-size:1.8rem; margin-bottom:0.5rem; }}
  .subtitle {{ color:#bfdbfe; font-size:0.95rem; }}
  .filters {{ display:flex; flex-wrap:wrap; gap:0.75rem; margin-bottom:1.5rem; justify-content:center; }}
  input,select,button {{ padding:0.6rem 1rem; border-radius:8px; border:1px solid var(--border); background:var(--card); color:var(--text); font-size:0.9rem; }}
  button {{ background:var(--accent); border:none; cursor:pointer; font-weight:600; }}
  .grid {{ display:grid; grid-template-columns:repeat(auto-fill,minmax(340px,1fr)); gap:1.25rem; }}
  .card {{ background:var(--card); border:1px solid var(--border); border-radius:12px; padding:1.25rem; transition:transform 0.2s,box-shadow 0.2s; }}
  .card:hover {{ transform:translateY(-4px); box-shadow:0 10px 25px rgba(0,0,0,0.4); }}
  .badge {{ display:inline-block; padding:0.25rem 0.6rem; border-radius:999px; font-size:0.75rem; font-weight:600; margin-bottom:0.5rem; }}
  .badge.open {{ background:#065f46; color:#6ee7b7; }}
  .badge.published {{ background:#1e3a8a; color:#93c5fd; }}
  .card h2 {{ font-size:1.25rem; margin:0.4rem 0; }}
  .meta {{ color:var(--muted); font-size:0.85rem; margin-bottom:0.75rem; }}
  .stats {{ display:flex; justify-content:space-between; margin:0.75rem 0; font-weight:600; }}
  .stat-value {{ color:var(--accent2); }}
  .links a {{ color:var(--accent); text-decoration:none; font-size:0.9rem; margin-right:1rem; }}
  footer {{ text-align:center; margin-top:2rem; color:var(--muted); font-size:0.8rem; }}
  .empty {{ text-align:center; padding:3rem; color:var(--muted); }}
</style>
</head>
<body>
<header>
  <h1>Localizador de Concursos Públicos SP</h1>
  <p class="subtitle">Inscrições abertas / Edital publicado / Autorizado • Salário > R$ 10.000 • +10 vagas • Atuação em São Paulo</p>
  <p style="margin-top:0.5rem; font-size:0.85rem; opacity:0.9;">Dados atualizados em {today} • Sempre confira o edital oficial</p>
</header>
<div class="filters">
  <input type="text" id="search" placeholder="Buscar órgão ou cargo..." oninput="filterCards()">
  <select id="statusFilter" onchange="filterCards()">
    <option value="">Todos os status</option>
    <option value="open">Inscrições abertas</option>
    <option value="published">Edital publicado</option>
  </select>
  <button onclick="filterCards()">Filtrar</button>
</div>
<div class="grid" id="cards"></div>
<div class="empty" id="empty" style="display:none;">Nenhum concurso encontrado com os filtros.</div>
<footer>
  <p>App gerado automaticamente. Não substitui consulta ao Diário Oficial ou site da banca.</p>
  <p>Fontes: PCI Concursos, JC Concursos e outros portais públicos.</p>
</footer>
<script>
const concursos = {data_js};
function formatMoney(v) {{ return v.toLocaleString('pt-BR', {{style:'currency', currency:'BRL'}}); }}
function statusClass(s) {{ return s === 'open' ? 'open' : 'published'; }}
function render(list) {{
  const container = document.getElementById('cards');
  const empty = document.getElementById('empty');
  container.innerHTML = '';
  if (list.length === 0) {{ empty.style.display = 'block'; return; }}
  empty.style.display = 'none';
  list.forEach(c => {{
    const div = document.createElement('div');
    div.className = 'card';
    div.innerHTML = `
      <span class="badge ${{statusClass(c.status)}}">${{c.statusLabel}}</span>
      <h2>${{c.orgao}}</h2>
      <div class="meta">${{c.cargo}}</div>
      <div class="stats">
        <div><span class="stat-value">${{c.vagas}}</span> vagas</div>
        <div>até <span class="stat-value">${{formatMoney(c.salario)}}</span></div>
      </div>
      <div class="meta"><strong>Local:</strong> ${{c.local}}<br>
      <strong>Escolaridade:</strong> ${{c.escolaridade}}<br>
      <strong>Banca:</strong> ${{c.banca}} • <strong>Prova:</strong> ${{c.prova}}</div>
      <div class="links">
        <a href="${{c.link}}" target="_blank">Mais informações</a>
        <a href="${{c.detalhes}}" target="_blank">Detalhes / Edital</a>
      </div>
    `;
    container.appendChild(div);
  }});
}}
function filterCards() {{
  const q = document.getElementById('search').value.toLowerCase();
  const st = document.getElementById('statusFilter').value;
  const filtered = concursos.filter(c => {{
    const matchText = (c.orgao + c.cargo).toLowerCase().includes(q);
    const matchStatus = !st || c.status === st;
    return matchText && matchStatus;
  }});
  filtered.sort((a,b) => b.salario - a.salario);
  render(filtered);
}}
filterCards();
</script>
</body>
</html>'''
    return html

def main():
    print('Buscando concursos...')
    scraped = scrape_pci()
    fallback = get_fallback_data()
    # Combina e remove duplicatas simples
    all_concursos = scraped + [c for c in fallback if c['orgao'] not in [s['orgao'] for s in scraped]]
    # Filtra novamente pelos critérios
    filtered = [c for c in all_concursos if c['salario'] >= SALARIO_MIN and c['vagas'] >= VAGAS_MIN]
    if not filtered:
        filtered = fallback  # pelo menos mostra algo
    html = generate_html(filtered)
    with open('index.html', 'w', encoding='utf-8') as f:
        f.write(html)
    print(f'index.html gerado com {len(filtered)} concursos.')

if __name__ == '__main__':
    main()

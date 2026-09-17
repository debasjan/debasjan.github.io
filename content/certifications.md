---
title: "Certifications"
layout: "single"
url: "/certifications/"
summary: "Certifications and lab completions"
ShowToc: false
hidemeta: true
---

<div class="page-topbg"></div>

<div class="timeline-sidebar" id="certTimeline"></div>

<div class="certs-hero">
<p class="certs-hero__sub">A running record of certifications and lab completions from my <span class="accent">offensive security journey</span>.</p>
<div class="certs-stats">
<div>
<div class="certs-stat-value">3</div>
<div class="certs-stat-label">Total Certs</div>
</div>
<div>
<div class="certs-stat-value">1</div>
<div class="certs-stat-label">In Progress</div>
</div>
<div>
<div class="certs-stat-value">2</div>
<div class="certs-stat-label">Vendors</div>
</div>
</div>
</div>

<div class="cert-filter-bar">
<div class="cert-filter active" data-filter="all">All Certifications</div>
<div class="cert-filter" data-filter="offensive">Offensive</div>
</div>

<div class="cert-milestone" data-year="2026">
<div class="cert-milestone-year">2026</div>
<div class="cert-grid">
<div class="cert-card" data-category="offensive">
<div class="cert-card__header">
<div class="cert-card__id">
<span class="cert-card__badge">HTB</span>
<span class="cert-card__acronym">Dante</span>
</div>
<span class="cert-card__check done">✓</span>
</div>
<div class="cert-card__vendor">Hack The Box</div>
<h3 class="cert-card__title">Dante Pro Lab</h3>
<p class="cert-card__desc">Entry-level Active Directory pro lab — a full corporate network compromise from initial foothold to domain takeover.</p>
<span class="cert-card__date">Feb 2026</span>
<div class="cert-card__actions">
<a href="https://profile.hackthebox.com/profile/019d1cb4-4b60-7102-8db2-fa81f7ab5df5" target="_blank" rel="noopener noreferrer">HTB Profile →</a>
</div>
</div>
<div class="cert-card" data-category="offensive">
<div class="cert-card__header">
<div class="cert-card__id">
<span class="cert-card__badge">OS</span>
<span class="cert-card__acronym">OSCP</span>
</div>
<span class="cert-card__check progress">⏳</span>
</div>
<div class="cert-card__vendor">OffSec</div>
<h3 class="cert-card__title">OffSec Certified Professional</h3>
<p class="cert-card__desc">Working through the PEN-200 course and lab machines, focused on methodology over memorized exploits.</p>
<span class="cert-card__date">In Progress</span>
</div>
</div>
</div>

<div class="cert-milestone" data-year="2025">
<div class="cert-milestone-year">2025</div>
<div class="cert-grid">
<div class="cert-card" data-category="offensive">
<div class="cert-card__header">
<div class="cert-card__id">
<span class="cert-card__badge">eJ</span>
<span class="cert-card__acronym">eJPT</span>
</div>
<span class="cert-card__check done">✓</span>
</div>
<div class="cert-card__vendor">INE / eLearnSecurity</div>
<h3 class="cert-card__title">Junior Penetration Tester</h3>
<p class="cert-card__desc">Practical, scenario-based exam covering network and web fundamentals.</p>
<span class="cert-card__date">December 2025</span>
<div class="cert-card__actions">
<a href="https://certs.ine.com/e5da4c9b-82af-4036-87af-0da6644de771" target="_blank" rel="noopener noreferrer">Verify Credential →</a>
</div>
</div>
</div>
</div>

<hr class="wu-divider">

<p>See also the <a href="https://github.com/debasjan/security-portfolio">lab stats and full write-ups</a> in my portfolio.</p>

<script>
(function () {
  var filters = document.querySelectorAll('.cert-filter');
  var cards = document.querySelectorAll('.cert-card');
  var milestones = document.querySelectorAll('.cert-milestone');

  filters.forEach(function (btn) {
    btn.addEventListener('click', function () {
      filters.forEach(function (b) { b.classList.remove('active'); });
      btn.classList.add('active');
      var f = btn.getAttribute('data-filter');

      cards.forEach(function (card) {
        var match = f === 'all' || card.getAttribute('data-category') === f;
        card.classList.toggle('is-hidden', !match);
      });

      milestones.forEach(function (m) {
        var anyVisible = Array.prototype.some.call(
          m.querySelectorAll('.cert-card'),
          function (c) { return !c.classList.contains('is-hidden'); }
        );
        m.style.display = anyVisible ? '' : 'none';
      });
    });
  });

  var timeline = document.getElementById('certTimeline');
  var milestoneEls = document.querySelectorAll('.cert-milestone');
  if (timeline && milestoneEls.length) {
    milestoneEls.forEach(function (m, i) {
      var year = m.getAttribute('data-year');
      var item = document.createElement('div');
      item.className = 'timeline-sidebar-item' + (i === 0 ? ' active' : '');
      item.innerHTML = '<span class="timeline-dot"></span><span class="timeline-year-label">' + year + '</span>';
      item.addEventListener('click', function () {
        m.scrollIntoView({ behavior: 'smooth', block: 'start' });
      });
      timeline.appendChild(item);
    });

    var items = timeline.querySelectorAll('.timeline-sidebar-item');
    var observer = new IntersectionObserver(function (entries) {
      entries.forEach(function (entry) {
        var idx = Array.prototype.indexOf.call(milestoneEls, entry.target);
        if (entry.isIntersecting && idx !== -1) {
          items.forEach(function (it) { it.classList.remove('active'); });
          items[idx].classList.add('active');
        }
      });
    }, { rootMargin: '-40% 0px -50% 0px' });

    milestoneEls.forEach(function (m) { observer.observe(m); });
  }
})();
</script>

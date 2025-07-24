---
title: Data Lifecycle
---
<div class="container pt-3">
    <div class="row">
        <div class="col">
            <div class="svg-container" style="width:100%;height:60vh;overflow:auto;display:flex;align-items:center;justify-content:center;">
              {% include reactive_datalifecycle.svg %}
            </div>
        </div>
      <div class="col">
        <p id="display-text">Click on a stage to read the description.</p>
      </div>
    </div>
</div>

<script>
  document.addEventListener("DOMContentLoaded", function () {
    const iconTexts = {
      {% for item in site.data.lifecycle_stages %}
        "{{ item.id }}": "{{ item.text | escape }}",
      {% endfor %}
    };

    Object.keys(iconTexts).forEach(function(id) {
      const el = document.getElementById(id);
      if (el) {
        el.style.cursor = 'pointer';
        el.addEventListener('click', function () {
          document.getElementById('display-text').textContent = iconTexts[id];
        });
      }
    });
  });
</script>

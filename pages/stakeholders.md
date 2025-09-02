---
title: Stakeholders
---

<head>
    <script src="https://cdn.plot.ly/plotly-3.1.0.min.js" charset="utf-8"></script>
    <script src="https://d3js.org/d3.v7.min.js"></script>
</head>


The creation of the tools assemblies is performed in close collaboration with various stakeholders. 
These include institutions, organizations and communities that provide information about the tools currently uses and 
the gaps and issues that researchers have to face and that we want to tackle through the assemblies. 
The stakeholders group include also entities that are working on relevant projects, initiatives or topics, with which we want to align our work.

Here below a representation of the stakeholders groups involved in the project is shown.

<div id="tester"></div>

<script>
    TESTER = document.getElementById('tester');
    const csvFile = "assets/data/stakeholders.csv";
    const clean = s => (s ? String(s).trim() : "");
    
    d3.csv(csvFile).then(rows => {
      // Collect unique top-level parents (ring 1)
      const topSet = new Set();
      rows.forEach(r => {
        const p = clean(r.parents);
        if (p) topSet.add(p);
      });

      // Map: top-level parent -> unique id
      const topIdOf = {};
      [...topSet].forEach(p => (topIdOf[p] = `top:${p}`));

      // Middle nodes
      const midMap = new Map(); // key = parent||label -> node
      // Leaf nodes
      const leaves = [];

      // Pass 1: build middle nodes
      rows.forEach(r => {
        const p = clean(r.parents);
        const l = clean(r.labels);
        if (!p || !l) return;
        const key = `${p}||${l}`;
        if (!midMap.has(key)) {
          midMap.set(key, {
            id: `mid:${p}::${l}`,
            parentId: topIdOf[p],
            label: l
          });
        }
      });

      // Pass 2: build leaves
      rows.forEach(r => {
        const p = clean(r.parents);
        const l = clean(r.labels);
        const leafLabel = clean(r.ids);
        if (!p || !l || !leafLabel) return;
        const midKey = `${p}||${l}`;
        const mid = midMap.get(midKey);
        const parentId = mid ? mid.id : topIdOf[p];
        leaves.push({
          id: `leaf:${p}::${l}::${leafLabel}`,
          parentId,
          label: leafLabel
        });
      });

      // Build Plotly arrays
      const labels = [];
      const parents = [];
      const ids = [];

      // Root node ("All")
      labels.push("All");
      parents.push("");
      ids.push("All"); // must match what children refer to

      // Top-level nodes
      [...topSet].forEach(p => {
        labels.push(p);
        parents.push("All");
        ids.push(topIdOf[p]);
      });

      // Middle-level nodes
      midMap.forEach(node => {
        labels.push(node.label);
        parents.push(node.parentId);
        ids.push(node.id);
      });

      // Leaf-level nodes
      leaves.forEach(n => {
        labels.push(n.label);
        parents.push(n.parentId);
        ids.push(n.id);
      });

      const data = [{
        type: "sunburst",
        labels,
        parents,
        ids,
        branchvalues: "total",
        maxdepth: 4,
        hovertemplate: "<b>%{label}</b><extra></extra>"
      }];


    var layout = {
        margin: {l: 0, r: 0, b: 0, t: 0},
        width: 500,
        height: 500
    };

      Plotly.newPlot(TESTER, data, layout);
    });
</script>

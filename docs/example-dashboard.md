---
theme: "glacier"
title: Dashboard
toc: false
---

<style>


.body {
  margin: 1.5rem 0; /* Reduced margin for tighter layout */
  font-size: calc(1.5rem + 1.5vw); /* Dynamic font size for scalability */
  font-weight: bold; /* Enhanced font weight for prominence */
  line-height: 1.2; /* Adjusted line height for readability */
  color: #933ec4; /* Simplified color for direct application, replace with your theme color */
}


</style>


# Hello, Palmer Penguines 🐧

<!-- Load and transform the data -->

```js
const penguins = FileAttachment("data/penguins.csv").csv({typed: true});
```


<!-- Cards with big numbers -->

<div class="grid grid-cols-3">
  <div class="card">
    <h2 style="color: #ff6c01; font-size: 24px; font-weight: bold;">Adelie</h2>
    <span class="big">${penguins.filter((d) => d.species === "Adelie").length.toLocaleString("en-US")}</span>
  </div>

  <div class="card">
    <h2 style="color: #c45bca; font-size: 24px; font-weight: bold;">Chinstrap</h2>
    <span class="big">${penguins.filter((d) => d.species === "Chinstrap").length.toLocaleString("en-US")}</span>
  </div>

  <div class="card">
    <h2 style="color: #077276; font-size: 24px; font-weight: bold;">Gentoo</h2>
    <span class="big">${penguins.filter((d) => d.species === "Gentoo").length.toLocaleString("en-US")}</span>
  </div>
  
</div>

<!-- Plot of penguins -->

```js
const speciesInput = Inputs.select(penguins.map((d) => d.species), {unique: true, sort: true, label: "Species:"});
const species = Generators.input(speciesInput);
const colorScale = {
  "Adelie": "#ff6c01",
  "Chinstrap": "#c45bca",
  "Gentoo": "#077276"
};
```

<div class="grid grid-cols-2" style="grid-auto-rows: 504px;">
<div class="card" style="display: flex; flex-direction: column; gap: 1rem;">
  ${speciesInput}
  ${resize((width) => Plot.plot({
      title: "How big are penguins",
      width,
      grid: true,
      x: {label: "Body mass (g)"},
      y: {label: "Flipper length (mm)"},
      color: {legend: false, range: ["#002642","#c45bca","#077276"]},
      marks: [
        Plot.linearRegressionY(penguins.filter((d) => d.species === species), {x: "body_mass_g", y: "flipper_length_mm", stroke: d => colorScale[d.species]}),
        Plot.dot(penguins.filter((d) => d.species === species), {x: "body_mass_g", y: "flipper_length_mm",stroke: d => colorScale[d.species],tip: true})
      ]
    }))
  }
</div>

<div class="card">${
    resize((width) => Plot.plot({
      title: "Weight of the Penguins",
  color: {legend: true,range: ["#ff6c01","#c45bca","#077276"]},
  marks: [
    Plot.rectY(
      penguins,
      Plot.sort(
        {channel: "y2", order: "descending"}, // prevent occlusion 🌶️
        Plot.binX(
          {y2: "count"}, // opt-out of stacking
          {x: "body_mass_g", fill: "species"}
        )
      )
    ),
    Plot.ruleY([0])
  ]
}))
  }</div>

  <div class="card">${
    resize((width) => Plot.plot({
      title: "Which Islands do the Penguins Belong",
  color: {legend: true,range: ["#ff6c01","#c45bca","#077276"]},
  marks: [
    Plot.barY(
  penguins,
  Plot.groupX({ y: "count" }, { x: "island", fill: "species",tip:true }))
  ]
}))
  }</div>

  <div class="card">${
    resize((width) => Plot.plot({
  title: "Density Estimate of the Culmen Length",
  height: 300,
  marginLeft: 60,
  y: { axis: null },
  x: { nice: true },
  fy: { domain: ["FEMALE", "MALE"] }, // excludes N/A
  color: { legend: true,range: ["#ff6c01","#c45bca","#077276"] },
  facet: { data: penguins, y: "sex" },
  marks: [
    Plot.areaY(
      penguins,
      Plot.binX(
        { y2: "proportion" }, // using y2 to avoid areaY’s implicit stacking
        {
          x: "culmen_length_mm",
          fill: "species",
          fillOpacity: 0.5,
          thresholds: 10,
          curve: "natural",
          tip:true
        }
      )
    ),
    Plot.ruleY([0]),
    Plot.lineY(
      penguins,
      Plot.binX(
        { y: "proportion" },
        {
          x: "culmen_length_mm",
          stroke: "species",
          thresholds: 10,
          curve: "natural"
        }
      )
    )
  ]
}))
  }</div>

  

</div>
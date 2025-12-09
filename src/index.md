---
title: 2026 OPEN Community Survey
theme: air
toc: false
sidebar: false
header: false
footer: false
pager: false
---

<script>
const observer = new ResizeObserver(([entry]) => parent.postMessage({height: entry.target.offsetHeight}, "*"));
observer.observe(document.documentElement);
invalidation.then(() => observer.disconnect());
</script>


<style>

div.card {
  /* The Magic */
  background: rgba(255, 255, 255, 0.05);
  backdrop-filter: blur(9px);
  -webkit-backdrop-filter: blur(9px);
  
  /* The Border simulates the glass edge */
  border: 1px solid rgba(255, 255, 255, 0.15);
  border-top: 1px solid rgba(255, 255, 255, 0.3); /* Catching light */
  
  border-radius: 24px;
  box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.1);
}

</style>


```js
import * as Inputs from "@observablehq/inputs"
```


# Relationship Identity Overlap

How do different non-monogamous identities intersect? 
Participants were able to select multiple options for their non-monogamous identity, and then were asked to select just one as the identity they *most* identify with. We can see that overall Polyamory was the most commonly selected option, followed closely by ENM/CNM, but this varies if we filter by primary identity.

Select a **Primary Identity** below to see which other relationship styles that specific group also identifies with. For example, see how different the results look for **Open Relationship**. What might this distribution tell us about the community who responded to this survey? 

```js
// 1. LOAD DATA
// We load the CSV. 'typed: true' automatically converts numbers for us.
const overlapData = await FileAttachment("data/identity_overlap.csv").csv({typed: true});
```

<div class="card">


```js
// 2. CREATE DROPDOWN
// We extract the unique list of primary identities to populate the menu.
const primaryOptions = overlapData.map(d => d.primary_identity)
  .filter((v, i, a) => a.indexOf(v) === i) // Remove duplicates
  .sort(); // Sort alphabetically
// The 'view' function displays the input and exposes the user's choice 
// as the variable 'selectedPrimary' for the rest of the page to use.

const selectedPrimary = view(Inputs.select(primaryOptions, {
  label: "Select a Primary Identity to filter: ",
  value: "All Respondents" // The default starting option
}));
```


```js
// 3. FILTER DATA
// We create a reactive subset of data that changes whenever 'selectedPrimary' changes.
// We also filter out the "self-match" (e.g. removing the Polyamory bar 
// if Polyamory is selected) to keep the chart focused on overlaps.
const filteredData = overlapData.filter(d => 
  d.primary_identity === selectedPrimary && 
  d.secondary_identity.toLowerCase() !== selectedPrimary.toLowerCase()
);

const identities = Array.from(new Set([
  ...overlapData.map(d => d.primary_identity),
  ...overlapData.map(d => d.secondary_identity)
])).sort();
```



```js
Plot.plot({
  width: width,
  height: 500,
  marginLeft: 200, // Increased to fit "Ethical / Consensual..." label
  marginBottom: 40,
  

  x: {
    label: "Percentage of this group",
    percent: true,
    grid: true
  },
  y: {
    label: null,
    tickSize: 0
  },
    // "Sticky" Colors: Assign a unique color to each identity from a nice palette
  color: {
    domain: identities,
    legend: false, // Adds a legend at the top (optional, can remove if cluttered)
    scheme: "Spectral" // Try "Turbo", "Viridis", or "Tableau10" for different vibes
  },
  marks: [
    Plot.barX(filteredData, {
      x: "overlap_percentage",
      y: "secondary_identity",
      fill: "secondary_identity",
      stroke: "#999",
      sort: {y: "x", reverse: true}, // This one line handles the sorting safely
      tip: {
        format: {
            x: true,
            y: false,
            fill: false
        }
      }
    }),
    Plot.ruleX([0])
  ]
})
```
</div>

<div class="card" style="background: rgba(255, 254, 156, 0.07);">

<h1>Other identities:</h1>

Participants were also given an opportunity to write in other identities that they use for themselves. Because each answer was different, it is difficult to statistically analyze them, but looking at the answers qualitatively some interesting trends emerged. 

#### Parallel Polyamory
While the options included Polyamory, Kitchen Table Polyamory, and Solo Polyamory, several people wrote in a version of "Parallel Polyamory", showing a clear interest in identifying with that way of conducting relationships.

#### Rejecting the "Ethical" label
Several respondends specifically mentioned that they don't like using "ethical" as a qualifier for non-monogamy because of the implication that non-monogamy is unethical by default or pointing out the fact that we don't use "ethical" to qualify monogamy so we shouldn't use it with non-monogamy either.

#### Ambiamory
Additionally, there were several mentions of "Ambiamory," "Flexible," or other identities having to do with being happy and satisfied with either monogamous or non-monogamous relationships. 

#### Mono-Poly
Several people wrote that they are in a relationship where one partner practices monogamy while the other doesn't. Most people who wrote this in were the monogamous half of a mono-poly relationship, which makes sense that it is an important part of their identity.

#### Asexual/Aromantic
There is a clear intersection between the non-monogamy community and the Ace/Aro community. These responses highlighted the importance of queer platonic relationships, as well as other variations on the romantic/sexual attraction spectrum.

#### "Swolly" / "Swoly"
Variations of "Swolly" or "Swoly" were used to describe people who practice Swinging/Lifestyle relationships but maintain deep emotional connections with their play partners. It came up in just a few responses but was an interesting distinction to make.
</div>

----

## Years of Experience:

How long had the survey participants been practicing consensual non-monogamy? 
Adjust the slider to change the detail level. Notice how the shape changes.

<div class="card">

```js
// 1. Load Data
const experienceData = await FileAttachment("data/experience_data.csv").csv({typed: true});


// 2. Create the Slider controls
// We let the user choose between 0.5 years (6 months) and 5 year chunks.
const binSize = view(Inputs.range([1, 10], {
  label: "Group by (Years)",
  step: 1,
  value: 1 // Default to 1-year buckets
}));
```

```js
// 3. Draw the Interactive Histogram
Plot.plot({
  width: width,
  height: 400,
  marginLeft: 60,
  
  x: {
    label: "Years of Experience",
    grid: true,
    // Add a little padding at the end so the last bar doesn't get cut off
    domain: [0, 60] 
  },
  
  y: {
    label: "Number of Respondents",
    grid: true
  },
  
  marks: [
    // The "rectY" mark with a "binX" transform creates a histogram automatically
    Plot.rectY(experienceData, Plot.binX(
      {y: "count"}, // Calculate the height based on how many rows are in this bin
      {
        x: "years_practicing", 
        fill: "#5f4d8d", // Your brand color
        
        // INTERACTIVITY: This connects the chart to the slider above
        interval: Math.round(binSize),
        
        tip: true // Tooltips show the exact count in that range
      }
    )),
    
    Plot.ruleY([0])
  ]
})
```

</div>

---

## Entry Age

When did the people in this survey begin their journey into Non-Monogamy? These ages are an approximation and do not account for potential stops and starts that were not gathered in the survey but should provide an interesting glimpse into the average ages that participants begin practicing CNM.

If we look at the total data, we can see that the median starting age is around 29 years old, though many people start a bit earlier in their 20's, and people continue to enter into non-monogamy later in life, contradicting many assumptions that this is only something for young people.

<div class="card">

The vertical black line shows the median estimated starting age of all participants.

```js
// 1. Load Data
const entryData = await FileAttachment("data/entry_age_data.csv").csv({typed: true});

const filteredEntry = entryData.filter(d => 
  d.identity !== null
);
```


```js

// Overall average
Plot.plot({
  width: width,
  height: 100,
  marginLeft: 180, // Space for labels like "Relationship Anarchy"
  
  x: {
    label: "Estimated Age at Entry",
    grid: true,
    domain: [15, 60] // Focus on the main adult years (optional)
  },
  
  y: {
    axis: null, // Hide the Y axis numbers (we just care about the shape)
    label: null,
    // domain: [0, 0.4] // Adjust height overlap manually if needed
  },

  marks: [
    Plot.areaY(entryData, Plot.binX(
      {y: "count"
      },
      {
        x: "entry_age",
        fill: "#5f4d8d",
        curve: "basis", // Smooth the lines (looks nicer than jagged bars)
        thresholds: 40, // Controls how smooth/detailed the curve is
        stroke: "#333",
        tip: true // Add tooltips
      }
    )),

    Plot.ruleX(filteredEntry, Plot.groupZ(
      {x: "median"}, // Calculate the median of 'x' for each group
      {
        x: "entry_age",
        stroke: "black", // Make the line black
        strokeWidth: 2,  // Make it thick enough to see
        strokeOpacity: 0.8
      }
    )),

    // Add a baseline for each row
    Plot.ruleY([0], {stroke: "#ccc"})
  ]
})
```

</div>


It is also interesting to look at the distribution of entry ages by their current primary non-monogamy identity. This does not necessarily mean they started their non-monogamy journey with the same identity but it shows some interesting things like how Relationship Anarchy has the youngest median starting point, and Lifestyle/Swinger has the oldest, with the others falling in between.

<div class="card">

Vertical black lines indicate median estimated starting age.

```js

// 2. The Ridgeline Plot
Plot.plot({
  width: width,
  height: 800,
  marginLeft: 180, // Space for labels like "Relationship Anarchy"
  
  x: {
    label: "Estimated Age at Entry",
    grid: true,
    domain: [15, 60] // Focus on the main adult years (optional)
  },
  
  y: {
    axis: null, // Hide the Y axis numbers (we just care about the shape)
    label: "",
    // domain: [0, 0.4] // Adjust height overlap manually if needed
  },
  
  // Define "Sticky" colors again so they match your previous charts
  color: {
    legend: false,
    scheme: "Spectral" 
  },

  marks: [
    Plot.areaY(filteredEntry, Plot.binX(
      {y: "proportion-facet",
        filter: null,
        label: null
      }, // 'prop' calculates density instead of raw count
      {
        x: "entry_age",
        fill: "identity", // Color by identity
        // THE RIDGELINE MAGIC:
        fy: "identity", // Split into rows by identity
        curve: "basis", // Smooth the lines (looks nicer than jagged bars)
        thresholds: 40, // Controls how smooth/detailed the curve is
        stroke: "#666",
        tip: false // Add tooltips
      }
    )),

    Plot.ruleX(filteredEntry, Plot.groupZ(
      {x: "median"}, // Calculate the median of 'x' for each group
      {
        x: "entry_age",
        fy: "identity", // Make sure we calculate it per row
        stroke: "black", // Make the line black
        strokeWidth: 2,  // Make it thick enough to see
        strokeOpacity: 0.8
      }
    )),

    // Add a baseline for each row
    // Plot.ruleY([0], {stroke: "white", strokeOpacity: 0.5})
  ]
})
```

</div>
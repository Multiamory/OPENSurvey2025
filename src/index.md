---
title: 2025 OPEN Community Survey Report
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

:root {
  --primary-color: #5f4d8d;
  --accent-color: #f57c00;
  --text-color: #333;
  --bg-color: #f9f9f9;
}

/* Ensure content uses full width */
.observablehq {
    max-width: 100% !important;
    padding: 0 1rem;
}

div.card {
  /* The Magic */
  background: rgba(255, 255, 255, 0.4); /* Higher opacity for better text readability */
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  
  /* The Border simulates the glass edge */
  border: 1px solid rgba(255, 255, 255, 0.6);
  border-top: 1px solid rgba(255, 255, 255, 0.8);
  
  border-radius: 20px;
  box-shadow: 0 10px 40px -10px rgba(0, 0, 0, 0.05); /* Softer shadow */
  padding: 2.5rem; 
  margin-bottom: 3rem;
  max-width: 1200px; /* Constrain max width for readability on large screens */
  margin-left: auto;
  margin-right: auto;
}

/* Typography Overrides */
h1, h2, h3 {
    font-weight: 700;
    color: var(--primary-color);
    margin: 0; /* Reset margins */
    padding: 0;
}
h1 { 
    font-size: 2.5rem; 
    margin-bottom: 2rem; 
    text-align: center; 
}
h2 { 
    font-size: 1.8rem; 
    margin-top: 3rem; 
    margin-bottom: 1.5rem; 
    border-bottom: 2px solid rgba(95, 77, 141, 0.1); 
    padding-bottom: 0.5rem;
    text-align: left; /* Consistent alignment */
}
h3 { 
    font-size: 1.4rem; 
    margin-bottom: 1rem;
    text-align: left;
}
p, li { 
    font-size: 1.1rem; 
    line-height: 1.6; 
    color: #4a4a4a;
}
li {
    margin-bottom: 0.5rem;
}

/* Fix "gutter" issue by centering and constraining text width to matching card width container */
main {
    max-width: 1200px !important;
    margin: 0 auto !important;
}

/* Force standard elements to expand */
p, ul, ol, li {
    max-width: 100% !important;
    width: 100%;
    box-sizing: border-box;
}

/* Button Styling */
.button-link {
    display: inline-block;
    padding: 14px 28px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white !important; /* Force white text */
    text-decoration: none !important;
    border-radius: 50px;
    font-weight: 600;
    font-size: 1.1rem;
    margin-top: 20px;
    box-shadow: 0 4px 15px rgba(102, 126, 234, 0.4);
    transition: transform 0.2s ease, box-shadow 0.2s ease;
}
.button-link:hover {
    transform: translateY(-2px);
    box-shadow: 0 6px 20px rgba(102, 126, 234, 0.6);
    color: white !important;
}

/* Responsive tweaks */
@media (max-width: 600px) {
    div.card { 
        padding: 1.5rem; 
        border-radius: 16px;
    }
    h1 { font-size: 1.8rem; }
    h2 { font-size: 1.5rem; }
    .observablehq { padding: 0 0.5rem; }
}

</style>


```js
import * as Inputs from "@observablehq/inputs";

// Global Responsive Check
const isMobile = width < 600;
```


# 2025 OPEN Community Survey Report

Multiamory was proud to be one of the primary sponsors of the **2025 OPEN Community Survey**. Conducted in partnership with Dr. Amy Moors of Chapman University (IRB #26-13), this year's survey represents the **largest dataset of non-monogamous experiences compiled to date**, with 5,885 respondents from 65 countries.

This report offers a look into the lives, experiences, and challenges of non-monogamous individuals. Below are some interactive visualizations and key findings.

<div class="card" style="text-align: center;">

### Read the Full Report

For the complete analysis, data breakdowns, and detailed methodology, please visit the official report on the OPEN website.

<a href="https://www.open-love.org/blog/2025-community-survey-report" class="button-link" target="_blank">View Main Report</a>

</div>


```js
const demographics = [
  {category: "LGBTQ+", value: 76, fill: "#5f4d8d"},
  {category: "Heterosexual", value: 24, fill: "#eee"}
];
```

<div class="card">

### LGBTQ+ Overlap

Significant overlap continues to exist between non-monogamy and LGBTQ+ identities:

- Only **24%** identified as heterosexual/straight.
- **71%** identified as cisgender.

```js
// 100% Stacked Bar
Plot.plot({
  width: width,
  height: 120,
  style: { fontSize: "14px", fontFamily: "Inter, sans-serif" },
  x: { axis: null, domain: [0, 100] },
  y: { axis: null },
  color: {
    domain: ["LGBTQ+", "Heterosexual"],
    range: ["#5f4d8d", "#e0e0e0"]
  },
  marks: [
    Plot.barX(demographics, Plot.stackX({
      x: "value",
      fill: "category",
      title: d => `${d.category}: ${d.value}%`,
      inset: 0.5,
      rx: 8 // Rounded corners for modern look
    })),
    // Labels centered on the segments
    Plot.text(demographics, Plot.stackX({
      x: "value",
      text: d => `${d.category}\n${d.value}%`,
      z: "category",
      fill: d => d.category === "LGBTQ+" ? "white" : "#333",
      fontWeight: "bold",
      fontFamily: "Inter, sans-serif"
    }))
  ]
})
```

</div>


# Relationship Identity Overlap

How do different non-monogamous identities intersect? 
Participants were able to select multiple options for their non-monogamous identity, and then were asked to select just one as the identity they *most* identify with. We can see that overall Polyamory was the most commonly selected option, followed closely by ENM/CNM, but this varies if we filter by primary identity.

Select a **Primary Identity** below to see which other relationship styles that specific group also identifies with.

```js
// 1. LOAD DATA
const overlapData = await FileAttachment("data/identity_overlap.csv").csv({typed: true});
```

<div class="card">

```js
// 2. CREATE DROPDOWN
const primaryOptions = overlapData.map(d => d.primary_identity)
  .filter((v, i, a) => a.indexOf(v) === i) // Remove duplicates
  .sort(); // Sort alphabetically

const selectedPrimary = view(Inputs.select(primaryOptions, {
  label: "Select a Primary Identity to filter: ",
  value: "All Respondents"
}));
```

```js
// 3. FILTER DATA
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
  marginLeft: isMobile ? 10 : 200, // On mobile, remove margin (labels go on top)
  marginBottom: 40,
  style: {
      fontSize: isMobile ? "12px" : "14px",
      fontFamily: "Inter, sans-serif"
  },
  
  x: {
    label: "Percentage of this group (%)",
    percent: true,
    grid: true
  },
  y: {
    label: null,
    tickSize: 0,
    tickPadding: 5,
    axis: isMobile ? null : "left" // HIDE axis on mobile
  },
    // "Sticky" Colors
  color: {
    domain: identities,
    legend: false,
    scheme: "Spectral"
  },
  marks: [
    Plot.barX(filteredData, {
      x: "overlap_percentage",
      y: "secondary_identity",
      fill: "secondary_identity",
      stroke: "#999",
      sort: {y: "x", reverse: true},
      tip: { format: { x: true, y: false, fill: false } }
    }),
    
    // DESKTOP: Labels are handled by axis "y" above.
    
    // MOBILE: Custom labels placed ABOVE the bars
    isMobile ? Plot.text(filteredData, {
        x: 0, // Align to left
        y: "secondary_identity",
        text: "secondary_identity",
        textAnchor: "start",
        dy: -12, // Move UP above the bar
        dx: 2,
        fill: "#333",
        fontWeight: "bold"
    }) : null,

    Plot.ruleX([0])
  ]
})
```

</div>


<div class="card" style="background: rgba(255, 254, 156, 0.15); border-color: rgba(255, 254, 156, 0.3);">

### Other identities:

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

---

# Stigma and Discrimination

A sobering finding from the survey is the persistence of stigma. **61%** of respondents reported experiencing stigma or discrimination based on their non-monogamous identity in at least one domain (employment, healthcare, family, etc.) over their lifetime.

```js
const stigmaData = [
  {type: "Experienced Stigma (Lifetime)", value: 61, fill: "#d32f2f"},
  {type: "Experienced Stigma (Last 12mo)", value: 40, fill: "#f57c00"},
  {type: "Fear Judgment", value: 54, fill: "#7b1fa2"}
];
```

<div class="card">

### Experiences of Stigma

Despite growing awareness, non-monogamous individuals continue to face significant challenges.

```js
Plot.plot({
  width: width,
  height: 250,
  // Increase margin significantly for desktop long labels, remove for mobile
  marginLeft: isMobile ? 10 : 250, 
  marginRight: 40,
  x: {
      label: "Percentage of Respondents (%)", 
      domain: [0, 80], 
      grid: true,
      ticks: 5
  },
  y: {
      label: null,
      domain: stigmaData.map(d => d.type),
      axis: isMobile ? null : "left" // HIDE axis on mobile
  },
  style: {
      fontSize: isMobile ? "12px" : "15px",
      fontFamily: "Inter, sans-serif"
  },
  marks: [
    // Bars
    Plot.barX(stigmaData, {
      x: "value",
      y: "type",
      fill: "fill",
      fillOpacity: 0.8,
      sort: {y: "x", reverse: true},
      tip: true
    }),
    
    // Value Labels at the end of bars
    Plot.text(stigmaData, {
        x: "value", 
        y: "type", 
        text: d => `${d.value}%`, 
        dx: 8, 
        textAnchor: "start", 
        fill: "#333",
        fontWeight: "bold"
    }),

    // MOBILE LABEL: Top of bar
    isMobile ? Plot.text(stigmaData, {
        x: 0, 
        y: "type",
        text: "type",
        textAnchor: "start",
        dy: -12, // Move UP above the bar
        dx: 2,
        fill: "#333",
        fontWeight: "bold"
    }) : null,

    Plot.ruleX([0])
  ]
})
```

*Respondents shared personal stories of family disownment, healthcare refusal, and housing discrimination. **54%** often wonder if others judge them for their non-monogamy.*

</div>

---

# A Call for Advocacy

There is broad support within the community for organizing and advocacy. **75%** agree it is important for non-monogamous people to get organized and fight for rights, and **82%** see the non-monogamy movement as part of the broader project for justice and liberation.

<div class="card" style="display: flex; flex-direction: column; align-items: center; text-align: center;">

### Join the Movement

OPEN advocates for legislative protections, public awareness, and inclusive policies.

<a href="https://www.open-love.org/join" class="button-link" target="_blank">Get Involved</a>

</div>


----

### Respondent Demographics

As we know from previous research, the non-monogamous population as a whole is diverse. In this study, however, the respondends skewed whiter and younger than global averages.

- **76%** of US respondents identified as White / European descent.
- The majority (50.6%) were between the ages of **27-39**.

## Years of Experience:

How long had the survey participants been practicing consensual non-monogamy? 
Adjust the slider to change the detail level. Notice how the shape changes.

<div class="card">

```js
// 1. Load Data
const experienceData = await FileAttachment("data/experience_data.csv").csv({typed: true});


// 2. Create the Slider controls
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
  marginTop: 20,
  marginLeft: isMobile ? 50 : 60,
  style: {
      fontFamily: "Inter, sans-serif"
  },
  
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
  color: {
      range: ["#5f4d8d"]
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
  height: 150,
  marginLeft: isMobile ? 40 : 180, 
  style: {
      fontSize: isMobile ? "11px" : "14px",
      fontFamily: "Inter, sans-serif"
  },
  
  x: {
    label: "Estimated Age at Entry",
    grid: true,
    domain: [15, 60] // Focus on the main adult years (optional)
  },
  
  y: {
    axis: null, // Hide the Y axis numbers (we just care about the shape)
    label: null,
  },

  marks: [
    Plot.areaY(entryData, Plot.binX(
      {y: "count"
      },
      {
        x: "entry_age",
        fill: "#5f4d8d",
        fillOpacity: 0.6,
        curve: "basis", // Smooth the lines (looks nicer than jagged bars)
        thresholds: 40, // Controls how smooth/detailed the curve is
        stroke: "#5f4d8d",
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
    
    // Annotate Median Text
    Plot.text(filteredEntry, Plot.groupZ(
        {x: "median"},
        {
            x: "entry_age",
            text: d => `Median: ${d3.median(d, d => d.entry_age)}`,
            dy: -20,
            fontWeight: "bold"
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
  marginLeft: isMobile ? 120 : 180, // Space for labels like "Relationship Anarchy"
  style: {
    fontSize: isMobile ? "11px" : "14px",
    fontFamily: "Inter, sans-serif"
  },
  
  x: {
    label: "Estimated Age at Entry",
    grid: true,
    domain: [15, 60] // Focus on the main adult years (optional)
  },
  
  y: {
    axis: null, // Hide the Y axis numbers (we just care about the shape)
    label: "",
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
  ]
})
```

</div>
```
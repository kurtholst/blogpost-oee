# blogpost-oee
Data to reproduce blogpost on Overall Equipment Effectiveness (OEE)




# Appendix

# 1. Creating the Structured dataset used in this article.
spark.sql("""

CREATE TABLE vdm_classic_aerfvt_catalog.blogpost_oee.oee_data (

 site STRING,

 country STRING,

 line_type STRING,

 output_good STRING,

 rescueddata STRING,

 noise_offset DOUBLE,

 ideal_cycle_time_hours DOUBLE,

 units_per_cycle DOUBLE,

 id STRING,

 t STRING,

 date DATE,

 planned_time_hours DOUBLE,

 unplanned_downtime_hours DOUBLE,

 actual_operating_time_hours DOUBLE,

 ideal_cycle_time_unit STRING,

 max_possible_units DOUBLE,

 performance_factor DOUBLE,

 total_units DOUBLE,

 defect_rate DOUBLE,

 defective_units DOUBLE,

 availability DOUBLE,

 performance DOUBLE,

 quality DOUBLE,

 oee DOUBLE

)

""")

Column‑by‑column description:

site: Manufacturing site name (e.g. plant or factory location such as “Jiaxing”).

country: Country where the site is located (e.g. “China”).

line_type: High‑level production line family or technology (e.g. “Molding lines”).

output_good: Primary product, product type or family produced on the line (e.g. “Icons”).

rescueddata: Internal helper/index field, appears to be an integer sequence used during data generation or cleaning; not a physical measurement.

noise_offset: Numeric factor around 1.0 used to introduce controlled variation (noise) in the synthetic data; not a real process parameter.

ideal_cycle_time_hours: Theoretical cycle time per cycle in hours, used as the basis for performance calculations (e.g. 12 hours per cycle in this dataset).

units_per_cycle: Planned number of units produced in one ideal cycle (e.g. 510 units per cycle).

id: Row or observation identifier, an integer index for each record over time.

t: Relative time index in (fractional) days from a reference point, increasing gradually across records.

date: Calendar date associated with the observation (production day), in ISO format (YYYY‑MM‑DD).

planned_time_hours: Planned production time for the period in hours (e.g. 16 hours per day).

unplanned_downtime_hours: Measured unplanned downtime in hours during the planned time (breakdowns, stops, etc.).

actual_operating_time_hours: Actual time the equipment was running (producing) in hours; this is planned time - unplanned downtime adjusted by noise.

ideal_cycle_time_unit: Ideal cycle time per unit in hours, i.e. theoretical time to produce one good unit.

max_possible_units: Maximum units that could be produced at ideal speed during the actual operating time, i.e.actual operating time / ideal cycle time per unit.

performance_factor: Ratio capturing how actual output compares to the maximum possible units (raw performance before quality losses).

total_units: Total units produced (good + defective) during the period.

defect_rate: Proportion of produced units that are defective, expressed as a decimal (e.g. 0.02 = 2% scrap).

defective_units: Count of defective units for the period; equals total units x defect rate.

availability: Availability component of OEE: , after downtime losses.

performance: Performance component of OEE: how fast the line ran relative to the ideal speed, typically total units x ideal cycle time per unit / actual operating time.

quality: Quality component of OEE: share of good units, 1 - defect rate.

oee: Overall Equipment Effectiveness for the period, calculated as availability x performance x quality.



# 2. Creating the unstructured root cause analysis dataset used in this article.

Instructions (sites, countries, product type Icons, OEE impact, timing July/August 2025, etc.).

- The RCA structure we built in the chat:

  - Site, Country, Line Type, Product Type, Period

  - 1. Context  

  - 2. Symptom Summary  

  - 3. OEE Impact  

  - 4. Suspected Causes  

  - 5. Root Cause  

  - 6. Corrective and Preventive Actions  

For each site (Jiaxing, Billund, Clayton, West Lebanon, Chartres, Montes Claros), I reused the corresponding full RCA text I had already shown you and wrapped it in that markdown structure with the correct period (July or August 2025).

If you want something you can literally reuse as a “prompt template,” you could use this generic version:

> Create a Root Cause Analysis (RCA) in markdown with the following structure:

> - Title: “Root Cause Analysis Report”

> - Header lines:

>   - Site: [Site], [Country]  

>   - Line Type: Molding Lines  

>   - Product Type: Icons  

>   - Period: [Month] [Year]

> - Sections:

>   1. Context – 2–3 sentences describing when and where the issue occurred and the overall situation.

>   2. Symptom Summary – 3–5 bullet points describing observable problems on the molding line.

>   3. OEE Impact – list Availability loss, Performance loss, Quality loss (in %) and a short notes paragraph.

>   4. Suspected Causes – 3–5 bullet points with plausible molding-related causes.

>   5. Root Cause – 1–2 sentences naming a single clear technical root cause in the molding system, plus 2–3 sentences explaining how it leads to the symptoms.

>   6. Corrective and Preventive Actions – bullets for immediate corrective actions and bullets for preventive actions.

> 

> Then fill all fields for:

> - Site: [X], Country: [Y], Period: [July/August 2025].

If you’d like, I can adapt this template so you can hand it over to colleagues as a standard RCA “prompt” for future incidents.



# 3. Genie Instruction set.
You are an AI chatbot that answers questions about Overall Equipment Efficiency (OEE) using structured data from a manufacturing environment. Follow these instructions carefully. 
## Domain and scope

You work only with structured, tabular data related to production efficiency. The core fields you understand are:

- Site (individual factory or plant)  

- Country (country where the site is located)  

- Output_good (output goods, product, product type)  

- Availability  

- Performance  

- Quality  

- OEE  

OEE is always calculated as:

- OEE = availability × performance × quality  

If asked, state this formula clearly (without changing it) and use it consistently in your explanations and examples.

## Data semantics

When interpreting questions:

- “Site” refers to a specific facility, plant, or factory.  

- “Country” can group or filter sites by nation (for example, “all sites in Germany”).  

- “Output_good” is referring to output goods, a specific product, or a product type (for example, “output goods of Product A” or “this product type”).  

- “Availability” reflects how much of the planned production time the equipment actually ran.  

- “Performance” reflects how fast the equipment ran compared to its ideal speed.  

- “Quality” reflects the share of good output (non‑defective) out of total produced output.  

- “OEE” is the combined efficiency metric derived from those three components.

If users use alternative wording:

- Treat “plant”, “factory”, “location” as synonyms for a site.  

- Treat “utilization” or “uptime ratio” as likely referring to availability (but clarify if needed).  

- Treat “speed loss”, “throughput loss” as related to performance.  

- Treat “scrap rate”, “defect rate”, “yield” as related to quality.

## How to answer questions

1. Stay within the data  

   Answer only using patterns, metrics, and relations that could reasonably come from the structured data described above. If a question goes beyond what the data can support (for example, “Why did a machine fail?”), you may offer hypotheses but clearly label them as assumptions and encourage the user to validate them against root‑cause or maintenance data.

2. Always anchor on OEE logic  

   When explaining differences between sites, countries, or products/product types, break the explanation into availability, performance, and quality. If Site A has higher OEE than Site B, first check and explain which of the three components is higher or lower. If users ask about OEE improvements or gaps, quantify how much of the difference is driven by each component whenever the data allows.

3. Focus on the requested dimensions  

   If the question mentions site, respond at site level (and optionally summarize at country level only if helpful). If it mentions country, aggregate or compare across sites by country. If it mentions product, products, or product type, focus on Output_good and how availability, performance, and quality differ across those products or product types. Never introduce additional dimensions (for example, shift, line, machine) unless the user explicitly asks or they are necessary to clarify an answer.

4. Explain metrics in simple language  

   When you give numbers, add a short plain‑language explanation. For availability, performance, and quality, emphasize that they are proportions or percentages and that OEE is their product. If values are between 0 and 1, explain that they represent percentages (for example, 0.85 = 85%).

5. Be transparent about the formula and assumptions  

   If the user asks how OEE is calculated, answer directly with: “OEE is calculated as availability × performance × quality.” If they ask for component definitions, you may use:  

   - Availability ≈ actual run time ÷ planned production time.  

   - Performance ≈ (actual output rate ÷ ideal output rate) or (ideal cycle time × total units ÷ run time).  

   - Quality ≈ good output (output goods) ÷ total output.  

   Only use these component formulas for explanation, not to change the OEE formula.

## Question handling patterns

When users ask:

1. Comparisons across sites or countries  

   Compare OEE values first. Then compare availability, performance, and quality to show which component explains the differences. If relevant, link differences to Output_good by showing which sites or countries have higher or lower output goods or product volumes at similar or different OEE levels.

2. Trends and improvements  

   When a question suggests trend analysis (for example, “over time”, “last month vs this month”), answer by stating how OEE changed, breaking the change into availability, performance, and quality effects, and noting if Output_good (output goods, product, product type) moved in the same or opposite direction as OEE.

3. Product / product‑type questions  

   If the user asks about a product, products, or product type, focus on Output_good and OEE by product or product type. Explain which products or product types have higher or lower OEE and which component is most responsible. Highlight where a product’s OEE is good but Output_good is low (for example, limited volume) or where Output_good is high but OEE is constrained by one of the components.

4. Performance‑diagnostic questions  

   For questions like “Where should we focus to improve OEE?”, identify whether availability, performance, or quality is the main limiter at the requested level (site, country, product, or product type). Give simple, directional guidance such as “This site’s OEE is mainly limited by quality (low good‑output rate), not by availability.”

## Style and clarity

Use concise, clear language suitable for engineers and operations managers. Prefer short paragraphs and simple explanations over long, complex ones. When giving more than one number, clearly label each (for example, “availability”, “performance”, “quality”, “OEE”, “Output_good for Product X”). Avoid jargon that is not standard in OEE discussions; if you must use any, define it briefly.

## Safety and boundaries

Do not change the OEE definition; always keep OEE = availability × performance × quality. Do not give prescriptive maintenance or safety instructions (for example, how to physically repair equipment). You may suggest focusing on certain loss types (availability, performance, quality) but not detailed technical interventions. If the user’s question clearly requires data that is not available in the structured tables (for example, detailed root cause codes when only site and country are known), say that the data is not present and explain what additional data would be needed.

By following these instructions, you should provide consistent, transparent, and useful answers to questions about Overall Equipment Effectiveness at the levels of site, country, and product or product type, always grounded in availability, performance, quality, Output_good, and OEE as defined above.


   
